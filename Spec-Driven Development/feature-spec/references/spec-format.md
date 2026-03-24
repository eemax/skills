# Spec Output Format

Every spec follows this structure. Remove sections that don't apply — don't leave empty sections or "N/A" placeholders.

---

```markdown
# Feature: [Name]

**Status**: Draft
**Date**: [YYYY-MM-DD]
**Input**: [One-line summary of what the user asked for]

## Stories

### Story 1 — [Title] (P1)

[2-4 sentences describing this user journey in plain language. Who does what, and what value do they get?]

**Why this priority**: [Why this is the most important story — what value it unlocks]

**Independent test**: [Concrete description of how to verify this story works on its own. "A user can [do X] and see [Y result]" — proving it delivers standalone value]

**Acceptance scenarios**:

1. **Given** [initial state], **When** [user action], **Then** [expected outcome]
2. **Given** [initial state], **When** [user action], **Then** [expected outcome]

---

### Story 2 — [Title] (P2)

[Same structure as above]

---

[Continue for each story. Most features need 2-5 stories.]

### Edge Cases

- What happens when [boundary condition]? Expected: [behavior]
- What happens when [error scenario]? Expected: [behavior]
- What happens when [concurrent/conflicting action]? Expected: [behavior]

## Requirements

### Functional Requirements

- **FR-001**: System MUST [specific, testable capability]
- **FR-002**: System MUST [specific, testable capability]
- **FR-003**: Users MUST be able to [specific interaction]

### Entities

- **[Entity Name]**: [What it represents. Key attributes: A, B, C. Relationships: belongs to X, has many Y]
- **[Entity Name]**: [What it represents. Key attributes and relationships]

## Success Criteria

- **SC-001**: [Measurable outcome from user perspective, e.g., "Users complete checkout in under 3 minutes"]
- **SC-002**: [Measurable outcome, e.g., "System supports 1,000 concurrent users without degradation"]
- **SC-003**: [Measurable outcome, e.g., "90% of users complete primary task on first attempt"]

## Assumptions

- [Reasonable default chosen and why, e.g., "Standard session-based auth assumed — spec did not specify auth method"]
- [Scope boundary, e.g., "Mobile support out of scope for initial release"]
- [Environmental assumption, e.g., "Users have stable internet connectivity"]
```

---

## Section Rules

**Stories** (mandatory): Ordered by priority. P1 is always the MVP — the one story you'd ship if you could only ship one. Each story must be a complete vertical slice, not a horizontal layer.

**Edge Cases** (mandatory): At minimum, cover: empty/zero state, maximum/overflow, concurrent access, malformed input, and permission denied.

**Requirements** (mandatory): Use MUST for non-negotiable, SHOULD for preferred. Every requirement traces to at least one user story. If a requirement doesn't map to a story, either add a story or question why the requirement exists.

**Entities** (include if the feature involves persistent data): Name, purpose, key attributes, and relationships only. No field types, no database schemas, no implementation details.

**Success Criteria** (mandatory): 3-6 criteria. Each must include a specific metric. "Improve performance" is not a criterion. "Users see search results within 2 seconds for 95% of queries" is.

**Assumptions** (mandatory): Document every reasonable default you chose where the user's description was silent. This is how you stay transparent about what you inferred vs. what was specified.
