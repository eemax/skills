# Plan Output Format

Every technical design follows this structure. Remove sections that don't apply.

---

```markdown
# Technical Design: [Feature Name]

**Spec**: [path or link to the feature spec]
**Date**: [YYYY-MM-DD]
**Status**: Draft

## Summary

[One paragraph. What are we building, what's the key technical approach, and what's the most important architectural decision?]

## Technical Context

| Dimension | Value |
|-----------|-------|
| Language | [e.g., Python 3.12] |
| Framework | [e.g., FastAPI 0.104] |
| Storage | [e.g., PostgreSQL 16, Redis 7] |
| Testing | [e.g., pytest with pytest-asyncio] |
| Platform | [e.g., Linux containers, AWS ECS] |
| Performance | [e.g., <200ms p95 response time] |
| Scale | [e.g., 10K concurrent users, 1M records] |

## Principles Check

*Include only if project principles/constitution exist. Remove entirely otherwise.*

| Principle | Status | Notes |
|-----------|--------|-------|
| [Principle name] | Pass | — |
| [Principle name] | Justified Violation | [What's violated, why it's necessary, what simpler alternative was rejected] |

## Decisions

### D1: [Decision Title]

**Context**: [What problem or choice point does this address? Why does it matter?]

**Options considered**:
| Option | Pros | Cons |
|--------|------|------|
| [Option A] | [strengths] | [weaknesses] |
| [Option B] | [strengths] | [weaknesses] |

**Decision**: [What was chosen]

**Rationale**: [Why this option won. Reference specific requirements or constraints from the spec.]

**Trade-offs**: [What we're giving up by choosing this option]

---

### D2: [Decision Title]

[Same structure]

---

## Data Model

### [Entity Name]

[One sentence: what this entity represents]

| Field | Type | Constraints | Purpose |
|-------|------|-------------|---------|
| [field] | [type] | [e.g., required, unique, FK to X] | [why this field exists] |

**Relationships**: [e.g., "belongs to Organization", "has many Projects"]

**State transitions** (if applicable):
```
[state A] → [state B] → [state C]
     ↘ [state D]
```

---

### [Entity Name]

[Same structure]

---

## Contracts

*Define what the system exposes. Format depends on project type.*

### [Interface Name]

[Contract definition. Examples by project type:]

**Web service example:**
```
POST /api/users
Request:  { name: string, email: string }
Response: { id: string, name: string, email: string, created_at: datetime }
Errors:   409 Conflict (email exists), 422 Validation Error
```

**Library example:**
```
create_user(name: str, email: str) -> User
Raises: DuplicateEmailError, ValidationError
```

**CLI example:**
```
myapp user create --name <name> --email <email>
Output: JSON { id, name, email, created_at }
Exit codes: 0 success, 1 validation error, 2 conflict
```

---

## Project Structure

```
project-root/
├── src/
│   ├── models/
│   │   ├── user.py
│   │   └── organization.py
│   ├── services/
│   │   └── user_service.py
│   ├── api/
│   │   └── routes/
│   │       └── users.py
│   └── config.py
├── tests/
│   ├── integration/
│   │   └── test_user_flow.py
│   └── unit/
│       └── test_user_service.py
└── [config files]
```

**Structure rationale**: [Why this layout — reference project type, team conventions, or framework expectations]
```

---

## Section Rules

**Summary**: One paragraph max. A reader should understand the big picture in 30 seconds.

**Technical Context**: No unknowns allowed here. Every field must be filled with a concrete value. If research was needed to determine a value, there's a corresponding Decision entry.

**Principles Check**: Only include if the project has a principles/constitution document. Don't invent principles. Every "Justified Violation" row must explain the violation, the necessity, and the rejected simpler alternative.

**Decisions**: One entry per non-trivial technical choice. Trivial choices (obvious framework defaults, standard library usage) don't need entries. Each decision must have at least 2 real options considered — if there's genuinely only one option, explain why.

**Data Model**: Logical level — clear enough to implement in any storage system. Include field types, constraints, relationships, and state machines. Don't include SQL DDL, ORM decorators, or migration scripts.

**Contracts**: Define the system's external interfaces. Choose the format that matches the project type. Every contract must trace to a requirement in the spec.

**Project Structure**: Concrete paths only. This should be copy-pasteable as a directory creation script. Include a one-line rationale for the layout choice.
