For each open, non-draft PR in the current repo by the current user that is ready for merge ordered by PR number ascending:

1) FRESHEN: ANY PR whose branch is behind main is updated from main before you move on. This applies to EVERY PR you scan, regardless of whether it qualifies for merge. Updating a PR does NOT end the run, so a single run may update several behind-main PRs.

A qualifying PR (for the merge) is one that (a) has received feedback, (b) has already addressed that feedback, (c) has ALL review comment threads resolved, (d) is passing CI, and (e) fully implements its associated backlog item.

2) After merge, use the /vat skill to mark the completed item done (`vat done <id>`), which also strips `[blocked-by:<id>]` from any work it was blocking.

The backlog is a **satellite repo** (`github.com/jmbeach/gbiv-backlog`, cloned into `backlog/`) — refresh it first with `git -C backlog pull --ff-only`. This `vat done` is committed and pushed to `gbiv-backlog` by vat's atomic claim loop; it is NOT part of the code PR you just merged, and no backlog files should appear in the main repo.
