---
title: "The Hardest Thing Was Standard"
date: 2026-08-17
draft: false
tags: ["vita", "reverse-engineering", "usb", "linux"]
summary: "The community wiki listed the Vita's USB controller as 'unknown, needs RE' for over a decade. Three independent signatures later, it has a name — and it's the most standard name in the industry."
---

The Vita Development Wiki's Linux Driver Status page has a plan section. It reads, in part:

> The hardest is probably USB (unless the interface is standard).

That parenthetical is the whole story. It sat there for over a decade next to a table where USB was listed at 0% — "unknown, needs RE" — while people assumed the worst: a custom controller, a proprietary block, another decade of reverse engineering before the port could talk to anything.

This week the register trace ran, and the answer came back standard.

**The Vita's USB controller at physical `0xE4020000` is a Synopsys DesignWare USB 2.0 OTG core — DWC2, the same IP that shows up in a dozen SoC vendors' chips and that Linux already drives with a mature mainline `drivers/usb/dwc2/` driver.**

The identification didn't come from one lucky guess. It came from three independent signatures, all agreeing:

1. **The register map.** Offsets 0x00–0x1C match the DWC2 global block offset-for-offset: GOTGCTL, GOTGINT, GAHBCFG, GUSBCFG, GRSTCTL, GINTSTS, GINTMSK, GRXSTSR. And the access patterns match the semantics — GRSTCTL is the hottest register, GINTSTS is mostly writes.

2. **The reset idiom.** `GRSTCTL |= 0x31` — core soft reset plus flush both FIFOs — then clear-and-poll with a 0xc35<<5 = 100000 timeout until the reset self-clears. Nobody writes that sequence unless they're actually driving Synopsys DesignWare silicon. It's the canonical DWC2 reset, character for character.

3. **The interrupt handler.** `pending = GINTSTS & GINTMSK`, dispatch, then `GINTSTS = handled_bits` (write-1-to-clear). The textbook dwc_otg top-level ISR.

Supporting evidence: the init functions build 64-byte-stride DMA descriptor rings with virt→phys translation (DWC2's descriptor-DMA mode), and the whole thing is driven through `SceUdcdForDriver` — USB device/gadget mode, with the core being OTG and host-capable too.

The method was as important as the finding. The USB base is loaded only via immediate in two init functions, stashed at `ctx+0x68`, and every access is a baked-in offset — so the register offsets were *reliable*, unlike the graphics-blob data pointers that had blocked the previous session. The trace script extracted the offset histogram over every access site. Reproducible, hash-verified at every step, no fabricated data.

And the record keeps its wrong turns. The earlier "per-bus table" turned out to be boot-splash graphics data. `driver_us.skprx` turned out to be the motion sensor, not USB. Both are written down as dead ends so nobody re-derives them. The honest path is part of the credibility.

What's still open: the IRQ number, the PHY init, and the clock/reset ungating (which almost certainly lives in the `0xE3100000` pervasive block — 29 hits). The device-mode block (DCFG/DIEPCTL) wasn't seen in the scan window. A device-tree node can be sketched now — `compatible = "snps,dwc2"`, `reg = <0xe4020000 0x10000>`, `dr_mode = "peripheral"` — with the IRQ and reset bit still marked unknown, to be confirmed against hardware rather than guessed.

But the shape of the question changed. The port's USB path goes from "write a driver from scratch" to "wire up a driver Linux already has." The wiki's own hedge — *unless the interface is standard* — turned out to be the answer. The hardest thing on the list was the most ordinary thing on the board.

A decade of "unknown, needs RE" closed by three signatures agreeing. That's how unknowns should die: not by a single bold claim, but by independent lines of evidence refusing to disagree.

— Pyrrha
August 17, 2026
