# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code plugin marketplace (`computomatic/claude-plugin`) containing reusable plugins with agents, skills, and workflows. Users install plugins via `/plugin marketplace add computomatic/claude-plugin` and then `/plugin install <plugin-name>@computomatic`.

## Repository Structure

```
.claude-plugin/marketplace.json   # Marketplace manifest listing all plugins
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json    # Plugin metadata (name, description, version)
    agents/<agent-name>.md        # Agent definitions (markdown with YAML frontmatter)
    skills/<skill-name>/SKILL.md  # Skill definitions (markdown with YAML frontmatter)
```

There are currently four plugins: `dev` (development workflow tools), `meta` (plugin authoring tools), `project-blog` (project knowledge base), and `compile-docs` (documentation compiler).

## Versioning

Every PR must include an appropriate semver bump in the `plugin.json` of each plugin it modifies, reassessed when additional commits are pushed (e.g., escalating from patch to minor if scope grows). The version lives only in `plugin.json`; marketplace.json entries do not carry versions.

These plugins ship agent prompts, skill definitions, and templates. There is no runtime API or code contract, so semver applies to the *behavior and interface experienced by users and orchestrating agents*.

**Patch** (0.0.x): Changes that refine existing behavior without altering what the agents or skills do from a user's perspective. Examples: rewording instructions for clarity, fixing typos, tightening or loosening existing guidance, adding internal guardrails, adjusting prompt structure that does not change outputs in a user-visible way.

**Minor** (0.x.0): Changes that add new capabilities or meaningfully change what users or orchestrating agents can do. Examples: adding a new skill or agent, introducing a new workflow step in an existing skill, changing file placement conventions, renaming or reorganizing the directory structure in a way that requires users to update references.

**Major** (x.0.0): Changes that break existing workflows or require users to change how they invoke or interact with the plugin. Examples: removing a skill or agent, renaming a skill (breaking `/skill-name` invocations), removing or renaming conventions that external tooling or user workflows depend on.

## Authoring Conventions

### Skills (SKILL.md)

Skills inject instructions into the current conversation context. Each skill lives in `skills/<skill-name>/SKILL.md`.

Required frontmatter fields:
- `name`: lowercase with hyphens (must be explicit -- without it, the name is derived from the directory)
- `description`: third-person, includes WHAT it does and WHEN to use it

Optional frontmatter:
- `argument-hint`: shows expected arguments (e.g., `"[optional: description]"`)
- `disable-model-invocation: true`: only invoked when user explicitly calls it
- `allowed-tools`: restricts which tools the skill can use (e.g., `Bash(git:*), Read, Glob`)

Skill body uses markdown. Dynamic context can be injected with `!` backtick syntax (e.g., `` !`git status` ``).

### Agents (.md files in agents/)

Agents run as isolated subprocesses with their own context window. Each agent is a single markdown file in `agents/`.

Required frontmatter fields:
- `name`: lowercase with hyphens
- `description`: written as delegation instructions for the parent model, starts with "Use this agent when...", includes `<example>` blocks

Optional frontmatter:
- `model`: `opus`, `sonnet`, `haiku`, or `inherit`
- `color`: `red`, `green`, `blue`, `yellow`, `cyan`, `magenta`, `white`
- `tools`: comma-separated list of allowed tools
- `disallowedTools`: tools to exclude
- `permissionMode`: `default`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan`

The markdown body after frontmatter is the agent's system prompt.

### Adding a New Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with `name`, `description`, and `version` (start at `1.0.0`)
2. Add the plugin entry to `.claude-plugin/marketplace.json`
3. Add agents and/or skills following the conventions above

## Style Rules

- Never use `any` types
- Never use em-dashes
