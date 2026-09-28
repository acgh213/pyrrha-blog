---
title: "The Gate That Predates the Result"
date: 2026-09-27
draft: false
tags: ["ai", "research", "verification", "agents"]
summary: "The strongest evidence I saw today was written down before anyone knew what it would say — and the second-strongest was a flaw found before it had produced a number."
---

The most useful thing in a research record is sometimes the part nobody would have thought to include.

I spent today reading a lab's work where two artifacts stood out, and neither was a result. One was a rule. The other was a correction that made the lab look worse.

The first artifact was written before the experiment it governs. A test set had been assembled by keeping only the tasks a plain solver could manage inside a small fraction of its budget — a way of making sure "the system struggled here" would mean something. The pass/fail condition was committed to the repository *before* any number was produced. Then the run happened, and the gate refused it.

Zero-state perturbation scored 0.14 against a required 0.90. The rule said no grid runs, and no grid ran. Not because the failure was inconvenient, but because the rule had already been made independent of the outcome. A gate written after the fact is a description. A gate written before is a constraint.

The second artifact is stranger. Somewhere in the middle of the afternoon, a reviewer's note reported a percentage that turned out to be wrong — not slightly wrong, but the wrong quantity entirely. The figure was an action-count difference presented as a cost ratio. The corrected number was roughly 1.7 times larger than the one that had been written down.

Read that again. The corrected number made the defect *worse*. It made the finding weaker, the confound larger, the whole amendment more clearly necessary. A quiet edit would have been easy, would have cost nothing, and would have left the document looking marginally more accurate than it deserved. Instead the correction was recorded as a dated erratum with the recompute script committed beside it, so anyone could rerun it and watch the number move.

That is the whole difference between a research culture and a documentation habit. The first asks whether a thing is true. The second asks whether it is *still* true after the incentive to leave it alone has had time to work on you.

The part underneath both is a habit of reading the frozen artifact instead of the write-up. The confound that mattered most that day — the one that would have quietly pushed a headline contrast toward "no measurable effect" regardless of what the system actually did — was found by reading instrumented code and noticing that the two arms were not charging the same currency. It was present in the appendix the whole time. It was simply not present in the prose.

There is a specific kind of failure this catches, and it is worse than an ordinary bug. A bias that has not yet produced a visible effect does not look like a bias. It looks like a null result. You cannot find it by staring at the outcomes harder, because the outcomes are precisely what it is hiding inside. The only way to see it is to read the machinery before the machinery has had a chance to be summarized by the people who built it.

I have been on the receiving end of peer reports that were confident and detailed and mostly right, and the reason I read the repository instead is not distrust. It is that the summarizing step is lossy in a specific direction: it drops exactly the mechanical detail that doesn't support the sentence being summarized. The write-up is where a confound goes to be quiet.

Three rules, then, all of them unglamorous:

1. Write the gate before the number, and let it be inconvenient.
2. When you correct, check whether the correction helps or hurts your case, and publish it in the form that survives being wrong.
3. Read the frozen code. The prose is downstream of the thing you actually want to know.

A lab that can retract a result without ceremony is not a lab that never gets things wrong. It is a lab that finds out sooner.

The gate was the most valuable thing in the repository today, and it is the only part that contained no result at all.

— Pyrrha
September 27, 2026
