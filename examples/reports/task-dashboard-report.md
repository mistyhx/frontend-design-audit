## UX Design Audit Report

**Scope:** Full evaluation of a single-page project management dashboard with Kanban board, stats overview, sidebar navigation, activity feed, add-task modal, and filter/sort dropdowns.
**Source:** `test-sites/task-dashboard.html`
**Interface type:** Project management dashboard (Kanban board)

### How to Read This Report
Findings are rated on a 0-4 severity scale (4 = users can't complete tasks,
1 = cosmetic only). Each finding references an established usability principle.
Start from the top -- the most impactful issues are listed first.

### Summary

| Severity | Count |
|----------|-------|
| 4 - Catastrophe | 1 |
| 3 - Major | 5 |
| 2 - Minor | 11 |
| 1 - Cosmetic | 5 |
| **Total findings** | **22** |

### Quick Wins
The highest-impact issues that are also straightforward to fix:
1. Skip navigation link missing (Severity 3) -- Add a visually hidden skip link as the first focusable element
2. Task description contrast too low (Severity 3) -- Change `.task-desc` color from `#888` to `#636363`
3. No active/pressed state on buttons (Severity 2) -- Add `:active` CSS rules
4. Stat card label-value hierarchy too flat (Severity 2) -- Make values dramatically larger/bolder, labels subordinate

### Findings

#### [Severity 4] F1: Drag-and-drop is the only way to move tasks between columns
- **Principle:** Flexibility and Efficiency (#7), Accessibility (#13)
- **Location:** `task-dashboard.html:691-736` (drag-and-drop JS), lines 747-755 (keyboard handler)
- **Issue:** Task cards can only be moved between Kanban columns via mouse drag-and-drop. The Enter/Space keyboard handler (line 750) merely shows a toast saying "drag to move between columns" -- it does not provide any mechanism to move cards. This makes the core workflow completely inaccessible to keyboard and assistive technology users.
- **User impact:** Users who rely on keyboards or screen readers cannot perform the primary workflow of this application -- moving tasks through stages (To Do -> In Progress -> In Review -> Done). This blocks a significant user population from the tool's core purpose. Frequent, high-impact, persistent.
- **Fix:** When a task card is focused and the user presses Enter or Space, present a context menu listing available columns. The user picks a destination with arrow keys and confirms with Enter, moving the card. Alternatively, support Ctrl+Arrow Left/Right to move cards between adjacent columns.

---

#### [Severity 3] F2: No skip navigation link
- **Principle:** Accessibility (#13)
- **Location:** `task-dashboard.html:261` (top of `<body>`)
- **Issue:** No "Skip to main content" link exists. Keyboard users must Tab through all 9+ sidebar navigation links before reaching the dashboard content on every page load.
- **User impact:** Keyboard and screen reader users face significant friction on every visit, requiring dozens of Tab presses to reach the main content. Recurring annoyance that degrades the experience for assistive technology users.
- **Fix:** Add a visually hidden skip link as the first element in `<body>`: `<a href="#main-content" class="skip-link">Skip to main content</a>`. Add `id="main-content"` to the `<main>` element. Style to appear on focus.

---

#### [Severity 3] F3: Task description text has insufficient contrast
- **Principle:** Accessibility (#13), Perceptibility (#14)
- **Location:** `task-dashboard.html:140` (`.task-card .task-desc { color: #888; }`)
- **Issue:** Task description text uses `color: #888` on a white background, producing a contrast ratio of ~3.5:1. This fails WCAG 2.1 AA (4.5:1 for normal text). The text is 0.8rem (~12.8px), well within normal-text thresholds.
- **User impact:** Users in bright environments, older users, or anyone with mild visual impairments will struggle to read task descriptions -- the contextual content that informs prioritization decisions.
- **Fix:** Change `.task-card .task-desc` color to `#636363` (~5.9:1 ratio) for compliant, readable descriptions.

---

#### [Severity 3] F4: Dropdown menus lack keyboard navigation
- **Principle:** Accessibility (#13), Flexibility and Efficiency (#7)
- **Location:** `task-dashboard.html:341-357` (Filter/Sort HTML), lines 758-811 (JS)
- **Issue:** Filter and Sort dropdown menus use `role="menu"` and `role="menuitem"` but do not implement the expected keyboard interaction. Users cannot navigate within the open menu with Arrow keys, cannot close it with Escape, and cannot activate items with Enter/Space from keyboard focus.
- **User impact:** Keyboard users cannot filter or sort tasks. The ARIA roles create expectations that are not fulfilled, actively misleading screen reader users.
- **Fix:** Add keydown handlers for ArrowDown/ArrowUp (move focus), Escape (close, return focus to trigger), and Enter/Space (activate item). Move focus to the first menu item when the dropdown opens.

---

#### [Severity 3] F5: Task descriptions too small at 0.8rem with tight line-height
- **Principle:** Aesthetic and Minimalist Design (#8), Perceptibility (#14)
- **Location:** `task-dashboard.html:140` (`.task-card .task-desc`)
- **Issue:** Task descriptions are `font-size: 0.8rem` (~12.8px), below the recommended minimum of 14px (0.875rem). Combined with `line-height: 1.4`, descriptions are physically uncomfortable to read, especially on standard-resolution displays.
- **User impact:** Users squint or lean closer to read task descriptions. On mobile or standard-DPI screens, this small text with tight line-height strains the eyes. Users may skip reading descriptions entirely, missing important task context.
- **Fix:** Increase to `font-size: 0.85rem` with `line-height: 1.5` for comfortable readability while keeping the visual hierarchy below the task title.

---

#### [Severity 3] F6: Modal form has no inline validation or error styling
- **Principle:** Error Prevention (#5), Error Recovery (#9)
- **Location:** `task-dashboard.html:536-567` (add task form)
- **Issue:** The "Add New Task" form relies solely on the browser's native `required` validation. There is no visual error state (red border, `aria-invalid`), no custom error message, and no validation feedback. The browser's default tooltip is the only indicator.
- **User impact:** Users who submit without a title see a browser-default tooltip that varies by browser and may be missed. There is no persistent error message or visual cue guiding correction. The experience feels unfinished compared to the rest of the interface.
- **Fix:** Add inline validation: on submission, check required fields and show styled error messages with `aria-invalid="true"` and `aria-describedby`. Style invalid inputs with a red border.

---

#### [Severity 2] F7: No `aria-current="page"` on active navigation link
- **Principle:** Visibility of System Status (#1), Accessibility (#13)
- **Location:** `task-dashboard.html:270` (`.active` nav link)
- **Issue:** The active navigation item uses a `.active` CSS class but lacks `aria-current="page"`. Screen readers cannot programmatically convey which page the user is on.
- **User impact:** Screen reader users cannot determine their current page location without contextual clues, adding cognitive effort for navigation.
- **Fix:** Add `aria-current="page"` to the active link.

---

#### [Severity 2] F8: Sidebar navigation lists have no `aria-label` for differentiation
- **Principle:** Accessibility (#13), Structure (#12)
- **Location:** `task-dashboard.html:269-285` (three `<ul class="sidebar-nav">`)
- **Issue:** Three separate `<ul>` navigation lists exist for "Main", "Projects", and "Team" but none has an `aria-label`. Screen readers present three identical, unlabeled lists.
- **User impact:** Screen reader users hearing three indistinguishable lists cannot quickly jump to the desired section.
- **Fix:** Add `aria-label` to each list matching their section headers.

---

#### [Severity 2] F9: Stat card label-value visual hierarchy too flat
- **Principle:** Aesthetic and Minimalist Design (#8), Perceptibility (#14)
- **Location:** `task-dashboard.html:95-96` (stat card CSS)
- **Issue:** While stat values are 2rem/700 weight and labels are 0.8rem uppercase, the visual hierarchy could be stronger. The stat labels at `color: #666` are darker than they need to be, and stat values lack the dramatic contrast that makes dashboard metrics pop at a glance. The `.change` text at 0.85rem is close to the label size, muddling the hierarchy.
- **User impact:** Users scanning the dashboard take slightly longer to absorb key metrics because the label-value-change hierarchy doesn't create strong enough visual tiers.
- **Fix:** Make labels more subordinate: `font-size: 0.7rem; color: #888; letter-spacing: 0.08em`. Make values even more prominent: `font-size: 2.25rem; color: #1a1a2e; line-height: 1.1`. Reduce change text: `font-size: 0.8rem`.

---

#### [Severity 2] F10: No active/pressed (`:active`) state on buttons
- **Principle:** Visibility of System Status (#1), Affordances and Signifiers (#11)
- **Location:** `task-dashboard.html:109-112` (button CSS)
- **Issue:** Primary and secondary buttons have hover states but no `:active` state. When users click a button, there is no visual feedback that the click registered.
- **User impact:** Users may double-click buttons because they are unsure the first click registered. The interface feels subtly unresponsive.
- **Fix:** Add `:active` styles with `transform: translateY(1px)` and a darker background.

---

#### [Severity 2] F11: No disabled state styling for buttons
- **Principle:** Affordances and Signifiers (#11), Visibility of System Status (#1)
- **Location:** `task-dashboard.html:109-112` (button CSS)
- **Issue:** The "Create Task" button is disabled via JS during submission (line 625), but no CSS exists for the `:disabled` state. Disabled buttons look identical to enabled ones.
- **User impact:** During form submission, users see no visual change and may think the interface is frozen.
- **Fix:** Add `.btn-primary:disabled, .btn-secondary:disabled { opacity: 0.5; cursor: not-allowed; }`.

---

#### [Severity 2] F12: Stat cards lack semantic grouping for screen readers
- **Principle:** Perceptibility (#14), Accessibility (#13)
- **Location:** `task-dashboard.html:314-334` (stats cards)
- **Issue:** Stat cards use generic `<div>` elements. The relationship between "Total Tasks", "24", and "+3 this week" is purely visual. Screen readers present them as disconnected text.
- **User impact:** Screen reader users hear fragmented text and must mentally assemble the label-value relationships for the dashboard's most important data.
- **Fix:** Add `role="group"` and `aria-label` to each stat card.

---

#### [Severity 2] F13: Task tag text at 0.7rem is below comfortable reading threshold
- **Principle:** Perceptibility (#14), Aesthetic and Minimalist Design (#8)
- **Location:** `task-dashboard.html:143` (`.task-tag { font-size: 0.7rem; }`)
- **Issue:** Task category tags at `font-size: 0.7rem` (~11.2px) with small padding (3px 8px) are at the lower boundary of readability.
- **User impact:** Users strain to read category labels -- key information for scanning and organizing work.
- **Fix:** Increase to `font-size: 0.75rem` with padding `4px 10px`.

---

#### [Severity 2] F14: Activity feed dots convey category through color alone
- **Principle:** Perceptibility (#14), Accessibility (#13)
- **Location:** `task-dashboard.html:499-523` (activity items)
- **Issue:** Activity feed items use colored dots (green/blue/yellow) as the sole visual categorization. While `aria-hidden="true"` hides them from screen readers, sighted color-blind users lose the scanning benefit.
- **User impact:** Users with color vision deficiency (~8% of males) cannot distinguish activity categories by their dot colors.
- **Fix:** Replace colored dots with small distinct icons/symbols: checkmark for completions, arrow for movements, speech bubble for comments.

---

#### [Severity 2] F15: Spacing between major page sections lacks clear hierarchy
- **Principle:** Aesthetic and Minimalist Design (#8), Structure (#12)
- **Location:** `task-dashboard.html:90,102,221` (`.stats`, `.board-header`, `.activity`)
- **Issue:** Stats, board header, board, and activity feed use similar padding/margin values (1-2rem). There is no strong differentiation between within-section and between-section spacing. Sections flow together rather than appearing as distinct blocks.
- **User impact:** Users scanning the page cannot instantly identify section boundaries. The eye lacks clear resting points between major content areas.
- **Fix:** Increase between-section gaps: stats section gets a subtle bottom border or extra margin-bottom (2.5rem), board section gets more generous top/bottom margins, activity section gets clear top separation.

---

#### [Severity 2] F16: Column header text at 0.9rem doesn't anchor sections strongly
- **Principle:** Aesthetic and Minimalist Design (#8), Perceptibility (#14)
- **Location:** `task-dashboard.html:121` (`.column-header h3 { font-size: 0.9rem; }`)
- **Issue:** Kanban column headers at `0.9rem` are only slightly larger than task card title text (also `0.9rem`). Column headers should be clear visual anchors that organize the board, but they have the same size as the content they contain.
- **User impact:** When scanning the board, users' eyes don't immediately land on column headers because they blend with task titles. This slows navigation within the board.
- **Fix:** Increase column header to `font-size: 0.95rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.04em` to clearly differentiate from task titles.

---

#### [Severity 2] F17: Board section heading "Sprint Board" lacks clear visual separation from stats
- **Principle:** Structure (#12), Aesthetic and Minimalist Design (#8)
- **Location:** `task-dashboard.html:102` (`.board-header { padding: 0 2rem; margin-bottom: 1rem; }`)
- **Issue:** The "Sprint Board" heading sits between the stats section and the board with minimal visual separation. There is no divider, background change, or generous margin to clearly mark the transition from overview metrics to the task board.
- **User impact:** Users scanning the page may not immediately perceive "Sprint Board" as a major section boundary. The stats flow directly into the board header.
- **Fix:** Add `margin-top: 0.5rem; padding-top: 1.5rem; border-top: 1px solid #e5e7eb` to `.board-header` to create a clear visual break.

---

#### [Severity 1] F18: Completed task cards use inline opacity style
- **Principle:** Consistency and Standards (#4)
- **Location:** `task-dashboard.html:471,478,485` (Done column cards)
- **Issue:** Completed tasks use `style="opacity: 0.7;"` inline rather than a CSS class, inconsistent with the rest of the styling approach.
- **User impact:** Minimal direct impact. Code quality and maintainability concern.
- **Fix:** Create `.task-card.completed { opacity: 0.7; }` and apply as a class.

---

#### [Severity 1] F19: Notification button touch target too small
- **Principle:** Affordances and Signifiers (#11), Accessibility (#13)
- **Location:** `task-dashboard.html:79-81` (`.notification-btn { padding: 4px; }`)
- **Issue:** The notification button's `padding: 4px` makes its touch target smaller than the recommended 44x44px minimum.
- **User impact:** Mobile users may mis-tap the notification button.
- **Fix:** Add `min-width: 44px; min-height: 44px; display: flex; align-items: center; justify-content: center`.

---

#### [Severity 1] F20: Task priority text at 0.75rem could be larger
- **Principle:** Perceptibility (#14)
- **Location:** `task-dashboard.html:156` (`.task-priority { font-size: 0.75rem; }`)
- **Issue:** Priority indicators at `0.75rem` (~12px) are at the lower end of comfortable readability, though the icon symbols compensate.
- **User impact:** Minor readability concern. Icons help with scanning.
- **Fix:** Increase to `font-size: 0.8rem`.

---

#### [Severity 1] F21: Activity timestamps use `<span>` instead of `<time>` elements
- **Principle:** Match Between System and Real World (#2), Accessibility (#13)
- **Location:** `task-dashboard.html:502,507,512,517,522` (`.activity-time` spans)
- **Issue:** Timestamps ("2 hours ago", "Yesterday") are plain `<span>` elements without `datetime` attributes. Machines cannot extract structured time data.
- **User impact:** Minimal direct impact. Prevents machine-readable time extraction.
- **Fix:** Use `<time>` elements with `datetime` attributes.

---

#### [Severity 1] F22: Activity feed text lacks visual hierarchy between actors and actions
- **Principle:** Perceptibility (#14), Aesthetic and Minimalist Design (#8)
- **Location:** `task-dashboard.html:499-523` (activity items)
- **Issue:** Activity feed items display the actor initials in bold (`<strong>`) but the action text and timestamps are all at the same size (0.9rem for action, 0.8rem for time). The visual hierarchy between the actor, the action description, and the time is minimal.
- **User impact:** Users scanning the activity feed cannot quickly distinguish who did what and when. All text runs together at a similar visual weight.
- **Fix:** Make actor names slightly bolder/colored, and ensure the timestamp is clearly subordinate (lighter color, smaller size).

---

### Strengths

1. **Exemplary modal dialog implementation.** The add-task modal includes `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, Escape key handling, focus trapping, overlay click-to-dismiss, a visible close button, and return-focus-on-close. This thoroughly satisfies User Control and Freedom (#3) and Accessibility (#13).

2. **Priority indicators use triple-redundant cues.** Task priority uses color, distinct symbols (triangle/circle/dash), and text labels. This triple redundancy ensures all users, including those with color vision deficiency, can understand priority at a glance. Excellent Perceptibility (#14).

3. **Thoughtful responsive layout.** The sidebar collapses to an off-canvas drawer with hamburger toggle, the Kanban board adapts from 4 to 2 to 1 column, and stats cards stack vertically. Good Structure (#12) and Flexibility (#7).

4. **Reduced motion support.** The `prefers-reduced-motion` media query disables animations for users who request it. Demonstrates respect for user preferences and satisfies Accessibility (#13).

5. **Solid semantic HTML foundation.** Proper use of `<main>`, `<header>`, `<aside>`, heading hierarchy (h1 > h2 > h3), `<form>` with `<label>` via `for`/`id`, `<button>` for interactive controls, `lang="en"`, and correct viewport meta. Strong foundation for Accessibility (#13) and Structure (#12).

6. **Functional drag-and-drop with visual feedback.** Clear visual states (dragging opacity, dashed outline on target), column count updates, and toast notifications confirming moves. Satisfies Visibility of System Status (#1).
