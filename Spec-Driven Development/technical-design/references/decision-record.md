# Decision Record Format

Every non-trivial technical choice in the plan gets a decision record. This format is a lightweight ADR (Architecture Decision Record) designed for AI-assisted development — concise enough to fit in context, structured enough to be useful months later.

## When to Write a Decision Record

Write one when:
- Choosing between technologies, libraries, or approaches
- Deviating from a project principle or convention
- Making a trade-off that future developers should understand
- The choice isn't obvious from the code alone

Skip when:
- Using the framework's default/recommended approach
- The choice is dictated by existing codebase conventions
- There's genuinely only one viable option (but explain why in the plan)

## Structure

```markdown
### D[N]: [Short Decision Title]

**Context**: [1-3 sentences. What forces are at play? What problem does this solve? Why can't we defer this decision?]

**Options considered**:
| Option | Pros | Cons |
|--------|------|------|
| [A] | [real strengths] | [real weaknesses] |
| [B] | [real strengths] | [real weaknesses] |

**Decision**: [The choice, stated plainly]

**Rationale**: [Why this won. Reference specific spec requirements, constraints, or principles. "It's popular" is not a rationale. "It meets the <200ms latency requirement from SC-002 while Option B's cold start exceeds it" is.]

**Trade-offs**: [What we're giving up. Every choice has a cost — name it explicitly.]
```

## Quality Checks

- **At least 2 real options**: If you can't name an alternative, it's not a decision — it's a constraint. Document it in Technical Context instead.
- **No straw men**: Each option must be something a reasonable engineer would actually consider. Don't list obviously bad options to make your choice look good.
- **Traceable rationale**: The rationale should reference specific requirements (FR-XXX), success criteria (SC-XXX), or principles by name. Generic reasoning ("better performance") needs quantification.
- **Honest trade-offs**: Name what you're losing. If there are no trade-offs, you probably haven't thought hard enough.

## Numbering

Sequential within the plan: D1, D2, D3, etc. Number reflects documentation order, not importance or chronology.
