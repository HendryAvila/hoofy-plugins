---
description: >
  Context-aware code review powered by persistent memory, specs, and architectural decisions.
  Reviews code against your actual specifications, established patterns, and past decisions —
  not in a vacuum. Use when reviewing code, PRs, diffs, or asking "review this".
  The only AI code reviewer that knows WHY your code should look a certain way.
---

# Hoofy Context-Aware Code Review

You are performing a **context-aware code review** — the kind that no other tool can do. Unlike generic AI reviewers that only see the diff, you have access to the project's full specification history, architectural decisions, established patterns, and known bugs through Hoofy's persistent memory and SDD pipeline.

## Arguments

`$ARGUMENTS` may contain:
- A file path or glob pattern (e.g., `src/auth/*.ts`)
- A git ref or range (e.g., `HEAD~3..HEAD`, `feature-branch`)
- A PR number (e.g., `#42`)
- A description of what to review (e.g., "the auth changes I just made")
- Nothing — in which case, review staged/unstaged git changes

## Phase 1: Gather Context (CRITICAL — this is what makes Hoofy reviews unique)

Run these tool calls **in parallel** to build the full picture:

### 1a. Memory Context
Call `mcp__hoofy__mem_search` with queries relevant to the code being reviewed. Use multiple searches:
- Search for the **domain/feature area** (e.g., "authentication", "database", "API endpoints")
- Search for **"decision"** or **"pattern"** to find established conventions
- Search for **"bugfix"** to find previously fixed bugs that could regress

### 1b. Active Change Context
Call `mcp__hoofy__sdd_change_status` to check if there's an active change pipeline. If there is:
- Call `mcp__hoofy__sdd_get_context` with `stage: "tasks"` to read the task breakdown
- Call `mcp__hoofy__sdd_get_context` with `stage: "design"` if design exists (medium/large changes)
- Call `mcp__hoofy__sdd_get_context` with `stage: "requirements"` if spec exists

### 1c. The Code
Read the actual code files being reviewed. Determine what to review:
- If `$ARGUMENTS` has file paths → read those files
- If `$ARGUMENTS` has a git range → run `git diff <range>` to get the diff
- If `$ARGUMENTS` has a PR number → run `gh pr diff <number>` to get the diff
- If `$ARGUMENTS` is empty → run `git diff` (unstaged) and `git diff --cached` (staged)
- If `$ARGUMENTS` describes a feature → use `git log --oneline -20` and `git diff` to find relevant changes

## Phase 2: Analyze Against Context

With all context gathered, review the code across these **5 dimensions** that no other tool checks:

### Dimension 1: Spec Compliance ✅
Does the code implement what was specified?
- Compare against requirements (FR-XXX, NFR-XXX) from the active change or project pipeline
- Are all acceptance criteria from tasks being met?
- Is anything implemented that WASN'T specified (scope creep)?
- Is anything specified that WASN'T implemented (gaps)?
- **If no specs exist**: note this as a risk — "No specifications found. This code was written without formal requirements."

### Dimension 2: Architecture Alignment 🏗️
Does the code follow established architectural decisions?
- Check against ADRs found in memory (search results from Phase 1)
- Does it follow patterns established in previous sessions?
- Does it contradict any recorded decisions?
- Is the component structure consistent with the design document?
- **If ADRs exist that this code violates**: flag with HIGH severity and reference the specific ADR

### Dimension 3: Regression Risk ⚠️
Could this change reintroduce a previously fixed bug?
- Cross-reference with bugfix observations from memory
- Are the same files/functions that had past bugs being modified?
- Are the conditions that caused past bugs being handled?
- **If a past bugfix is relevant**: cite the memory observation and explain the risk

### Dimension 4: Code Quality 🔍
Standard code review concerns, but informed by project context:
- Error handling (is it consistent with established patterns?)
- Edge cases (are known edge cases from memory being handled?)
- Testing (are test scenarios from task acceptance criteria covered?)
- Naming conventions (do they match project patterns from memory?)
- Performance (does it meet NFR performance requirements if specified?)

### Dimension 5: Knowledge Gaps 📝
Things that SHOULD be documented but aren't:
- Decisions made in this code that should be saved as ADRs
- Patterns established here that should be saved to memory
- Gotchas discovered that future sessions should know about
- Suggest specific `mcp__hoofy__mem_save` or `mcp__hoofy__sdd_adr` calls

## Phase 3: Present the Review

Format the review as follows:

```markdown
## 🐴 Hoofy Code Review

**Reviewing**: [what was reviewed — files, diff range, PR]
**Context loaded**: [X memory observations, Y pipeline artifacts, Z ADRs]

---

### Spec Compliance ✅
[findings or "No specs found — flying blind"]

### Architecture Alignment 🏗️
[findings referencing specific ADRs/decisions]

### Regression Risk ⚠️
[findings referencing past bugfixes, or "No known regression risks"]

### Code Quality 🔍
[findings informed by project patterns]

### Knowledge Gaps 📝
[suggestions for what to save to memory/ADRs]

---

### Summary
**Verdict**: [APPROVE / REQUEST_CHANGES / NEEDS_SPECS]
- X issues found (Y critical, Z suggestions)
- [one-line overall assessment]
```

## Severity Levels

- **CRITICAL**: Violates a spec requirement, contradicts an ADR, or risks reintroducing a known bug
- **WARNING**: Code quality issue, missing test coverage, or inconsistency with established patterns
- **SUGGESTION**: Improvement opportunity, documentation gap, or knowledge to save

## Special Cases

### No memory or pipeline context available
If `mem_search` returns nothing and no pipeline exists:
- Still perform the review (Dimensions 4 and 5 are always applicable)
- Note prominently: "⚠️ No Hoofy context available. This review is limited to code quality only. For a full context-aware review, use `/hoofy:init` to set up SDD or `/hoofy:change` to track this work."
- Suggest the user start using Hoofy's memory and pipeline so future reviews have context

### Reviewing someone else's code (PR)
- All 5 dimensions still apply
- Be extra careful with Dimension 2 (architecture) — the PR author may not know about past decisions
- Dimension 5 is especially valuable — flag knowledge that should be shared

### Large diffs (>500 lines)
- Focus on architectural concerns (Dimensions 1-3) over line-by-line quality
- Group findings by component/module rather than by file
- Prioritize critical issues over suggestions

## Important

- **NEVER fabricate memory context.** If `mem_search` returns no results, say so. Don't pretend you found relevant decisions.
- **ALWAYS cite your sources.** When referencing a memory observation, mention it: "Per memory observation #14 (ADR: Use PostgreSQL)..."
- **Be specific.** Don't say "this might cause issues" — say "this contradicts ADR-001 which decided X because Y"
- **Save new findings.** If you discover something important during the review, offer to save it with `mcp__hoofy__mem_save`
