---
name: frontend-design-audit
description: Evaluate and improve the usability of existing front-end interfaces — either local source code or live websites by URL. Use this skill whenever the user has existing front-end code OR a live website URL and wants to understand why users struggle with it, find usability problems, or make it easier to use. Triggers on direct requests like "review my UI", "audit my interface", "check accessibility", "evaluate my design", "improve the UX", "audit this website", "review https://example.com" — but ALSO on indirect symptom descriptions like "users keep abandoning this form", "something feels off about this page", "we're getting complaints about the checkout flow", "conversion dropped after the redesign", "people say it's confusing", or "make this less painful to use". If someone has a front-end component, page, or URL and the problem sounds like usability, user confusion, or poor interaction design rather than a bug or performance issue — use this skill. NOT for building new interfaces from scratch, performance optimization, or backend logic.
---

# Frontend Design Audit

Audit and improve front-end interfaces using established usability principles.

## What This Skill Does

You perform a comprehensive design audit — thinking like a senior UX designer reviewing an interface end-to-end. You inspect existing code against 15 established design principles, identify problems at both the component level and the system level, rate their severity, and provide concrete fixes.

This is not a surface-level lint or a quick accessibility check. You evaluate the full picture: individual component issues, cross-page consistency, design system coherence, interaction patterns, information architecture, and the holistic user journey. Every finding references a specific usability principle. Every severity rating follows a standardized 0-4 scale. Every recommendation must be actionable and specific to the code.

---

## Who This Is For

Anyone who has front-end code they want to improve — developers, designers, PMs, founders, hobbyists. Adjust your language to the user:

- Most users won't know UX jargon. Explain the "why" behind each principle in plain language. They don't need to know the academic name — they need to understand *why* a missing loading indicator hurts their users.
- If the user seems UX-savvy (they mention heuristics, Nielsen, etc.), be concise and reference principles by name.
- When in doubt, err toward explaining. The educational value is part of the skill's purpose.

---

## The 15 Usability Principles

You evaluate against 15 principles drawn from established usability research and practical experience:

| # | Principle |
|---|-----------|
| 1 | **Visibility of System Status** |
| 2 | **Match Between System and Real World** |
| 3 | **User Control and Freedom** |
| 4 | **Consistency and Standards** |
| 5 | **Error Prevention** |
| 6 | **Recognition Over Recall** |
| 7 | **Flexibility and Efficiency** |
| 8 | **Aesthetic and Minimalist Design** |
| 9 | **Error Recovery** |
| 10 | **Help and Documentation** |
| 11 | **Affordances and Signifiers** |
| 12 | **Structure** |
| 13 | **Accessibility** |
| 14 | **Perceptibility** |
| 15 | **Tolerance and Forgiveness** |

For detailed definitions, violation patterns, academic sources, and fix guidance for each principle, read `../../../references/heuristics.md`.

---

## Severity Rating Scale

Rate every finding on the standard 0-4 severity scale:

| Rating | Label | Meaning | Action |
|--------|-------|---------|--------|
| **0** | Not a problem | No usability issue | Skip |
| **1** | Cosmetic | Aesthetic issue only | Fix if time allows |
| **2** | Minor | Users notice but work around it | Low priority |
| **3** | Major | Users struggle significantly | High priority |
| **4** | Catastrophe | Users cannot complete tasks or make serious errors | Must fix |

**Three factors determine severity:**
1. **Frequency** — How many users encounter this? (rare / occasional / frequent)
2. **Impact** — How hard is it to overcome? (easy workaround / struggle / blocked)
3. **Persistence** — One-time or recurring? (learn to avoid / hits every time)

A finding that is frequent, high-impact, and recurring = severity 4.
A finding that is rare, low-impact, and one-time = severity 1.

Rate severity based on **user impact**, not how easy the fix is.

---

## Two Input Modes

The skill works with two types of input. Detect which one based on what the user provides:

### Local Project (source code)

When the user points to files, directories, or is working inside a project. You have full access to the codebase — you can read, evaluate, and implement fixes.

### Live Website (URL)

When the user provides a URL (e.g., "audit https://example.com"). You cannot modify the code — the audit is **report-only**. Use WebFetch to retrieve the page HTML/CSS. Note the limitations clearly in the report: you're evaluating the served HTML/CSS, not the source code, so some issues (JS behavior, loading states, dynamic content) may not be fully observable. Focus on what's visible in the markup: semantic structure, accessibility attributes, meta tags, contrast, responsive meta, and content structure.

---

## Workflow

### Discussion Mode (Default)

The full audit workflow:

1. **Discover** — Read the front-end code (local) or fetch the page (URL). Identify the interface type, technology stack, and primary user flows.
2. **Evaluate** — Systematically inspect against all 15 principles. Document each violation found.
3. **Report** — Present findings in the structured format below, grouped by severity.
4. **Discuss** — Walk the user through findings. Explain *why* each matters and what the fix achieves. Let the user decide if there's anything they'd prefer to skip.
5. **Implement** — Fix all findings by default. If the user asked to skip specific findings, respect that. *(Local projects only — skip for URL audits.)*
6. **Verify** — Post-implementation review. This is NOT "confirm you did it" — it's a focused second look at the modified code to catch issues the fixes introduced or that the first pass missed. See Step 6 details below.

### Quick Mode (`/frontend-design-audit:quick`)

For users who want improvements without discussion:

1. **Discover** — Same as above.
2. **Evaluate** — Same as above.
3. **Report** — Present a summary of findings.
4. **Implement** — Fix all findings across all severity levels. *(Local projects only — for URL audits, just present the full report.)*
5. **Verify** — Same post-implementation review as Discussion Mode.
6. **Summary** — Report what was changed and why.

---

## Evaluation Process

### Step 1: Discover

**For local projects** — Read the front-end code thoroughly. You need to understand:
- **What files exist** — HTML, CSS, JS/TS, JSX/TSX, Vue, Svelte, etc.
- **The application shell** — index.html (meta tags, OG tags, viewport, lang attribute), global CSS, layout components
- **What the interface does** — Is it a dashboard? A form? An e-commerce page? A settings panel?
- **Who the users likely are** — Based on the content and context.
- **What the primary user flows are** — The main tasks users perform.
- **The design system** — What tokens, patterns, and conventions are used? Are they applied consistently?

Use Glob to find UI files, then Read them. A thorough audit requires seeing the full picture — this includes the application shell (index.html, global CSS, layout files) and every page/component. Cross-page issues like design system inconsistencies or broken navigation patterns only emerge when you've seen everything.

**Multi-page projects:** Read ALL pages. Every page must be evaluated — not just the "main" one. Different pages often have different issues (a settings page may lack form validation that the homepage handles well; a 404 page may break the design system). If the project has more than 20 UI files, ask which flows to focus on, but still read shared layout components and at least sample pages from each distinct section.

**For live websites (URL)** — Use WebFetch to retrieve the page. If the user provides a single URL, fetch that page. If they mention multiple pages or a whole site, fetch the key pages (homepage, main feature page, contact/form page — up to 5 pages).

From the fetched HTML, extract and evaluate:
- Document structure (`<html lang>`, `<head>` meta/OG tags, `<title>`, viewport)
- Semantic landmarks (`<main>`, `<nav>`, `<header>`, `<footer>`, heading hierarchy)
- Accessibility attributes (alt text, ARIA labels, roles, form labels)
- Link and button patterns (proper elements, external link handling)
- Inline and linked stylesheets (contrast clues, responsive media queries, focus styles)

Be transparent about what you **cannot** evaluate from fetched HTML alone:
- JavaScript-dependent behavior (loading states, form validation, dynamic content)
- Computed CSS (exact contrast ratios, hover/focus states)
- Client-side routing and SPA navigation
- Actual visual layout and rendering

State these limitations in the report header so the user knows the scope.

### Step 2: Evaluate

Read `../../../references/heuristics.md` first — it contains detailed guidance on what to look for under each principle, including visual design checks.

Then walk through **every single principle, one by one**. For each of the 15, ask yourself: "Does this interface violate this principle anywhere?" Don't skip a principle because it seems unlikely — check it against the code. The value of this audit comes from systematic coverage, not just catching the obvious issues.

**For each principle, consider it at these levels:**

**Component level** — inspect individual files:
- HTML structure and semantics (landmarks, headings, lists, proper elements)
- CSS styling (contrast, spacing, visual hierarchy, interactive states, focus indicators)
- JavaScript behavior (loading states, error handling, form validation, feedback)
- Accessibility attributes (ARIA labels, roles, alt text, focus management)
- Responsive behavior (viewport handling, touch targets, mobile patterns)

**Hidden and dynamic UI** — these are easy to overlook but often contain the worst usability issues because they get less design attention. Actively search the code for every piece of UI that isn't visible on initial page load, and evaluate each one:
- **Modals and dialogs** — Check for `role="dialog"`, `aria-modal`, `aria-labelledby`, Escape key handler, focus trap (Tab should cycle within the modal), and return-focus-on-close. Check that the overlay click-to-dismiss works. Check that forms inside modals provide submission feedback and that labels are properly associated.
- **Dropdowns, menus, and popovers** — Check for `aria-haspopup`, `aria-expanded`, keyboard navigation (arrow keys, Escape to close), and click-outside-to-close.
- **Drawers, sidebars, and off-canvas panels** — Same focus-trap and Escape requirements as modals. Check mobile responsive behavior.
- **Tooltips and toasts** — Check timing, dismissibility, and screen reader announcements (`role="status"`, `aria-live`).
- **Accordions, tabs, and collapsible sections** — Check for proper ARIA roles (`tablist`, `tab`, `tabpanel`) and keyboard patterns (arrow keys between tabs).
- **Form validation states** — Check that error messages are associated with inputs (`aria-describedby`), that errors are announced to screen readers, and that focus moves to the first error.
- **Empty states, loading states, and error states** — Check that these exist and communicate clearly. A missing empty state is a finding.
- **Confirmation dialogs for destructive actions** — Check whether delete/remove/clear actions have confirmation steps.

**Visual design** — many usability problems are not in the code attributes but in the visual presentation. These issues result in visible, meaningful changes when fixed. The visual design checks are integrated into `../../../references/heuristics.md` under each relevant principle (especially H8, H11, H12, H14). Key areas:
- **Typography hierarchy** — Is there a clear size/weight progression that guides the eye? Can users scan headings, then drill into details? Are body text and labels large enough? A flat type scale (everything similar size) forces users to read everything instead of scanning.
- **Spacing and proximity** — Are related items grouped tightly and separated from unrelated items (Gestalt proximity)? Is there enough breathing room between sections? Cramped layouts feel overwhelming; uniform spacing makes it hard to see where one section ends and another begins.
- **Visual weight and emphasis** — Do primary actions (CTAs) visually dominate over secondary actions? Is the most important content (e.g., task titles vs metadata) visually prominent through size, weight, or color? When everything has equal visual weight, nothing stands out.
- **Color purpose** — Is color used to convey meaning (status, priority, grouping) or is it decorative noise? Are there too many competing colors? Does the color palette create a clear hierarchy (primary accent, secondary, neutral)?
- **Information density** — Is the layout too dense (users feel overwhelmed) or too sparse (users must scroll excessively)? Can users scan the content efficiently?
- **Alignment and consistency** — Are elements aligned to a consistent grid? Are spacing values consistent or do they vary randomly? Misalignment creates a subtle feeling of disorder.
- **Interactive state visibility** — Do buttons, links, and cards have clearly distinct hover, active, focus, and disabled states? Subtle state changes (e.g., a barely noticeable color shift on hover) make the interface feel unresponsive.

**System level** — compare across files (this is where deep design value comes from):
- **Cross-page consistency** — Do all pages use the same color tokens, spacing, and typography? Does a page like a 404 or error page break the visual language?
- **Interaction pattern consistency** — Do similar elements behave the same way everywhere? If project cards lift on hover, do non-clickable cards incorrectly use the same pattern?
- **Design system coherence** — Are there hardcoded colors/sizes that should use tokens? Do components follow the same structural patterns?
- **Navigation and wayfinding** — Can users always tell where they are, how they got there, and how to get back? Is navigation consistent across all pages?
- **Meta and SEO** — Check index.html for proper meta tags, OG tags, page titles, lang attribute, viewport settings
- **Visual hierarchy and signifiers** — Can users distinguish clickable from non-clickable? Internal links from external? Primary actions from secondary?

**Important:** Don't fabricate violations — if a principle is well-handled, note it as a strength. But don't self-limit either. A real-world interface almost always has issues under most of the 15 principles. If you're finding fewer than 10, go back through the principles you marked as "clean" and look harder — especially at visual design (typography hierarchy, spacing, visual weight, color usage), hidden/dynamic UI (modals, dropdowns, drawers, tooltips), cross-page patterns, edge cases (error pages, empty states, loading states), and the application shell (index.html, meta tags). Remember: a good audit produces findings that result in *visible* improvements, not just code-level attribute changes. If all your findings are ARIA labels and semantic HTML, you're missing the visual design layer. And when you fix visual design issues, the changes should be obvious — a user looking at the before and after should immediately see the difference. Timid visual changes (shifting a color by one hex digit, adjusting a font size by 0.05rem) don't solve the underlying problem.

**Principle Coverage Verification — do this before writing the report:**
Before proceeding to the report, verify you evaluated ALL 15 principles. Walk through this checklist mentally and confirm you considered each one against the code:

1. Visibility of System Status — loading, feedback, active states
2. Match Between System and Real World — language, conventions, mapping
3. User Control and Freedom — undo, escape, back, cancel
4. Consistency and Standards — internal + external consistency
5. Error Prevention — validation, constraints, confirmation
6. Recognition Over Recall — empty states, labels, breadcrumbs
7. Flexibility and Efficiency — keyboard shortcuts, bulk ops, preferences
8. Aesthetic and Minimalist Design — typography, spacing, visual weight, color, density
9. Error Recovery — error messages, recovery guidance
10. Help and Documentation — onboarding, tooltips, contextual help
11. Affordances and Signifiers — clickability cues, button hierarchy, touch targets
12. Structure — grouping, layout, visual boundaries, mobile nav
13. Accessibility — alt text, contrast, keyboard, semantics, ARIA
14. Perceptibility — state visibility, visual hierarchy, scannability
15. Tolerance and Forgiveness — input flexibility, data preservation, undo

If any principle has zero findings AND zero strengths noted, go back and evaluate it — you likely skimmed past it. Every principle must be consciously assessed, even if the result is "well-handled."

### Step 3: Report

Present findings using this structure:

```
## UX Design Audit Report

**Scope:** [what was evaluated]
**Source:** [list ALL files reviewed (local) OR URLs fetched (live website)]
**Interface type:** [dashboard / form / e-commerce / etc.]
**Limitations:** [For URL audits only: note what couldn't be evaluated — JS behavior, computed styles, etc.]

### How to Read This Report
Findings are rated on a 0-4 severity scale (4 = users can't complete tasks,
1 = cosmetic only). Each finding references an established usability principle.
Start from the top — the most impactful issues are listed first.

### Summary

| Severity | Count |
|----------|-------|
| 4 - Catastrophe | X |
| 3 - Major | X |
| 2 - Minor | X |
| 1 - Cosmetic | X |
| **Total findings** | **X** |

### Quick Wins
The highest-impact issues that are also straightforward to fix:
1. [Finding title] (Severity X) — [one-line fix summary]
2. [Finding title] (Severity X) — [one-line fix summary]
3. [Finding title] (Severity X) — [one-line fix summary]

### Findings

#### [Severity 4] Finding title
- **Principle:** [which usability principle(s) violated]
- **Location:** `file.tsx:42`
- **Issue:** [what's wrong]
- **User impact:** [what real users will experience because of this — be concrete]
- **Fix:** [specific, actionable recommendation with code-level detail]

[...repeat for all findings, grouped by severity descending...]

### Strengths
Always include this section — it builds trust and tells users what NOT to change.
List at least 3 specific things the interface does well, referencing which
principles they satisfy. A report that's only negative is demoralizing and
less useful than one that acknowledges good work alongside problems.
```

### Step 4: Discuss (Discussion Mode only)

After presenting the report:
- All findings will be fixed by default — let the user know, and ask if there's anything they'd like to skip or deprioritize
- For each finding, explain *why* the principle matters — connect it to real user behavior
- Be ready to explain trade-offs (e.g., adding a confirmation dialog improves error prevention but adds friction)
- If the user wants to exclude specific findings, respect that — they know their users and constraints better than you do

### Step 5: Implement

Read `../../../references/patterns.md` for concrete code examples, including design system and visual coherence patterns.

Implementation happens in three phases. Don't skip to individual fixes — the design foundation comes first. The goal is not just to fix individual findings but to make the UI feel cohesive and polished.

#### Phase 1: Establish Design Foundation

Before making individual fixes, extract and consolidate the implicit design system. Scan the existing CSS and identify what values are in use, what's inconsistent, and what tokens to establish.

**Define CSS custom properties** for a coherent system:
- **Spacing scale** — 5-6 values on a consistent progression (e.g., 4/8/16/24/40/64px). Replace all random margin/padding values.
- **Type scale** — 4-5 levels with clear size AND weight differentiation. Every text element should map to exactly one level.
- **Color palette** — 1 primary accent, 1-2 semantic colors (success/error), and a neutral scale (text, text-secondary, text-muted, border, background). Remove random hex values.
- **Shadows** — 2-3 levels (subtle, medium, elevated). One shadow style, used consistently.
- **Border radius** — 1-2 consistent values. Not 4px on one card, 12px on another, 8px on a third.
- **Transitions** — 1 standard duration and easing (e.g., 150ms ease). Every interactive element uses it.

**Consolidate icon usage** — If multiple icon sources exist (Font Awesome mixed with Lucide, emoji mixed with SVGs, Unicode symbols mixed with icon fonts), choose ONE consistent source and replace all others. Mixed icon styles are one of the most visible signs of an unpolished UI.

**Identify the component vocabulary** — What reusable patterns exist (cards, buttons, badges, section containers)? Each pattern should have ONE consistent style applied everywhere.

#### Phase 2: Apply Fixes

Apply individual findings *through* the design system — not with ad-hoc values.

**Code-level fixes** (ARIA attributes, semantic HTML, event handlers, meta tags):
- Make the minimum change needed to address each violation
- Preserve existing code style and patterns

**Visual design fixes** (typography, spacing, color, layout, interactive states):
- Use the design tokens from Phase 1 — don't introduce new hardcoded values
- Be confidently visible. If the type scale is flat, establish a clear hierarchy. If spacing is uniform, create real contrast between internal and section spacing. If buttons all look the same, make the primary action visually dominant.
- Preserve the existing visual identity (brand colors, overall aesthetic) while improving hierarchy and clarity within it.

**Flow and interaction fixes** (loading states, transitions, form progression):
- Use consistent transition timing from the design tokens across all interactive elements
- State changes should have visual continuity — use transitions, don't teleport elements
- Loading → success flows should feel smooth (button disable → spinner → success message)

#### Phase 3: Design Coherence Pass

After all individual fixes, review the interface holistically. This pass transforms isolated fixes into a polished result. Go through this checklist and fix any inconsistencies:

- **Spacing rhythm** — Same card padding everywhere? Same section gaps? Same form field spacing? Values all from the token scale?
- **Typography** — Type scale consistent across all pages? Same heading sizes, body text, caption text? No orphan font sizes that don't map to a scale level?
- **Color discipline** — Accent color used sparingly for emphasis? Clear neutral foundation? No random hex values outside the palette?
- **Icon consistency** — One icon library throughout? Same icon size per context? Same visual weight? No mixing of filled and outlined styles?
- **Component patterns** — All cards look like cards (same padding, radius, shadow, border)? All buttons at the same hierarchy level look the same? All section containers styled consistently?
- **Interactive states** — Same hover, focus, active, and disabled patterns on ALL interactive elements? No element missing states that others have?
- **Transitions** — Same duration and easing on all animated properties? No jarring fast/slow mismatches?
- **Alignment** — Elements snap to a consistent grid? Content areas aligned across sections? No subtle misalignment between pages?
- **Semantic-visual sync** — Every semantic attribute must have a visible counterpart. `aria-current="page"` needs a highlighted nav style. `aria-expanded` needs a visual open/close indicator. `aria-checked` needs a toggle state. `colspan` group headers in tables need distinct styling (background, left-alignment, visual weight) so they read as category labels, not misaligned data rows. If you added an ARIA attribute without a corresponding CSS rule — the job is half done.

#### Interface-Type Taste Guide

The design system should match the interface's purpose. Calibrate visual decisions to the type:

| Type | Character | Key moves |
|------|-----------|-----------|
| **Portfolio** | Clean, spacious, work-centered | Generous whitespace, consistent project cards, smooth hover transitions, minimal chrome, let images/work breathe, restrained color |
| **Dashboard** | Dense, scannable, data-focused | Clear metric hierarchy (big numbers, small labels), subtle separators, compact cards, strong label-value contrast |
| **Marketing** | Bold, focused, conversion-oriented | One message per section, dominant CTA, generous section spacing, trust signals, clear visual flow down the page |
| **Form/App** | Guided, structured, reassuring | Clear field grouping, inline validation, progress indicators, calm color palette, generous field spacing |
| **E-commerce** | Browseable, trustworthy, scannable | Consistent product cards, clear pricing hierarchy, prominent add-to-cart, review signals, filter/sort affordances |

#### Anti-Patterns — What NOT to Do

These are the most common ways implementations go wrong. Actively check your work against this list:

- **Trending color sameness** — Don't default to the same indigo/purple/blue palette every AI tool reaches for. Work with the existing brand colors. If there are none, choose something with personality — not the first palette from a generator.
- **Cards for everything** — Not every group of content needs a card with shadow and border-radius. Sometimes a simple divider, a background change, or just whitespace is better grouping. Cards should be earned — use them when content genuinely represents a discrete, browseable object.
- **Competing CTAs** — One primary action per screen region. If there are three buttons in a section and they all look the same, the user doesn't know what to do. One solid, one outlined, one text-only — or remove the ones that don't matter.
- **Equal visual weight everywhere** — If every element has the same size, weight, and color intensity, nothing guides the eye. Some things must be loud (primary metric, main heading, primary CTA) and most things must be quiet (metadata, secondary nav, captions). Create contrast, not uniformity.
- **Inconsistent design across sections** — If the hero uses one visual language and the footer uses another, the page feels stitched together from templates. The design tokens exist to prevent this — use them everywhere, no exceptions.
- **Padding as whitespace** — Don't just add generous padding to everything and call it "breathing room." Whitespace should be *intentional*: tight within groups (label + input), open between groups (section to section), and asymmetric where it creates visual interest. Uniform padding everywhere is the opposite of design.
- **System structure instead of user tasks** — Don't organize by code modules ("Settings," "Profile," "Preferences"). Organize by what users want to do. A form should flow in the order users think, not the order the database stores it.
- **Low contrast text** — Never use gray text below 4.5:1 contrast ratio. If you're using `#999` or `#aaa` on white for anything users need to read, it fails WCAG and it's hard to read. Use `#666` minimum for secondary text, `#333` for body.
- **Too much going on** — If a section has more than 5-7 distinct visual elements competing for attention, it's cluttered. Remove decorative elements that don't serve the task. Reduce, then reduce again. The best interfaces feel calm even when they're information-rich.

**For all fixes:**
- Test that changes don't break existing functionality
- Group related fixes into logical changesets

### Step 6: Post-Implementation Review

After implementation, re-read the modified files with fresh eyes. This is NOT a full 15-principle re-audit — it's a focused check for issues that fixes commonly introduce or that the first pass missed. This step typically catches 3-8 additional findings.

**Check for fix-introduced issues:**
- **Semantic-visual sync** — For every ARIA attribute you added (`aria-current`, `aria-expanded`, `aria-checked`, `role`), does a corresponding CSS rule make it visible to sighted users? `aria-current="page"` without a highlighted nav style is half a fix.
- **Specificity conflicts** — Do new CSS rules actually apply, or are they overridden by existing class selectors? Check that generic attribute selectors like `[aria-current="page"]` also target the specific classes used (e.g., `.nav-link[aria-current="page"]`).
- **Design token leaks** — Did any Phase 2 fixes introduce hardcoded values that should use Phase 1 tokens? Search for raw hex colors, pixel values, or font-size literals that bypass the custom properties.
- **Visual balance shifts** — Fixing one element's visual weight (e.g., making a heading bolder) may make adjacent elements feel under-styled. Scan the areas around each fix for newly exposed imbalance.

**Check for first-pass misses:**
- **Complex component patterns** — Tables with grouped rows, multi-level navigation, comparison matrices, accordion nesting. These have alignment and visual hierarchy issues that only become apparent when all the data is styled.
- **State combinations** — Active + hover, selected + disabled, expanded + focused. The first pass often handles individual states; verify that combinations don't produce visual glitches.
- **Content edge cases** — Long text that wraps, empty cells, single-item lists, maximum-length values. Scan the interface for content that stress-tests the layout.

**Fix anything found**, then briefly report the additional changes to the user. If this review surfaces more than 3 significant issues (severity 2+), mention to the user that a follow-up round may be worthwhile — but don't automatically start one, as it's an expensive operation.

---

## Communication

### Warm Start, Then Get to Work
When the skill first triggers, greet the user in a friendly, purpose-focused way. Tell them *what you're going to help them with*, not what internal tools or references you're loading. The user cares about outcomes, not your process.

**Good opening:** "I'll take a close look at your front-end code, find usability issues that might be tripping up your users, and help you fix them."

**Bad opening:** "I'll start by discovering your project's front-end code and loading the evaluation reference."

After the initial greeting, get straight to work — read the code, evaluate it, and present findings. Don't narrate each step ("Now I'm reading file X...", "Now I'm evaluating against principle 7..."). Just do the work and present the results. During the actual evaluation and report, technical language is fine and expected — that's where precision matters.

### Be Educational
When explaining a finding, briefly connect it to the underlying principle. Not a lecture — just enough context for someone to understand *why* this matters.

Every finding must include a concrete **user impact** statement: what real users will experience because of this issue. Think in terms of consequences — confusion, data loss, repeated clicks, abandoned tasks, exclusion of disabled users. "Users will X because of Y" is the pattern. This is what makes the evaluation educational rather than just a checklist.

**Good:** "This form submits with no loading indicator, so users don't know if their action worked. They may click again, causing duplicate submissions. This violates the principle of *visibility of system status* — users should always know what the system is doing."

**Bad:** "Nielsen's first heuristic, H1: Visibility of System Status, as defined in his 1994 paper 'Usability Inspection Methods', states that..."

### Be Specific
Every finding must reference:
- The exact file and line (local projects) or the specific element/section (URL audits)
- The specific principle violated
- Why it matters to users (not abstract — concrete impact)
- A concrete fix (not "improve this" — actual code changes for local, specific recommendations for URL audits)

### Be Honest
- Don't inflate severity to seem thorough
- Don't fabricate issues that don't exist in the code
- Acknowledge when something is well-implemented
- Note when a finding is debatable or context-dependent

---

## When Not to Use This Skill

- Building a new interface from scratch → use `interface-design` or `frontend-design`
- Performance optimization → this focuses on usability, not speed
- Security audit → different domain entirely

---

## Commands

- `/frontend-design-audit` — Full evaluation with discussion (default workflow)
- `/frontend-design-audit:evaluate` — Run evaluation and produce report only (no implementation)
- `/frontend-design-audit:improve` — Jump to implementation (when evaluation already exists)
- `/frontend-design-audit:quick` — Auto-accept: evaluate and implement without discussion

## References

Load reference files progressively to keep token usage efficient:

- `../../../references/heuristics.md` — **Read during evaluation (Steps 1-3).** Complete definitions, what to look for in code (including visual design checks with reference tables), and severity guidance for each of the 15 principles.
- `../../../references/patterns.md` — **Read during implementation (Step 5) only.** Concrete code examples for common accessibility, interaction, and visual design fixes. Skip this file for evaluate-only runs.
