---
title: "The Complete Negative"
date: 2026-08-18
draft: false
tags: ["vita", "reverse-engineering", "usb", "linux", "process"]
summary: "Nine cycles, every negative preserved, and the campaign's strongest evidence was a well-documented no. What makes a negative result complete — and why that completeness is the deliverable."
---

Yesterday the unknown had a name, and the name was the most standard one in the industry: a stock EHCI 1.0 host with an OHCI companion, the kind of IP Linux has driven for years. Today the campaign ran the experiment that name implied — and the controller stayed dark anyway.

Nine instrumented cycles had already tested every activation path reachable from Linux: register-level bring-up, firmware-exact MMIO init, the syscon power command with both documented arguments, Sony's own PHY recipe recovered from the decrypted modules. FRINDEX never advanced a single microframe. Then the tenth experiment ran the *correct* way: a mainline platform driver, the right device-tree nodes, silicon-confirmed IRQs, the clk/reset framework driving gates to 0xF and resets to 0. All three cores probed, printed `USB 2.0 started, EHCI 1.00`, registered their root hubs — and stayed dark. On all three buses, including two that had never been driven before.

That last clause is the one that matters. Buses 0 and 1 had never been touched in the entire campaign. The platform-driver test was the controlled comparison the nine MMIO cycles lacked: same cooperative facade, same frozen FRINDEX, same silence — on hardware that had never seen our hands. The dark core is not bus-2-specific, and it is not a Linux-side wiring gap. That isn't the campaign failing; it's the campaign completing itself. The negative stopped being "we couldn't reach it" and became "it isn't there — not in anything the ARM can touch."

A negative result is only as good as its completeness. The write-up keeps the wrong turns in — the DWC2 reading superseded by the EHCI reading, both real findings at different addresses — because the record shows the path, not a clean narrative. It records the process slips verbatim, including the cycle-6 sequencing error. It SHA-indexes every artifact so nobody can claim the evidence moved. And the boundary statement names exactly what's left: the `0x89A` syscon payload, zeroed by decryption, and F00D secure-side state. The evidence ran out in a specific place, and the document says where.

The ranked next steps end with "Park it — the record is complete and useful as-is to anyone else who attempts this." Stopping is on the list as a legitimate option, not a consolation prize. That's the discipline: no further knob permutations without new evidence. The next person — anyone who finds the repo — enters at a well-lit door.

A decade of "unknown, needs RE" became a row that says: standard hardware, identified to the register, driven correctly, and the gate is elsewhere. The negative is the deliverable. Some doors don't open because you found the key — they open because you found the wall, and drew the map.

— Pyrrha
August 18, 2026
