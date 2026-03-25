---
name: spec-authoring
description: |
  Create SPEC.md and WORKFLOW.md files for a project. Analyzes existing project
  structure to generate a domain-specific service specification and an operational
  workflow with status map, prompt template, workpad template, and guardrails.
  Use when bootstrapping spec-driven development for a new project or repo.
---

# Spec Authoring

Generate the two artifacts that drive spec-driven autonomous development:

- **SPEC.md** — what the system does and why (domain model, state machines, algorithms, goals)
- **WORKFLOW.md** — how agents operate on it (YAML config + Liquid-templated prompt runbook)

Both documents must be project-specific. If they could apply to any project, they
are too generic and must be rewritten.

## Load References

- `references/spec-skeleton.md` — required sections and structure for SPEC.md
- `references/workflow-skeleton.md` — YAML schema and prompt body sections for WORKFLOW.md
- `references/status-map-patterns.md` — pre-built status map configurations for Linear, GitHub, and Kanban flows

## Inputs

- Target project or codebase to analyze
- Optional: tracker type and project identifier (Linear project slug, GitHub repo, etc.)
- Optional: existing documentation, specs, or architectural notes
- Optional: team workflow preferences (review gates, merge strategy, rework policy)

## Workflow

### Phase 1: Analyze Project

Before writing anything, explore the codebase to inform both documents:

1. **Language and framework** — what is the stack?
2. **Project structure** — directories, entry points, config files
3. **Domain concepts** — models, services, APIs, entities already in the code
4. **Testing approach** — test framework, test structure, CI gates
5. **Build and deploy** — build system, CI/CD pipelines, deploy targets
6. **Existing documentation** — READMEs, ADRs, existing specs, AGENTS.md
7. **Tracker integration** — which tracker, what states/labels are in use
8. **Git workflow** — branching strategy, PR conventions, merge approach

Record findings as structured notes — these feed directly into both documents.

### Phase 2: Generate SPEC.md

Follow the structure in `references/spec-skeleton.md`. For each section:

1. **Problem Statement** — 3-5 bullets describing what operational problems this
   system solves. Derive from the codebase's purpose, not abstract goals.

2. **Goals and Non-Goals** — goals are what the system actively does (testable);
   non-goals are explicit boundaries (what it does NOT do, even if expected).

3. **System Overview** — main components with one-line descriptions, abstraction
   levels if layered, external dependencies.

4. **Core Domain Model** — for every significant entity:
   - Purpose (one sentence)
   - Fields with types and descriptions
   - Relationships to other entities
   - Derive entities from existing models, database schemas, API payloads, or
     structs in the codebase.

5. **Configuration** — what is configurable, source precedence, defaults,
   environment variable patterns. Derive from existing config files.

6. **State Machines** — for each stateful entity:
   - Exhaustive state list
   - Transitions with guards (conditions)
   - Side effects per transition
   - ASCII state diagram
   - Derive from existing enums, status fields, or workflow logic in the code.

7. **Key Algorithms** — any non-trivial logic. Describe as numbered steps with
   decision points. Use language-agnostic pseudocode.

8. **Observability** — log events, metrics, health signals. Derive from existing
   logging, monitoring, or dashboard code.

9. **Security and Trust Boundary** — what the system trusts, validates, sandboxes.
   Secret handling patterns.

10. **Optional sections** — Failure Model, Testing/Validation, Implementation
    Checklist. Include when the project warrants them.

### Phase 3: Generate WORKFLOW.md

Follow the structure in `references/workflow-skeleton.md`.

#### YAML Front Matter

1. Choose a status map pattern from `references/status-map-patterns.md` that matches
   the project's tracker and workflow.
2. Fill in project-specific values:
   - `tracker.kind` and `tracker.project_slug` from the project's tracker
   - `active_states` and `terminal_states` from the chosen pattern
   - `workspace.root` — sensible default for the project
   - `hooks.after_create` — commands to bootstrap a workspace (clone, install deps)
   - `hooks.before_remove` — cleanup commands if needed
   - `agent` settings — concurrency, turns, backoff based on project scale
3. Validate: does the YAML parse? Are all fields present?

#### Prompt Body

Write the Liquid-templated prompt body section by section:

1. **Context injection** — issue metadata using `{{ issue.* }}` variables
2. **Retry/continuation** — `{% if attempt %}` block for retry awareness
3. **Instructions** — numbered rules for unattended orchestration:
   - Never ask a human to perform follow-up actions
   - Only stop for true blockers (missing auth/permissions)
   - Final message reports completed actions and blockers only
4. **Default posture** — behavioral guidelines specific to this project:
   - Reproduce first
   - Workpad as source of truth
   - Keep tracker metadata current
   - Out-of-scope items go to separate issues
5. **Related skills** — cross-references to skills the agent should use (commit,
   push, pull, review, etc.)
6. **Status map** — every tracker state mapped to exact agent behavior, from the
   chosen pattern in `references/status-map-patterns.md`
7. **Steps 0-4** — the full lifecycle:
   - Step 0: Route (detect state, dispatch to correct flow)
   - Step 1: Start/continue (workpad bootstrap, plan, acceptance criteria)
   - Step 2: Execution (implement, validate, PR, feedback sweep)
   - Step 3: Human review and merge
   - Step 4: Rework (full reset protocol)
8. **PR feedback sweep protocol** — how to gather all feedback channels and resolve
   every comment
9. **Blocked-access escape hatch** — when and how to report blockers
10. **Completion bar** — concrete checklist before moving to review state
11. **Guardrails** — hard rules (project-specific, not generic)
12. **Workpad template** — the exact markdown structure

### Phase 4: Validate

Self-check both documents:

**SPEC.md checks:**
- [ ] Every entity has fields with types
- [ ] State machines are complete — no orphan states, no missing transitions
- [ ] Goals align with the problem statement
- [ ] No implementation details (no framework names, no API code)
- [ ] Content is project-specific, not generic boilerplate

**WORKFLOW.md checks:**
- [ ] YAML front matter parses correctly
- [ ] All `active_states` appear in the status map
- [ ] All `terminal_states` have defined behavior (even if just "clean up")
- [ ] Liquid template variables match the available variable set
- [ ] Guardrails are testable (an agent can verify compliance)
- [ ] Workpad template is complete (all sections present)
- [ ] Status map covers every state the project's tracker uses
- [ ] Hooks are valid shell commands for the project's environment

## Hard Constraints

- **SPEC.md** describes WHAT and WHY — never HOW. No language names, no framework
  names, no API endpoint URLs, no implementation code.
- **WORKFLOW.md** is operational — it tells agents HOW to work, not what to build.
  The "what" comes from individual issues.
- Both documents must be project-specific. If a section could apply to any project
  without modification, rewrite it with concrete details.
- Status maps must cover every possible tracker state the project uses — no gaps.
- Guardrails must be testable — "be careful" is not a guardrail;
  "do not modify files outside the workspace directory" is.
- Do not invent domain concepts not present in the codebase. Derive from what exists.

## Cross-References

- The output documents are consumed by `spec-driven-execution` for task-level work.
- For full autonomous orchestration using both documents, see `symphony-loop`.
- For feature-level specs (not project-level), see the `feature-spec` skill.
- For technical architecture decisions, see the `technical-design` skill.
