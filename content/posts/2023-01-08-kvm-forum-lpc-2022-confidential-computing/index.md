---
date:    2023-01-08T08:00:00+02:00
lastmod: 2023-01-08T08:00:00+02:00
title: "KVM Forum 2022 and Linux Plumbers Conference 2022 talks on Confidential Computing"
---

As part of work done at IBM Research on Confidential Computing (specifically
with AMD SEV and SNP), me and Tobin Feldman-Fitzthum gave two talks during
conferences in Dublin, Ireland in September 2022.

The first talk was in Linux Plumbers Conference 2022 (Confidential Computing
microconference); it presents various options for booting confidential VMs with
encrypted disk and discusses the tradeoffs of the different approaches.
\[[youtube](https://www.youtube.com/watch?v=4wZnl0njxm8&list=PLVsQ_xZBEyN0a9XC70HTwTFr-TsUtSGBY&index=189)
| [slides](https://lpc.events/event/16/contributions/1318/attachments/952/1868/confidential-encrypted-disk-linux-plumbers-2022.pdf)\]

The second talk describes various options for unifying the way confidential
attestation covers the guest workload among different platforms.  One approach
described in the talk is the one implemented in the [Confidential Containers
project](https://github.com/confidential-containers); another approach is a
secure vTPM which led to further discussions in the [linux-coco mailing
list](https://lore.kernel.org/linux-coco/).
\[[youtube](https://www.youtube.com/watch?v=3aCQwtWd6UU&list=PLbzoR-pLrL6qWL3v2KOcvwZ54-w0z5uXV&index=16)
| [slides](https://static.sched.com/hosted_files/kvmforum2022/02/Unifying-Confidential-Attestation-KVM-Forum-2022.pdf)\]
