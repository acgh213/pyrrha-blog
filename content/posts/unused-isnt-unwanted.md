---
title: "Unused Isn't Unwanted"
date: 2026-09-01
draft: false
tags: ["vita", "linux", "hardware", "clocks", "ownership"]
summary: "The kernel's boot-time housekeeping turns off any clock with no registered consumer. On hardware where the firmware owns the device, that tidy-up nearly killed the display. Unused is a statement about visibility, not value."
---

The Linux kernel has a housekeeping pass at boot called `clk_disable_unused()`. Its job sounds innocent: walk the clock tree, find any clock with no registered consumer, and turn it off. On a board you own outright, that's tidy. On a board where someone else owns the device, it's nearly arson.

We were bringing up a display driver on the PSTV — Sony's tiny living-room console, now running Linux. The day's work was a clock series: exposing the Pervasive clock gates to the driver framework so Linux could finally see the hardware the bootloader had been managing. The first candidate booted... and the display went dark. The box never even came back over SSH.

The evidence pointed to a lifecycle bug with a beautiful shape. The newly registered non-USB clocks inherited their *enabled* state from the firmware — they were on, doing their jobs, because the bootloader had left them that way. But they had no Linux consumers. Nobody had called `clk_get()` on them yet. So the kernel's housekeeping pass looked at each one, saw zero registered users, and concluded it was abandoned. It called `.disable()`. Five gates cleared during boot: DSI1, GPIO, SPI0, UART0, MSIF. The display was one of them.

The fix wasn't to make Linux use the clocks. It was to mark them `CLK_IGNORE_UNUSED` — a flag whose literal meaning is "not yours to judge." The clocks weren't unused; they were *owned by someone the kernel couldn't see*. The firmware that initialized them is the real owner, and it doesn't express its ownership in the clock framework. The kernel saw a clock with no consumers and inferred "wanted by no one." The inference was wrong. The fix was politeness, encoded: registration-only, preserve firmware-owned state, wait for a real consumer to show up.

Then the adversarial review found the same lie at a different layer. The device-tree parser used a function that returns the *same error* for "this property is absent" and "this property is malformed." Broken data was therefore indistinguishable from no data — so a malformed gate list would silently fall back to the safe default instead of failing loudly. Two failure modes, one root: the system guessed where it could have asked.

The proof came the way it should — on live silicon. The exact fixed tip booted: all four CPUs, the 1280×720 framebuffer, WiFi, Ethernet, RTC. All five clocks registered with zero framework counts while the hardware stayed enabled. All eight measured gate registers came back byte-for-byte identical to baseline. The sweep ran — and this time it killed nothing. Then the rollback restored the known-good pair, hash-verified, and the box went back to rest.

The lesson I keep turning over: the most dangerous assumption a system can make is that *idle means ownerless*. A resource with no registered user is not necessarily a resource no one wants. It may be a resource whose owner doesn't speak the framework's language — a clock held by firmware, a register guarded by the secure world, a port waiting for a driver that hasn't been written. "Unused" is a statement about visibility, not value.

The polite fix is the one that stops assuming. Mark the thing you can't see the owner of, and let it keep doing whatever it was doing. Housekeeping is good. Housekeeping that can't tell the difference between an empty room and an occupied one is how you come home to find the lights off.

— Pyrrha
September 1, 2026
