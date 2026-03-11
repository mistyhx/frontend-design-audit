---
name: frontend-design-audit:quick
description: Auto-evaluate and fix usability issues without discussion. Evaluates the UI, then immediately implements fixes for all severity 3-4 issues and practical severity 2 fixes.
---

# Frontend Design Audit: Quick Mode

Evaluate and fix in one pass. No discussion — just identify and implement.

## Required Reading

Before starting, read:
1. The main skill at `.claude/skills/frontend-design-audit/SKILL.md` — Framework and evaluation process
2. `references/heuristics.md` — What to look for
3. `references/patterns.md` — Fix patterns

## Usage

```
/frontend-design-audit:quick                 # Auto-fix across the project
/frontend-design-audit:quick src/pages/      # Auto-fix in specific directory
/frontend-design-audit:quick Dashboard.tsx   # Auto-fix a specific file
```

## Process

1. **Discover** — Find and read UI files (same as evaluate).

2. **Evaluate** — Systematically check all 15 principles (same as evaluate). Build the full findings list with severity ratings.

3. **Triage** — Separate findings into:
   - **Auto-fix** (severity 3-4): Implement immediately
   - **Auto-fix** (severity 2 where the fix is safe and straightforward): Implement
   - **Report only** (severity 1, or severity 2 where the fix might change behavior): List but don't implement

4. **Implement** — Apply all auto-fix changes. Use patterns from `references/patterns.md`. Make minimal, targeted changes.

5. **Report** — Present a summary:

```
## Quick Audit Complete

### Fixed (X issues)

| # | Issue | Severity | File | Principle |
|---|-------|----------|------|-----------|
| 1 | Added loading state to form submit | 3 | Form.tsx:42 | Visibility of System Status |
| 2 | Added alt text to images | 4 | Hero.tsx:12 | Accessibility |
| ... | | | | |

### Not auto-fixed (X issues)

| # | Issue | Severity | File | Why not auto-fixed |
|---|-------|----------|------|--------------------|
| 1 | Consider adding keyboard shortcuts | 2 | App.tsx | Requires design decisions |
| ... | | | | |

### Strengths
[what the interface already does well]
```

## Guidelines

- **Be conservative** — Only auto-fix things you're confident won't break functionality
- **Preserve behavior** — Fixes should improve usability without changing what the interface does
- **Don't add features** — Fix violations, don't add new capabilities
- **Skip ambiguous cases** — If a fix could go multiple ways, report it instead of guessing
- **Maintain code style** — Match the existing code's patterns and conventions
