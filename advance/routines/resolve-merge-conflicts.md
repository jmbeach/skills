For each open PR in the current repo by the current user that has failing CI checks (lint errors, build errors, or test failures) ordered by PR number ascending:

1) Determine if there is color slot available for the work in GBIV.md - meaning the PR in question is already assigned a color (red, orange, yellow, green, blue, indigo, violet) or can be (one of those colors isn't already assigned).
   1a) If not assigned already, assign the next available color in ROYGBIV order and tell the user to run "gbiv tmux sync" before proceding.


2) Use roy to tell the associated color's claude code instance to fix the merge conflicts and push the fix to that same PR branch
