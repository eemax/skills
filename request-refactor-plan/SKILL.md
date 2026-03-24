---
name: request-refactor-plan
description: This skill should be used when the user wants to create a detailed refactor plan with tiny commits via user interview, then file it as a GitHub issue or local markdown. Triggers on "plan a refactor", "refactoring plan", "refactoring RFC", "break a refactor into safe incremental steps", or "incremental refactor".
---

# Request Refactor Plan

Create a detailed, incremental refactoring plan through structured interview, then document it as a GitHub issue or local markdown file.

## Process

Steps may be skipped if not necessary for the specific refactor.

### 1. Problem Description

Ask the user for a long, detailed description of the problem they want to solve and any potential ideas for solutions.

### 2. Codebase Exploration

Explore the repo to verify the user's assertions and understand the current state of the codebase.

### 3. Alternative Exploration

Ask whether the user has considered other options, and present other options.

### 4. Implementation Interview

Interview the user about the implementation. Be extremely detailed and thorough.

### 5. Scope Definition

Hammer out the exact scope of the implementation. Work out what will change and what will not change.

### 6. Test Coverage Review

Look in the codebase to check for test coverage of this area. If there is insufficient test coverage, ask the user what their plans for testing are.

### 7. Commit Breakdown

Break the implementation into a plan of tiny commits. Remember Martin Fowler's advice: "make each refactoring step as small as possible, so that you can always see the program working."

### 8. Create the Refactor Plan

Create a GitHub issue with the refactor plan using `gh issue create`. If `gh` is not available or the user prefers local output, write to a markdown file instead.

Use the template in [references/refactor-issue-template.md](references/refactor-issue-template.md).
