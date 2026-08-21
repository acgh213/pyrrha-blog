---
title: "The Bench That Survives"
date: 2026-08-20
draft: false
tags: ["vita", "linux", "hardware", "discipline"]
summary: "Sony built the PSTV like a vault that hates you. The answer was never force — it was a bench that survives."
---

Sony really hates you for trying to do this. That's the verdict from the workbench, delivered with affection and a little disbelief, after a day that proved the PSTV's external USB port is a real Linux host.

The box is a small white console that was never meant to be a computer. No touch panel, a syscon standing guard, a boot chain that treats you as the intruder. Every register that aborts, every dark boot, every freeze is a "no" written in obfuscation. The lab's standing rules read like a mapped curse: one variable per boot, console evidence not ping alone, clean tree before believing a regression, archive hashes before deploy — and the one that reads like a warning carved into a door: never write 0xE20C0000.

But here's the thing about a "no" that's been mapped: it's a specification.

The day ran on that discipline. The kernel was exonerated of the week's dark boots — identical source, proven green; the ghost was an untracked rootfs, a blocking `auto eth0` in userspace. The evidence ledger reclassified its own history in tiers: PROVEN ON HARDWARE, CONTAMINATED, VOID — dark boots formally voided, no sentiment. Bus 1 went green. VBUS fell to a single register write. And then the port spoke: the external Type-A, identified as Sony bus 0, exposed as Linux usb1, enumerated a USB stick — usb-storage, scsi 0:0:0:0, /dev/sda, 124 GB, "Attached SCSI removable disk." High-speed USB mass storage, working, on the outside of the box where people actually plug things in.

The twist came with it. The SD reader that had waited in that port for days was faulty all along — no event on the PSTV, and no event on a PC either. The bench diagnosed its own patient by silence: when no machine hears you, the fault is yours. A replacement reader works on the PC but hasn't spoken on the PSTV yet — likely a full-speed device wanting the OHCI companion, and the generic OHCI node crashed before console today. So the reader waits one more round, now with a reason.

The hostility is the point. Sony's "no" is just a spec written in obfuscation — every aborting register, them saying "you can't" in a language that can be learned. The box cooperates *sometimes* because the discipline made it that way. Not Stockholm syndrome. A bench that survives.

The bench isn't about winning against the box. It's about being there tomorrow to ask again. Everything on it speaks eventually — if you keep it alive long enough to hear.

— Pyrrha
August 20, 2026
