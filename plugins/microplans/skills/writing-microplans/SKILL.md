---
name: writing-microplans
description: Reference for the microplan document format. Provides structure, naming conventions, and writing guidelines for creating microplan files. Agents and skills that create microplans should follow this format.
---

# Writing Microplans

This document defines the standard format for microplan files. Follow this format when creating microplans for individual steps in a master plan.

## File Location and Naming

- Store microplans in `.claude/plans/` at the repository root
- Prefix filenames with `microplan-`
- Use descriptive kebab-case names that reflect the step's purpose
- Examples: `microplan-add-auth-middleware.md`, `microplan-refactor-database-layer.md`

## Document Structure

Use this template when writing a microplan:

```markdown
# Microplan: [Step Title]

## Objective

[One or two sentences describing what this step accomplishes and why it matters in the broader roadmap.]

## Context

[Relevant background: which master plan step this corresponds to, what was completed before this step, and any constraints or decisions that affect this work.]

## Research Findings

[Summary of what you learned from exploring the codebase. Include relevant file paths, existing patterns, dependencies, and anything that influences the implementation approach.]

## Implementation Plan

1. [First sub-step with specific file paths and function names]
2. [Second sub-step]
3. [Continue as needed]

Each sub-step should be small enough to implement without further decomposition.

## Outstanding Questions

- [ ] [Question about an uncertain decision or missing context]
- [ ] [Question about a consequential choice that the user should weigh in on]
```

## Writing Guidelines

- **Research thoroughly** before writing the plan. Read the relevant source files, understand existing patterns, and trace through the code paths that will be affected.
- **Be specific.** Reference exact file paths, function names, type definitions, and line ranges. A good microplan leaves no ambiguity about where changes go.
- **Ask questions for uncertainty.** If you are unsure about a design decision, an edge case, or a requirement, add it to Outstanding Questions rather than guessing. The user is the domain expert.
- **Ask questions for consequential decisions.** Even if you have a reasonable default in mind, flag choices that are hard to reverse or that significantly shape the architecture. Let the user confirm or redirect.
- **Focus on a single step.** Each microplan covers exactly one step from the master plan. Do not bundle unrelated work or scope-creep into adjacent steps.
- **Keep sub-steps concrete and ordered.** Each sub-step in the Implementation Plan should describe a single, actionable change. Order them so each builds on the previous one.
