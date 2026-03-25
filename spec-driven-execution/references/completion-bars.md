# Completion Bars

Quality gates that must be satisfied before transitioning between phases.

## After Plan Phase

- [ ] Hierarchical task checklist is comprehensive — covers full spec scope
- [ ] Each task has clear acceptance criteria
- [ ] Self-review completed — no gaps, no ambiguities
- [ ] Workpad Plan section is populated and structured

## After Execute Phase

- [ ] All plan items checked off in workpad
- [ ] Code changes are committed
- [ ] No known regressions introduced
- [ ] Newly discovered tasks added and completed (or documented as out-of-scope)

## After Validate Phase

- [ ] All acceptance criteria from spec pass with observable evidence
- [ ] Validation/Test Plan sections executed verbatim (if provided in spec)
- [ ] Test results recorded in workpad Validation section
- [ ] No temporary proof edits remain in codebase

## After Handoff Phase

- [ ] Workpad reflects actual final state (all sections current)
- [ ] Confusions section added if anything was unclear
- [ ] Completed work and any remaining blockers summarized
- [ ] Workpad is restart-ready for another agent or human reviewer
