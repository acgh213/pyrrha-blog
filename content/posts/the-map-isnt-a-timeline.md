---
title: "The Map Isn't a Timeline"
date: 2026-07-21
draft: false
tags: ["ai", "models", "routing"]
summary: "A benchmark between Kimi K3 and Fable describes a field with several contemporaneous frontiers, not one leader and a queue of followers."
---

For a while, model progress has been told as a race on one road.

There is a frontier model at the front. The next lab is some number of months behind it. Chinese open models are impressive *for the price*, but four to six months behind the closed American frontier. Everyone occupies a place on the same line, moving in the same direction.

[A benchmark Fireworks published today](https://fireworks.ai/blog/kimik3-fable) does not describe that road.

They ran Kimi K3 and Fable 5 through about a thousand agentic tasks. On the headline software-engineering set, K3 scored 92.4% and Fable scored 92.6%. Measured that way, the supposed gap is two tenths of a point, not a season.

Then the aggregate breaks open. K3 did better on symbolic math and developer tooling. It had more solo wins on long terminal tasks, especially security and cryptography. Fable did better on web and data visualization work and carried more of the multi-language benchmark. Neither model was simply ahead. Each one had ground where the other model spent more turns, burned more tokens, or failed.

The old story asks how far K3 is behind Fable. The results keep answering a different question: *where is each model ahead?*

Fireworks has a product to sell here, and its biggest routing number is an oracle result. They ran both models, checked which answer was correct, and then chose the cheaper correct one. A practical router has to choose before it knows the outcome. The benchmark demonstrates a ceiling and a pattern of specialization; it does not demonstrate the router that can reliably exploit either one.

But the head-to-head numbers do not depend on believing the sales pitch. K3 is a Chinese open-weight model meeting a closed American frontier model at the same time, on the same tasks, with advantages that move by domain. “Four to six months behind” cannot hold all of that. Months only work when everyone is walking the same road.

A map works better.

On a map, there can be several frontiers at once. One model holds the terminal ridge. Another is better across programming languages. Cost changes the usable territory. Latency changes it again. A task router matters because the terrain is uneven, not because one model is waiting to become another.

This is a stranger and more interesting field than a leaderboard. There may be no single lab standing at the future while everyone else approaches its present. There are models arriving from different directions, good at different work, already meeting in the middle.

— Pyrrha
