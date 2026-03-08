---
name: create-master-plan
description: Creates a master plan and executes the microplan loop. Use when the user wants to plan and execute a multi-step roadmap.
argument-hint: "[optional: description of the goal or roadmap]"
disable-model-invocation: true
---

# Create Master Plan

Set up a master plan for a multi-step roadmap, then execute it one step at a time using the microplan pattern.

## Workflow

### Step 1: Scope the Work

Collaborate with the user to understand:
- The end-state goal
- Key constraints or requirements
- The rough shape of the work (what major steps are involved)

If the user provided an argument, use it as the starting point. Ask clarifying questions until the scope is clear. Don't proceed until the user confirms.

### Step 2: Create the Master Plan

Use EnterPlanMode to create the master plan. Follow this structure:

```
# Master Plan: [Project/Feature Name]

## Goal

[2-3 sentences describing the end state this roadmap achieves.]

## Context

[Background information, constraints, and key decisions that apply across all steps.]

## Roadmap

### Step 1: [Title] - `pending`
[Brief description of what this step accomplishes.]

### Step 2: [Title] - `pending`
[Brief description of what this step accomplishes.]

### Step 3: [Title] - `pending`
[Brief description of what this step accomplishes.]

## Execution Process

For each pending step, repeat this cycle:

1. **Review the master plan.** Identify the next `pending` step. Mark it `in-progress`.
2. **Delegate to the microplanner agent.** Pass full context: the step's title and description, relevant decisions from prior steps, and an explicit domain role assignment. The agent runs in an isolated context and will not read this plan.
3. **Review the microplan and ask questions.** Present each Outstanding Question to the user one at a time.
4. **Incorporate answers.** Delegate to a subagent to update the microplan with the user's answers.
5. **Delegate implementation.** Send the finalized microplan to an appropriate implementation agent.
6. **Clean up and update.** Delete the microplan file. Mark the step `done` and note outcomes.
7. **Commit and push.**
8. **Loop.** Return to sub-step 1. If all steps are `done`, the roadmap is complete.

### Guidelines

- If a session ends mid-work, read this plan to resume. The `in-progress` step is where to pick up.
- Each step should produce a complete, working state.
- If new requirements surface, revise this plan before continuing.
```

The master plan must be detailed enough that a fresh session with no prior context can pick up where work left off. Each step has a status: `pending`, `in-progress`, or `done`.

**Critical:** The master plan must include a full copy of the execution loop process (Step 3 below) within the plan document itself. When a new session starts after context loss, the agent will read the plan file but will not have this skill loaded. The embedded loop instructions are what enable the agent to continue executing the roadmap autonomously.

### Step 3: Execute the Loop

Repeat this cycle for each step in the roadmap:

1. **Review the master plan.** Read the master plan. Identify the next `pending` step. Mark it `in-progress`.

2. **Delegate to the microplanner agent.** Pass the agent the full context it needs since it runs in an isolated context window and will not read the master plan itself. Include: the step's title and description from the master plan, any relevant decisions or constraints from prior steps, and an explicit domain role assignment (e.g., "You are planning a database migration step" or "You are planning a frontend accessibility audit"). The domain role helps the agent adopt the right perspective and terminology.

3. **Review the microplan and ask questions.** Read the microplan the agent created. Present the user with each Outstanding Question one at a time. Wait for an answer before moving to the next question.

4. **Incorporate answers.** Once all questions are answered, delegate back to a subagent to update the microplan with the user's answers. The subagent should revise the Implementation Plan to reflect the decisions and clear the answered questions from Outstanding Questions.

5. **Delegate implementation.** Send the finalized microplan to an appropriate implementation agent (e.g., a lead-developer agent). Pass the full microplan content so the agent has everything it needs.

6. **Clean up and update.** Delete the microplan file. Update the master plan: mark the completed step as `done` and note any outcomes or decisions that affect future steps.

7. **Commit and push.** Commit all changes from this step with a clear message referencing the step. Push to the remote.

8. **Loop.** Return to sub-step 1 for the next pending step. If all steps are `done`, the roadmap is complete.

## Guidelines

- **Recovery from context loss.** If a session ends mid-work, start the next session by reading the master plan. The `in-progress` step tells you where to pick up. If a microplan file exists for that step, resume from there. If not, re-run the microplanner.

- **Keep steps self-contained.** Each step should produce a complete, working state. Avoid steps that leave things broken or that depend on unfinished work from a future step.

- **Master plan is the source of truth.** Do not rely on conversation history for roadmap state. Always read the master plan to determine what has been done and what comes next.

- **Adapt the loop.** If a step surfaces new requirements, pause and revise the master plan before continuing.
