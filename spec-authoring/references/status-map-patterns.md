# Status Map Patterns

Pre-built status map configurations for common tracker setups. Choose the pattern
that matches your project's workflow, then customize states and behaviors.

---

## Pattern 1: Standard Linear Flow

The default Symphony pattern. Full lifecycle with human review gate and rework loop.

### YAML Config

```yaml
tracker:
  kind: linear
  active_states:
    - Todo
    - In Progress
    - Merging
    - Rework
  terminal_states:
    - Done
    - Closed
    - Cancelled
    - Canceled
    - Duplicate
```

### Status Map

```markdown
## Status map

- `Backlog` -> Out of scope. Do not modify.
- `Todo` -> Claim: move to `In Progress`, bootstrap workpad, start execution.
  - Special: if PR already attached, treat as feedback/rework loop.
- `In Progress` -> Continue execution from current workpad state.
- `Human Review` -> PR attached and validated. Wait and poll for approval.
- `Merging` -> Approved by human. Sync main, resolve conflicts, merge, move to `Done`.
- `Rework` -> Full reset: close PR, delete workpad, fresh branch, re-execute.
- `Done` -> Terminal. No action.
- `Closed` / `Cancelled` / `Duplicate` -> Terminal. Clean up workspace.
```

### State Diagram

```
Backlog ──(human)──> Todo ──> In Progress ──> Human Review ──> Merging ──> Done
                                   ^               │
                                   │               v
                                   └──── Rework <──┘
```

---

## Pattern 2: GitHub Issues Flow

Simpler flow for projects using GitHub Issues. Fewer states, no explicit merge gate.

### YAML Config

```yaml
tracker:
  kind: github
  active_states:
    - open
    - in-progress
  terminal_states:
    - closed
```

### Status Map

```markdown
## Status map

- `open` -> Claim: add `in-progress` label, bootstrap workpad, start execution.
- `in-progress` -> Continue execution from current workpad state.
  - When complete: create/update PR, request review, remove `in-progress` label.
- `closed` -> Terminal. Clean up workspace.
```

### State Diagram

```
open ──> in-progress ──> (PR created, review requested) ──> closed
              ^                                                │
              └──────── (changes requested) ───────────────────┘
```

### Notes

- GitHub Issues lack built-in state machines. Use labels (`in-progress`, `blocked`)
  to track state.
- Rework is implicit: when PR changes are requested, the issue stays `in-progress`
  and the agent addresses feedback directly (no full reset).
- Merge is typically handled by GitHub's merge queue or reviewer, not the agent.

---

## Pattern 3: Kanban Flow

For teams using a kanban board without PR-based review gates. Common in
infrastructure, scripts, and internal tooling projects.

### YAML Config

```yaml
tracker:
  kind: linear  # or github/jira
  active_states:
    - Ready
    - Doing
    - Review
  terminal_states:
    - Done
    - Archived
```

### Status Map

```markdown
## Status map

- `Backlog` -> Out of scope. Do not modify.
- `Ready` -> Claim: move to `Doing`, bootstrap workpad, start execution.
- `Doing` -> Continue execution from current workpad state.
  - When complete: push changes, move to `Review`.
- `Review` -> Wait for human review. If feedback, address in-place (no full reset).
- `Done` -> Terminal. No action.
- `Archived` -> Terminal. Clean up workspace.
```

### State Diagram

```
Backlog ──(human)──> Ready ──> Doing ──> Review ──> Done
                                 ^          │
                                 └──────────┘
                              (feedback loop)
```

### Notes

- No explicit merge state — changes are pushed directly or via lightweight PR.
- Rework is in-place, not a full reset (appropriate for smaller changes).
- Add a `Blocked` column if your team uses one, and map it to the blocked-access
  escape hatch behavior.

---

## Customization Checklist

When adapting a pattern for your project:

- [ ] All tracker states your project uses appear in either `active_states` or `terminal_states`
- [ ] Every `active_state` has a corresponding entry in the status map
- [ ] Terminal states include all "done" variants your tracker uses
- [ ] The status map specifies exact behavior for each state (not vague guidance)
- [ ] Rework behavior is defined: full reset vs. in-place fix
- [ ] Blocked handling is defined: what counts as a blocker, how to report it
