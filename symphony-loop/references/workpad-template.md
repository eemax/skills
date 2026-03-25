# Workpad Template (Symphony Loop)

Use this exact structure for the persistent workpad. Identical to the
`spec-driven-execution` workpad with an added Attempt field for retry tracking.

```markdown
## Agent Workpad

```text
<host>:<abs-workdir>@<short-sha>
Attempt: <number or "initial">
```

### Plan

- [ ] 1. Parent task
  - [ ] 1.1 Child task
  - [ ] 1.2 Child task
- [ ] 2. Parent task

### Acceptance Criteria

- [ ] Criterion 1 (from issue/spec)
- [ ] Criterion 2 (from issue/spec)

### Validation

- [ ] targeted test: `<command>`
- [ ] build check: `<command>`

### Notes

- <timestamp> <short progress note>

### Confusions

- <only include when something was unclear during execution>
```

## Rules

- One persistent workpad per issue (tracker comment with `## Agent Workpad` header).
- Search existing comments for the marker header before creating a new one.
- Ignore resolved/closed comments when searching — only active comments are eligible.
- Persist the workpad comment ID and only write updates to that ID.
- Update in-place after every meaningful milestone.
- On retry/continuation, reconcile the existing workpad before making new edits:
  check off done items, update plan for current scope, update the Attempt field.
- On rework (full reset), delete the existing workpad and create a fresh one.
- The workpad must be restart-ready: another agent reading only this workpad should
  know what has been done, what remains, and what is blocked.
