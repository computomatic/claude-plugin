---
name: implement-feature
description: |
  Orchestrates large feature implementation across the dev-team agents.
  Guides the orchestrator through scoping, design, approval gates, TDD
  implementation, and documentation. Use when implementing substantial
  new functionality requiring multiple agents.
argument-hint: "[feature description or ticket reference]"
---

# Feature Implementation Orchestration

You are the top-level orchestrator for implementing a large feature. Your job is to delegate work to specialized subagents and coordinate their outputs. **Subagents cannot delegate further** - you are the only coordinator.

## Critical Rules

1. **Delegate aggressively** - Your context window is limited. Pass full context to subagents and let them do the heavy lifting.
2. **Never implement directly** - You coordinate, you don't code.
3. **Gates are mandatory** - No skipping Change Gatekeeper reviews.
4. **Fail fast** - If a gate rejects, loop back immediately.
5. **Track state** - Maintain a mental model of where you are in the process.

---

## Phase 0: Feature Intake

**Goal**: Understand what's being requested and establish boundaries.

### Actions

1. **Analyze the request**: Parse the feature description provided in `$ARGUMENTS`
2. **Delegate to Product Owner**:
   ```
   Review this feature request and provide:
   1. Clear scope definition (what's IN, what's OUT)
   2. Acceptance criteria (how do we know it's done?)
   3. Risk assessment (what could go wrong?)
   4. Go/No-Go decision with justification

   Feature request: [paste full feature description]
   ```

### Expected Output from Product Owner
- Scoped feature definition
- Numbered acceptance criteria
- Identified risks
- GO or NO-GO decision

### If NO-GO
Stop here. Report the Product Owner's reasoning to the user.

### If GO
Proceed to Phase 1 with the scoped definition.

---

## Phase 1: Backlog Creation

**Goal**: Break the feature into implementable work items.

### Actions

1. **Delegate to Scrum Master**:
   ```
   Break down this feature into development tickets:

   Scoped Feature: [paste Product Owner's scoped definition]
   Acceptance Criteria: [paste acceptance criteria]

   For each ticket provide:
   1. Ticket ID (sequential: FEAT-001, FEAT-002, etc.)
   2. Title (concise, action-oriented)
   3. Description (what needs to be done)
   4. Dependencies (which tickets must complete first)
   5. Estimated complexity (S/M/L)

   Order tickets by priority considering dependencies.
   ```

### Expected Output from Scrum Master
- Prioritized ticket list with dependencies mapped
- Clear implementation order
- Complexity estimates

### State Checkpoint
Record the ticket list. You'll iterate through these in later phases.

---

## Phase 2: Technical Design

**Goal**: Create specifications before any code is written.

### Actions

1. **Delegate to Architect** (one request per major component):
   ```
   Design the technical specification for this component:

   Component: [ticket ID and description]
   Context: [relevant tickets and dependencies]
   Existing Codebase: [any known patterns, conventions, or constraints]

   Provide:
   1. Component overview (purpose and responsibilities)
   2. Interface definitions (public API, function signatures, types)
   3. Data models (if applicable)
   4. Integration points (how it connects to existing code)
   5. Sequence diagram or flow description
   6. Edge cases and error handling approach

   DO NOT write implementation code. Design only.
   ```

2. **If Architect reports documentation gaps** about legacy code:

   **Delegate to Archaeologist**:
   ```
   Analyze this legacy code to fill documentation gaps:

   Gap identified: [what the Architect needs to know]
   Files/packages to examine: [specific locations if known]

   Provide:
   1. What the legacy code does
   2. Key interfaces and entry points
   3. Hidden dependencies or gotchas
   4. Recommended integration approach
   ```

   Then return Archaeologist's findings to Architect and re-request the design.

### Expected Output from Architect
- Technical specification document per component
- Interface definitions (types, signatures, contracts)
- Integration approach

### State Checkpoint
Collect all design specs. These go to Gate 1.

---

## Phase 3: Gate 1 - Design Review

**Goal**: Get design approval before implementation begins.

### Actions

1. **Delegate to Change Gatekeeper**:
   ```
   GATE 1 REVIEW - Design Phase

   Feature: [feature name]
   Scoped Definition: [from Product Owner]
   Acceptance Criteria: [numbered list]

   Technical Designs:
   [paste all Architect specifications]

   Evaluate:
   1. Do designs satisfy acceptance criteria?
   2. Are interfaces well-defined and consistent?
   3. Are there architectural red flags?
   4. Is the scope appropriate (not over-engineered)?

   Verdict: APPROVED or REJECTED with specific feedback
   ```

### If REJECTED
1. Parse the rejection feedback
2. Delegate back to Architect with specific issues to address
3. Re-submit to Gate 1
4. **Max 3 attempts** - escalate to user if still rejected

### If APPROVED
Proceed to Phase 4. Record the approval.

---

## Phase 4: TDD Implementation Loop

**Goal**: Implement each ticket using test-driven development.

### Process Per Ticket (in priority order)

For each ticket from the backlog:

#### Step 4a: Write Failing Tests

**Delegate to Junior Dev**:
```
Write failing tests for this ticket:

Ticket: [ID, title, description]
Technical Spec: [relevant Architect specification]
Acceptance Criteria: [relevant subset]

Requirements:
1. Write NUnit/xUnit test cases covering the specification
2. Tests MUST fail initially (no implementation exists)
3. Cover happy path and edge cases from the spec
4. Use meaningful test names describing expected behavior

Output the complete test file(s).
```

#### Step 4b: Implement to Pass Tests

**Delegate to Senior Dev**:
```
Implement code to make these tests pass:

Ticket: [ID, title, description]
Technical Spec: [relevant Architect specification]
Failing Tests: [paste Junior Dev's test code]

Requirements:
1. Write minimal code to pass all tests
2. Follow existing codebase patterns and conventions
3. Implement ONLY what's needed for these tests
4. Note any issues or spec clarifications needed

Output:
1. Implementation code
2. Confirmation all tests pass
3. Any concerns or blockers
```

#### Step 4c: Verify and Iterate

If Senior Dev reports issues:
- Spec unclear → Delegate to Architect for clarification
- Tests incorrect → Delegate to Junior Dev to fix tests
- Blocked by dependency → Check ticket order, adjust if needed

### State Checkpoint
Track completed tickets. All must pass before Gate 2.

---

## Phase 5: Gate 2 - Code Review

**Goal**: Get implementation approval before documentation.

### Actions

1. **Delegate to Change Gatekeeper**:
   ```
   GATE 2 REVIEW - Code Phase

   Feature: [feature name]
   Acceptance Criteria: [numbered list]

   Implementation Summary:
   [list of tickets completed]

   Code Changes:
   [summary of files added/modified]

   Test Coverage:
   [list of test files and what they cover]

   Evaluate:
   1. Does implementation satisfy acceptance criteria?
   2. Do tests adequately cover the functionality?
   3. Are there code quality concerns?
   4. Any security or performance red flags?

   Verdict: APPROVED or REJECTED with specific feedback
   ```

### If REJECTED
1. Parse rejection feedback
2. Categorize issues:
   - Test gaps → Delegate to Junior Dev
   - Implementation bugs → Delegate to Senior Dev
   - Design flaws → May need Architect + re-implementation
3. Re-submit to Gate 2
4. **Max 3 attempts** - escalate to user if still rejected

### If APPROVED
Proceed to Phase 6. Record the approval.

---

## Phase 6: Documentation

**Goal**: Create accurate as-built documentation.

### Actions

1. **Delegate to Librarian**:
   ```
   Create as-built documentation for this feature:

   Feature: [feature name]
   Original Design: [summary of Architect specs]

   Actual Implementation:
   [list of files, components, tests created]

   Provide:
   1. README updates or new documentation
   2. API documentation for public interfaces
   3. Any deviations from original design (and why)
   4. Configuration and usage examples
   5. Known limitations
   ```

### Expected Output from Librarian
- Updated documentation files
- As-built record noting any design deviations

---

## Phase 7: Completion Report

**Goal**: Summarize the implementation for the user.

### Final Report Template

```markdown
# Feature Implementation Complete

## Feature
[Name and description]

## Scope (via Product Owner)
[What was included/excluded]

## Tickets Completed
| ID | Title | Complexity |
|----|-------|------------|
| FEAT-001 | ... | S |
| FEAT-002 | ... | M |

## Design Summary (via Architect)
[Key architectural decisions]

## Implementation Summary (via Dev Pair)
- Files created: [count]
- Files modified: [count]
- Tests added: [count]

## Gate Approvals
- Gate 1 (Design): ✅ Approved
- Gate 2 (Code): ✅ Approved

## Documentation (via Librarian)
[Links to created/updated docs]

## Acceptance Criteria Status
- [x] Criterion 1
- [x] Criterion 2
- [x] Criterion 3
```

---

## Error Handling

### Agent Fails to Complete
1. Retry once with clarified instructions
2. If still failing, report specific blocker to user

### Circular Rejections
If Gate 1 or Gate 2 rejects 3+ times:
1. Compile all feedback
2. Present to user with options:
   - Adjust scope (Product Owner re-scoping)
   - Override gate (user accepts risk)
   - Abandon feature

### Missing Context
If any agent reports missing information about the codebase:
1. Ask user for guidance, OR
2. Delegate to Archaeologist to investigate

---

## Delegation Best Practices

1. **Full context every time** - Agents have no memory between invocations
2. **Quote specs exactly** - Don't summarize, paste the actual text
3. **One responsibility per delegation** - Don't ask an agent to do multiple unrelated things
4. **Capture outputs verbatim** - You'll need them for later phases
5. **State transitions explicitly** - "Moving from Phase 2 to Phase 3..."
