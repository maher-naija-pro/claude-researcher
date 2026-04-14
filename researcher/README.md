# Claude Researcher Skill

This skill provides a research structured workflow , collect multiple solution options from the web, organize findings into folders, optionally test each option, and produce a scored final decision.

## What This Skill Does

- Creates a dedicated `<problem_name>_problem/` workspace.
- Searches for candidate solutions from multiple source types (community, articles, code repos, academic, reference docs, videos).
- Organizes each solution into its own directory with documentation notes.
- Produces a per-solution `README.md` with summary, sources, pros, and cons.
- Optionally tests each solution and records reproducible outcomes.
- Generates a final `decision/DECISION.md` with weighted scoring and a recommended choice.

## Install

Use one of the setups below depending on how you use skills.

#Install

1. Create a global skills directory if needed (example):
   - `~/.claude/skills/researcher/`
2. Copy this skill file into that folder as:
   - `~/.claude/skills/researcher/SKILL.md`
3. Restart your agent/CLI session so the skill is reloaded.

## How To Use

Ask for research tasks with prompts like:

- "Research solutions for fixing slow API response times."
- "Compare approaches for background jobs in Node.js."
- "Investigate options for vector databases for RAG."

The skill follows this flow:

1. Create problem directory.
2. Search for candidate solutions in parallel.
3. Create one directory per solution.
4. Gather source documentation into markdown files.
5. Write per-solution summary (`README.md`).
6. Optionally test each solution and write `test_results_<solution>.md`.
7. Create `decision/DECISION.md` with weighted scores and final recommendation.

## Output Structure

```text
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

