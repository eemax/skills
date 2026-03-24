# Task Format

Every task follows this exact format. No variations.

```
- [ ] T001 [P] [US1] Create User model with validation in src/models/user.py
```

## Anatomy

```
- [ ] T001 [P] [US1] Create User model with validation in src/models/user.py
  │    │    │    │    │                                   │
  │    │    │    │    │                                   └─ exact file path (REQUIRED)
  │    │    │    │    └─ action description with verb
  │    │    │    └─ story label (required in story phases only)
  │    │    └─ parallel marker (only if truly independent)
  │    └─ sequential task ID
  └─ markdown checkbox
```

## Components

**Checkbox**: Always `- [ ]`. No exceptions.

**Task ID**: Sequential across all phases. T001, T002, T003... through the entire document. No gaps, no restarts per phase.

**[P] marker**: Include ONLY when:
- The task touches a different file than other [P] tasks in the same phase
- The task has no dependency on any incomplete task in the current phase
- Two agents could work on this and the other [P] task simultaneously without conflicts

Omit [P] when the task depends on a prior task's output (e.g., service depends on model being created first).

**[USn] label**: Maps to user story priorities from the spec.
- US1 = Story with priority P1
- US2 = Story with priority P2
- US3 = Story with priority P3

| Phase | Story label? |
|-------|-------------|
| Foundation | No |
| User Story phases | Yes — every task gets [USn] |
| Polish | No |

**Description**: Starts with an action verb. Includes enough detail that the executor knows exactly what to create/modify. Ends with the file path.

**File path**: Exact path relative to project root. Not `src/models/` but `src/models/user.py`. If a task creates a directory, use the directory path.

## Examples

Correct:
```
- [ ] T001 Create project directory structure per technical design
- [ ] T002 Initialize Python project with pyproject.toml and dependencies
- [ ] T003 [P] Configure pytest with conftest.py in tests/conftest.py
- [ ] T004 [P] Create base database configuration in src/config/database.py
- [ ] T010 [P] [US1] Create User model with email validation in src/models/user.py
- [ ] T011 [P] [US1] Create Organization model in src/models/organization.py
- [ ] T012 [US1] Implement UserService with CRUD operations in src/services/user_service.py
- [ ] T013 [US1] Add user registration endpoint in src/api/routes/users.py
- [ ] T030 [P] Update API documentation in docs/api.md
- [ ] T031 Add request rate limiting middleware in src/middleware/rate_limit.py
```

Wrong:
```
- [ ] Create model                              # no ID, no path
- [ ] T001 [US1] Update things                  # no file path
- [ ] T001 Implement feature                    # vague, no path
- T001 [P] [US1] Create model in src/model.py   # missing checkbox
- [ ] T005 [US2] Create helper                  # no file path, vague
```
