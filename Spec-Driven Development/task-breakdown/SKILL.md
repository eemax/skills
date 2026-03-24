---
name: task-breakdown
description: "Break a technical design into phased, dependency-ordered, machine-executable tasks organized by user story. Use when you have a plan/design and need to generate a task list, decompose work into steps, create an implementation checklist, or figure out what to build first."
---

# Task Breakdown

Generate a task list that an agent or developer can execute without asking clarifying questions. Every task has an ID, a file path, and a clear action. Tasks are organized by user story so each phase is independently shippable.

## Load References

1. Read `references/task-format.md` before generating any tasks — it defines the strict format every task must follow.
2. Read `references/phase-structure.md` to understand how tasks are organized into phases.

## Workflow

1. **Load inputs.** Read the technical design and the original feature spec. Also read any additional design artifacts referenced in the plan (data model, contracts, research).

2. **Extract structure.** From the inputs, identify:
   - Tech stack and project layout (from the plan)
   - User stories with priorities (from the spec — P1, P2, P3...)
   - Entities and their relationships (from the data model)
   - Interface contracts (from the contracts section)
   - Shared infrastructure needs (from the project structure)

3. **Generate tasks by phase.** Follow the structure in `references/phase-structure.md`:

   - **Phase 1: Foundation** — Everything that blocks all user stories: project scaffold, config, shared utilities, base models that multiple stories depend on. Keep this minimal — only truly shared infrastructure belongs here.

   - **Phase 2+: One phase per user story** in priority order. Each phase contains ALL tasks to make that story work end-to-end:
     - Models/entities needed for this story
     - Services/business logic for this story
     - Interfaces (API routes, CLI commands, UI components) for this story
     - Integration with existing components
     - Story-specific config or migrations

   - **Final phase: Polish** — Cross-cutting concerns that span multiple stories: documentation, performance optimization, security hardening, cleanup.

4. **Mark parallelism and dependencies.** Within each phase:
   - Mark tasks [P] if they touch different files and have no dependency on incomplete tasks in the same phase
   - Order non-parallel tasks by dependency: models before services, services before interfaces
   - Between phases: each story phase depends on Foundation being complete, but story phases are independent of each other

5. **Validate completeness.**
   - [ ] Every user story from the spec has a corresponding phase
   - [ ] Every task has a concrete file path
   - [ ] Every story phase is independently testable — if you completed only Foundation + this phase, does something work?
   - [ ] No orphan tasks (every task traces to a story or infrastructure need)
   - [ ] Task IDs are sequential across all phases (T001, T002, ... no gaps)
   - [ ] Parallel markers are accurate (no [P] on tasks that depend on unfinished work)

6. **Write the task list.** Include the phase structure, dependency summary, and execution strategy.

## Hard Constraints

- Every task MUST follow the strict format in `references/task-format.md`. No exceptions, no shortcuts.
- Every task MUST include a concrete file path. "Implement the service" is not a task. "Implement UserService in src/services/user_service.py" is.
- Every story phase MUST be independently shippable. Foundation + any single story phase = working software.
- Tests are OPTIONAL. Only generate test tasks if the spec explicitly requests TDD or the user asks for tests. When included, test tasks come before implementation tasks within each phase.
- No task should require the executor to read additional context or make architectural decisions. The task list is the final specification of work — all decisions have already been made in the design.
- Do not add tasks for things that aren't in the plan. No "nice to have" tasks, no speculative future work, no "we might need this later."
