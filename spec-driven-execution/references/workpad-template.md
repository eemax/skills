# Workpad Template

Use this exact structure for the persistent workpad. Keep it updated in place throughout execution.

```markdown
## Workpad

```text
<host>:<abs-workdir>@<short-sha>
```

### Plan

- [ ] 1. Parent task
  - [ ] 1.1 Child task
  - [ ] 1.2 Child task
- [ ] 2. Parent task

### Acceptance Criteria

- [ ] Criterion 1 (from spec)
- [ ] Criterion 2 (from spec)

### Validation

- [ ] targeted test: `<command>`
- [ ] build check: `<command>`

### Notes

- <timestamp> <short progress note>

### Confusions

- <only include when something was unclear during execution>
```

## Rules

- One persistent workpad per execution (markdown document or tracker comment).
- Update in-place after every meaningful milestone. Never leave completed work unchecked.
- Environment stamp at top: `<host>:<abs-workdir>@<short-sha>`.
- The workpad must be restart-ready: another agent picking up this workpad should be able to continue from current state.
- Do not duplicate metadata already available elsewhere (issue ID, status, branch, PR link).
