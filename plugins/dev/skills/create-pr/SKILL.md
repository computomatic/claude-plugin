---
name: create-pr
description: Creates a GitHub PR for current work. Handles branch creation, committing, pushing, and PR creation.
argument-hint: "[optional: PR description]"
allowed-tools: Bash(git:*), Bash(gh pr create:*), Read, Glob
---

# Create Pull Request

Creates a complete PR from current work: branch, commit, push, and open the PR.

## Git State

Status:
!`git status`

Changes:
!`git diff`

Current branch:
!`git branch --show-current`

Recent commits:
!`if git rev-parse --verify HEAD >/dev/null 2>&1; then git log --oneline -5; else echo "(no commits yet)"; fi`

## Workflow

### Step 1: Check Branch State

Determine the current branch:
- If on `main` or `master`, create a new feature branch
- If already on a feature branch, continue on that branch

**Branch naming:** Generate from conversation context using format `{category}/{short-description}`:
- `feat/add-user-auth`
- `fix/login-validation`
- `refactor/extract-utils`

### Step 2: Identify Changes to Include

Review the conversation context and git diff:
- If an argument was provided, use it to determine which changes are in scope
- If no argument, infer scope from the conversation context
- Only stage files related to the intended PR scope
- Unrelated changes should NOT be staged

If uncertain about which files belong, ask the user.

### Step 3: Stage and Commit

1. Stage only the relevant files with `git add`
2. Write a clear, concise commit message
3. Commit the changes

### Step 4: Push to Remote

Push the branch to origin:
```
git push -u origin <branch-name>
```

### Step 5: Check for PR Template

Search for a PR template in the repository. Check these locations in priority order (filenames are case-insensitive):

1. `.github/pull_request_template.md`
2. `.github/PULL_REQUEST_TEMPLATE/` (directory with multiple templates)
3. `docs/pull_request_template.md`
4. `pull_request_template.md` (repository root)

Use `Glob` to find matching files and `Read` to read the template content.

If `.github/PULL_REQUEST_TEMPLATE/` contains multiple templates, pick the one most relevant to the changes (e.g., a bug fix template for fixes, a feature template for new features). If unsure which template fits, ask the user.

### Step 6: Create the PR

Create the PR using `gh pr create`:
- Title: Clear, imperative summary
- If argument was provided, use it to inform the description

**If a PR template was found:** Use the template's structure for the body, filling in each section based on the conversation context and the changes being submitted.

**If no PR template was found:** Write a plain text body of 1-2 paragraphs describing what and why. No headers, no "Test Plan" section, no markdown formatting in the body.

**Line breaks:** GitHub renders every newline inside a paragraph as a visible line break, unlike most markdown renderers. Never wrap prose to a column width. Write each paragraph, list item, table row, and heading as one continuous line however long it runs, and separate blocks with a blank line. This applies to the template case and the plain prose case alike.

Wrong, because the newline before `5xx` renders as a break mid-sentence:

```
Closes #412. Adds a retry path to `HttpClient` so a transient
5xx no longer reaches callers as a hard failure.
```

Right:

```
Closes #412. Adds a retry path to `HttpClient` so a transient 5xx no longer reaches callers as a hard failure.
```

```
gh pr create --title "..." --body "..."
```

## Writing the Description

A description is written for posterity, not for the reviewer. It is a permanent record of why the code is as it is, read years later by a contributor who has the code and this description and little else. Write it as settled fact, on the assumption the change is correct and agreed. Never ask the reader to check, confirm, or weigh anything, and never flag something for a reviewer's attention.

A description has to stand on its own. Someone scanning a PR list, or reading the squash-merge commit later, should understand what the change does and why without opening the diff. So state the approach, and leave the mechanics that implement it to the diff: "how we fixed the problem", not "how we implemented the fix".

**The shape is fixed:** what the change does, why it was needed, and the substantial decisions behind it. A template's sections map onto that shape; with no template it is paragraphs in that order. Nothing else has a place in a description.

**Pitch the approach at the highest level that still conveys the change.** Name specific code only when the named thing *is* the change, as with a new method callers will invoke, a renamed setting, or an altered endpoint or payload. When the change is internal, say what now behaves differently and leave the symbols out. Which function moved, which helper it borrowed, and which call sites were rethreaded are the diff's business.

**Put reasoning with the decision, not with the approach.** Where the template has a section for decisions, rationale, or tradeoffs, that is where "why this and not the obvious alternative" belongs, and the approach section stays a plain statement of what was done. With no such section, or with no template at all, keep the approach and the reasoning in separate paragraphs, approach first, and let a change with real decisions to record run past the usual 1-2 paragraph norm rather than drop them.

**Length tracks decisions, not diff size.** Not the number of files or rules touched, not how long the change took, not how much thought went into it. One paragraph per substantial decision. A change that touches twenty files and settles one architectural question gets one paragraph of reasoning.

**Only substantial decisions earn a record.** Architecture, a data model, a protocol or interface contract, a dependency taken on, a constraint accepted for the long term: the calls a future contributor could reverse without realising what it costs. Local choices that any competent contributor would make the same way need no record, however long they took to settle.

**Delete outright:**

- Implementation mechanics: which internal helper was reused, which call site was threaded through, which existing structure was borrowed.
- Which files changed, and which tests were added.
- Inventories of the change: a list of the sections, rules, options, or components it contains.
- What did not change. Never write that a file, a config, or a consumer "needed no change".
- Your own process: how you verified the work, what you tried first, what you double-checked, how you arrived at the change, and anything you measured while deciding.
- Conclusions the reader reaches unaided. State the fact and stop; do not append the inference that follows from it.
- Pre-existing conditions this change neither introduces nor touches, and advice aimed at future unrelated work.
- Bookkeeping: version bumps, changelog entries, status flips, file moves.

**Never compress** what the code alone will not tell a future contributor. Reasoning survives where someone reading the code years from now would otherwise undo the decision, or repeat the problem it was made to avoid. Reasoning about how the change was drafted, its structure, its wording, its examples, the scope it settled on, does not survive; that belongs to the moment, not to the record.

- Why the change was needed, and what it unblocked.
- Why a substantial decision was made, and why the alternatives were rejected.
- Anywhere the implementation departs from its spec, ticket, or design doc, and the reasoning that justifies the departure.

Conciseness comes from deleting whole passages, not from shortening the reasoning. A passage recording a hard judgment call earns its length even if it ends up the longest thing in the description. A passage narrating what the diff already shows earns none.

### Example: deletion and placement

For a template with a decisions section, this:

```
## Solution Strategy

Adds `retry()` to `HttpClient`, reusing the existing `backoff()` helper in `src/net/backoff.ts` and threading its delay through the same `AbortSignal` that `request()` already builds, so no new cancellation plumbing was needed. The mock client, the barrel export, and the generated types all needed no change.

Requests retry up to three times on 5xx and on connection errors, with exponential backoff. The existing per-request timeout bounds the whole sequence rather than each attempt.

429 is deliberately not retried, because a rate limit needs the delay the server dictates in `Retry-After` and honouring that inside a generic backoff loop would stall the caller for an unbounded period with no way to observe it.

Unit tests cover a first-attempt success, a single retry, and exhaustion after three attempts. I confirmed the assertions are not vacuous by temporarily removing the retry loop: the retry and exhaustion tests both failed, and passed again once reverted.

The changelog entry and the package version are updated.
```

becomes:

```
## Solution Strategy

Adds `retry()` to `HttpClient`. Requests retry up to three times on 5xx and on connection errors, with exponential backoff, and the existing per-request timeout bounds the whole sequence rather than each attempt.

## Architectural Decisions

429 is deliberately not retried. A rate limit needs the delay the server dictates in `Retry-After`, and honouring that inside a generic backoff loop would stall the caller for an unbounded period with no way to observe it. Callers that care about rate limits handle 429 themselves, which is what both existing callers already do.
```

Three things happened. The approach collapsed to one paragraph, keeping the retry policy and the timeout interaction and dropping the borrowed helper, the signal plumbing, and the list of things that needed no change. The test inventory, the verification narrative, and the bookkeeping are gone. The rate-limit rationale moved out of the approach and into the decisions section, where it grew rather than shrank, because it is the only part of the description the diff cannot show.

`retry()` is named because the new method is the change: callers will invoke it by that name.

### Example: altitude

When nothing about the change is caller-facing, drop to behaviour instead. This:

```
Hoists the `seen` set out of `processBatch()` into the `BatchRunner` constructor, changes `dedupe()` to accept it as a parameter rather than allocating its own, and updates the three call sites in `worker.ts` to thread it through.
```

becomes:

```
Deduplication state now lives for the lifetime of the runner rather than being rebuilt for each batch, so a record that appears in two consecutive batches is dropped instead of processed twice.
```

The second version is barely shorter. The difference is altitude: the first describes the edit, the second describes the change in behaviour. No symbol is named, because no caller can see any of them, and a reader who needs that detail is already in the diff.

## Guidelines

- When a PR template is found, respect its structure and fill in all sections
- When no template is found, keep descriptions as plain prose (1-2 paragraphs) with no headers or sections, extending only to record a substantial decision
- Never break a line in the middle of a paragraph or list item; blank lines between blocks are the only line breaks GitHub renders as intended
- Write for posterity, not the reviewer; state the change as settled and never ask the reader to check or confirm anything
- Follow the fixed shape: what and why, then the substantial decisions behind it; nothing else belongs
- State the approach so the description stands alone in a PR list or a squash-merge message, at the highest level that still conveys the change; name code only when the named thing is the change itself
- Put "why not" reasoning in the template's decisions section; keep the approach section a plain statement of what was done
- Cut whole passages rather than compressing reasoning; the paragraph explaining a hard decision is allowed to be the longest one
- Let length track the number of substantial decisions, never the size of the diff or the effort behind it; local choices any competent contributor would repeat need no record
- Never inventory the change; if a sentence lists what the diff contains, cut it at the colon
- If multiple unrelated changes exist, only include those relevant to the conversation or argument
- Always push before creating the PR
- Never add a signature line like "Generated with Claude Code" or similar to the PR description
