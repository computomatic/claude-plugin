---
name: compile-docs
description: "Use when compiling documentation for a CLI tool, API, or other system from its reference material."
argument-hint: "[tool or system to document]"
---

Orchestrates a multi-phase pipeline that discovers, inventories, plans, and compiles documentation for a target system using specialized subagents.

## Orchestration Rules

- Never write documentation directly. All documentation authoring is delegated to `doc-author`.
- Delegate research tasks to the microplanner or reference agents as appropriate.
- Keep context lean. Summarize subagent results rather than reproducing their full output.
- Track progress via the master plan. Update step statuses as work is completed, blocked, or in progress.
- Minimize questions and proceed autonomously. Only escalate when a decision cannot be resolved from available context.

## Phase 1: Discover Reference Structure

- Dispatch `reference-scout` with all available info about the target (name, URLs, install location, etc.)
- Use the scouting report to inform Phase 2; do not stop to ask the user

## Phase 2: Build Reference Inventory

- Create a temp directory: `mktemp -d /tmp/compile-docs-XXXXXX`
- Dispatch `reference-explorer` with:
  - The scouting report from Phase 1
  - Target file: `<tmpdir>/inventory-001.md`
- If the explorer reports `status: partial`:
  - Dispatch continuation explorers, each writing the next numbered file (`inventory-002.md`, `inventory-003.md`, etc.)
  - Repeat until all explorers report `status: complete`
- Combine all partial inventories into `<tmpdir>/inventory-complete.md`

## Phase 3: Plan Documentation Hierarchy

- Dispatch `doc-planner` with:
  - Inventory file: `<tmpdir>/inventory-complete.md`
  - Output directory (default `docs/`)
  - Target plan file: `<tmpdir>/hierarchy-plan.md`
- Review the returned plan for completeness: every inventory entry must appear in the File Mapping table

## Phase 4: Hand Off to create-master-plan

- Invoke `/create-master-plan` with the following inputs:
  - **Reference inventory path:** `<tmpdir>/inventory-complete.md`
  - **Hierarchy plan path:** `<tmpdir>/hierarchy-plan.md`
  - **Style guide path:** `.claude/skills/compile-docs/style-guide.md`
  - **Agent roster:** microplanner (planning), doc-author (writing), doc-editor (review)
  - **Step list:** dynamically generated at runtime from the hierarchy plan's File Mapping table
    - One step per File Mapping entry (1:1 mapping)
    - Penultimate step: editorial review by `doc-editor`, issues written to `<tmpdir>/review-issues.md`
    - Final step: `doc-author` fixes based on review issues
  - **Autonomous execution guidance:** the user is not a domain expert; minimize questions; continue autonomously when no outstanding questions exist

## Agent Roster

- `reference-scout` -- reconnaissance; maps reference source structure and navigation method
- `reference-explorer` -- systematic enumeration; produces structured inventory files
- `doc-planner` -- documentation architect; designs file hierarchy from inventory
- `doc-author` -- technical writer; produces reference pages from microplans
- `doc-editor` -- senior editor; reviews completed docs for quality and completeness
- `microplanner` -- planning agent; creates per-step microplans for doc-author
