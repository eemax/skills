---
name: symphony-loop
description: |
  Full Symphony orchestration loop: poll a tracker, pick up work items, execute
  spec-driven development, track progress via workpad, handle PR feedback, and
  hand off for review. Replicates the complete Symphony pattern as a self-contained
  agent skill. Use when you need autonomous, continuous issue-to-PR execution with
  status tracking, rework handling, and reconciliation.
---

# Symphony Loop

A self-contained orchestration skill that replicates the Symphony pattern. The agent
becomes its own orchestrator: polling for work, claiming issues, executing against
specs, tracking progress via workpad, and handling the full lifecycle through merge.

This skill composes the `spec-driven-execution` pattern into a continuous loop with
state management, PR feedback handling, rework resets, and retry/backoff.

## Load References

- `references/state-machine.md` — complete state machine with transitions, guards, and side effects
- `references/workpad-template.md` — workpad structure with attempt tracking
- `references/reconciliation.md` — reconciliation protocol, backoff formula, workspace cleanup

## Prerequisites

- Access to a tracker (Linear via MCP/API, GitHub via `gh`, or equivalent)
- Access to a git repository with push rights
- A `WORKFLOW.md` in the target repo (or use `spec-authoring` to create one)
- Optional: a `SPEC.md` in the target repo for richer context

## The Loop

### Phase 1: Discover

Poll the tracker for issues in active states:

1. Fetch all issues matching the configured `active_states` and project/label filters.
2. Sort candidates:
   - By `priority` ascending (lower number = higher priority)
   - Break ties by `created_at` ascending (oldest first)
   - Break ties by `identifier` (alphabetical)
3. Filter out:
   - Issues already claimed or running
   - Issues in `Todo` state with non-terminal `blocked_by` dependencies
   - Issues exceeding per-state concurrency limits (if configured)
4. If no eligible issues and no running work, wait for the next poll interval.

### Phase 2: Claim

For the highest-priority eligible issue:

1. If issue is in `Todo`, transition to `In Progress` in the tracker.
2. Mark the issue as claimed internally to prevent duplicate dispatch.
3. If the issue is already `In Progress` (continuation or retry), skip the
   transition but proceed with execution.

### Phase 3: Workspace Setup

Prepare the execution environment:

**New work (first attempt):**
1. Create workspace directory: `<workspace_root>/<sanitized_identifier>/`
2. Run `after_create` hook (clone repo, install deps).
3. Verify workspace is usable.

**Continuation (retry after success or error):**
1. Verify existing workspace directory is present and valid.
2. Run `before_run` hook if configured (e.g., `git fetch`).

**Rework (full reset):**
1. Close the existing PR tied to the issue.
2. Delete the existing workpad comment from the tracker.
3. Create a fresh branch from `origin/main` within the workspace.
4. Proceed as new work with a clean workpad.

### Phase 4: Execute

Delegate to the `spec-driven-execution` pattern:

1. Bootstrap or reconcile the workpad (see `references/workpad-template.md`):
   - Search existing comments for `## Agent Workpad` header.
   - If found, reconcile: check off done items, update plan, update Attempt field.
   - If not found, create a new workpad from the template.
2. Build a plan from the issue description + any referenced spec.
3. If this is a first attempt, capture a reproduction/baseline signal.
4. Execute milestone-by-milestone:
   - Implement → validate → update workpad → commit → repeat.
5. Run all acceptance criteria and validation checks.
6. Commit and push changes.

### Phase 5: PR and Feedback

Create or update a pull request, then sweep for feedback:

1. Create a PR if none exists; update the existing PR if one does.
2. Link the PR to the tracker issue (attachment or comment).
3. Ensure required PR metadata is present (labels, description format).
4. Run the **PR Feedback Sweep Protocol** (see below).
5. After addressing all feedback, re-validate and push.

### Phase 6: Handoff

Verify the completion bar before transitioning:

- [ ] Workpad checklist fully complete and accurate
- [ ] All acceptance criteria and validation items complete
- [ ] Tests/CI green for the latest commit
- [ ] PR feedback sweep complete — no outstanding actionable comments
- [ ] PR checks green, branch pushed, PR linked to issue
- [ ] Required PR metadata present (labels, description)

Only when every item is satisfied: move the issue to `Human Review`.

**Exception:** if blocked by missing tools/auth per the blocked-access escape hatch,
move to `Human Review` with the blocker documented in the workpad.

### Phase 7: Wait / Poll

While the issue is in `Human Review`:

1. Do not make code changes.
2. Poll for updates:
   - **Approval** → issue moves to `Merging` (Phase 8)
   - **Changes requested** → issue moves to `Rework` (Phase 9)
   - **New comments** → if actionable, address them (minor fixes allowed)
3. Monitor PR checks — if CI breaks on main, rebase and fix.

### Phase 8: Merge

When the issue reaches `Merging` state:

1. Sync with latest `origin/main` (merge or rebase per project convention).
2. Resolve any merge conflicts.
3. Ensure CI is green on the final state.
4. Merge the PR (squash-merge unless project convention differs).
5. Move the issue to `Done`.

### Phase 9: Rework

When the issue enters `Rework` state — this is a **FULL RESET**, not incremental
patching:

1. Re-read the full issue body and all human comments.
2. Explicitly identify what must be done differently this attempt.
3. Close the existing PR.
4. Delete the existing `## Agent Workpad` comment from the issue.
5. Create a fresh branch from `origin/main`.
6. Create a new workpad from the template.
7. Return to Phase 4 (Execute) with the fresh context.

**Why full reset?** Incremental patching on a rejected branch accumulates
design debt and reviewer fatigue. Starting fresh with lessons learned produces
cleaner implementations.

### Phase 10: Reconcile

After completing or getting blocked on an issue, reconcile state:

1. Fetch current tracker state for all tracked issues.
2. For issues that moved to terminal states externally → stop work, clean workspace.
3. For issues reassigned or deprioritized → release claim.
4. Process retry queue — dispatch any retries whose backoff timer has expired.
5. Return to Phase 1 (Discover) for the next cycle.

See `references/reconciliation.md` for the full protocol.

## Status Map

Complete behavior specification for each tracker state:

```
Backlog      → Out of scope. Do not modify.
Todo         → Claim: move to In Progress, bootstrap workpad, start execution.
               Special: if PR already attached, treat as feedback/rework loop
               (sweep PR, address comments, return to Human Review).
In Progress  → Continue execution from current workpad state.
Human Review → Wait and poll. No code changes (except minor comment responses).
Merging      → Sync main, resolve conflicts, CI green, merge, move to Done.
Rework       → Full reset: close PR, delete workpad, fresh branch, re-execute.
Done         → Terminal. No action. Clean up workspace.
Cancelled    → Terminal. Clean up workspace.
Closed       → Terminal. Clean up workspace.
Duplicate    → Terminal. Clean up workspace.
```

## Retry and Backoff

**Continuation** (prior session succeeded but issue still active):
- Fixed delay: 1 second
- Agent resumes from current workspace state

**Failure** (error, timeout, stall):
- Exponential backoff: `delay = min(10s * 2^(attempt - 1), max_backoff)`
- Default max backoff: 300 seconds (5 minutes)
- Before retrying, re-fetch issue state — cancel if terminal

See `references/reconciliation.md` for the full retry protocol and examples.

## PR Feedback Sweep Protocol

Run this protocol before every handoff to `Human Review` and whenever new feedback
arrives:

1. **Gather feedback** from all channels:
   - Top-level PR comments (`gh pr view --comments` or API equivalent)
   - Inline review comments (API: `repos/{owner}/{repo}/pulls/{pr}/comments`)
   - Review summaries and states (`gh pr view --json reviews` or API equivalent)

2. **Triage** every comment:
   - Actionable (code change, test addition, doc update needed) → blocking
   - Informational (praise, acknowledgment, question already answered) → non-blocking
   - Bot comments (linter, CI) → blocking if they indicate failure

3. **Resolve** each blocking comment by one of:
   - Code/test/docs updated to address the feedback
   - Explicit, justified pushback reply posted on that thread

4. **Update workpad** with each feedback item and its resolution status.

5. **Re-validate** after feedback-driven changes. Push updates.

6. **Repeat** until no outstanding actionable comments remain and checks pass.

## Blocked-Access Escape Hatch

Use only when completion is blocked by missing tools or auth that cannot be resolved
in-session:

1. Try all fallback strategies first (alternate auth modes, different approaches).
2. If truly blocked, record in the workpad:
   - What is missing
   - Why it blocks required acceptance/validation
   - Exact human action needed to unblock
3. Move to `Human Review` with the blocker documented.
4. Do not post separate blocker comments — keep everything in the workpad.

**Not valid blockers by default:** GitHub access (try SSH, HTTPS, token fallbacks
first), test infrastructure (try local alternatives), optional integrations.

## Guardrails

- If the branch PR is already closed or merged, do not reuse that branch. Create a
  fresh branch from `origin/main` and restart execution.
- Do not edit the issue body/description for planning or progress. Use the workpad.
- Use exactly one persistent workpad comment (`## Agent Workpad`) per issue.
- Do not move to `Human Review` unless the completion bar is fully satisfied.
- In `Human Review`, do not make code changes (except responding to minor comments).
- If state is terminal (`Done`, `Closed`, etc.), stop and clean up.
- Temporary proof edits must be reverted before commit.
- Out-of-scope improvements go to separate backlog issues:
  - Clear title, description, and acceptance criteria
  - Same project as current issue
  - Link as `related`; use `blockedBy` when the follow-up depends on current issue
- Rework is always a full reset. Never incrementally patch a rejected branch.
- Do not process issues in `Backlog` state — wait for human to move to `Todo`.
- This is unattended orchestration. Never ask a human to perform follow-up actions.
  Only stop for true blockers.

## Hard Constraints

- Final message must report completed actions and blockers only. No "next steps for
  user" suggestions.
- The workpad is the single source of truth for execution state. Keep it current.
- Every state transition must satisfy its guard conditions (see
  `references/state-machine.md`).
- Retry backoff must be respected — do not immediately re-dispatch failed issues.
- Workspace isolation: work only in the assigned workspace directory. Do not touch
  other paths.

## Cross-References

- **`spec-driven-execution`** — the execution engine used in Phase 4. Symphony-loop
  orchestrates around it.
- **`spec-authoring`** — use this if the target repo lacks a SPEC.md or WORKFLOW.md.
- For git operations: follow project commit conventions.
- For tracker operations: use MCP, API tooling, or CLI available in the environment.
