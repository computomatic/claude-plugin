---
name: creating-apm-instructions
description: Create project-level APM instructions. Use only when the target repository contains apm.yml.
---

# Create APM Instructions

Use this skill only when the target repository contains a canonical `apm.yml`.
If it does not, APM conventions do not apply; use that repository's normal
instruction mechanism.

1. Create the file at `.apm/instructions/<name>.instructions.md`, using a
   lowercase kebab-case `<name>`.
2. Add frontmatter with a clear `description` and an `applyTo` pattern that
   narrowly selects the intended target files. Omit `applyTo` only for an
   intentional unconditional instruction.
3. Write direct, scoped guidance. Keep reusable workflows in skills and
   autonomous responsibilities in agents.
4. Ensure the path and declaration agree with `apm.yml`.
5. Run `apm compile --validate`, then use `apm compile --dry-run` before
   generating instruction output. Compilation handles instructions only; it
   does not deploy skills or agents.
