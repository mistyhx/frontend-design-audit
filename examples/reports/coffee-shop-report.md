## UX Design Audit Report

**Scope:** Full single-page audit of the Brew & Bean coffee shop website
**Source:** `/Users/xinhu/Projects/UXSkills/test-sites/coffee-shop.html`
**Interface type:** Marketing / e-commerce landing page with order form

### How to Read This Report
Findings are rated on a 0-4 severity scale (4 = users can't complete tasks,
1 = cosmetic only). Each finding references an established usability principle.
Start from the top — the most impactful issues are listed first.

### Summary

| Severity | Count |
|----------|-------|
| 4 - Catastrophe | 2 |
| 3 - Major | 5 |
| 2 - Minor | 8 |
| 1 - Cosmetic | 3 |
| **Total findings** | **18** |

### Quick Wins
The highest-impact issues that are also straightforward to fix:
1. Missing alt text on all images (Severity 4) — Add descriptive `alt` attributes to all 7 `<img>` elements
2. Viewport blocks pinch-to-zoom (Severity 4) — Remove `user-scalable=no` from viewport meta tag
3. Form inputs have no labels (Severity 3) — Add `<label>` elements associated with each input via `for`/`id`
4. No focus-visible styles (Severity 3) — Add `:focus-visible` outlines and remove `outline: none`

---

### Findings

#### [Severity 4] F1: All images missing alt text
- **Principle:** Accessibility (#13)
- **Location:** `coffee-shop.html:147, 155, 163, 171, 179, 187, 198`
- **Issue:** All seven `<img>` elements have no `alt` attribute. Screen readers announce the raw Unsplash URL, which is meaningless noise.
- **User impact:** Blind and low-vision users have zero understanding of what drinks are offered or what the shop looks like. This is a WCAG Level A failure (Success Criterion 1.1.1). Search engines also cannot index the image content.
- **Fix:** Add descriptive alt text to each image (e.g., `alt="A shot of classic espresso in a ceramic cup"`).

---

#### [Severity 4] F2: Viewport meta tag blocks pinch-to-zoom
- **Principle:** Accessibility (#13), Flexibility and Efficiency (#7)
- **Location:** `coffee-shop.html:5`
- **Issue:** `user-scalable=no` in the viewport meta tag prevents users from pinch-zooming on mobile devices.
- **User impact:** Users with low vision who rely on pinch-to-zoom are completely blocked. This is a WCAG 2.1 Level AA failure (SC 1.4.4). There is no workaround — users cannot access the site comfortably on mobile.
- **Fix:** Remove `user-scalable=no`. The tag should read: `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.

---

#### [Severity 3] F3: Form inputs have no associated labels — placeholder only
- **Principle:** Accessibility (#13), Recognition Over Recall (#6)
- **Location:** `coffee-shop.html:233-255`
- **Issue:** All form inputs rely solely on `placeholder` text for identification. No `<label>` elements exist, and inputs have no `id` attributes. Placeholders disappear when users type.
- **User impact:** Screen reader users hear "edit text" with no indication of what each field expects. Sighted users who begin typing lose context. Users with cognitive disabilities are disproportionately affected.
- **Fix:** Add visible `<label>` elements for each input, linked via `for`/`id` attributes.

---

#### [Severity 3] F4: No focus-visible styles; outline explicitly removed
- **Principle:** Accessibility (#13), Perceptibility (#14)
- **Location:** `coffee-shop.html:88` (`outline: none`); no `:focus-visible` anywhere
- **Issue:** Form inputs explicitly remove the outline with `outline: none`, and no replacement focus indicator is provided. Links, buttons, and the mobile toggle have no focus styles at all.
- **User impact:** Keyboard users cannot see which element is focused, making the site nearly impossible to navigate without a mouse. WCAG 2.4.7 failure.
- **Fix:** Replace `outline: none` with a custom visible focus style. Add `:focus-visible` to all interactive elements.

---

#### [Severity 3] F5: No semantic landmark regions or skip navigation
- **Principle:** Accessibility (#13), Structure (#12)
- **Location:** `coffee-shop.html:123-289`
- **Issue:** The page has no `<main>`, no `<header>`, and no skip-to-content link. The `<nav>` has no `aria-label`. There is no `<html lang>` attribute.
- **User impact:** Screen reader users must navigate linearly through the entire page. They cannot jump between regions or skip past the navigation. The missing `lang` attribute causes mispronunciation of all content.
- **Fix:** Add `<html lang="en">`, `<header>` around nav, `<main id="main-content">`, `aria-label` on nav, and a skip link.

---

#### [Severity 3] F6: Email field uses type="text"; phone has no input type optimization
- **Principle:** Error Prevention (#5), Flexibility and Efficiency (#7)
- **Location:** `coffee-shop.html:236-237`
- **Issue:** The email field uses `type="text"` instead of `type="email"`, and the phone field uses `type="text"` instead of `type="tel"`. No `autocomplete` attributes anywhere.
- **User impact:** Mobile users get a generic keyboard without `@` key for email or numeric pad for phone. Invalid emails are accepted. Browser autofill does not work.
- **Fix:** Use `type="email"`, `type="tel"`, and add `autocomplete` attributes to all applicable fields.

---

#### [Severity 3] F7: Form submission uses alert() and destroys user data
- **Principle:** Visibility of System Status (#1), Tolerance and Forgiveness (#15)
- **Location:** `coffee-shop.html:281-285`
- **Issue:** The `submitOrder` function uses `alert()` for feedback, then immediately calls `e.target.reset()` which wipes all form data. No loading state, no error handling.
- **User impact:** Users get a jarring system popup. Their form data is destroyed — if they want to order again, they must re-enter everything. No error handling exists for failure scenarios.
- **Fix:** Replace `alert()` with an inline success message. Show order summary. Don't auto-reset; add a loading state to the submit button.

---

#### [Severity 2] F8: Menu cards have hover lift but are not clickable (false affordance)
- **Principle:** Affordances and Signifiers (#11)
- **Location:** `coffee-shop.html:54`
- **Issue:** Menu cards have `transform: translateY(-8px)` and elevated shadow on hover — a strong interactivity signal. But they are not links or buttons.
- **User impact:** Users hover, see the card lift, click, and nothing happens. This false affordance erodes trust.
- **Fix:** Remove the hover lift and reduce shadow change, or make cards link to the order section with the drink pre-selected.

---

#### [Severity 2] F9: Mobile navigation toggle lacks accessible state and close mechanism
- **Principle:** User Control and Freedom (#3), Accessibility (#13)
- **Location:** `coffee-shop.html:127, 276-279`
- **Issue:** The hamburger button has no `aria-label`, no `aria-expanded`, no `aria-controls`. No Escape key handler or click-outside-to-close.
- **User impact:** Screen reader users can't tell what the button does or if the menu is open. Keyboard users have no way to close the menu except finding the hamburger again.
- **Fix:** Add `aria-label="Menu"`, `aria-expanded`, `aria-controls`. Add Escape key close handler.

---

#### [Severity 2] F10: No active/current state for navigation links
- **Principle:** Visibility of System Status (#1), Perceptibility (#14)
- **Location:** `coffee-shop.html:128-133`
- **Issue:** Navigation links have no active or current state. No indication of which section the user is viewing.
- **User impact:** Users scrolling through the single-page site lose their sense of position.
- **Fix:** Add scroll-spy via `IntersectionObserver` that applies an active class and `aria-current` to the visible section's nav link.

---

#### [Severity 2] F11: Social media links have no accessible names
- **Principle:** Accessibility (#13), Affordances and Signifiers (#11)
- **Location:** `coffee-shop.html:268-270`
- **Issue:** Social icons use abbreviations ("FB", "IG", "TW") with no `aria-label`. All point to `href="#"`, scrolling to page top on click.
- **User impact:** Screen reader users hear "link FB" with no clarity. Clicking scrolls to page top instead of going to a social profile.
- **Fix:** Add `aria-label` attributes. Replace `href="#"` with actual URLs or disable until real profiles exist.

---

#### [Severity 2] F12: Footer links are dead (`href="#"`)
- **Principle:** User Control and Freedom (#3), Match Between System and Real World (#2)
- **Location:** `coffee-shop.html:262-266`
- **Issue:** All four footer links point to `href="#"`, scrolling users to page top. For a site collecting personal data, a non-functional privacy policy link is particularly concerning.
- **User impact:** Users clicking "Privacy Policy" are unexpectedly scrolled to page top. No policy is visible.
- **Fix:** Link to actual pages or display "Coming soon" on click.

---

#### [Severity 2] F13: Button hierarchy is flat — all CTAs look identical
- **Principle:** Aesthetic and Minimalist Design (#8), Affordances and Signifiers (#11)
- **Location:** `coffee-shop.html:33-39`
- **Issue:** The same `.btn` style is used for the hero CTA ("Explore Our Menu"), the about CTA ("Order Now"), and the form submit ("Place Order"). The most important action — placing an order — has no more visual weight than exploring the menu.
- **User impact:** Users scanning the page can't quickly identify the primary conversion action.
- **Fix:** Create a visual hierarchy: primary button (solid, larger, bolder) for "Place Order"; secondary for "Order Now"; ghost/outline for "Explore Our Menu."

---

#### [Severity 2] F14: Buttons have no active/pressed state
- **Principle:** Visibility of System Status (#1), Affordances and Signifiers (#11)
- **Location:** `coffee-shop.html:33-39`
- **Issue:** The `.btn` class has a hover state but no `:active` (pressed) state. Users get no visual feedback that their click registered.
- **User impact:** Users click buttons and see no immediate press feedback, creating uncertainty about whether the action worked.
- **Fix:** Add `.btn:active { transform: translateY(1px); background: #b08454; }`.

---

#### [Severity 2] F15: Menu card typography hierarchy could be stronger
- **Principle:** Aesthetic and Minimalist Design (#8), Perceptibility (#14)
- **Location:** `coffee-shop.html:57-59`
- **Issue:** Drink name (1.3rem), price (1.1rem bold), and description (0.9rem) have moderate differentiation, but price and name compete visually. Description text color (#888) is close to other text colors, creating a flat feel inside cards.
- **User impact:** When scanning cards, users must work to distinguish drink name from price from description. The hierarchy doesn't strongly guide the eye.
- **Fix:** Increase name weight to 700, reduce price size slightly, push description to lighter color and smaller size for clearer subordination.

---

#### [Severity 1] F16: No `prefers-reduced-motion` media query
- **Principle:** Flexibility and Efficiency (#7), Accessibility (#13)
- **Location:** `coffee-shop.html:37-38, 52-54`
- **Issue:** Multiple transitions exist but no `prefers-reduced-motion` query respects user preferences.
- **User impact:** Users with vestibular disorders may experience discomfort from hover animations, especially the 8px card lift.
- **Fix:** Add `@media (prefers-reduced-motion: reduce)` to disable transforms and reduce transition durations.

---

#### [Severity 1] F17: Star ratings in testimonials have no accessible text
- **Principle:** Accessibility (#13), Perceptibility (#14)
- **Location:** `coffee-shop.html:211, 216, 222`
- **Issue:** Star ratings use Unicode `★` characters with no `aria-label`. Screen readers read "black star" five times.
- **User impact:** Screen reader users hear awkward repeated star announcements rather than "5 out of 5 stars."
- **Fix:** Add `role="img" aria-label="5 out of 5 stars"` to `.stars` divs.

---

#### [Severity 1] F18: No meta description or Open Graph tags
- **Principle:** Match Between System and Real World (#2)
- **Location:** `coffee-shop.html:3-6`
- **Issue:** The `<head>` has no meta description, no OG tags, no Twitter card tags.
- **User impact:** When shared on social media or in search results, the link shows a bare title with no preview. This reduces discoverability for a business trying to attract customers.
- **Fix:** Add `<meta name="description">` and OpenGraph tags.

---

### Strengths

1. **Cohesive visual identity and warm aesthetic (Aesthetic and Minimalist Design #8):** The warm brown/cream/gold color palette is applied consistently — logo, nav hover, prices, star ratings, CTA buttons, social icons. The serif font (Georgia) reinforces the artisan brand feel.

2. **Logical content flow matches customer mental model (Structure #12, Match #2):** The page follows hero -> menu -> about -> testimonials -> order — mirroring how a customer thinks: "What is this? What do they serve? Can I trust them? How do I order?"

3. **Solid responsive grid layout (Flexibility #7):** A well-structured 768px breakpoint collapses grids, shows the hamburger, adjusts hero text size, and stacks content properly.

4. **Comfortable reading experience (Aesthetic and Minimalist Design #8):** Body text line-heights of 1.5-1.8 provide comfortable reading. Section headings at 2.5rem create strong visual anchors. Generous section padding prevents cramped content.

5. **Smart use of constrained inputs (Error Prevention #5):** Drink and milk preference fields use `<select>` dropdowns, completely eliminating typos. The `required` attribute prevents empty submissions on key fields.
