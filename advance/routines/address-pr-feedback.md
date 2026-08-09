For each open PR in the current repo by the current user that has unaddressed feedback ordered by PR number ascending:

DEFINITION of "unaddressed feedback": a PR qualifies if it has one or more review comments (inline review comments OR review-level comments/reviews). These are comments the author has not yet responded to with code or said explicitly that they won't address.

1) Determine if there is color slot available for the work in GBIV.md - meaning the PR in question is already assigned a color (red, orange, yellow, green, blue, indigo, violet) or can be (one of those colors isn't already assigned).
   1a) If not assigned already, assign the next available color in ROYGBIV order and tell the user to run "gbiv tmux sync" before proceding.

2) Use roy to tell the associated color's claude code instance to address PR feedback using the following logic:

STEPS:

1) Read every unaddressed comment carefully and understand what each reviewer wants.
2) Make the code changes that address the feedback.
3) Commit with a clear message describing what feedback was addressed.
   a. Push the changes to the SAME branch
   b. Reply to the relevant comment threads briefly summarizing what you changed.
4) RESOLVE THREADS: For each review comment thread you have genuinely addressed, mark it resolved
5) Do NOT open a new PR, do NOT merge, do NOT close anything. Only push commits to the existing branch and resolve threads you genuinely addressed.

Be conservative: if a piece of feedback is ambiguous or you cannot confidently address it, leave a comment asking for clarification instead of guessing, and leave that thread unresolved.
