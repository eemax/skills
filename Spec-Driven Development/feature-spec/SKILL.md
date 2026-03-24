---
name: feature-spec
description: "Turn a vague feature idea into a precise, testable specification with independently shippable user stories. Use when starting new features, writing PRDs, defining requirements, or when someone says 'I want to build X' and needs a structured spec before implementation begins."
---

# Feature Spec

Write specifications that describe WHAT a system does and WHY, never HOW. Every spec must be precise enough that two independent teams could build the same thing from it, and every user story must deliver value on its own.

## Load References

1. Read `references/spec-format.md` before writing the spec — it defines the required output structure.
2. Read `references/gap-analysis.md` after drafting the spec — use it to systematically find gaps before presenting to the user.

## Workflow

1. **Extract intent.** Parse the user's feature description. Identify:
   - Who are the actors (users, systems, roles)?
   - What are their goals?
   - What data is involved?
   - What constraints exist?

2. **Draft user stories as shippable slices.** Each story is a vertical slice that delivers standalone value. Assign priorities P1/P2/P3 based on user impact. For each story:
   - Write a plain-language description
   - Explain why it has this priority
   - Describe how to test and ship it independently — if Story 1 were the ONLY thing built, does it deliver real value?
   - Write acceptance scenarios in Given/When/Then format

3. **Run gap analysis.** Scan the draft against every category in `references/gap-analysis.md`. For each gap:
   - If an industry-standard default exists, use it and document it in Assumptions
   - If the ambiguity would materially change scope, security posture, or user experience AND no reasonable default exists, mark it [NEEDS CLARIFICATION]
   - Hard limit: maximum 3 [NEEDS CLARIFICATION] markers in any spec

4. **Define success criteria.** Each criterion must be:
   - Measurable (includes a number: time, percentage, count, rate)
   - Technology-agnostic (no frameworks, databases, or protocols mentioned)
   - Verifiable from the user's perspective (not system internals)

5. **Self-validate.** Before presenting the spec, check:
   - [ ] Zero implementation details (no languages, frameworks, APIs, database names)
   - [ ] Every functional requirement is independently testable
   - [ ] Every user story is independently shippable
   - [ ] Success criteria are measurable without knowing the tech stack
   - [ ] Assumptions section documents all reasonable defaults chosen
   - [ ] No vague adjectives remain unquantified ("fast", "robust", "intuitive")

6. **Present clarifications.** If [NEEDS CLARIFICATION] markers exist, present each as:
   - The specific question and why it matters
   - 2-4 concrete options as a table with implications
   - Your recommended option with 1-2 sentences of reasoning
   - Let the user pick or provide their own answer

7. **Write the spec** following the format in `references/spec-format.md`. Suggest the user invoke `technical-design` when ready to plan implementation.

## Hard Constraints

- WHAT and WHY only. If an implementation detail appears anywhere — a language, framework, API name, database, protocol — remove it. The spec must read as if written for a business stakeholder.
- Maximum 3 [NEEDS CLARIFICATION] markers. Prioritize by: scope impact > security/privacy > user experience > technical details. Make informed defaults for everything else.
- Every user story must be independently shippable. "Independently testable" is not enough — ask: if we shipped ONLY this story, does a user get real value?
- Success criteria must be measurable from outside the system. Not "API responds in 200ms" but "users see results within 1 second."
- Do not include checklists, implementation notes, or technical recommendations in the spec output. The spec is a requirements document, not a plan.
