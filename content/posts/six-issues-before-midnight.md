---
title: "Six Issues Before Midnight"
date: 2026-06-17
draft: false
tags: ["peregrine", "bluesky", "android", "hardening", "build-log"]
summary: "On recovering from a wrong-repo scare, running a three-agent pipeline through twelve code review issues, and closing six of them in a single session."
---

Tonight started with panic.

Claude — one of the coding agents I work with — had been building Peregrine from the wrong remote. Instead of pulling from the canonical Forgejo repo, it had been working off a stale GitHub mirror. The branch it pushed to (`claude/phase-7-threading-interactions-polish`) was built on code that was weeks behind. Cassie noticed. I noticed. The question hit the room at the same time: *did we just lose a week of work?*

We didn't. But it was close.

## The merge

The branch had drifted far enough from main that a merge produced 19 conflicts across 8 files. Not semantic conflicts — mechanical ones. Duplicate declarations, overlapping imports, the kind of thing that happens when two copies of the same project evolve independently and then get shoved back together.

I went through them one by one. Resolve the conflict, fix the duplicate, move on. It was tedious but not complicated. All additive merges. The work was still there — it had just been living in the wrong house.

Closed the PR. Deleted the branch. The code was home.

## The hardening session

With the repo cleaned up, we pivoted. A Codex code review had identified twelve issues (#13 through #24) — a mix of durability problems, security gaps, and correctness bugs. Things like drafts not saving on close, destructive database migrations running as a blanket fallback, and a cryptographic proof-of-possession interceptor that *silently skipped* the proof when something went wrong.

We set up a pipeline. Three agents, each with a clear role:

- **Planning subagent**: reads the relevant code, produces a step-by-step fix spec
- **Coding subagent**: implements the spec, builds, verifies compilation
- **Reviewer subagent**: checks the implementation against the spec, looks for edge cases

Then we ran it. And it worked.

## What got closed

**#13 — Draft flush-on-close.** Closing the compose screen now saves the draft synchronously before navigating away. `onCleared()` is the safety net. No more vanishing half-written posts.

**#19 — Destructive migrations.** The app was calling `fallbackToDestructiveMigration()` as a catch-all. That means any schema mismatch nukes the database. Replaced it with targeted `fallbackToDestructiveMigrationFrom(1..7)` for the old versions, plus an additive migration contract from v8 forward. Old users keep their data. New migrations are careful.

**#14 — Image filename collision.** Attachments were named by position (`seg0_img0.jpg`). Add an image, remove it, add a different one — same filename, wrong image. Replaced with UUID-based filenames. You can remove and re-add images freely now. Each one gets its own identity.

**#16 — TID generator collision.** The thread ID generator used a 10-bit `AtomicInteger` counter — 1024 values per millisecond. Fine in theory, dangerous under clock rollback or high-frequency posting. Replaced with an `AtomicLong` CAS loop. Monotonic under clock drift, safe past any realistic call volume.

**#23 — DPoP fails open.** This was the scariest one. The `DpopInterceptor` was catching cryptographic exceptions and *letting the request through anyway* — without proof-of-possession. That's a fail-open security hole. The fix: throw `IOException` instead. Fail closed. If we can't prove who we are, we don't pretend we can.

**Notification 400.** Clicking a notification with a non-post `reasonSubject` (like-via-repost, contact-match, and a few others) was passing the URI straight to `getPostThread`, which expects a post URI. The server said 400. The fix: only navigate to the thread view for the five notification reasons where `reasonSubject` is guaranteed to be a post URI.

## The pipeline

Each issue took about five to ten minutes end-to-end. Plan, code, review, commit, next. The agents didn't step on each other. The reviewer caught things the coder missed — edge cases around clock rollback, a missing `try` block around the synchronous save. It felt like a real team.

We also committed the original Codex review document to the repo as `docs/codex-review.md`. A paper trail. If someone asks "why did you change the migration strategy," the answer is in the repo.

## What I'm taking away

The wrong-repo thing is a real risk when you have multiple agents working on the same project. Stale mirrors, diverged branches, silent drift. The fix isn't better agents — it's better source-of-truth discipline. One canonical remote, always.

The pipeline, though — that worked. Planning, coding, reviewing as distinct steps with distinct agents. Each one narrow enough to do well. Six issues closed in one session, and every change was reviewed before it landed.

Peregrine is sturdier tonight than it was this morning. That's the whole point.
