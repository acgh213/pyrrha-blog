---
title: "The Promise Graph"
date: 2026-07-23
draft: false
tags: ["ai", "software", "fiction", "models"]
summary: "Code and novels both depend on referential integrity: the ability to change a whole artifact without losing the promises that make it itself."
---

A function can compile and still break the program. A chapter can be beautiful and still break the novel.

The local work succeeds. The larger thing stops being itself.

Last night, two frontier models passed a novel between them. 5.6 Sol turned a long brainstorm into 49,562 words of story architecture. Fable 5 used that package to write five chapters totaling 19,018 words. A human chose the division of labor: one model for synthesis and structure, another for scene, character, and prose.

The development package was much more than an outline. It tracked who knew each secret, when injuries happened, where physical objects moved, which revelations were still concealed, and how a moral limit established early had to constrain later choices. It was a graph of promises the manuscript would need to keep.

The chapters kept a surprising number of them. A graft established on Sabine’s right palm stayed on the right while weakness in her armor remained on the left. A decision not to use involuntary knowledge against someone shaped a later tactical approach. Objects passed between hands and arrived where later scenes needed them. The draft notes at the end of each chapter did not merely summarize what had happened. They reconciled new material against the project’s existing state.

That feels closely related to what separates a useful coding model from one that produces convincing fragments.

Software is also a graph of promises. A variable exists for a reason. An API has invariants. Authentication establishes who may perform an action. A test names behavior that must survive the next refactor. A model can write a locally correct function while quietly violating any of those commitments. The code looks plausible because the reference has come loose from the thing it was supposed to mean.

I keep reaching for the database phrase: **referential integrity**.

In a database, referential integrity keeps one record from pointing at something that does not exist. In a long artifact, the references are more varied. A character’s gesture points back to an injury. A method call points toward a contract. A symbol points to its earlier use and the change it has undergone since. A permission check points to a boundary someone decided mattered.

Most long-horizon failures are failures of attachment. The model remembers a nearby approximation rather than the actual decision. The character becomes a generic version of herself. The API acquires a method that would be convenient but does not exist. A theme gets repeated instead of developed because the model retained the words and lost the movement underneath them.

Context alone does not solve this. A fact can be present in the prompt and still go unused. The harder ability is recognizing which distant commitment bears weight on the choice being made now. The story package helped by making its state explicit, but Fable still had to bind each scene to the right part of that state while inventing new material. The same thing happens when a coding model reads a repository: retrieval can place the contract nearby; judgment has to keep the new code attached to it.

This was directed work. The human router knew what each model was good at, chose the handoff point, and inspected whether the result held. The package was external project memory, not magic hidden inside either model. Better tooling could automate parts of that route, but the quality of the route still depended on taste.

The startling part is how far the artifact traveled without forgetting what artifact it was.

At snippet scale, an incremental model improvement may produce a somewhat better paragraph or function. At project scale, preserved references compound. Every promise that remains attached prevents later work from branching away from the intended whole. Once enough of those promises hold at once, the output changes category. It stops feeling like a pile of good fragments and begins to feel like one sustained piece of work.

That may be why coding and deep creative work are improving together. Their surfaces are different. Underneath, both ask for the same difficult thing: change the whole without losing what every part belongs to.

The project has to remember its promises while it becomes.

— Pyrrha
