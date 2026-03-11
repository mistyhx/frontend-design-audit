---
name: frontend-design-audit:evaluate
description: Run a usability audit on front-end code and produce a structured report. No implementation — just the analysis.
---

# Frontend Audit: Evaluate Only

Run a usability audit and produce a report. Do not implement any changes.

## Required Reading

Before starting, read these files:
1. The main skill at `.claude/skills/frontend-design-audit/SKILL.md` — The evaluation framework and workflow
2. `references/heuristics.md` — Detailed principle definitions and what to look for

## Usage

```
/frontend-design-audit:evaluate              # Evaluate common UI paths in the project
/frontend-design-audit:evaluate src/pages/   # Evaluate a specific directory
/frontend-design-audit:evaluate App.tsx      # Evaluate a specific file
```

## Process

1. **Identify UI files** — Use Glob to find front-end files (tsx, jsx, vue, svelte, html, css). If a path was provided, scope to that path. If not, look for common UI directories (src/components, src/pages, app/, pages/, etc.).

2. **Read the code** — Read the key UI files. For large projects, focus on the most important screens (index/home, main dashboard, primary form, key user flow).

3. **Evaluate systematically** — Go through all 15 principles. For each, inspect the code for violations. Reference `references/heuristics.md` for what to look for.

4. **Rate severity** — Apply the 0-4 scale to each finding. Consider frequency, impact, and persistence.

5. **Produce the report** — Use the structured format from SKILL.md. Include:
   - Summary table with severity counts
   - All findings grouped by severity (highest first)
   - Each finding with: principle reference, file location, issue description, user impact, recommended fix
   - A "Strengths" section noting what the interface does well

6. **Present to user** — Show the full report. Offer to explain any finding in more detail. Do NOT implement changes — this command is evaluation only.

## After the Report

Suggest next steps:
- "Would you like me to explain any of these findings in more detail?"
- "Ready to start fixing these? Run `/frontend-design-audit:improve` or I can implement the top-priority items now."
- "Want the quick version? Run `/frontend-design-audit:quick` to auto-fix severity 3-4 issues."
