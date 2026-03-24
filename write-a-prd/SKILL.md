---
name: write-a-prd
description: This skill should be used when the user wants to create a Product Requirements Document through user interview, codebase exploration, and module design, then submit as a GitHub issue. Triggers on "write a PRD", "create a product requirements document", "plan a new feature", "product requirements", or "feature requirements".
---

# Write a PRD

Create a PRD through structured interview, codebase exploration, and module design. Steps may be skipped if not necessary.

## Process

### 1. Problem Discovery

Ask the user for a long, detailed description of the problem they want to solve and any potential ideas for solutions.

### 2. Codebase Analysis

Explore the repo to verify the user's assertions and understand the current state of the codebase.

### 3. Design Interview

Interview the user relentlessly about every aspect of this plan until reaching a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one.

### 4. Module Architecture

Sketch out the major modules needed to build or modify to complete the implementation. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user that these modules match their expectations. Check which modules they want tests written for.

### 5. Create the PRD

Once there is a complete understanding of the problem and solution, create the PRD as a GitHub issue using `gh issue create`. If `gh` is not available or the user prefers local output, write to a markdown file instead.

Use the template in [references/prd-template.md](references/prd-template.md).
