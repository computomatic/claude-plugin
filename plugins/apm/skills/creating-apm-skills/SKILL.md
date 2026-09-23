---
name: creating-apm-skills
description: Create project-level APM skills. Use only when the target repository contains apm.yml.
---

# Create APM Skills

Use this skill only when the target repository contains a canonical `apm.yml`.
If it does not, APM conventions do not apply; use that repository's normal
skill location and authoring mechanism.

1. Reuse the repository's existing skill-authoring guidance where it exists.
2. Create the skill at `.apm/skills/<name>/SKILL.md`. Use a lowercase
   kebab-case `<name>` that matches the containing directory.
3. Include valid Agent Skills frontmatter with a specific `description`. When
   adding `name`, make it match the containing directory, then make the body
   focused, direct, and actionable.
4. Reuse the relevant existing skill-writing guidance before adding resources.
   Keep optional scripts, references, assets, and examples within the skill
   directory.
5. Run `apm audit --file .apm/skills/<name>/SKILL.md`, then preview installation
   with `apm install --dry-run` for the intended target before release.
