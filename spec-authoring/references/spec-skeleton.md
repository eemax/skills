# SPEC.md Skeleton

Use this section structure when generating a SPEC.md. Every section is required unless
marked optional. Replace guidance comments with project-specific content.

```markdown
# <Project Name> Service Specification

Status: Draft v1

Purpose: <One sentence — what this service does and why it exists.>

## 1. Problem Statement

<!-- 3-5 bullets describing the operational problems this system solves.
     Be specific to the project — not generic software problems. -->

## 2. Goals and Non-Goals

### 2.1 Goals

<!-- What the system actively does. Each goal is testable. -->

### 2.2 Non-Goals

<!-- Explicit boundaries. What this system does NOT do, even if someone might expect it. -->

## 3. System Overview

### 3.1 Main Components

<!-- Numbered list of components with one-line descriptions.
     Example:
     1. `Orchestrator` — Owns the poll tick, dispatch decisions, and retry state.
     2. `Workspace Manager` — Maps issue identifiers to filesystem paths. -->

### 3.2 Abstraction Levels

<!-- Layer diagram if the system has distinct layers.
     Example: Policy Layer → Configuration Layer → Coordination Layer → Execution Layer -->

### 3.3 External Dependencies

<!-- What the system requires from outside: APIs, filesystems, CLIs, auth. -->

## 4. Core Domain Model

### 4.1 Entities

<!-- For each entity:
     - Purpose (one sentence)
     - Fields table: name, type, description
     - Relationships to other entities

     Example:
     #### 4.1.1 Issue
     Normalized issue record used by orchestration and prompt rendering.
     Fields:
     - `id` (string) — Stable tracker-internal ID.
     - `title` (string)
     - `state` (string) — Current tracker state name.
-->

### 4.2 Stable Identifiers and Normalization Rules

<!-- How IDs are derived, compared, and sanitized. -->

## 5. Configuration

<!-- What is configurable at runtime.
     - Source precedence (file, env, defaults)
     - Key configuration fields with types and defaults
     - Dynamic reload behavior (if any) -->

## 6. State Machines

<!-- For each stateful entity:
     - States (exhaustive list)
     - Transitions with guards (conditions)
     - Side effects per transition
     - ASCII diagram

     Example:
     ```
     Unclaimed → Claimed → Running → Succeeded
                                   → Failed → RetryQueued → Claimed
     ``` -->

## 7. Key Algorithms

<!-- Any non-trivial logic: polling, scheduling, dispatch, retry, conflict resolution.
     Describe as numbered steps with decision points.
     Use pseudocode if helpful — keep it language-agnostic. -->

## 8. Observability

<!-- What an operator needs to see:
     - Structured log events with required context fields
     - Metrics (if any)
     - Health signals
     - Optional: dashboard/API endpoints -->

## 9. Security and Trust Boundary

<!-- What the system trusts vs. validates vs. sandboxes.
     - Secret handling patterns ($VAR indirection)
     - Filesystem safety requirements
     - Hook script safety considerations -->

## 10. Failure Model (optional)

<!-- Failure classes and recovery behavior.
     - Transient vs. permanent failures
     - Retry strategies
     - Operator intervention points -->

## 11. Testing and Validation (optional)

<!-- Conformance profiles, test matrix, or validation checklist. -->

## 12. Implementation Checklist (optional)

<!-- Required items for a conforming implementation.
     Recommended extensions.
     Operational validation steps before production. -->
```

## Guidance

- SPEC.md describes WHAT and WHY — never HOW (no framework names, no API code, no implementation details).
- Every entity must have fields with types.
- State machines must be complete — no orphan states, no missing transitions.
- Goals must align with the problem statement.
- Be project-specific — if this spec could apply to any project, it is too generic.
