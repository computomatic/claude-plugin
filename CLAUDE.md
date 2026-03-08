# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code plugin marketplace (`computomatic/claude-plugin`) containing reusable plugins with agents, skills, and workflows. Users install plugins via `/plugin marketplace add computomatic/claude-plugin` and then `/plugin install <plugin-name>@computomatic`.

## Repository Structure

```
.claude-plugin/marketplace.json   # Marketplace manifest listing all plugins
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json    # Plugin metadata (name, description)
    agents/<agent-name>.md        # Agent definitions (markdown with YAML frontmatter)
    skills/<skill-name>/SKILL.md  # Skill definitions (markdown with YAML frontmatter)
```

There are currently three plugins: `dev` (development workflow tools), `meta` (plugin authoring tools), and `project-blog` (project knowledge base).

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

1. Create `plugins/<name>/.claude-plugin/plugin.json` with `name` and `description`
2. Add the plugin entry to `.claude-plugin/marketplace.json`
3. Add agents and/or skills following the conventions above

## Style Rules

- Never use `any` types
- Never use em-dashes
