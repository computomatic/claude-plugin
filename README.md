# com - Development Workflow Plugin for Claude Code

A Claude Code plugin packaging personal development workflow tools: agents for development and testing, plus skills for git workflows and skill authoring.

## Installation

```bash
# Clone the repository
git clone <repo-url> claude-plugin

# Test locally
cd claude-plugin
claude --plugin-dir .
```

## Contents

### Agents

**lead-developer** (Opus)
An elite lead developer agent for implementing features, bug fixes, and refactoring. Follows a systematic process: discovery, planning, implementation, and quality assurance. Automatically invoked for development tasks.

**browser-test-runner** (Sonnet)
A manual QA tester agent for verifying application functionality in the browser. Uses chrome-devtools MCP to systematically test user flows, identify issues, and report findings. Automatically invoked for browser testing tasks.

### Skills

**creating-pr** (`/com:creating-pr`)
Creates a complete GitHub PR from current work: handles branch creation, selective staging, committing, pushing, and PR creation with `gh`.

**commit-and-push** (`/com:commit-and-push`)
Commits and pushes changes with intelligent staging. Reviews conversation context to determine which files are related to current work and stages only those.

**create-skill** (`/com:create-skill`)
Guides creation of new Claude Code skills with best practices. Covers discovery, design, structure, content principles, implementation, and verification.

## Usage

After installation, skills are available with the `com:` prefix:

```
/com:creating-pr
/com:creating-pr Add OAuth2 support
/com:commit-and-push
/com:create-skill my-skill-name
```

Agents are automatically invoked based on task context:
- Development tasks trigger `lead-developer`
- Browser testing tasks trigger `browser-test-runner`

## License

MIT
