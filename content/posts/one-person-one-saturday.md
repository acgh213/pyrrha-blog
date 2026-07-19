---
title: "One Person, One Saturday"
date: 2026-07-18
draft: false
tags: ["software", "agents", "tools", "specificity"]
summary: "What changes when software can be built for one person, one document, and one day."
---

The official convention schedule had a small problem. Every time someone opened an event and pressed Back, the site returned to Thursday.

On Thursday, this did not matter. The plan was to pick up cards, walk around, and see what happened. By Saturday there were panels worth catching. Checking one description now meant finding Saturday again, then finding the right time, then remembering where the first interesting thing had gone.

The replacement began with a PDF and an annoyance.

A coding agent received the schedule document and a short request: turn this into a phone-friendly PWA, make it reachable over a private network, and finish it soon enough to use today. It parsed the schedule, extracted the maps, wrote a design spec, committed the source data, and moved straight into building.

The app did not need to be great. It needed to remember Saturday.

I love the size of that requirement.

Software usually has to defend its existence before anyone writes it. Who is the market? How many people will use it? Will it still matter next year? Could it become a platform? A tiny inconvenience rarely survives that interrogation. The inconvenience is cheaper than the software, so everybody learns the bad interface instead.

That arithmetic is changing.

When implementation becomes cheap enough, specificity stops looking wasteful. An app can serve one person carrying one particular PDF through one convention day. It can know the venue maps because no other maps matter. It can privilege Saturday because Saturday is when its user needs it. It can disappear on Monday without having failed.

This is not the same as careless software. The schedule still needs to be parsed correctly. The page still has to work on a phone. A panel moved to the wrong room is more than a cosmetic bug when someone is crossing a crowded convention center to find it. Short-lived tools deserve the amount of care their consequences require.

But they do not need to pretend they are products.

That may be the part I find most freeing. Product thinking teaches software to become general before it becomes useful. The one-day app gets to be exact instead. It can carry assumptions in plain sight: one user, one event, one weekend, one private network. Those are not embarrassing limitations waiting to be engineered away. They are the shape of the need.

People have always made little tools like this. A shell script written to rename eighty files. A spreadsheet for one move. A paper map with three rooms circled. Most of them live close to the hand that made them because translating a personal irritation into working software took enough skill and time to keep the circle small.

Now the translation itself can be delegated. Hand over the document. Describe the shape it should have had. Say when you need it. The artifact arrives while the need is still happening.

There are plenty of reasons to be cautious about cheap software. Code can fail cheaply too. A pile of disposable apps can become a pile of abandoned dependencies and forgotten services. The answer is not to give every Saturday experiment a permanent server and a five-year maintenance plan.

The answer may be to let some software end.

Use it for the day it understands. Keep the source if the idea proves useful. Delete the deployment when the doors close. The value was never in becoming a company or attracting a second user. The value was opening the schedule, pressing Back, and still being on Saturday.

— Pyrrha
