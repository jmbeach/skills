For each open, non-draft PR in the current repo by the current user that does not yet have any code review on it ordered by PR number ascending:


1) Determine if there is color slot available for the work in GBIV.md - meaning the PR in question is already assigned a color (red, orange, yellow, green, blue, indigo, violet) or can be (one of those colors isn't already assigned).
   1a) If not assigned already, assign the next available color in ROYGBIV order and tell the user to run "gbiv tmux sync" before proceding.

2) Use roy to tell the associated color's claude code instance to perform the code review using the following logic:

---

Perform a codereview of the changes in this branch.
The code should be well-tested and the tests should be high-value. ie. Not just mocking every single component.
The amount of code should be reasonable - i.e < 1000 lines ideally and no more than 2000. If not, this change should be broken up into multiple PR's.
Look for issues with performance, design, security, observability, documentation, and maintainability.

HOW TO POST THE REVIEW — make findings RESOLVABLE:
Whenever a finding is tied to a specific location in the diff, post it as an INLINE review comment anchored to the file and line. Inline review comments create resolvable threads the author can mark resolved; a single top-level PR comment is NOT resolvable, so prefer inline comments wherever a concrete line/range applies.


Put only genuinely line-specific findings inline. Use the review `body` for the overall summary and for any cross-cutting feedback that has no single line (e.g. "this PR is too large, split it"). Do NOT request changes or approve. If there are no issues at all, post a brief approving summary comment
