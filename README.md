# computomatic - Development Workflow Marketplace

A Claude Code plugin marketplace with personal development workflow tools.

## Installation

```bash
# Add the marketplace
/plugin marketplace add <your-github-username>/claude-plugin

# Install plugins
/plugin install dev@computomatic
/plugin install meta@computomatic
```

## Plugins

### dev

Development workflow tools: lead developer agent, browser testing, PR creation, and commit management.

**Agents:**
- **lead-developer** (Opus) - Elite lead developer for implementing features, bug fixes, and refactoring
- **browser-test-runner** (Sonnet) - Manual QA tester for verifying application functionality in the browser

**Skills:**
- `/dev:creating-pr` - Creates a complete GitHub PR from current work
- `/dev:commit-and-push` - Commits and pushes changes with intelligent staging

### meta

Plugin authoring tools for creating Claude Code skills.

**Skills:**
- `/meta:create-skill` - Guides creation of new Claude Code skills with best practices

## Usage

After installation, skills are available with plugin prefixes:

```
/dev:creating-pr
/dev:creating-pr Add OAuth2 support
/dev:commit-and-push
/meta:create-skill my-skill-name
```

Agents are automatically invoked based on task context:
- Development tasks trigger `lead-developer`
- Browser testing tasks trigger `browser-test-runner`

## License

MIT
