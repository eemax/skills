# Phase Structure

Tasks are organized into phases. Each phase has a clear purpose, entry criteria, and a checkpoint that confirms it's complete.

---

## Phase 1: Foundation

**Purpose**: Project skeleton and shared infrastructure that blocks all user stories.

**What belongs here**:
- Project initialization (directory structure, config files, dependency setup)
- Shared utilities and base classes used by multiple stories
- Database/storage setup and base configuration
- Authentication/authorization framework (if multiple stories need it)
- Base models that multiple stories depend on

**What does NOT belong here**:
- Anything specific to a single user story (put it in that story's phase)
- "Nice to have" infrastructure not yet needed
- Testing setup beyond basic config (test specifics belong in story phases)

**Checkpoint**: Project builds/runs, base configuration works, shared utilities are available. No user-facing functionality yet.

**No story labels on tasks.**

---

## Phase 2: [Story 1 Title] (P1) — MVP

**Purpose**: Complete vertical slice for the highest-priority user story. After this phase, you have a minimum shippable product.

**What belongs here**: EVERYTHING needed to make this story work end-to-end:
- Models/entities specific to this story
- Business logic / services for this story
- Interface layer (API routes, CLI commands, UI views) for this story
- Any migrations or data setup for this story
- If tests requested: test tasks BEFORE implementation tasks

**Goal**: Foundation + this phase = working software that delivers real value to users.

**Checkpoint**: All acceptance scenarios for this story pass. A user can perform the complete journey described in the story.

**All tasks labeled [US1].**

---

## Phase 3: [Story 2 Title] (P2)

Same structure as Phase 2, but for the next-priority story.

**Key rule**: This phase must not break Phase 2. Adding Story 2 extends the product — it doesn't require modifying Story 1's core functionality.

**All tasks labeled [US2].**

---

## Phase 4+: Additional Stories

Continue the pattern. One phase per story, in priority order.

If a later story genuinely requires modifying an earlier story's code (not just using it), include those modification tasks in the later story's phase with a clear note about what's changing and why.

---

## Final Phase: Polish

**Purpose**: Cross-cutting improvements that span multiple stories.

**What belongs here**:
- Documentation (API docs, README updates, deployment guides)
- Performance optimization across stories
- Security hardening (input sanitization, rate limiting, audit logging)
- Code cleanup and refactoring
- Observability (logging, metrics, health checks)

**What does NOT belong here**:
- New functionality (that's a new story)
- Bug fixes from earlier phases (fix them in-phase, not here)

**Checkpoint**: All success criteria from the spec are met. Documentation is complete. The product is production-ready.

**No story labels on tasks.**

---

## Execution Strategies

### MVP (Solo developer)
1. Complete Foundation
2. Complete Story 1 (P1)
3. **Stop and validate** — does the MVP work?
4. Continue to Story 2, 3, etc.
5. Polish when all desired stories are done

### Incremental Delivery
1. Foundation → Story 1 → Ship/Demo (MVP)
2. Story 2 → Ship/Demo (enhanced)
3. Story 3 → Ship/Demo (full feature)
4. Polish → Ship/Demo (production-ready)

Each increment adds value without breaking previous increments.

### Parallel Team
1. Team completes Foundation together
2. After Foundation:
   - Dev A works Phase 2 (Story 1)
   - Dev B works Phase 3 (Story 2)
   - Dev C works Phase 4 (Story 3)
3. Stories integrate independently
4. Team collaborates on Polish

This works because story phases are designed to be independent.

---

## Dependency Summary Format

Include this at the end of the task list:

```markdown
## Dependencies

- **Foundation** → blocks all story phases
- **Story 1 (P1)** → independent after Foundation
- **Story 2 (P2)** → independent after Foundation (may use Story 1 components but doesn't modify them)
- **Story 3 (P3)** → independent after Foundation
- **Polish** → depends on all desired story phases being complete

### Parallel Opportunities
- Foundation: [list of [P] task groups]
- Story 1: [list of [P] task groups]
- [etc.]
```
