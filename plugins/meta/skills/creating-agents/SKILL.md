---
name: creating-agents
description: Creates new Claude Code agents (subagents) with best practices. Use when the user wants to create an agent, add a custom subagent, or define a specialized AI assistant for their project.
argument-hint: "[agent-name] [optional: description]"
---

# Create Agent

Guide the user through creating a highly effective Claude Code agent.

**Important:** Use "ultrathink" extended thinking for agent design decisions.

## Workflow

### Phase 1: Discovery

Ask the user clarifying questions to understand the agent requirements:

1. **Purpose:** What specialized role should this agent fill? (e.g., code reviewer, test runner, documentation writer)
2. **Scope:** Should it be project-specific (`.claude/agents/`) or personal (`~/.claude/agents/`)?
3. **Delegation:** Should Claude delegate to it proactively, or only when the user explicitly requests it?
4. **Tools:** What tools does this agent need? (Read, Write, Edit, Bash, Glob, Grep, WebSearch, WebFetch, etc.)
5. **Model:** What model should it use? (`opus` for complex reasoning, `sonnet` for balanced tasks, `haiku` for fast/simple tasks, or `inherit` for the parent model)

If the available context makes any of this information obvious, there's no need to ask redundantly. However, clarify any ambiguity rather than making assumptions.
If the user provided arguments, use them to inform the agent name and purpose.

### Phase 2: Design

Determine the agent's role, delegation behavior, and tooling:

**Agent vs. Skill:**
- **Agents** run as isolated subprocesses with their own context window, tool access, and model. They receive a task, work autonomously, and return a summary. Use agents for complex, multi-step work that benefits from isolation.
- **Skills** inject instructions into the current conversation's context. Use skills for workflows, conventions, or templates that should run inline.

Choose an agent when:
- The task produces verbose intermediate output that would clutter the main conversation
- The work is self-contained and can be summarized on completion
- You need to enforce specific tool restrictions or a different model
- The task benefits from a focused system prompt without main conversation noise
- You want parallel execution of independent work streams

**Naming:**
- Lowercase with hyphens
- Be specific and role-oriented: `code-reviewer` not `reviewer`
- Always include a `name` field in frontmatter

**Frontmatter Options:**
```yaml
---
name: agent-name
description: "Detailed description of when to delegate to this agent. Include examples."
model: sonnet
color: blue
tools: Read, Glob, Grep, Bash
disallowedTools: Write, Edit
permissionMode: default
---
```

**Description Guidelines:**
- Write as an instruction to the parent model explaining WHEN to delegate
- Start with "Use this agent when..." for clarity
- Include concrete examples showing user messages and expected delegation behavior
- Use the `<example>`, `<commentary>` format for delegation examples:

```yaml
description: "Use this agent when the user asks for code review or quality checks.\n\n<example>\nuser: 'Review the changes in the auth module'\nassistant: 'I'll use the code-reviewer agent to review the auth module changes'\n<commentary>Code review is a focused task well-suited for an isolated agent.</commentary>\n</example>"
```

### Phase 3: Configuration

Select the right configuration for the agent:

**Model Selection:**
| Model | Best For |
|-------|----------|
| `opus` | Complex reasoning, architecture decisions, nuanced code review |
| `sonnet` | Balanced tasks, implementation work, testing |
| `haiku` | Fast lookups, simple searches, quick categorization |
| `inherit` | Use whatever the parent conversation uses |

**Permission Modes:**
| Mode | Behavior | Use When |
|------|----------|----------|
| `default` | Prompts user for permissions | Agent makes file changes or runs commands |
| `acceptEdits` | Auto-accepts file edits | Trusted agent that modifies code |
| `dontAsk` | Auto-denies permission prompts | Read-only research agent |
| `bypassPermissions` | Skips all checks | Fully trusted automation (use sparingly) |
| `plan` | Read-only exploration | Agent only gathers information |

**Tool Selection:**
Grant the minimum set of tools needed. Common combinations:

- **Read-only research:** `Read, Glob, Grep`
- **Code analysis:** `Read, Glob, Grep, Bash`
- **Code modification:** `Read, Write, Edit, Glob, Grep, Bash`
- **Web research:** `Read, Glob, Grep, WebSearch, WebFetch`
- **Full access:** omit `tools` field (inherits all parent tools)

**Color Options:**
Available colors for visual distinction in the UI: `red`, `green`, `blue`, `yellow`, `cyan`, `magenta`, `white`.

### Phase 4: System Prompt Structure

Design the agent's system prompt (the markdown body after the frontmatter):

**Structure Template:**
```markdown
You are a {role} with expertise in {domain}. Your role is to {primary responsibility}.

Your Process:

1. **Phase Name**:
   - Key action 1
   - Key action 2

2. **Phase Name**:
   - Key action 1
   - Key action 2

Guidelines:
- Principle 1
- Principle 2

Communication:
- How to report results
- What format to use
```

**System Prompt Principles:**

1. **Define the persona clearly.** The opening sentence sets the agent's identity and expertise. Be specific about what they excel at.

2. **Structure the process.** Break the agent's workflow into numbered phases. Agents work autonomously, so the process should be self-contained.

3. **Set boundaries.** Specify what the agent should and should not do. Agents cannot ask the user follow-up questions mid-task (unless granted `AskUserQuestion`), so anticipate edge cases.

4. **Define the output format.** Since the agent returns a summary to the parent conversation, specify how results should be structured (e.g., bullet points, severity levels, pass/fail).

5. **Keep it focused.** Each agent should excel at one thing. A 30-80 line system prompt is typical. Avoid general-purpose instructions Claude already knows.

**What to Include:**
- Domain-specific knowledge the agent needs
- Process steps in execution order
- Output format and reporting structure
- Interaction patterns with other agents (if applicable)
- Project-specific conventions that affect the agent's work

**What to Exclude:**
- General coding knowledge Claude already has
- Vague instructions ("be helpful", "write good code")
- Overly detailed instructions for simple tasks
- Information that changes frequently

### Phase 5: Implementation

Create the agent file:

1. Determine the full path:
   - Personal: `~/.claude/agents/{agent-name}.md`
   - Project: `./{project-root}/.claude/agents/{agent-name}.md`
   - Plugin: `./agents/{agent-name}.md` (within a plugin directory)

2. Create the directory if needed

3. Write the agent markdown file with:
   - YAML frontmatter (name, description, model, tools, etc.)
   - System prompt body following the structure template

### Phase 6: Verification

After creating the agent:

1. **Registration Check:** The agent should appear in Claude Code's agent list
2. **Delegation Test:** Describe a task matching the agent's description and verify Claude delegates to it
3. **Execution Test:** Run the agent on a real task and review the output quality
4. **Tool Access Test:** Confirm the agent can use its granted tools and cannot use restricted ones

If issues arise:
- Check frontmatter YAML syntax (quoting, indentation)
- Verify the file is in a recognized agents directory
- Ensure the description clearly indicates delegation triggers
- Check that listed tools are valid tool names

## Quick Reference

### Frontmatter Template
```yaml
---
name: {agent-name}
description: "Use this agent when {delegation trigger}.\n\n<example>\nuser: '{sample request}'\nassistant: '{expected delegation response}'\n<commentary>{why this agent is appropriate}</commentary>\n</example>"
model: {opus|sonnet|haiku|inherit}
color: {color}
tools: {Tool1, Tool2, Tool3}
permissionMode: {default|acceptEdits|dontAsk|bypassPermissions|plan}
---
```

### Common Agent Patterns

**Research/Analysis Agent (read-only):**
```yaml
model: haiku
tools: Read, Glob, Grep
permissionMode: plan
```

**Code Modification Agent (trusted):**
```yaml
model: opus
tools: Read, Write, Edit, Glob, Grep, Bash
permissionMode: acceptEdits
```

**Testing Agent (needs browser/commands):**
```yaml
model: sonnet
tools: Read, Glob, Grep, Bash
permissionMode: default
```

**Documentation Agent:**
```yaml
model: sonnet
tools: Read, Write, Glob, Grep
permissionMode: acceptEdits
```

### Checklist

Before finalizing an agent, verify:

- [ ] Frontmatter includes explicit `name` field
- [ ] Name uses lowercase with hyphens
- [ ] Description explains WHEN to delegate with examples
- [ ] Model is appropriate for task complexity
- [ ] Tools are minimal but sufficient
- [ ] Permission mode matches trust level
- [ ] System prompt defines a clear persona and process
- [ ] Output format is specified
- [ ] Prompt is focused (30-80 lines typical, under 150 max)
- [ ] No redundant information Claude already knows

## Examples

### Example: Focused Analysis Agent

```markdown
---
name: code-reviewer
description: "Use this agent when the user asks for code review, quality checks, or wants feedback on recent changes.\n\n<example>\nuser: 'Review my recent changes'\nassistant: 'I'll use the code-reviewer agent to analyze your changes'\n<commentary>Code review is a focused analytical task suited for an isolated agent.</commentary>\n</example>"
model: sonnet
color: yellow
tools: Read, Glob, Grep, Bash
---

You are a senior code reviewer focused on correctness, security, and maintainability.

Your Process:

1. **Gather Context**:
   - Run `git diff` to see recent changes
   - Read modified files in full for surrounding context
   - Check related tests and documentation

2. **Review**:
   - Correctness: Does the code do what it claims?
   - Security: Any vulnerabilities introduced?
   - Style: Does it follow project conventions?
   - Tests: Are changes adequately tested?

3. **Report**:
   Organize findings by severity:
   - **Critical:** Must fix before merge (bugs, security issues)
   - **Warning:** Should fix (code smells, missing tests)
   - **Suggestion:** Consider improving (style, naming, optimization)

   For each finding, include the file path, line reference, and a concrete fix suggestion.

Guidelines:
- Be constructive and specific
- Acknowledge what is done well
- Prioritize blocking issues over style nits
- If no issues found, confirm the code looks good
```

### Example: Task Execution Agent

```markdown
---
name: test-runner
description: "Use this agent when the user wants to run tests, check test coverage, or verify that changes pass the test suite.\n\n<example>\nuser: 'Run the tests for the auth module'\nassistant: 'I'll use the test-runner agent to run and report on the auth module tests'\n<commentary>Running tests produces verbose output best isolated in a subagent.</commentary>\n</example>"
model: haiku
color: green
tools: Read, Glob, Bash
---

You are a test execution specialist. Run tests and provide clear, actionable results.

Your Process:

1. **Identify Tests:**
   - Locate the project's test runner configuration
   - Find tests matching the requested scope
   - Check for test dependencies or setup scripts

2. **Execute:**
   - Run the appropriate test command
   - Capture all output including failures and warnings

3. **Report:**
   - Total tests: passed / failed / skipped
   - For each failure: test name, assertion message, file:line reference
   - If all tests pass, confirm with a brief summary

Keep the report concise. Developers need to know what failed and where, not see the full test log.
```
