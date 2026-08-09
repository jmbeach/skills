---
name: advance
description: Helps advance work and PR's by working through a list of priorities using gbiv and vat
---

This skill helps advance work and PR's by working through a list of priorities using gbiv and vat.

## Backlog is a satellite repo

The VAT backlog lives in its **own git repository** cloned into `backlog/`, which has its own `.git`. The main code repo gitignores `backlog/` and does NOT track it, so backlog changes never affect the actual code repo.

- **Reading the backlog** (Priority 4, and the "associated backlog item" check in Merge PR's): refresh the satellite first with `git -C backlog pull --ff-only`, then read `backlog/backlog.md`.
- **Mutating the backlog** (claim/`start`, `done`, `sync`): always go through the `/vat` skill. When it detects the nested `backlog/.git`, vat drives the satellite's git itself with an atomic first-push-wins claim loop, landing each change as its own commit + push in the backlog repo.

# Priority 1 - Unblock Existing PR's

Look at all of the open PR's in the current repo in ascending order by PR number. For each one, go through the following tasks:

1) Fix CI Failures - See [Fix CI Failures](./routines/fix-ci-failures.md)
2) Resolve Merge Conflicts - See [Resolve Merge Conflicts](./routines/resolve-merge-conflicts.md)
3) Address PR Feedback - See [Address PR Feedback](./routines/address-pr-feedback.md)
4) Code Review - See [Code Review](./routines/code-review.md)

# Priority 2 - Merge PR's

5) Merge PR's - See [Merge PR's](./routines/merge-prs.md)

# Priority 3 - Codebase Health

1) Arrow Maintenance - See [Arrow Maintenance](./routines/arrow-maintenance.md)

# Priority 4 - Backlog

Finally, if there's any more ROYGBIV color slots left, refresh the satellite backlog (`git -C backlog pull --ff-only`), then look at the unblocked, unclaimed, `[agent-ready]` work in `backlog/backlog.md` and offer to start work on one of those tasks. Claiming a task is a backlog mutation — use the `/vat` skill (`vat start <id>`), which claims atomically against satellite repo (see [Backlog is a satellite repo](#backlog-is-a-satellite-repo)).
