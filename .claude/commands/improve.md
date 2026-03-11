---
name: frontend-design-audit:improve
description: Implement usability improvements based on a previous audit. Discusses each change with the user before implementing.
---

# Frontend Audit: Improve

Implement improvements from a usability audit. This command is for after an audit has been done — either from `/frontend-design-audit:evaluate` or a full `/frontend-design-audit` run.

## Required Reading

Before starting, read:
1. The main skill at `.claude/skills/frontend-design-audit/SKILL.md` — Workflow and principles
2. `references/patterns.md` — Common fix patterns with code examples

## Process

### If an evaluation exists in the conversation

1. Reference the existing findings
2. Present them as a prioritized list, grouped by severity
3. Ask the user which ones to address: "Which findings would you like me to fix? I can do all of them, or you can pick specific ones."

### If no evaluation exists

1. Ask: "I don't have an audit to work from. Would you like me to run one first (`/frontend-design-audit:evaluate`), or do you have specific issues you'd like me to address?"

### Implementation

For each approved fix:

1. **Explain** — Briefly state what you're changing and why (connect to the usability principle)
2. **Implement** — Make the minimum change needed. Don't refactor surrounding code.
3. **Verify** — Confirm the fix addresses the violation without breaking anything
4. **Report** — State what was done

Work through fixes in priority order (severity 4 first, then 3, then 2, then 1).

### After Implementation

Summarize all changes made:
```
## Changes Applied

1. **[Finding title]** (Severity X → Fixed)
   - File: `path/to/file.tsx`
   - Change: [what was changed]
   - Principle: [which usability principle this addresses]

[...repeat for each fix...]
```

Offer to re-evaluate: "Want me to re-run the audit to check if there are remaining issues?"
