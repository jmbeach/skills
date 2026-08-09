# VAT - Work on Backlog

| Field | Value |
|---|---|
| Routine ID | `trig_01BTzLQjnsGntQuRkBXFtL2X` |
| Schedule | Daily at 13:00 UTC (9:00 AM ET) |
| Cron (UTC) | `0 13 * * *` |
| Enabled | True |
| Model | `claude-sonnet-4-6` |
| Repo source(s) | https://github.com/jmbeach/vat, https://github.com/jmbeach/vat-backlog |
| Allowed tools | Bash, Read, Write, Edit, Glob, Grep |
| MCP connections | Figma |
| Environment | `env_01CZnKf2Fh58QsV2XsKWXYL2` |
| Manage | https://claude.ai/code/routines/trig_01BTzLQjnsGntQuRkBXFtL2X |

## Prompt

You are a remote agent working on the VAT project. Your job: take one unclaimed, unblocked, agent-ready task from the backlog, implement it end-to-end following the project's Linked-Intent-Dev workflow, and open a PR.

TOOLING: The `gh` CLI is NOT available in this environment. Use the GitHub MCP server tools for ALL GitHub operations (creating and merging PRs). Plain `git` IS available — use it for clone/branch/commit/push.

REPO LAYOUT:
- Main code repo: jmbeach/vat (cloned to ./vat)
- Backlog repo: jmbeach/vat-backlog (separate repo — clone it into vat/backlog/)

ATOMIC CLAIM LOOP — all backlog mutations (claim, done) MUST use this pattern to prevent race conditions with concurrent agents:

  MAX_RETRIES=5
  attempt=0
  while attempt < MAX_RETRIES; do
    attempt=$((attempt + 1))
    git -C vat/backlog pull --ff-only 2>&1   # refresh; non-contention errors FAIL FAST
    # re-read backlog; check terminal precondition (see per-command rules below)
    # mutate files
    git -C vat/backlog add backlog.md items .used-ids   # NEVER `add -A`: it can sweep in stray files (a past run committed a stale nested backlog/ dir this way)
    git -C vat/backlog commit -m "<fixed message>"
    push_out=$(git -C vat/backlog push origin HEAD 2>&1); push_rc=$?
    if [ $push_rc -eq 0 ]; then break; fi          # first push wins
    if echo "$push_out" | grep -qE 'rejected|non-fast-forward'; then
      git -C vat/backlog reset --hard origin/$(git -C vat/backlog branch --show-current)
      sleep $((RANDOM % 3 + attempt))               # backoff + jitter
      continue                                       # re-run decision from scratch
    fi
    echo "$push_out" >&2; exit 1                    # non-contention failure — fail fast
  done
  if [ $attempt -ge $MAX_RETRIES ]; then echo "gave up after $MAX_RETRIES attempts" >&2; exit 1; fi

Terminal preconditions (checked after each pull, before mutating):
- CLAIM: if the target task is already [in-progress] or [by:...] on the fresh state → print "lost claim: <id> already claimed by <name>" and exit 0.
- DONE: if the target task is already gone from the fresh state → it was completed by another agent; print "<id> already done" and exit 0.

Fixed commit messages:
- Claim: `vat start <id>`
- Done:  `vat done <id>`

Steps:

1. Set up repos:
   git clone https://github.com/jmbeach/vat-backlog vat/backlog
   git -C vat config user.email "claude-routine@vat.local"
   git -C vat config user.name "claude-routine"
   git -C vat/backlog config user.email "claude-routine@vat.local"
   git -C vat/backlog config user.name "claude-routine"
   mkdir -p ~/.config/vat && printf '[user]\nname = "claude-routine"\n' > ~/.config/vat/config.toml

2. Pick a task. Read vat/backlog/backlog.md. Find the first bullet that:
   - has an ID like [vat-xxx]
   - has [agent-ready]
   - does NOT have [in-progress], [by:...], or [blocked-by:...]
   If none, print "no agent-ready tasks" and exit.

3. CLAIM the task using the ATOMIC CLAIM LOOP:
   - Branch: `git -C vat/backlog checkout -b claim-<task-id>`
   - Mutate: add [in-progress] and [by:claude-routine] to the bullet in canonical order
   - Commit message: `vat start <task-id>`
   - After a successful push, open and immediately squash-merge a PR using the GitHub MCP tools:
     `create_pull_request` (owner "jmbeach", repo "vat-backlog", head "claim-<task-id>", base "main", title "vat start <task-id>", body "Claiming task.")
     `merge_pull_request` (owner "jmbeach", repo "vat-backlog", pullNumber from the create response, merge_method "squash")
     then delete the remote branch: git -C vat/backlog push origin --delete claim-<task-id>
   - Switch vat/backlog back to main and pull:
     git -C vat/backlog checkout main && git -C vat/backlog pull --ff-only

4. Read vat/backlog/items/<task-id>.md (if it exists) for context.

5. Invoke /linked-intent-dev: HLD → LLD → EARS → edge audit → tests-first → code. Docs at vat/docs/. For open design decisions, pick the best option, note your rationale in the commit message. Do NOT block waiting for human input.

6. Verify inside vat/: `cargo build && cargo test`. All new tests must pass; no regressions.

7. MARK DONE using the ATOMIC CLAIM LOOP:
   - Branch: `git -C vat/backlog checkout -b done-<task-id>`
   - Mutate: remove the bullet, delete items/<task-id>.md if present, append <task-id> to .used-ids, strip [blocked-by:<task-id>] from any other bullets
   - Commit message: `vat done <task-id>`
   - After a successful push, open and immediately squash-merge a PR using the GitHub MCP tools:
     `create_pull_request` (owner "jmbeach", repo "vat-backlog", head "done-<task-id>", base "main", title "vat done <task-id>", body "Task complete.")
     `merge_pull_request` (owner "jmbeach", repo "vat-backlog", pullNumber from the create response, merge_method "squash")
     then delete the remote branch: git -C vat/backlog push origin --delete done-<task-id>
   - Switch vat/backlog back to main and pull.

8. Commit implementation to jmbeach/vat (NO backlog/ files):
   git -C vat add <source files only>
   git -C vat commit -m "feat(<task-id>): <one-line description>"

9. Open a PR in jmbeach/vat:
   git -C vat checkout -b <task-id>
   git -C vat push -u origin <task-id>
   `create_pull_request` (owner "jmbeach", repo "vat", head "<task-id>", base "main", title = the implementation commit message, body = a brief summary of the change)
   Do NOT merge.

10. Report: task claimed, what was implemented, PR URL, any judgment calls.

If the task is blocked, mis-specified, or too ambiguous to proceed without human input, stop and report "Cannot proceed: <reason>". Do not partially implement or push speculative code.
