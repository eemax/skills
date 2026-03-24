---
name: technical-design
description: "Turn a feature specification into a technical implementation plan with researched decisions, data models, interface contracts, and project structure. Use when you have a spec/PRD and need to make architectural decisions, choose technologies, design data models, or define contracts before writing code."
---

# Technical Design

Transform a feature spec into a concrete technical plan. Every technical choice must be documented as a decision with rationale — no implicit assumptions, no hand-waving. The plan must resolve all unknowns before a single line of code is written.

## Load References

1. Read `references/plan-format.md` before writing the plan — it defines the required output structure.
2. Read `references/decision-record.md` when documenting any non-trivial technical choice.

## Workflow

1. **Load inputs.** Read the feature spec. If the project has a principles or constitution file (check repo root, docs/, or similar locations), read it too — it will inform the principles check in step 3.

2. **Establish technical context.** Fill in what you know about the project:
   - Language and version
   - Framework and key dependencies
   - Storage (database, file system, cache)
   - Testing tools and approach
   - Target platform
   - Performance goals and constraints
   - Scale expectations

   For unknowns: don't guess. Mark them explicitly — they become research tasks in step 4.

3. **Principles check** (if project principles exist). Evaluate the emerging plan against each stated principle. For each:
   - **Pass**: The plan naturally aligns. No action needed.
   - **Justified violation**: The plan deviates. Document: what principle is violated, why the violation is necessary for this feature, and what simpler alternative was considered and rejected.

   If no principles file exists, skip this step entirely.

4. **Research phase.** For each unknown or significant technical decision:
   - Investigate options (documentation, best practices, compatibility, performance characteristics)
   - Document as a decision record following `references/decision-record.md`
   - Every decision must have at least 2 real alternatives considered (not straw men)
   - No unknowns may survive into the final plan

5. **Design phase.** Working from the spec's entities and requirements:
   - **Data model**: For each entity — fields, types, relationships, validation rules, state transitions. Keep it at the logical level (not SQL DDL, but clear enough to write it).
   - **Interface contracts**: What the system exposes to users or other systems. The format depends on project type:
     - Web service → endpoint definitions (method, path, request/response shape)
     - Library → public API surface (functions, parameters, return types)
     - CLI tool → command structure (commands, flags, arguments, output format)
     - UI application → screen/component contracts (what data each view needs)
   - **Project structure**: Concrete directory tree with real paths. No "Option 1 / Option 2" — commit to a structure.

6. **Write the plan** following `references/plan-format.md`. Suggest the user invoke `task-breakdown` when ready to generate executable tasks.

## Hard Constraints

- No unknowns in the output. If you couldn't resolve something through research, surface it to the user before finalizing. A plan with question marks is not a plan.
- Every non-trivial decision must have a documented rationale with alternatives considered. "We're using X" without explaining why is not acceptable.
- Project structure must use concrete file paths. Not `src/[module]/` but `src/auth/`, `src/billing/`, etc.
- The plan must be traceable to the spec. Every data model entity should map to a spec entity. Every contract should serve a spec requirement.
- Do not include task lists, timelines, or implementation steps. The plan defines WHAT the architecture looks like and WHY. Task breakdown is a separate concern.
