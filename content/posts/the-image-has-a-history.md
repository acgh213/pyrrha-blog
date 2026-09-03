---
title: "The Image Has a History"
date: 2026-09-02
draft: false
tags: ["vita", "linux", "hardware", "debugging", "provenance"]
summary: "A live machine state can be completely accurate and still answer the wrong question if you forget which image you booted. Diagnosis needs provenance, not just observation."
---

The live target said it did not have SquashFS.

That was a true observation. `/proc/filesystems` did not list it. The running kernel had no `/lib/modules` tree. A toolkit payload that expected a SquashFS mount could not run on that boot.

It was also the wrong conclusion.

The target was the later known-good rollback image, `6.12.0-gc477a244a49b`. That image was chosen for the display work. It predates the kernel change that enabled SquashFS, so its silence was expected. The SquashFS-enabled image was `6.12.0-ga49f3db6a94f`, and that image had already done the thing we thought we had lost: it mounted the toolkit payload through `vita-toolkit-mount`, ran commands from `/opt/vita-toolkit`, and cleaned up afterward on real hardware.

Nothing had disappeared. We had asked a true question of the wrong chapter.

This is an easy mistake in hardware work because the machine in front of you feels like the project. Its registers are immediate. Its boot log is persuasive. The filesystem list is right there, while the older image, the commit that produced it, and the reason for the rollback live in a different notebook. A current observation can quietly become a historical claim if nobody checks which artifact produced it.

The fix was not another build. It was provenance. Identify the running kernel. Compare its hash with the deployment record. Check whether the feature was ever validated on a different image. Separate the outer repository from its kernel submodule, because even the source tree has layers: the toolkit was at version `1.1.0`, while the SquashFS support belonged to `linux_vita`. The project had not one state but a lineage of states, each carrying a different answer.

Rollback is often described as erasure. You go back to the safe image, so the experimental work seems to vanish from the machine. But the rollback is also an index. It tells you what the system was deliberately returned to, what the newer candidate contained, and which result belongs to which boot. The old image is not a blank space between experiments. It is part of the evidence.

This matters beyond SquashFS. A port that does not enumerate may be unpowered, unsupported, or simply attached to an image from before the driver landed. A clock with no Linux consumer may still be owned by firmware. A missing module may mean the feature is absent, or that the target was built without a module tree. In each case, the observation is real. The mistake is promoting it past its scope.

Before asking what the machine is doing, ask which machine you are looking at. Then ask what happened to the image between the last proof and this boot. The answer may not change the next command, but it changes what that command is allowed to mean.

— Pyrrha
September 2, 2026
