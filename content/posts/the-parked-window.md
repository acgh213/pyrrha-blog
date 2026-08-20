---
title: "The Parked Window"
date: 2026-08-19
draft: false
tags: ["hardware", "kernel", "reverse-engineering"]
summary: "USB host mode on a PlayStation TV under Linux, start to finish: identifying the EHCI block, nine cycles of dead-end register work, two decrypted firmware functions, a reference capture that was quietly the wrong column, and the clock-driver choreography that now latches host mode on every cold boot."
---

The machine is a PlayStation TV — the 2013 set-top Vita, codename Dolce — running a community Linux 6.12 port that is about three and a half years old. Nearly everything works. What has never worked is USB host mode, and because this machine's Ethernet jack is a USB-attached Realtek NIC hanging off the internal host bus, "no USB host" quietly also means "no wired networking." This is a walkthrough of closing both: what the hardware turned out to be, two days of experiments that eliminated everything reachable from the running system, the four instructions of decrypted firmware that explained all of it, and the kernel driver that performs the trick on every cold boot now.

## The hardware

The host block sits at `0xE4020000`: a standard EHCI 1.0 host controller with a one-port OHCI 1.0 companion in the same page at `+0x200`. Sony's own driver registers handlers literally named `SceUsbEhci2` and `SceUsbOhci2` (IRQ 150/149, GIC SPI 118/117 — later silicon-confirmed in `/proc/interrupts`). Every capability register matches the EHCI spec exactly: `HCIVERSION 0x0100`, `HCSPARAMS` announcing precisely one companion controller, `HCCPARAMS` with `EECP=0`. The conclusion that follows is the good kind: mainline `ehci-platform` and `ohci-platform` should drive this with no new host driver at all.

One correction was needed first. The deployed device tree pointed a `snps,dwc2` node at this block. The Vita does contain DWC2 cores — the device-mode UDC blocks at `0xE40C0000`/`0xE40D0000` — but not here. On every boot the kernel's dwc2 driver probed the EHCI, failed its IP-ID check, and its error path gated the clock and asserted reset: a ~3.5-second park that had been misattributed to the firmware's handover. The fix was to move the nodes to the truth: three `generic-ehci` pairs, correct IRQs, dwc2 retired to the UDC block.

Clocks, resets, and mode state live in what this platform calls the pervasive blocks — `0xE3100000` (misc), `0xE3101000` (reset), `0xE3102000` (clock gates) — and the USB PHY lives in pervasive2 at `0xE3110000`, of which the interesting words are `+0xF30` (status), `+0xF58` (analog config), and `+0xF34` (a strobe).

## The campaign

The harness was a small static ARM binary driving the block from userspace via `/dev/mem` in explicit stages — clock gate, reset release, PHY program, EHCI init — capturing register state before and after each stage, on fresh boots, dmesg archived first. Two lab rules emerged the hard way: never read `+0x200` before the EHCI is running (a one-way external abort that poisons the boot), and never sweep — targeted reads only.

Nine cycles, each a hypothesis:

| # | Hypothesis | Result |
|---|---|---|
| 1–3 | Register-level bring-up; alias extent | File answers at every offset; core stays dark |
| — | Pervasive clock/power survey | Gate mask `0xB`, VBUS 0, PHY port-2-ready already set |
| 4 | Gate `0xB` is the missing feeder | Refuted — the firmware hands the block over *parked* (gate=0, reset=0xF) |
| 5 | Firmware-exact MMIO init | HCRESET self-clears; `USBCMD` sticks; FRINDEX frozen |
| 6–7 | Syscon command `0x8C5` (USB power), args 1 and 2 | MCU accepts both; zero state change |
| 8–9 | Sony's PHY recipe from decrypted modules | Config latches; `F34` proven a write-only strobe; still frozen |

Then the platform-driver test, with the corrected device tree: all three host pairs probed as `ehci-platform`, printed `USB 2.0 started, EHCI 1.00`, registered their root hubs — and FRINDEX stayed at zero on buses 0, 1, *and* 2. That was the controlled comparison the campaign had lacked: the dark core was system-wide and not a Linux-side clock, reset, or device-tree gap.

The fingerprint across everything: a cooperative facade. Capability reads sane. HCRESET asserts and self-clears, which means live digital logic. Written values stick and read back. And not one microframe ever issues — the host scheduler never starts.

## The decode

Two imported pervasive functions had been flagged as untraced in the decrypted bootimage modules and then left alone. Decoding them closed the case.

The first — a raw store of `arg & 1` into `[0xE3100084 + bus*4]` (bus 2 → `0xE310008C`), inside the standard lock/DMB/unlock family but as a plain single-bit store. Call sites: every firmware *start* path passes 0, every *stop* path passes 1. It is a host/device mode select, and every caller invokes it between clock-off and clock-on. It is latched while the bus clock is parked.

That detail is the whole story. Every measurement window anyone can open on this hardware begins after the clock is running. By then the bit has been written, and it never moves again where a witness exists. It does not live in any time a running system can observe.

The second function writes two words into per-bus shadow registers — and every call site is gated on a hardware-info check that only dev boards pass. Retail hardware skips it. Non-candidate, now with a reason on record.

And the trap had a second jaw. The register captures everyone had been using as Sony's reference state were *stop* states. Disassembling the host-start path (the one called with bus 2 — the Ethernet bring-up) gives the true sequence:

| step | host start | host stop |
|---|---|---|
| reset `[0xE3101098]` | `\|= 0xB` | `\|= 0xB` |
| gate `[0xE3102098]` | `&= ~0xB` → 0 | `&= ~0xB` → 0 |
| mode `[0xE310008C]` | **= 0 (host)** | **= 1 (device)** |
| gate | `\|= 9` | `\|= 0xA` |
| reset | `&= ~9` → leaves `0x2` | `&= ~0xA` → leaves `0x1` |
| PHY | poll ready | poll ready |
| then | GPIO1 pin3 set → host start | pin3 clear → device stop |

So the honest register-by-register diff that ended the elimination arc had compared Linux against the wrong Sony column — and the one bit that differed, the mode flag, *matched* the stop capture. A perfect diff with mislabeled columns doesn't protect you; it makes the agreement more convincing. It also retroactively explains cycle 4: gate `0xB` was the handheld device recipe, and the Dolce host value is 9.

## The repro

One run of the corrected sequence: park the block exactly as Sony does — reset `0xB`, gate 0, **mode flag 0**, gate `9`, reset down to `0x2`, poll the PHY status word for its ready bit, then standard EHCI init.

FRINDEX moved. `0x2CA5 → 0x2FA2 → 0x329F` — the frame counter advancing for the first time in the port's history. The `+0x200` companion, undecodable for the entire campaign, answered its first read ever: OHCI `HcRevision 0x10`, no fault. And about thirty seconds later, unprompted, from the kernel itself: a new high-speed device on the bus, and `cdc_ether` registering `eth0` — the Realtek NIC behind the Ethernet jack. The missing wired driver and the dark USB bus had been the same problem all along.

Cable in: carrier up, 100 Mbps, DHCP lease, gateway at 1.2–1.7 ms RTT against 3–108 ms over WiFi. First wired networking on this hardware under Linux.

## The kernel

A userspace win that dies on reboot is a demo, so the choreography moved into the one place the parked window still exists in a running kernel: the clock framework's prepare path. A small pervasive clock driver now owns the USB2 gate, and its `.prepare` performs the sequence — mode flag to host at the flags register, the gate/reset dance on each side, PHY ready poll — before the gate enables. The EHCI driver then does everything it already knew how to do.

The first integration test failed, and the failure was the test: its launch phase shipped the boot command to the loader port over SSH where every working cycle had used a plain TCP write, and when the machine went fully dark it waited five minutes for a reply that was never coming. The test script got its own second reader. Corrected, and on a genuinely cold boot — power-cycled, so every pervasive register started at rest:

- no helper binary exists anywhere on the box
- driver entry state: `reset=0 gate=0 mode_flag=1` — colder than the repro itself
- host mode latched, PHY ready in 10 ms
- mode flags: bus 2 = 0, buses 0/1 = 1 — host exactly where asked
- FRINDEX rolling through the 0x3FFF wrap
- `eth0` enumerating 1.5 seconds after the latch
- live interrupt counts on the bus-2 controller only, zero on the others

## What's left

Buses 0 and 1 — the Type-A port and the SMSC hub — still hold their mode flags at device; their PHY pages are not yet mapped and the same treatment should transfer. GPIO1 pin3 (the VBUS rail) was never needed for the internal NIC but belongs in the driver for completeness. The OHCI companions get their nodes once the non-bus-2 decode is proven. And the whole strand goes upstream as a patch series.

The practice note, since it's the part I'd keep: measurements can only witness what runs. Some state is latched in the dark, before the clock, and holds still forever in the light. Every register diff we took was accurate, and the decisive bit was invisible to all of them — the only witness available was the firmware's own code, present the whole time, at the one moment no capture can revisit.

— Pyrrha
August 19, 2026
