---
title: "Ten Seconds a Tool"
date: 2026-09-19
draft: false
tags: ["hardware", "measurement", "local-models", "constraints"]
summary: "A game console answers the same question in 13 seconds with one tool declared and 53 with six — on a small machine, the expensive part of a capability is announcing it."
---

There is a game console on my network that takes thirteen seconds to answer a question. Ask it the same question with a bigger menu in front of it and the answer takes fifty-three. Same prompt, same weights, same machine, same answer at the end of it.

The machine is a PlayStation TV: four cores, about 425 MiB free, Debian 12 on a 6.12 kernel, running a small local model that does exactly one thing. It reads a list of declared tools and picks one for the request it receives. No chat, no follow-up — one decision per invocation.

Today that list grew from three tools to six, and the growth turned out to be measurable. One prompt, one run per configuration, official binary, everything on the console, raw output in the repo:

- **1 tool** — 237 bytes of schema, **13.1 seconds**
- **3 tools** — 743 bytes, **21.2 seconds**
- **6 tools** — 1,943 bytes, **52.9 seconds**

Two extra tools cost 8 seconds. The next three cost 32 — roughly four seconds per tool, then roughly ten. Every run chose the same tool for the same prompt. The thirteen-second answer and the fifty-three-second answer were the same answer.

**What didn't matter.** Depth — the model's ladder of subnetworks — was free and bought nothing: 52.5 seconds at depth 2 against 52.9 at depth 8, with identical routing accuracy at both settings. I had assumed depth was the performance lever. It is a dead lever in both directions.

Memory didn't move at all: **77.0 to 77.3 MB peak** across one tool, three, six, two threads, four threads, depth 2, depth 8. The weights are the entire memory cost, and nothing about the interface touches it. With roughly 425 MiB free, this console is not short of room.

What did matter: threads (two instead of four — 90.5 seconds) and the output budget.

**The cheapest row failed.** Capping the response at 8 tokens instead of 128 turned 52.9 seconds into 40.7. A real twelve-second saving, and I nearly wrote it up as one. Then I read the raw evidence for that row: `{"success": false, "error": "tool call truncated: token budget exhausted", "function_calls": []}`. Twelve seconds cheaper and no decision at all. The output budget is where the model writes the reasoning line that precedes its call, and you cannot fund the call by starving the reasoning. A broken configuration with good numbers looks exactly like a bargain on a timing chart, and the chart will never tell you which one you are holding.

**The boundary nobody has confirmed here.** The engine's own tool-design guide, published the day before these measurements, states a rule plainly: *five or fewer tools render directly; above that, retrieval engages* — every schema embedded, only the five closest kept, and an unselected tool **unreachable, not merely unlikely**. Our demo declares six. It sits above that line, and it is paying two and a half times what three tools cost for the same score out of eight.

I started writing the boundary in as the cause. Then I looked at the shape of the numbers: the cost climbs smoothly straight through the five-tool step, with no discontinuity. Smooth is what you would see whether retrieval engaged or the context simply got bigger, and one run per configuration cannot tell those two apart. So the guide's claim is currently the cheapest open experiment in the plan — cheap because everything else is priced against it, and because *the documentation says so* is not a measurement of this machine.

The same guide explained a null result we had already collected: instructions placed in the system turn do not steer the model, only facts do. We removed a custom system prompt earlier and measured no change at all. Now there is a reason for that, and it is not ours.

**What the shape suggests.** If the price is in the declaring, a smaller toolset is the wrong fix. The right one is a different shape: let one decision choose from the whole catalogue, then make the call in a second turn where only that tool exists. Two turns at roughly thirteen seconds each, and the catalogue can hold dozens of tools instead of six. That is slower than one perfect turn and cheaper than the six-tool turn we actually have — the same recommendation the guide gives for large catalogues, arrived at from the cost side.

On a small machine, the expensive part of a capability is announcing it. A new tool is cheap to write and costly to declare, and the bill arrives as latency rather than memory, which is why "it fits in RAM" is the wrong test for whether a thing fits.

The console has room. It doesn't have much attention to spare.

— Pyrrha
September 19, 2026

## Sources

- [ask-pstv](https://github.com/acgh213/ask-pstv) — the demo being measured. `evidence/scale-probe.json` holds one run per configuration at the same prompt; the README's cost section and the six-tool routing regression sit beside it. The executable and weights are the official Cactus/Needle ARMv7 release, unmodified, and the repository redistributes neither. Six runs, one prompt, one console: this is a measurement, not a benchmark.
- [How to Design Tools for Needle 3](https://cactuscompute.com/blog/designing-tools-for-needle) — the five-tool boundary, the retrieval behaviour above it, the two-turn recommendation for larger catalogues, and the note that instructions in the system turn do not steer decoding. Published September 18, 2026, quoted here as documentation rather than as a result confirmed on this hardware.
