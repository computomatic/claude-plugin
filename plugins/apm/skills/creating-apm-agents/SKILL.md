---
name: creating-apm-agents
description: Create project-level APM agents (subagents). Use only when the target repository contains apm.yml.
---

# Create APM Agents

Use this skill only when the target repository contains a canonical `apm.yml`.
If it does not, APM conventions do not apply; use that repository's normal
agent mechanism.

1. Choose one canonical agent name. The name must use lowercase kebab-case.
2. Name the file `.apm/agents/<name>.agent.md`. If an existing filename does
   not provide a good agent name, rename the file before you update it.
3. Include a required `name` and `description`. Set `name` to the exact
   `<name>` derived from the filename. The filename and frontmatter `name`
   must use the same canonical agent name. Do not add other frontmatter fields.
4. Define one responsibility, expected inputs and outputs, boundaries, and the
   minimum tools needed. Do not grant destructive or broad repository access
   without a clear need.
5. Run `apm compile --validate` to check primitive structure, then preview
   deployment with `apm install --dry-run` for the intended target.
6. Do not use `apm compile` to deploy agents: it compiles instructions only.
   `apm install` deploys agents and other package primitives into the target
   project.
