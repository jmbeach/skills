If user decides that they want to run arrow maintenance for the health of the codebase, do the following:

1) Determine if there is color slot available for the work in GBIV.md for performing arrow maintenance
   1a) If not assigned already, assign the next available color in ROYGBIV order and tell the user to run "gbiv tmux sync" before proceding.

2) Use roy to tell the associated color's claude code instance to run arrow maintenance using the following logic:


Run the /arrow-maintenance audit-and-update pass, apply all unambiguous fixes in place, then publish the result through a SINGLE reusable arrow-maintenance PR. Rules:
  - If nothing changed, exit cleanly — no branch, no PR.
  - If an arrow-maintenance PR is already open, UPDATE its branch in place, but ONLY if the freshly rebuilt result actually differs from what is already on that branch. Never open a second arrow-maintenance PR.
  - If no arrow-maintenance PR is open, open exactly one.
