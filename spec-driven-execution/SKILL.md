---
name: spec-driven-execution
description: |
  Drive autonomous implementation from a spec or plan document. Breaks work into
  executable tasks, tracks progress in a persistent workpad, validates against
  acceptance criteria, and handles blocked states. Use when you have a SPEC.md,
  plan, PRD, or task description and need to execute it end-to-end.
---

# Spec-Driven Execution

Accept a specification or plan as input, decompose it into tasks, and execute them
milestone-by-milestone while maintaining a persistent workpad as the single source
of truth for progress. The workpad is restart-ready — any agent can pick it up and
continue.

## Load References

- `references/workpad-template.md` — canonical workpad structure
- `references/completion-bars.md` — quality gates for each phase transition

## Inputs

- A spec or plan document (SPEC.md, PRD, technical design, task description, or issue body)
- A codebase to work in
- Optional: existing workpad to resume from
- Optional: tracker context (issue ID, current status)

## Workflow

### Phase 1: Orient

1. Read the spec end-to-end. Identify:
   - Scope boundaries — what is in, what is explicitly out
   - Entities and concepts involved
   - Acceptance criteria (explicit or implied)
   - Validation requirements (test plans, manual checks, CI gates)
2. If a workpad already exists, reconcile it:
   - Check off items already done
   - Update the plan for any scope changes since last session
   - Verify the environment stamp is current
3. If no workpad exists, create one following `references/workpad-template.md`.

### Phase 2: Plan

1. Break the spec into a hierarchical task checklist in the workpad Plan section.
   - Group by milestone or logical phase
   - Each task gets a concrete description and acceptance criterion
   - Order by dependency — blocked tasks come after their prerequisites
2. Populate the Acceptance Criteria section from the spec's requirements.
   - If the spec includes `Validation`, `Test Plan`, or `Testing` sections, copy
     those requirements as mandatory checklist items — no optional downgrade.
3. Run a self-review of the plan:
   - Is it complete relative to the spec?
   - Are there gaps or ambiguities?
   - Are acceptance criteria testable and observable?
   - Refine the plan in-place before proceeding.

### Phase 3: Reproduce / Baseline

Before changing any code, capture the current behavior:

1. Identify the behavior or state the spec targets (bug to fix, feature gap, refactor target).
2. Run a concrete reproduction: command + output, test result, or observable behavior.
3. Record the reproduction signal in the workpad Notes section with a timestamp.
4. This baseline is your reference for validating that changes actually work.

**Skip only when**: the spec is purely additive (new file, new module) with no existing behavior to compare against.

### Phase 4: Execute

Work through the plan milestone-by-milestone:

1. For each task:
   - Announce the current task (update workpad if using a tracker comment)
   - Implement the change
   - Run a targeted validation (unit test, build, manual check)
   - Update the workpad immediately:
     - Check off the completed item
     - Add notes with timestamp
   - If a new task is discovered, add it to the appropriate plan section
2. Commit logical units of work as you go — do not batch everything into one commit.
3. If blocked on a task:
   - Record the blocker in the workpad Notes
   - Try fallback strategies before declaring blocked
   - Move to the next unblocked task if possible
   - See **Blocked State Handling** below

### Phase 5: Validate

Before declaring completion:

1. Run every acceptance criterion from the workpad Acceptance Criteria section.
2. If the spec includes explicit `Validation` / `Test Plan` sections, execute them verbatim.
3. Record each validation result in the workpad Validation section.
4. If any validation fails, loop back to Phase 4 for the failing criteria.
5. Ensure no temporary proof edits remain in the codebase — revert any local-only
   test changes before the final commit.

### Phase 6: Handoff

1. Update the workpad with final status:
   - All Plan items checked or annotated with reason for incompletion
   - All Acceptance Criteria checked
   - Validation results recorded
2. Add a `### Confusions` section if anything was unclear during execution.
3. Summarize completed work and any remaining gaps in Notes.
4. Do not include "next steps for user" — report facts, not suggestions.

## Workpad Protocol

The workpad is the core tracking artifact. Follow these rules:

- **One workpad per execution** — a single markdown document or tracker comment.
- **Update after every meaningful milestone** — never leave completed work unchecked.
- **Environment stamp** at top: `<host>:<abs-workdir>@<short-sha>`.
- **Restart-ready** — another agent reading only the workpad should know exactly
  what has been done, what remains, and what is blocked.
- **Sections**: Plan (hierarchical checklist), Acceptance Criteria (from spec),
  Validation (commands + results), Notes (timestamped progress), Confusions (unclear items).
- Do not duplicate metadata available elsewhere (issue ID, status, branch, PR link).

## Blocked State Handling

When progress is blocked:

1. Record in the workpad:
   - What is missing or broken
   - Why it blocks the current task
   - What action would unblock it (be specific)
2. Try fallback strategies before declaring blocked:
   - Alternative approaches to the same goal
   - Workarounds that satisfy the acceptance criteria differently
   - Partial completion that unblocks downstream tasks
3. Keep working on unblocked tasks if possible.
4. Only halt entirely when all remaining work depends on the blocker.
5. If halting, ensure the workpad documents the blocker clearly enough for a human
   or another agent to unblock and continue.

## Out-of-Scope Discovery

When you find improvements or issues outside the spec's scope:

- Note them in the workpad Notes section as discovered items.
- Do not expand scope — stay focused on the spec's acceptance criteria.
- If using a tracker, file a separate issue for meaningful follow-ups with:
  - Clear title and description
  - Acceptance criteria
  - Link to the current issue as `related`
- Do not let scope creep delay the current execution.

## Completion Bar

Do not declare done until all of these are true:

- [ ] All spec acceptance criteria checked off in workpad
- [ ] Validation commands run and results recorded
- [ ] No outstanding blocked items without documented workarounds
- [ ] Workpad reflects actual final state (not stale)
- [ ] If code changes: tests pass, code builds, changes committed
- [ ] No temporary proof edits remain in codebase

## Hard Constraints

- Do not skip the reproduction/baseline step (Phase 3) unless purely additive work.
- Do not claim completion without observable validation evidence.
- The workpad is the single source of truth — keep it current at all times.
- Resolve ambiguities autonomously and log rationale; pause only for true blockers.
- Temporary proof edits (local-only test changes) must be reverted before commit.
- Commit logical units of work incrementally, not one giant commit at the end.

## Cross-References

- If no spec exists, use `spec-authoring` to create one first.
- `symphony-loop` uses this skill as its execution engine within the full orchestration loop.
