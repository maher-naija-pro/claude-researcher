---
name: researcher
description: Use when the user asks to research a problem, find solutions, compare approaches, or investigate a technical topic by searching the internet and organizing findings into structured directories.
---

# Researcher

## Overview

A structured research workflow that searches the web for solutions to a problem, organizes findings into a dedicated directory, tests each candidate solution, and documents results.

## When to Use

- User asks to "research X", "find solutions for X", or "investigate X"
- Problem requires searching multiple sources on the internet
- User wants organized, documented findings rather than a quick answer

## Core Pattern

> **After completing each step, pause and ask the user to confirm before proceeding to the next step.**

### Step 1 — Create Problem Directory

```
mkdir <problem_name>_problem/
```

Use snake_case. Keep it short and descriptive.

**→ Confirm with user before proceeding.**

### Step 2 — Search for Solutions in Parallel

Dispatch multiple subagents (model: `claude-sonnet-4-6`) — one per search angle. Each subagent:
- Searches the web for candidate solutions
- Returns: solution name, summary, source URLs, and page location/section
- Sources to cover:
  - **Community:** Stack Overflow, Reddit, Hacker News, dev.to
  - **Articles:** Medium, InfoQ, DZone
  - **Code:** GitHub, Papers With Code, npm/PyPI/crates.io package pages
  - **Academic:** arXiv, Google Scholar, ResearchGate, Semantic Scholar, IEEE Xplore, ACM Digital Library, USENIX, Distill.pub
  - **Reference:** official docs, tutorials, Wikipedia
  - **Video:** YouTube conference talks (NeurIPS, ICML, OSDI, CCC, etc.)

Present the list of solutions found to the user.

**→ Confirm with user before proceeding (user may exclude solutions).**

### Step 3 — Create a Directory per Solution

For each candidate solution, create:

```
<problem_name>_problem/
  solution_<solution_name>/
    README.md
    documentation/
```

### Step 4 — Gather Documentation per Solution

For each solution, dispatch one subagent (model: `claude-sonnet-4-6`) per source category in parallel:

- **Community:** Stack Overflow, Reddit, Hacker News, dev.to
- **Articles:** Medium, InfoQ, DZone
- **Code:** GitHub, Papers With Code, npm/PyPI/crates.io
- **Academic:** arXiv, Google Scholar, ResearchGate, Semantic Scholar, IEEE Xplore, ACM Digital Library, USENIX, Distill.pub
- **Reference:** official docs, Wikipedia
- **Video:** YouTube conference talks (NeurIPS, ICML, OSDI, etc.)

For each document found, create a file inside `documentation/`:

```
documentation/
  <doc_topic>.md
```

Each `<doc_topic>.md` contains:

```markdown
# <Document Title>

## Source
- URL: <url>
- Page section / heading: "<where on page>"

## Summary
Key information found on this page relevant to the solution.
```

### Step 5 — Document Each Solution

Each `README.md` contains:

```markdown
# Solution: <Solution Name>

## Summary
One-paragraph description of the approach.

## Sources
- [Title](URL) — section: "heading or paragraph where found"

## Pros
- ...

## Cons
- ...
```

**→ Ask the user: "Do you want to test each solution, or compare them based on documentation only?"**

- If **yes, test** → proceed to Step 6
- If **no, skip testing** → go directly to Step 7 (Decision Summary uses documentation and README only)

### Step 6 — Test Each Solution *(only if user confirmed testing)*

Spawn one subagent (model: `claude-sonnet-4-6`) per solution — **sequentially, one at a time**. Each subagent independently attempts an implementation or reproduction of its assigned solution, then writes `test_results_<solution_name>.md`. Wait for each subagent to complete before spawning the next.

```markdown
# Test Results: <Solution Name>

## Test Description
What was tested and how.

## Steps Taken
1. ...
2. ...

## Outcome
- [ ] Works as documented
- [ ] Partially works (describe limitations)
- [ ] Failed (describe errors)

## Errors / Issues Encountered
Paste relevant error messages or unexpected behavior.

## Verdict
Short assessment: recommended / conditional / avoid — and why.
```

### Step 7 — Decision Summary *(model: `claude-opus-4-6`)*

After all solutions are tested, create `decision/` inside the problem directory:

```
<problem_name>_problem/
  solution_<name_a>/
    README.md
    documentation/
      <doc_topic>.md
    test_results_<name_a>.md
  solution_<name_b>/
    README.md
    documentation/
      <doc_topic>.md
    test_results_<name_b>.md
  decision/
    DECISION.md
```

Before writing `DECISION.md`, define:

**1. Decision Aspects** — derive from the problem context. Examples:
- Ease of implementation
- Performance
- Community support
- Documentation quality
- Test result (works / partial / failed)
- Maintenance burden
- Security
- Cost / dependencies

**2. Scoring Method** — score each aspect per solution:

| Score | Meaning |
|-------|---------|
| 3 | Excellent |
| 2 | Good |
| 1 | Poor |
| 0 | Fails / N/A |

Each aspect may have a **weight** (1–3) reflecting its importance for this problem. Weighted score = score × weight.

`DECISION.md` contains:

```markdown
# Decision: <Problem Name>

## Scoring Aspects & Weights

| Aspect | Weight |
|--------|--------|
| Ease of implementation | 2 |
| Performance | 3 |
| Documentation quality | 2 |
| Test result | 3 |
| Community support | 1 |
| ... | ... |

## Scoring Table

| Aspect (weight) | solution_a | solution_b | solution_c |
|-----------------|-----------|-----------|-----------|
| Ease of impl. (×2) | 3 → 6 | 2 → 4 | 1 → 2 |
| Performance (×3) | 2 → 6 | 3 → 9 | 1 → 3 |
| Test result (×3) | 3 → 9 | 2 → 6 | 0 → 0 |
| Community (×1) | 2 → 2 | 3 → 3 | 2 → 2 |
| **TOTAL** | **23** | **22** | **7** |

## Solutions Overview

| Solution | Works? | Pros | Cons |
|----------|--------|------|------|
| solution_a | ✅ Yes | ... | ... |
| solution_b | ⚠️ Partial | ... | ... |
| solution_c | ❌ No | ... | ... |

## Final Decision

**Chosen solution:** `solution_a` (score: 23/max)

### Why
Explanation covering score breakdown, trade-offs, and fit for the problem.

### How to Apply
Brief guidance on using the chosen solution.
```

**→ Present the decision and scoring table to the user and confirm.**

## Quick Reference

| Step | Action | Model | Confirm? |
|------|--------|-------|----------|
| 1 | `mkdir <problem>_problem/` | — | Yes |
| 2 | Parallel subagents — search (docs, tutorials, GitHub, Medium, Reddit, SO) | Sonnet | Yes (user may exclude) |
| 3 | `mkdir solution_<name>/` + `documentation/` per solution | — | — |
| 4 | Parallel subagents — gather docs into `documentation/<topic>.md` | Sonnet | — |
| 5 | Write `README.md` with summary, sources, pros/cons | — | Yes |
| 6 | Sequential subagents — test each solution, write `test_results_<name>.md` | Sonnet | — |
| 7 | Create `decision/DECISION.md` with comparison table and final decision | **Opus** | Yes |

## Common Mistakes

- **Single search query**: Use multiple angles (official docs, tutorials, Stack Overflow, GitHub issues)
- **Missing source location**: Always note the section/heading where info was found, not just the URL
- **Skipping tests**: Every solution must be tested — no untested verdicts
- **Vague test results**: Document exact errors, commands run, and environment details
