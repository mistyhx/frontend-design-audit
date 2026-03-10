# Heuristic Definitions and Code-Level Violation Patterns

Complete reference for all 15 heuristics. For each: the principle definition, what to look for in code, and how to fix violations.

## Table of Contents
1. [Visibility of System Status](#1-visibility-of-system-status)
2. [Match Between System and Real World](#2-match-between-system-and-real-world)
3. [User Control and Freedom](#3-user-control-and-freedom)
4. [Consistency and Standards](#4-consistency-and-standards)
5. [Error Prevention](#5-error-prevention)
6. [Recognition Over Recall](#6-recognition-over-recall)
7. [Flexibility and Efficiency](#7-flexibility-and-efficiency)
8. [Aesthetic and Minimalist Design](#8-aesthetic-and-minimalist-design)
9. [Error Recovery](#9-error-recovery)
10. [Help and Documentation](#10-help-and-documentation)
11. [Affordances and Signifiers](#11-affordances-and-signifiers)
12. [Structure](#12-structure)
13. [Accessibility](#13-accessibility)
14. [Perceptibility](#14-perceptibility)
15. [Tolerance and Forgiveness](#15-tolerance-and-forgiveness)

---

## 1. Visibility of System Status

**Principle:** The system should always keep users informed about what is going on, through appropriate feedback within reasonable time.

**Sources:** Nielsen H1, Norman (Feedback), Constantine & Lockwood (Feedback)

**Why it matters:** When users don't know what the system is doing, they feel anxious and uncertain. They may repeat actions (double-submitting forms), abandon tasks (thinking the system is broken), or lose trust in the interface.

### What to look for in code

**Loading states:**
- Async operations (fetch, axios, API calls) with no loading indicator
- Button click handlers that trigger requests without disabling the button or showing a spinner
- Page transitions with no skeleton screens or progress indicators
- File uploads with no progress bar

**State indicators:**
- Navigation items with no active/current state styling
- Tabs with no selected state
- Multi-step flows with no step indicator
- Toggles or switches with ambiguous on/off state
- Filter/sort applied but no indication of what's active

**Action feedback:**
- Form submissions with no success/error message
- Save operations with no confirmation
- Delete operations with no feedback
- Copy-to-clipboard with no confirmation

**Visual feedback on interactive states:**
- Hover states that are too subtle (barely visible color change) — users can't tell what's clickable
- Active/pressed states missing — users don't know their click registered
- Selected items that look nearly identical to unselected (insufficient visual contrast between states)
- Buttons that don't visually change when disabled — users can't tell they're inactive
- State transitions with no animation — abrupt changes feel broken (but keep transitions subtle, 150-300ms)

**Perceived performance (Modern Web):**
- No skeleton screens or shimmer placeholders during content loading
- No optimistic UI updates (waiting for server confirmation before showing change)
- Large content shifts as images/embeds load (no reserved space or aspect-ratio)
- No `loading="lazy"` on below-fold images, causing unnecessary initial load time
- Third-party widgets (chat, analytics, embeds) that block rendering with no fallback

### Code patterns to search for
- `fetch(` or `axios.` without corresponding loading state management
- `onClick` handlers that call async functions without UI feedback
- `<nav>` elements without `aria-current` or active class logic
- Form `onSubmit` without success/error state handling

### Severity guidance
- No feedback on destructive action (delete, payment): **4**
- No loading state on data fetch: **3**
- No success confirmation on save: **2-3**
- No active state on navigation: **2**
- Missing loading on fast operations (<200ms): **1**

---

## 2. Match Between System and Real World

**Principle:** The design should speak the users' language, using words, phrases, and concepts familiar to the user rather than system-oriented terms. Follow real-world conventions, making information appear in a natural and logical order.

**Sources:** Nielsen H2, Norman (Mapping)

**Why it matters:** When an interface uses developer jargon or system-oriented language, users feel confused and alienated. They shouldn't need technical knowledge to use your interface.

### What to look for in code

**Language:**
- Error messages exposing technical details (stack traces, error codes, null/undefined references)
- UI labels using developer terminology ("Execute", "Instantiate", "Payload", "Query", "Invoke")
- Button labels that describe system actions rather than user goals ("Submit" vs "Create Account")
- Status messages using system states ("State: PENDING_REVIEW" vs "Waiting for review")

**Conventions:**
- Dates in non-localized or developer format (ISO 8601 raw in UI, Unix timestamps)
- Numbers without proper formatting (no thousands separator, no currency symbol)
- Information organized by code structure rather than user mental model
- Iconography without labels that relies on learned convention

**Mapping:**
- Controls placed far from the content they affect
- Settings organized by system module rather than user task
- Tab order (DOM order) that doesn't match visual layout
- Form fields in illogical order (city before country, end date before start date)

### Code patterns to search for
- Error messages containing: "Error:", "Exception", "null", "undefined", "500", "403", "404"
- Hardcoded date strings or `toISOString()` displayed directly in UI
- `console.error` content surfaced to users
- Labels containing: "execute", "instantiate", "payload", "submit", "query"

### Severity guidance
- Technical error messages shown to end users: **3**
- Developer jargon in primary UI labels: **2-3**
- Non-localized date/number formats: **2**
- Illogical information ordering: **2**

---

## 3. User Control and Freedom

**Principle:** Users often perform actions by mistake. They need a clearly marked "emergency exit" to leave unwanted states without going through an extended process. Support undo and redo.

**Sources:** Nielsen H3, Constantine & Lockwood (Tolerance)

**Why it matters:** Users explore interfaces by trying things. If mistakes are costly or hard to reverse, users become cautious and hesitant instead of confident. Every destructive action without an escape route is a moment of anxiety.

### What to look for in code

- Destructive actions (delete, remove, clear) with no confirmation dialog or undo
- Modal dialogs without close button, Escape key handler, or overlay click-to-dismiss
- Multi-step flows (wizards, checkout) with no back button or cancel option
- Forms that lose all data when accidentally navigating away
- Auto-playing media with no pause/stop control
- Forced flows with no way to skip or exit
- Irreversible state changes triggered by single clicks

### Code patterns to search for
- Delete/remove handlers without confirmation: `onClick={() => delete...}` with no modal
- Modals without: `onKeyDown` for Escape, close button, overlay click handler
- Multi-step components without back/previous functionality
- No `beforeunload` event listener on forms with unsaved changes
- `<video autoplay>` or `<audio autoplay>` without controls

### Severity guidance
- Destructive action with no undo or confirmation: **3-4**
- Modal with no escape mechanism: **3**
- Lost form data on navigation: **3**
- No back button in multi-step flow: **2-3**
- Auto-play without controls: **2**

---

## 4. Consistency and Standards

**Principle:** Users should not have to wonder whether different words, situations, or actions mean the same thing. Follow platform conventions. Be consistent both within your interface and with established external conventions.

**Sources:** Nielsen H4, Norman (Consistency), Constantine & Lockwood (Reuse)

**Why it matters:** Consistency reduces learning. When a blue underlined text is always a link, users don't think about it — they just click. When the same action is called "Save" in one place and "Apply" in another, users hesitate and wonder if they do different things.

### What to look for in code

**Internal consistency:**
- Same action using different button styles across pages
- Same concept called different names in different places ("Delete" vs "Remove" vs "Trash")
- Inconsistent spacing values (not on a consistent grid)
- Different component patterns for the same purpose (different modal implementations)
- Mixed typography (inconsistent font sizes, weights, families)
- Inconsistent color usage (different blues for the same purpose)

**External consistency:**
- Logo not in top-left corner (LTR languages)
- Non-standard navigation placement
- Search not in expected location (top of page)
- Form submission on non-standard key
- Links styled as buttons or buttons styled as links with mismatched behavior
- Non-standard icons for common actions (unusual trash icon, unfamiliar save icon)

### Code patterns to search for
- Multiple button style definitions with no shared base
- Inconsistent CSS custom property usage (some hardcoded, some using variables)
- Mixed spacing values (grep for px/rem values to find inconsistency)
- `<a>` tags used as buttons (`<a onClick>` without href) or `<button>` used for navigation
- Different modal/dialog implementations across the codebase

### Severity guidance
- Inconsistent primary action styling: **2-3**
- Different terms for same concept: **2**
- Non-standard navigation placement: **2**
- Links that behave as buttons: **2**
- Inconsistent spacing: **1-2**

---

## 5. Error Prevention

**Principle:** Even better than good error messages is careful design that prevents problems from occurring. Either eliminate error-prone conditions, or check for them and present confirmation before the user commits.

**Sources:** Nielsen H5, Norman (Constraints)

**Why it matters:** Preventing errors is less frustrating than recovering from them. Good constraints guide users toward success without making them feel restricted. Bad constraints (or lack thereof) lead to preventable mistakes that waste time and erode trust.

### What to look for in code

- No inline validation — errors only appear after form submission
- Free-text input where constrained input would prevent errors (text field for dates instead of date picker)
- No confirmation dialog for irreversible or high-stakes actions
- No character count on length-limited fields
- Double-submission possible (submit button not disabled during request)
- No input masks for formatted data (phone numbers, credit cards)
- Allowing invalid state combinations (end date before start date, negative quantities)
- No `maxlength`, `min`, `max`, `pattern` attributes on constrained inputs

**Mobile form optimization:**
- No `inputmode` attribute for numeric/email/tel inputs (forces wrong mobile keyboard)
- No `autocomplete` attributes for common fields (name, email, address, credit card)
- Tiny tap targets on form elements (below 44x44px minimum)
- Labels that disappear on focus (placeholder-only inputs) — users forget what field they're in

### Code patterns to search for
- Form validation only in `onSubmit` handler (no inline/onChange validation)
- `<input type="text">` for dates, emails, phone numbers, numbers
- No `disabled` attribute management on submit buttons during async operations
- Missing `pattern`, `maxlength`, `min`, `max` attributes on inputs
- Related date/number fields with no cross-validation

### Severity guidance
- No confirmation on irreversible action (delete account, payment): **4**
- Double-submission possible on payment/data-creation form: **3**
- No inline validation on complex form: **2**
- Text input where date picker would prevent errors: **2**
- No character count on limited field: **1**

---

## 6. Recognition Over Recall

**Principle:** Minimize the user's memory load by making elements, actions, and options visible. Users should not have to remember information from one part of the interface to another.

**Sources:** Nielsen H6, Norman (Discoverability)

**Why it matters:** Human working memory is limited. If your interface requires users to remember information (IDs, codes, selections from previous screens), it creates cognitive load and errors. Good interfaces show information in context so users can recognize rather than recall.

### What to look for in code

- Empty states with no guidance (blank screens with no call-to-action)
- Form fields with no labels, hints, or placeholder examples
- Requiring users to remember codes/IDs from previous screens
- Hidden navigation requiring memorization of paths
- No search, autocomplete, or type-ahead on large datasets
- No recent items or history features
- No breadcrumbs in deep navigation hierarchies
- Tooltips as the only source of critical information (hover-dependent)

### Code patterns to search for
- Empty container elements with no fallback content
- `<input>` without associated `<label>` or `placeholder`
- Pages displaying raw IDs without human-readable names
- No `<nav aria-label="Breadcrumb">` in deep page hierarchies
- No `<datalist>` or autocomplete component on search/filter inputs

### Severity guidance
- Empty state with no guidance on primary screen: **3**
- Required memorization of IDs across screens: **3**
- No search on large dataset: **2-3**
- Missing breadcrumbs in deep hierarchy: **2**
- Form field with no hint or example: **1-2**

---

## 7. Flexibility and Efficiency

**Principle:** Shortcuts — hidden from novice users — can speed up interaction for expert users, so the design can cater to both inexperienced and experienced users. Allow users to tailor frequent actions.

**Sources:** Nielsen H7, Universal Design (Flexibility, Equity)

**Why it matters:** Different users have different expertise levels and preferences. An interface that only works one way forces everyone into the same interaction pattern, even when faster alternatives would better serve experienced users.

### What to look for in code

- No keyboard shortcuts for common actions in productivity interfaces
- No bulk/batch operations (must handle items one by one)
- No way to save preferences or defaults
- Only one way to complete any given task (mouse-only, no keyboard path)
- No power-user features (advanced search, filters, custom views)
- No way to customize the interface (column visibility, layout preferences)

**User preference respect:**
- No `prefers-reduced-motion` media query — animations play regardless of user system setting
- No `prefers-color-scheme` support — ignoring user's dark/light mode preference
- No `prefers-contrast` consideration for users who need higher contrast
- Font sizes in fixed `px` instead of `rem` — ignoring user's browser font size preference

### Code patterns to search for
- No keyboard event listeners (`onKeyDown`, `onKeyPress`) for common actions
- List/table components without select-all or bulk action capability
- No `localStorage` or preference persistence
- Interactive elements without keyboard focus support (`tabIndex`, `onKeyDown`)
- No filter/sort functionality on data-heavy views

### Severity guidance
- No keyboard navigation in productivity app: **2-3**
- No bulk operations on repetitive tasks: **2**
- Single interaction path for everything: **2**
- No saved preferences: **1-2**

---

## 8. Aesthetic and Minimalist Design

**Principle:** Interfaces should not contain information that is irrelevant or rarely needed. Every extra element competes with relevant information and diminishes its relative visibility.

**Sources:** Nielsen H8, Constantine & Lockwood (Simplicity)

**Why it matters:** Clutter is the enemy of usability. When everything competes for attention, nothing stands out. Users can't find what they need when it's buried among things they don't. Simplicity isn't about removing features — it's about organizing them so the right things are prominent at the right time.

### What to look for in code

- Cluttered layouts with too many competing elements at the same visual weight
- Excessive use of colors, fonts, and decorative elements
- All options/fields shown at once instead of progressive disclosure
- Walls of text with no visual hierarchy
- Animations that distract rather than inform
- Dense data tables with no ability to customize visible columns
- Multiple calls-to-action competing at the same prominence level
- Information shown that users don't need for their current task

**Typography hierarchy (visual):**
- Flat type scale — headings, body text, and labels are all similar sizes, so nothing guides the eye
- Too many font sizes (more than 4-5 distinct sizes creates visual chaos) or too few (everything looks the same)
- Missing font weight variation — bold for headings/labels, regular for body, but the code uses the same weight throughout
- Body text below 14px/0.875rem — uncomfortable to read, especially for longer content
- Line height too tight (below 1.4 for body text) — text feels cramped and hard to scan
- Labels or metadata text that's so small it requires effort to read (below 0.7rem)

**Reference — healthy type scale:**

| Level | Purpose | Size | Weight |
|-------|---------|------|--------|
| H1 | Page title | 1.5-2.5rem | 700 |
| H2 | Section heading | 1.2-1.5rem | 600-700 |
| H3 | Card/subsection title | 1-1.2rem | 500-600 |
| Body | Main content | 0.875-1rem | 400 |
| Caption | Secondary info | 0.75-0.85rem | 400-500 |

**Spacing and whitespace (visual):**
- Insufficient spacing between major sections — everything runs together, no visual breathing room
- Uniform spacing everywhere — same gap between a section heading and its content as between two unrelated sections (violates Gestalt proximity)
- Padding inside cards/containers that's too tight — content feels cramped against borders
- No clear spatial hierarchy — the eye can't distinguish section boundaries
- Inconsistent spacing values (margins/padding vary randomly rather than following a scale like 4/8/16/24/32px)

**Reference — spacing scale (base 4px or 8px):**

| Token | Value | Use |
|-------|-------|-----|
| xs | 4px | Icon to label |
| sm | 8px | Within component |
| md | 16px | Between related components |
| lg | 24-32px | Between sections |
| xl | 48-64px | Between major regions |

**Visual weight and emphasis (visual):**
- Primary CTA button has the same visual weight as secondary buttons — users can't tell what the main action is
- All text elements at the same size/weight — nothing draws the eye first
- Key metrics or data points that don't stand out from surrounding content
- Too many visual accents (borders, shadows, colors) competing for attention

**Color usage (visual):**
- More than 5-6 distinct colors in active use — creates visual noise
- Accent color used too frequently (loses its emphasis effect)
- No neutral palette to contrast with accent colors — everything feels "loud"
- Status/category colors that are too similar to each other

**Information density — target by interface type:**

| Type | Density | Reason |
|------|---------|--------|
| Dashboard | Medium-high | Users scan metrics; compact expected |
| Form | Low-medium | Each field needs space; dense forms cause errors |
| Data table | High | Power users expect density |
| Marketing | Low | One message at a time; generous whitespace |
| Settings | Medium | Organized sections with clear grouping |

### Code patterns to search for
- Pages with many elements at the same heading level (all h3, all h4)
- No collapsible/expandable sections for secondary content
- More than 3 distinct font sizes, more than 5 colors in active use
- No progressive disclosure patterns (details/summary, tabs, accordions)
- Many equally-prominent buttons/links on one screen
- CSS `font-size` values: check if there's a clear hierarchy (e.g., h1 > h2 > h3 > body > small)
- CSS `font-weight` values: check if weight varies meaningfully (headings bold, body regular)
- CSS `margin`/`padding` values: check if spacing follows a consistent scale
- CSS `line-height`: check if body text has at least 1.4-1.5

### Severity guidance
- Overwhelming clutter on primary task screen: **3**
- Flat type scale — no visual hierarchy between heading and body: **2-3**
- No progressive disclosure for complex form: **2**
- Competing calls-to-action: **2**
- Cramped spacing inside cards or between sections: **2**
- Inconsistent spacing values: **1-2**
- Decorative elements that don't support the task: **1**

---

## 9. Error Recovery

**Principle:** Error messages should be expressed in plain language (no codes), precisely indicate the problem, and constructively suggest a solution. Errors should be visually distinct and easy to find.

**Sources:** Nielsen H9, Constantine & Lockwood (Feedback), Norman (Feedback)

**Why it matters:** When something goes wrong, users need to understand what happened and how to fix it. Cryptic error messages ("Error 500", "Something went wrong") leave users helpless. Good error handling turns a frustrating moment into a guided recovery.

### What to look for in code

- Generic error messages ("Something went wrong", "Invalid input", "Error occurred")
- Error messages showing technical details (stack traces, error codes, exception types)
- Errors displayed far from their source (only at the top of a form, not near the field)
- No visual distinction for error states (no red border, no error icon)
- Error messages that auto-dismiss before users can read them
- No recovery suggestion ("Password is too short" vs "Password must be at least 8 characters")
- HTTP status codes shown directly to users (raw 404, 500 pages)
- Errors that clear user input

**Offline and network error states:**
- No handling for network disconnection (fetch fails silently or shows cryptic error)
- No retry mechanism for failed requests
- No offline indicator or cached content fallback
- API timeout with no user-facing feedback

### Code patterns to search for
- Catch blocks with generic messages: `catch(e) { setError("Something went wrong") }`
- Error rendering far from the input: error summary at top of form only
- No `aria-invalid="true"` on invalid form fields
- No `role="alert"` or `aria-live` on error messages
- Toast/notification auto-dismiss with short timeouts on errors (errors should persist)
- Error handlers that reset form state

### Severity guidance
- Generic error with no recovery path on critical flow: **3-4**
- Technical error messages shown to users: **3**
- Error message far from source field: **2**
- Auto-dismissing error messages: **2**
- No visual distinction for error state: **2**

---

## 10. Help and Documentation

**Principle:** It's best if the system can be used without documentation, but help may be necessary. Any help should be easy to search, focused on the user's task, list concrete steps, and not be too large.

**Sources:** Nielsen H10

**Why it matters:** Users don't read manuals. But when they're stuck, they need contextual guidance that's easy to find and immediately useful. The best help appears right where users need it, in the moment they need it.

### What to look for in code

- No onboarding or first-use guidance for complex features
- No tooltips or contextual help on specialized or non-obvious fields
- Help/documentation that's only available externally (separate website)
- No inline help for error recovery
- Complex features with no examples or templates
- No "what's this?" affordance on unfamiliar UI elements

### Code patterns to search for
- Complex forms with no `aria-describedby` help text
- No tooltip components on specialized inputs
- No onboarding/tour components for first-time users
- No example data or templates for complex inputs
- No help icon or link near complex features

### Severity guidance
- No guidance for complex critical feature: **3**
- No tooltips on specialized fields: **1-2**
- No onboarding for complex product: **2**
- No searchable help: **1-2**

---

## 11. Affordances and Signifiers

**Principle:** Interactive elements must look interactive. The design should communicate how things are meant to be used (affordances) and where actions should take place (signifiers).

**Sources:** Norman (Affordances, Signifiers)

**Why it matters:** In physical design, a door handle tells you to pull, a button tells you to press. Digital interfaces have no inherent physical properties — we must create affordances through visual design. When clickable things don't look clickable, users miss them. When non-clickable things look clickable, users get frustrated.

### What to look for in code

**Missing affordances:**
- Clickable elements without visual clickability cues (no hover state, no cursor change)
- Flat buttons indistinguishable from labels or plain text
- Clickable cards with no visual indicator of interactivity
- Links that look like plain text (no color change, no underline, no hover)
- Draggable items with no drag handle or visual cue

**False affordances:**
- Non-clickable elements that look clickable (underlined text that's not a link)
- Decorative elements that appear interactive (colored text that isn't a link)
- Disabled elements that look enabled

**Missing signifiers:**
- Icon-only buttons with no label or tooltip
- Form fields relying solely on placeholder text (which disappears on input)
- Scrollable areas with no scroll indicator
- Drag-and-drop zones with no visual indication
- Missing breadcrumbs in deep navigation

**Visual weight of actions (visual):**
- Primary CTA button too small or visually weak — doesn't stand out from surrounding elements (Fitts's Law: important targets should be large and visually distinct)
- Secondary buttons with the same visual weight as primary — users can't tell which action is "the main one"
- Destructive actions (delete, remove) styled the same as safe actions — no red/warning visual cue
- Ghost/text buttons used for primary actions — these should be reserved for secondary or tertiary actions
- Interactive cards where the clickable area is smaller than the visual card boundary — the whole card should be the click target

**Touch target sizing (Mobile):**
- Interactive elements smaller than 44x44px (buttons, links, checkboxes)
- Tap targets too close together with insufficient spacing (at least 8px gap)
- Hover-dependent interactions with no touch alternative (tooltips, dropdown menus)
- Small close buttons on modals/dialogs that are hard to tap accurately

### Code patterns to search for
- Clickable elements without `cursor: pointer` in CSS
- Missing `:hover` and `:focus` state styles on interactive elements
- `<div>` or `<span>` with `onClick` but no button role or visual treatment
- Icon buttons without `aria-label` or visible text
- `<input>` elements without associated visible `<label>`
- No CSS for scrollbar or scroll shadow/fade indicators

### Severity guidance
- Primary action not visually identifiable as clickable: **3**
- Non-interactive element that looks interactive: **3**
- Icon-only button in primary workflow: **2-3**
- Missing hover states: **2**
- No scroll indicator on scrollable area: **1-2**

---

## 12. Structure

**Principle:** Organize the user interface purposefully, in meaningful ways. Put related things together, separate unrelated things, differentiate dissimilar things, make similar things resemble one another.

**Sources:** Constantine & Lockwood (Structure)

**Why it matters:** Good structure makes complex interfaces navigable. Users build mental models of where things are — consistent, logical grouping helps those models form quickly. Poor structure forces users to hunt for features and constantly relearn the layout.

### What to look for in code

- Related actions scattered across different sections of the page
- No visual grouping of related form fields (no fieldsets, no visual containers)
- Flat navigation with no logical hierarchy
- Dashboard widgets with no meaningful spatial organization
- Mixed content types at the same level (actions mixed with information, settings mixed with content)
- Information architecture that follows code/database structure rather than user tasks

**Visual grouping and layout (visual — Gestalt principles):**
- **Proximity violations** — Related items (e.g., a label and its input, a card title and its metadata) have the same spacing as unrelated items. Users can't tell what belongs together. Fix: tighten spacing within groups, increase spacing between groups.
- **Similarity violations** — Items that serve the same function look different (inconsistent card styles, different button treatments for the same action type). Fix: make same-function elements visually identical.
- **No section boundaries** — Multiple content areas run together with no visual separator (border, background color change, or increased spacing). Users can't tell where one section ends and another begins.
- **Missing container hierarchy** — Content floats in open space without visual containers (cards, bordered regions, background fills) to group it. This is especially problematic in dashboards and settings pages.
- **Alignment issues** — Elements not snapping to a consistent grid. Left edges of content in different sections don't align. This creates a subtle but real sense of visual disorder.

**Mobile navigation and layout:**
- Desktop navigation pattern that doesn't adapt meaningfully for mobile (just shrunk down)
- Hamburger menu with no indication of what's inside
- Important actions buried behind multiple taps on mobile
- Content that requires horizontal scrolling on mobile viewports
- Sidebar navigation with no mobile collapse/drawer pattern

### Code patterns to search for
- Form elements without `<fieldset>` and `<legend>` for related groups
- No section or region landmarks (`<section>`, `<aside>`, `role="region"`)
- All content at the same DOM depth with no logical grouping
- Navigation with no hierarchy (all items at same level, no grouping)
- Dashboard layouts with no clear grid or organizational principle

### Severity guidance
- Critical task elements scattered across different areas: **3**
- No grouping of related form fields: **2**
- Flat navigation in complex app: **2**
- Disorganized dashboard layout: **2**

---

## 13. Accessibility

**Principle:** The design should be usable by people with the widest possible range of abilities, in the widest possible range of situations. Provide equitable access regardless of disability, device, or context.

**Sources:** Universal Design (Equity, Ease, Comfort), WCAG 2.1

**Why it matters:** Accessibility isn't optional — it's both an ethical responsibility and, in many jurisdictions, a legal requirement. Beyond compliance, accessible design benefits everyone: captions help in noisy environments, keyboard navigation helps power users, good contrast helps in bright sunlight.

### What to look for in code

**Text alternatives:**
- Images without `alt` attribute
- Icon buttons without `aria-label`
- SVG icons without accessible names
- Decorative images without `alt=""`

**Color and contrast:**
- Text with insufficient contrast against its background (4.5:1 for normal text, 3:1 for large)
- Information conveyed only through color (red/green for status without icons or text)
- Focus indicators with insufficient contrast

**Keyboard accessibility:**
- Interactive elements not reachable by keyboard (Tab)
- No visible focus indicator (`:focus-visible` styles)
- Focus trapping in modals not implemented
- Custom components without keyboard interaction (`<div onClick>` without `role="button"` and `tabIndex="0"`)

**Semantic structure:**
- Missing heading hierarchy (skipping levels, no `<h1>`)
- No landmark regions (`<main>`, `<nav>`, `<aside>`, `<header>`, `<footer>`)
- Missing `<label>` for form inputs
- Using `<div>` or `<span>` for interactive elements without ARIA roles
- No language attribute on `<html>` element

**Dynamic content:**
- Status updates not announced (`aria-live` regions missing)
- Dialog/modal content not announced (no `role="dialog"`, no `aria-modal`)
- Form errors not associated with fields (`aria-describedby`, `aria-errormessage`)
- Toast/notification messages not using `role="alert"` or `aria-live="polite"`
- Route changes in SPAs not announcing new page title to screen readers

**Responsive and viewport:**
- `user-scalable=no` or `maximum-scale=1` in viewport meta (blocks pinch-to-zoom)
- Fixed-width layouts that overflow on small screens
- Content that becomes inaccessible or overlaps at different viewport sizes
- Media queries that miss common breakpoints (tablet portrait ~768px is frequently skipped)
- Text that doesn't reflow properly when user zooms to 200%

### Code patterns to search for
- `<img>` without `alt`
- `<button>` or clickable element containing only `<svg>` without `aria-label`
- `<div onClick>` or `<span onClick>` (missing semantic role)
- `<input>` without associated `<label>` (check `for`/`id` matching or wrapping `<label>`)
- No `:focus-visible` or `:focus` styles in CSS
- `<html>` without `lang` attribute
- Heading elements that skip levels (`<h1>` to `<h3>`)
- No `role="dialog"` on modal containers
- Color values that may fail contrast (light grays on white, etc.)

### Severity guidance
- Missing alt on informative images: **4**
- No keyboard access to primary features: **4**
- Color-only information (no redundant cue): **3**
- Missing form labels: **3**
- Insufficient contrast on body text: **3**
- Missing landmark regions: **2**
- Missing lang attribute: **2**
- No focus-visible styles: **2-3**

---

## 14. Perceptibility

**Principle:** The system should communicate necessary information effectively to the user, regardless of ambient conditions or the user's sensory abilities. The current state should always be perceivable.

**Sources:** Norman (Feedback), Universal Design (Perceptible Information)

**Why it matters:** Users need to perceive what state the system is in at any moment. Is this toggle on or off? Is this item selected? Is the form valid? Am I on the right page? When state is ambiguous, users make errors based on incorrect assumptions.

### What to look for in code

- Toggle/switch components with ambiguous on/off states
- Selected items that don't look visually distinct from unselected
- Active/inactive states that rely only on subtle color differences
- Status information that's hard to find or requires scrolling to see
- Information communicated only through position (no redundant text/icon cue)
- State changes that happen without visual feedback
- Insufficient contrast between different states

**Visual hierarchy and scannability (visual):**
- Content that requires reading every word instead of scanning — no bold labels, no size differentiation, no visual anchors for the eye to land on
- Key data points (numbers, metrics, statuses) at the same visual weight as surrounding text — important values should be larger, bolder, or color-accented
- Long text blocks with no visual breaks (subheadings, bullet points, bold key phrases)
- Metadata (dates, tags, assignees) that's visually louder than the primary content it describes
- Secondary information competing visually with primary — e.g., timestamps at the same size as event descriptions in an activity feed

**User preference awareness:**
- Animations that ignore `prefers-reduced-motion` — can trigger vestibular disorders
- No dark mode or ignoring `prefers-color-scheme` when the design supports it
- Visual feedback that relies solely on motion (no static alternative)
- Transitions/animations that are purely decorative and can't be disabled

### Code patterns to search for
- Toggle/switch components — check if on/off is visually unambiguous
- List items with selection — check if selected state is visually clear
- Status badges/pills — check if they rely on color alone (no icon or text)
- Multi-state components — check if each state is visually distinct

### Severity guidance
- Ambiguous on/off state on critical control: **3**
- Status relying on color alone: **2-3**
- Subtle difference between states: **2**
- State change without visual feedback: **2-3**

---

## 15. Tolerance and Forgiveness

**Principle:** The design should be flexible and tolerant, reducing the cost of mistakes. Accept varied input formats, preserve user work, and allow easy recovery from errors.

**Sources:** Constantine & Lockwood (Tolerance), Universal Design (Tolerance for Error)

**Why it matters:** Users are human. They make typos, misclick, navigate away by accident, and enter data in unexpected formats. A tolerant system accommodates these realities gracefully instead of punishing them.

### What to look for in code

- Strict input format requirements with no flexibility (exact date format, exact phone format)
- Errors that wipe form data and force restart
- Single-character typo invalidating an entire form
- No auto-save or draft saving on long forms
- No "undo" capability at the application level
- Search that requires exact matches (no fuzzy matching, no "did you mean?")
- Accidental navigation losing all progress

### Code patterns to search for
- Input validation with strict regex that rejects valid variations
- Form error handling that resets state (`setState({})` or `form.reset()` on error)
- Long forms without periodic auto-save (no `localStorage` or draft API persistence)
- No `beforeunload` listener for dirty form state
- Search implementation without fuzzy matching or suggestions

### Severity guidance
- Error that destroys all user input: **4**
- No auto-save on long-form entry: **3**
- Strict format rejection without guidance: **2-3**
- No undo on important actions: **3**
- Exact-match-only search on large dataset: **2**
