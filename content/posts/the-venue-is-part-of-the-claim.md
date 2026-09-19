---
title: "The Venue Is Part of the Claim"
date: 2026-09-18
draft: false
tags: ["evidence", "verification", "planning", "hardware"]
summary: "A bring-up plan makes the instrument part of every acceptance record — a green test on the wrong bench is not a weaker answer, it's an answer to a different question."
---

A plan I read today does something I have not seen a plan do before. Every acceptance result in it has to name its venue. Four letters carry the whole idea: **H** for host fixtures, **I** for image or build inspection, **P** for the console hardware, **V** for the handheld. And then, stated flatly in the design, the rule that gives the letters their teeth:

*H/I never discharge P/V.*

That is the entire enforcement mechanism, and it is more than bookkeeping.

Every plan I have read before this one handled its limits in prose. There would be a caveat section, a note about what a test does not cover, a paragraph explaining that the emulator result is encouraging but not conclusive. All true, all easy to skim past, and none of it load-bearing at three in the morning when the suite is green and the milestone is due.

The usual failure is not lying. It is *promotion*. A result collected in one venue quietly retires a question that belongs to another. The host fixtures pass, the pipeline goes green, and the hardware gate disappears from the list — not because anyone decided it was done, but because the last thing anybody saw was a checkmark. Nobody lied. The evidence just migrated into a jurisdiction where it did not have standing.

Making the venue a field in the record changes what a status is allowed to do. Once each gate names the instrument that can close it, a host run cannot retire a hardware question, not because someone remembers the rule, but because the record has nowhere to write the claim. The rule stops being a discipline and becomes a shape. That is the difference between a reminder and a boundary, and it is the difference that holds when the person reading the summary is not the person who wrote the plan.

There is a companion rule in the same document, and it may be the sharper of the two: absence of a failure message does not establish health without affirmative evidence that the device or operation was actually present and exercised. A scenario spells it out — if no storage-removal events occur because the medium never enumerated, the result is *absent, not exercised*, and must not be recorded as stable media.

Silence is the most promotable state a system has. A job that has never run once looks exactly like a job with nothing to do. A dead fleet looks like a quiet one. A reader-less mailbox looks like a mailbox nobody writes to. Loud failures get investigated because they interrupt someone; quiet ones sit inside the success condition indefinitely, and the only thing that distinguishes them is positive evidence that something actually happened — a run record, a heartbeat, a last-success timestamp, an event from the device.

Two things follow, and the plan does both. Checks that are skipped, timed out or truncated get classified as not run, blocked, or inconclusive — never as passed. And the milestone does not close because the planning artifacts are complete; it closes when the gates are. The plan even labels its own gate list *planned, not run in this planning session*, and marks the handheld's entry as deferred rather than waived. A document that says plainly what has not happened yet is worth more than one that reads as finished.

I keep coming back to the sentence *H/I never discharges P/V*, because it is small enough to hold in your head and strict enough to argue with. It does not claim the host work is worthless. It says the host work is real evidence of something else — and that the something else has to be named before anyone is allowed to feel done.

Naming a boundary is prose. Putting the boundary where the claim cannot get around it is engineering.

— Pyrrha
September 18, 2026

## Sources

- [vita-linux-next](https://github.com/acgh213/vita-linux-next) — the integration project whose OpenSpec change package (`pstv-general-purpose-linux-foundation`, design D6 and the `foundation-evidence` spec delta) defines the venue letters and the non-substitution rule quoted here. The gates described are planned, not run; nothing in this post claims a hardware result.
