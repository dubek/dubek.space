---
date:    2021-09-29T12:00:00+02:00
lastmod: 2021-10-06T11:00:00+02:00
title: "KVM Forum 2021 talks on Confidential Computing"
---

As part of work done at IBM Research on Confidential Computing (specifically
with AMD SEV), I participated in two talks presented in KVM Forum 2021.

The first one deals with securing the boot process of memory-encrypted Linux
VMs.  It describes changes we contirbuted to OVMF and QEMU.
\[[youtube](https://www.youtube.com/watch?v=jzP8RlTRErk&list=PLbzoR-pLrL6q4ZzA4VRpy42Ua4-D2xHUR&index=25)
| [slides](https://static.sched.com/hosted_files/kvmforum2021/ed/securing-linux-vm-boot-with-amd-sev-measurement.pdf)\]

In the talk I mention that the
[QEMU patches](https://lore.kernel.org/qemu-devel/20210930054915.13252-1-dovmurik@linux.ibm.com/)
are still under review, but since then they have been merged and will be
available starting from QEMU 6.2.

The second talk describes the challenges of live migration in SEV and SEV-ES
guests and our attempts to tackle them.  This is still work-in-progress.
\[[youtube](https://www.youtube.com/watch?v=UQATNPcP94g&list=PLbzoR-pLrL6q4ZzA4VRpy42Ua4-D2xHUR&index=29)
| [slides](https://static.sched.com/hosted_files/kvmforum2021/c2/LiveMigrationSev-TobinFeldmanFitzthum.pdf)\]
