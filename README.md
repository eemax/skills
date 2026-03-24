# Skills

A collection of agent skills designed to be copied into various AI agent harnesses (Claude Code, Cursor, Agent Commander, etc.). Each skill is a self-contained folder with a `SKILL.md` and optional `references/` for progressive disclosure.

## Spec-Driven Development

A three-skill workflow for building software specification-first. Inspired by GitHub's [spec-kit](https://github.com/github/spec-kit), distilled to the portable essence.

The core idea: **specifications describe WHAT and WHY, code expresses HOW.** Every user story must be independently shippable, every technical decision must have documented rationale, and every task must be machine-executable.

```
feature-spec  →  technical-design  →  task-breakdown
```

### feature-spec

Turn a vague feature idea into a precise, testable specification. Produces prioritized user stories with Given/When/Then acceptance scenarios, functional requirements, measurable success criteria, and edge cases.

Key discipline: zero implementation details in the spec. A structured gap analysis taxonomy catches ambiguities systematically rather than by chance, with a hard limit of 3 clarification questions.

### technical-design

Turn a feature spec into an architectural plan. Resolves all unknowns through a research phase, documents every technical choice as a lightweight ADR (decision record with context, options, rationale, and trade-offs), and produces data models, interface contracts, and a concrete project structure.

Optionally checks the plan against project principles/constitution if one exists — violations must be explicitly justified.

### task-breakdown

Break a technical design into phased, dependency-ordered tasks. Every task follows a strict format with IDs, parallelism markers, story labels, and file paths. Tasks are organized as: Foundation → one phase per user story (in priority order) → Polish.

Each story phase is independently shippable — Foundation + any single story = working software.

## Inspiration Skills

A curated set of skills sourced from [mattpocock/skills](https://github.com/mattpocock/skills). These cover various development workflows: PRD writing, TDD, code architecture review, refactoring, issue triage, and more. Browse them for ideas or drop them into your harness as-is.

## Usage

1. Pick the skill(s) you need
2. Copy the skill folder into your agent harness's skills directory
3. The `SKILL.md` frontmatter (`name` and `description`) tells the harness when to trigger it
4. Reference files in `references/` are loaded on-demand — the SKILL.md tells the agent when to read each one
