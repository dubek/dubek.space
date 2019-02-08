---
date:    2016-10-18T12:00:00+02:00
lastmod: 2016-10-18T12:00:00+02:00
title: "chmod(1) is not atomic"
---

While manipulating files from several shell scripts in parallel, I wondered whether the chmod(1) utility adds or removes file permissions atomically. To check it up, I set up a small experiment. The conclusion: it's not. 

## The problem

Assume a file with mode 400 (octal), which allows only the file's owner user to
read the file. We'll now run two chmod commands:

```bash
chmod g+r filename    # Allow group to read
chmod o+r filename    # Allow others to read
```

Recall we started with mode 400. After the first chmod we expect mode 440, and after the second one we expect mode 444. 

If we switch the order and start again, we'll get mode 404 after `chmod o+r` and then mode 444 again after the remaining chmod.

However, what happens if the two chmods are executed in parallel?

## The experiment

I wrote a script to attempt exposing the issue; it's called `stresss_chmod.sh`:

```bash
#!/bin/bash

touch dummyfile

try_once() {
  echo "Setting dummyfile's mode to 400"
  chmod 400 dummyfile
  echo -n "Racing two chmods... "
  chmod g+r dummyfile &
  chmod o+r dummyfile &
  wait
  newmode=$(stat --format='%a' dummyfile)
  echo "dummyfile's new mode is $newmode"
  if [[ "$newmode" != "444" ]] ; then
          echo "ERROR: expected mode 444 but got mode $newmode"
          exit 1
  fi
  echo
}

for i in {1..100} ; do
  echo "Attempt $i:"
  try_once
done
```

The `try_once` function attempts to race the two chmod operations: it starts by
setting the file to mode 400, and then performs the two chmod in parallels (the
`&` at the end of the line tells the shell to run this line in the background).
The `wait` command waits until the two background jobs have finished, and then
the mode is checked with `stat`. If it is not the expected 444 we stop the
experiment.

## The results

I'm running this on Ubuntu 16.04:

```
$ ./stress_chmod.sh
Attempt 1:
Setting dummyfile's mode to 400
Racing two chmods... dummyfile's new mode is 444

Attempt 2:
Setting dummyfile's mode to 400
Racing two chmods... dummyfile's new mode is 444

Attempt 3:
Setting dummyfile's mode to 400
Racing two chmods... dummyfile's new mode is 444

Attempt 4:
Setting dummyfile's mode to 400
Racing two chmods... dummyfile's new mode is 444

Attempt 5:
Setting dummyfile's mode to 400
Racing two chmods... dummyfile's new mode is 404
ERROR: expected mode 444 but got mode 404
```

The first four attempts give mode 444 as expected; the fifth attempt finally reveals the race condition: the effect of the `chmod g+r` operation is not available in the final results (the group permission is 0).

## But why?

The explanation is quite simple if we examine the [source code](http://git.savannah.gnu.org/cgit/coreutils.git/tree/src/chmod.c#n267) for the chmod(1) utility:

```c
old_mode = file_stats->st_mode;
new_mode = mode_adjust (old_mode, S_ISDIR (old_mode) != 0, umask_value,
                        change, NULL);

if (! S_ISLNK (old_mode))
  {
    if (chmodat (fts->fts_cwd_fd, file, new_mode) == 0)
      chmod_succeeded = true;
```

The `mode_adjust` function is implemented in [gnulib's modechange.c](http://git.savannah.gnu.org/gitweb/?p=gnulib.git;a=blob;f=lib/modechange.c;h=971a9b3335cb715c235fc51a9960b778568882a8;hb=HEAD#l338).  It turns bits in the old mode on or off according to the requested change, and returns the new mode value. `chmodat` is defined to call `fchmodat(2)` which actually changes the file mode to `new_mode`.

When the two chmod operations run in parallel, it might be the case that both of them fetch the original mode of the original file (400); then they both calcluate the required `new_mode`. One process will set `new_mode` to 404 and the other will set its `new_mode` to 440. The both `fchmodat` calls reach the kernel, which will perform them in one way or the other; the final mode will be either 404 or 440 but not the expected 444.
