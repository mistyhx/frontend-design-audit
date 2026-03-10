# Common Fix Patterns

Concrete code examples for the most frequent heuristic violations. Organized by category.

## Table of Contents
1. [Loading and Feedback States](#1-loading-and-feedback-states)
2. [Form Validation and Error Handling](#2-form-validation-and-error-handling)
3. [Accessibility Essentials](#3-accessibility-essentials)
4. [Navigation and Wayfinding](#4-navigation-and-wayfinding)
5. [Interactive Element Affordances](#5-interactive-element-affordances)
6. [User Control Patterns](#6-user-control-patterns)
7. [Progressive Disclosure](#7-progressive-disclosure)
8. [Responsive and Touch Patterns](#8-responsive-and-touch-patterns)
9. [Visual Design Fixes](#9-visual-design-fixes)
10. [Design System and Coherence](#10-design-system-and-coherence)

---

## 1. Loading and Feedback States

### Button loading state

```jsx
// Before: No feedback on click
<button onClick={handleSubmit}>Save</button>

// After: Loading state prevents double-submit and shows progress
<button
  onClick={handleSubmit}
  disabled={isLoading}
  aria-busy={isLoading}
>
  {isLoading ? 'Saving...' : 'Save'}
</button>
```

### Skeleton screen for data loading

```jsx
// Before: Empty space while loading
{data && <DataTable data={data} />}

// After: Skeleton placeholder communicates loading
{isLoading ? (
  <div aria-busy="true" aria-label="Loading data">
    <div className="skeleton skeleton-row" />
    <div className="skeleton skeleton-row" />
    <div className="skeleton skeleton-row" />
  </div>
) : (
  <DataTable data={data} />
)}
```

```css
.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: 4px;
  height: 1em;
  margin-bottom: 0.5em;
}
@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

### Toast notification for action feedback

```jsx
// After save/delete/action - provide confirmation
function showToast(message, type = 'success') {
  const toast = document.createElement('div');
  toast.setAttribute('role', 'status');
  toast.setAttribute('aria-live', 'polite');
  toast.className = `toast toast-${type}`;
  toast.textContent = message;
  document.body.appendChild(toast);
  // Auto-remove success messages; keep errors until dismissed
  if (type !== 'error') {
    setTimeout(() => toast.remove(), 4000);
  }
}
```

### Active navigation state

IMPORTANT: Both the HTML attribute AND the CSS visual style are required.
Adding `aria-current` without a visible style means sighted users see no highlight.

```jsx
// Before: No indication of current page
<nav>
  <a href="/dashboard">Dashboard</a>
  <a href="/settings">Settings</a>
</nav>

// After: Current page indicated
<nav aria-label="Main navigation">
  <a href="/dashboard" aria-current={currentPath === '/dashboard' ? 'page' : undefined}>
    Dashboard
  </a>
  <a href="/settings" aria-current={currentPath === '/settings' ? 'page' : undefined}>
    Settings
  </a>
</nav>
```

```css
/* Use BOTH the attribute selector AND the specific class used on the links.
   If your nav links have a class like .nav-link, target both so the style
   applies regardless of specificity: */
[aria-current="page"],
.nav-link[aria-current="page"] {
  font-weight: 600;
  color: var(--color-primary);
  border-bottom: 2px solid var(--color-primary);
}
```

---

## 2. Form Validation and Error Handling

### Inline validation

```jsx
// Before: Validation only on submit
<form onSubmit={handleSubmit}>
  <input name="email" type="text" />
  {submitError && <div className="error">{submitError}</div>}
</form>

// After: Inline validation with accessible error messages
<form onSubmit={handleSubmit} noValidate>
  <div className="field">
    <label htmlFor="email">Email address</label>
    <input
      id="email"
      name="email"
      type="email"
      aria-invalid={errors.email ? 'true' : undefined}
      aria-describedby={errors.email ? 'email-error' : 'email-hint'}
      onBlur={validateEmail}
    />
    <span id="email-hint" className="hint">We'll never share your email</span>
    {errors.email && (
      <span id="email-error" className="error" role="alert">
        {errors.email}
      </span>
    )}
  </div>
</form>
```

```css
.field .error {
  color: var(--color-error);
  font-size: 0.875rem;
  margin-top: 0.25rem;
}
input[aria-invalid="true"] {
  border-color: var(--color-error);
  box-shadow: 0 0 0 1px var(--color-error);
}
```

### Specific, actionable error messages

```js
// Before: Generic errors
"Invalid input"
"Something went wrong"
"Error occurred"

// After: Specific with recovery guidance
"Password must be at least 8 characters with one number and one uppercase letter"
"This email is already registered. Try signing in or use a different email."
"We couldn't save your changes. Check your internet connection and try again."
```

### Confirmation dialog for destructive actions

```jsx
// Before: Instant delete, no way back
<button onClick={() => deleteItem(id)}>Delete</button>

// After: Confirmation with clear consequence
<button onClick={() => setShowConfirm(true)}>Delete</button>

{showConfirm && (
  <dialog open role="alertdialog" aria-labelledby="confirm-title" aria-describedby="confirm-desc">
    <h2 id="confirm-title">Delete this project?</h2>
    <p id="confirm-desc">
      This will permanently delete "My Project" and all its files.
      This action cannot be undone.
    </p>
    <div className="dialog-actions">
      <button onClick={() => setShowConfirm(false)} autoFocus>Cancel</button>
      <button onClick={() => { deleteItem(id); setShowConfirm(false); }} className="destructive">
        Delete project
      </button>
    </div>
  </dialog>
)}
```

### Preserve form data on error

```jsx
// Before: Error resets form
catch (error) {
  setFormData({});
  setError("Submission failed");
}

// After: Preserve input, show error near cause
catch (error) {
  // Don't reset form data - user shouldn't lose their work
  setError("We couldn't submit the form. Please check the highlighted fields and try again.");
  // Scroll to first error
  document.querySelector('[aria-invalid="true"]')?.focus();
}
```

---

## 3. Accessibility Essentials

### Image alt text

```html
<!-- Informative image: describe what it conveys -->
<img src="chart.png" alt="Sales increased 40% in Q3, reaching $2.4M">

<!-- Decorative image: empty alt to hide from screen readers -->
<img src="decorative-divider.png" alt="" role="presentation">

<!-- Icon button: label the action, not the icon -->
<button aria-label="Close dialog">
  <svg aria-hidden="true"><!-- X icon --></svg>
</button>
```

### Form labels

```html
<!-- Before: Relying on placeholder alone -->
<input placeholder="Enter your name" />

<!-- After: Visible label + helpful placeholder -->
<div class="field">
  <label for="name">Full name</label>
  <input id="name" placeholder="e.g., Jane Smith" />
</div>
```

### Keyboard-accessible custom elements

```jsx
// Before: div acting as button, keyboard-inaccessible
<div className="card" onClick={handleClick}>Click me</div>

// After: Semantically correct OR with proper ARIA
<button className="card" onClick={handleClick}>Click me</button>

// If div is necessary for styling:
<div
  className="card"
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => { if (e.key === 'Enter' || e.key === ' ') handleClick(e); }}
>
  Click me
</div>
```

### Focus management in modals

```jsx
function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef(null);
  const previousFocus = useRef(null);

  useEffect(() => {
    if (isOpen) {
      previousFocus.current = document.activeElement;
      modalRef.current?.focus();
    }
    return () => {
      previousFocus.current?.focus(); // Restore focus on close
    };
  }, [isOpen]);

  if (!isOpen) return null;

  return (
    <div className="overlay" onClick={onClose}>
      <div
        ref={modalRef}
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
        tabIndex={-1}
        onClick={(e) => e.stopPropagation()}
        onKeyDown={(e) => { if (e.key === 'Escape') onClose(); }}
      >
        {children}
      </div>
    </div>
  );
}
```

### Color contrast and non-color indicators

```css
/* Before: Status relies only on color */
.status-good { color: green; }
.status-bad { color: red; }

/* After: Color + icon + text for redundancy */
.status-good::before { content: "✓ "; }
.status-bad::before { content: "✗ "; }
```

```html
<!-- Before: Color-only indication -->
<span class="status-good">Active</span>

<!-- After: Icon provides redundant signal -->
<span class="status-good">
  <svg aria-hidden="true" class="icon-check">...</svg>
  Active
</span>
```

### Focus visible styles

```css
/* Ensure visible focus for keyboard users without affecting mouse users */
:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Remove default outline only when focus-visible is supported */
:focus:not(:focus-visible) {
  outline: none;
}
```

### HTML language attribute

```html
<!-- Always include lang on the html element -->
<html lang="en">
```

---

## 4. Navigation and Wayfinding

### Breadcrumbs

```html
<nav aria-label="Breadcrumb">
  <ol>
    <li><a href="/">Home</a></li>
    <li><a href="/products">Products</a></li>
    <li><a href="/products/widgets" aria-current="page">Widgets</a></li>
  </ol>
</nav>
```

### Empty states with guidance

```jsx
// Before: Empty table, no guidance
{items.length === 0 && <p>No items.</p>}

// After: Informative empty state with call to action
{items.length === 0 && (
  <div className="empty-state">
    <svg className="empty-state-icon" aria-hidden="true">...</svg>
    <h3>No projects yet</h3>
    <p>Create your first project to get started.</p>
    <button onClick={handleCreateProject}>Create project</button>
  </div>
)}
```

### Skip navigation link

```html
<!-- First element in <body> for keyboard users -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<style>
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: 8px 16px;
  background: var(--color-primary);
  color: white;
  z-index: 100;
}
.skip-link:focus {
  top: 0;
}
</style>

<main id="main-content">...</main>
```

---

## 5. Interactive Element Affordances

### Hover and focus states

```css
/* Every interactive element needs these states */
.btn {
  cursor: pointer;
  transition: background-color 0.15s ease, box-shadow 0.15s ease;
}
.btn:hover {
  background-color: var(--color-primary-hover);
}
.btn:active {
  background-color: var(--color-primary-active);
  transform: translateY(1px);
}
.btn:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}
```

### Clickable cards

```css
/* Before: Card looks static, no indication it's clickable */
.card { padding: 16px; border: 1px solid #eee; }

/* After: Visual affordance for interactivity */
.card-interactive {
  padding: 16px;
  border: 1px solid var(--color-border);
  cursor: pointer;
  transition: box-shadow 0.15s ease, border-color 0.15s ease;
}
.card-interactive:hover {
  border-color: var(--color-border-hover);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}
.card-interactive:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

### Link vs button semantics

```html
<!-- Links navigate to a URL -->
<a href="/settings">Settings</a>

<!-- Buttons perform actions -->
<button type="button" onClick={handleAction}>Save changes</button>

<!-- NEVER: Link that acts as a button -->
<!-- <a href="#" onClick={handleAction}>Save changes</a> -->

<!-- NEVER: Button that navigates -->
<!-- <button onClick={() => navigate('/settings')}>Settings</button> -->
<!-- Exception: In React Router / Next.js, Link components render <a> tags -->
```

---

## 6. User Control Patterns

### Undo with toast

```jsx
function handleDelete(item) {
  // Soft-delete: mark as deleted but don't remove yet
  softDelete(item.id);

  showToast({
    message: `"${item.name}" deleted`,
    action: {
      label: 'Undo',
      onClick: () => restoreItem(item.id),
    },
    duration: 8000, // Give users time to undo
  });

  // Hard-delete after timeout if not undone
  setTimeout(() => {
    if (isStillDeleted(item.id)) {
      hardDelete(item.id);
    }
  }, 10000);
}
```

### Unsaved changes warning

```jsx
useEffect(() => {
  if (!isDirty) return;

  const handleBeforeUnload = (e) => {
    e.preventDefault();
    e.returnValue = ''; // Required for the browser dialog
  };

  window.addEventListener('beforeunload', handleBeforeUnload);
  return () => window.removeEventListener('beforeunload', handleBeforeUnload);
}, [isDirty]);
```

### Modal with proper escape mechanisms

```jsx
// Three ways to close: button, Escape key, overlay click
<div className="modal-overlay" onClick={onClose}>
  <div
    className="modal"
    role="dialog"
    aria-modal="true"
    onClick={(e) => e.stopPropagation()}
    onKeyDown={(e) => e.key === 'Escape' && onClose()}
  >
    <button className="modal-close" onClick={onClose} aria-label="Close">
      ×
    </button>
    {children}
  </div>
</div>
```

---

## 7. Progressive Disclosure

### Collapsible sections

```html
<!-- Native HTML progressive disclosure -->
<details>
  <summary>Advanced settings</summary>
  <div class="details-content">
    <!-- Advanced fields here -->
  </div>
</details>
```

### Show/hide toggle

```jsx
// For non-critical options that would clutter the main view
<div className="form-section">
  <h3>Basic Information</h3>
  {/* Primary fields always visible */}
  <input ... />
  <input ... />

  <button
    type="button"
    onClick={() => setShowAdvanced(!showAdvanced)}
    aria-expanded={showAdvanced}
    aria-controls="advanced-section"
  >
    {showAdvanced ? 'Hide' : 'Show'} advanced options
  </button>

  {showAdvanced && (
    <div id="advanced-section">
      {/* Secondary fields */}
    </div>
  )}
</div>
```

---

## 8. Responsive and Touch Patterns

### Minimum touch targets

```css
/* WCAG 2.5.8: Minimum 24x24px, recommended 44x44px for touch */
button, a, [role="button"] {
  min-height: 44px;
  min-width: 44px;
}

/* For inline links in text, ensure adequate spacing */
p a {
  padding: 4px 0;
  /* Inline links don't need 44px, but should have breathing room */
}
```

### Responsive text

```css
/* Avoid text that's too small on any device */
body {
  font-size: clamp(1rem, 0.9rem + 0.5vw, 1.125rem);
  line-height: 1.5;
}

/* Don't disable zoom - users need it */
/* NEVER: <meta name="viewport" content="maximum-scale=1, user-scalable=no"> */
/* CORRECT: */
/* <meta name="viewport" content="width=device-width, initial-scale=1"> */
```

### Auto-save for long forms

```jsx
// Persist form state to prevent data loss
useEffect(() => {
  const saved = localStorage.getItem('form-draft');
  if (saved) {
    const shouldRestore = confirm('You have an unsaved draft. Would you like to restore it?');
    if (shouldRestore) setFormData(JSON.parse(saved));
    else localStorage.removeItem('form-draft');
  }
}, []);

useEffect(() => {
  if (isDirty) {
    const timeout = setTimeout(() => {
      localStorage.setItem('form-draft', JSON.stringify(formData));
    }, 1000); // Debounce saves
    return () => clearTimeout(timeout);
  }
}, [formData, isDirty]);
```

---

## 9. Visual Design Fixes

### Typography hierarchy

```css
/* Before: Flat — everything looks the same */
h1 { font-size: 1.1rem; font-weight: 500; }
h2 { font-size: 1rem; font-weight: 500; }
.body { font-size: 0.9rem; }
.meta { font-size: 0.85rem; }

/* After: Clear hierarchy — eye knows where to go */
h1 { font-size: 1.5rem; font-weight: 700; letter-spacing: -0.01em; }
h2 { font-size: 1.2rem; font-weight: 600; }
.body { font-size: 0.95rem; font-weight: 400; line-height: 1.5; }
.meta { font-size: 0.8rem; font-weight: 400; color: #666; }
```

### Spacing hierarchy

```css
/* Before: Uniform spacing — everything 1rem */
.section { margin-bottom: 1rem; }
.card { padding: 1rem; }
.label { margin-bottom: 1rem; }

/* After: Tight within groups, open between sections */
.section { margin-bottom: 2rem; }
.section-heading { margin-bottom: 1rem; }
.card { padding: 1.25rem; }
.label { margin-bottom: 0.25rem; }
.form-field { margin-bottom: 1rem; }
```

### Section boundaries

```css
/* Before: Sections run together */
.stats { padding: 1rem; }
.board { padding: 1rem; }

/* After: Clear visual separation */
.stats { padding: 1.5rem 2rem; border-bottom: 1px solid #e5e7eb; }
.board { padding: 0 2rem 2rem; margin-top: 0.5rem; }
```

### Comparison tables with category groupings

Tables with section groupings (e.g., "Storage & Sync", "Collaboration") need the
group headers to visually read as category labels, not as misaligned data rows.
The key problem: `colspan` headers span the full table width, but the data rows
below have individual cells — this creates a visual disconnect.

```html
<!-- Use <th> with scope="colgroup" for semantics, but style distinctly -->
<tr class="section-header">
  <th scope="colgroup" colspan="4">Storage &amp; Sync</th>
</tr>
<tr>
  <td class="feature-name">Storage</td>
  <td>10 GB</td>
  <td>100 GB</td>
  <td>Unlimited</td>
</tr>
```

```css
/* Section group headers need 3 things to look right:
   1. Background tint — distinguishes them from data rows
   2. Left-aligned text — anchors them to the feature-name column below
   3. Visual weight — uppercase + bold + primary color reads as a label */
.comparison-table .section-header th {
  padding-top: var(--space-xl);
  padding-bottom: var(--space-xs);
  font-weight: var(--font-bold);
  font-size: var(--text-xs);
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: var(--color-primary);
  background: var(--color-bg-subtle);
  border-bottom: 2px solid var(--color-border);
  text-align: left;
}

/* Data cells — visually subordinate to section headers */
.comparison-table td {
  padding: var(--space-sm) var(--space-md);
  font-size: var(--text-sm);
  color: var(--color-text-secondary);
  border-bottom: 1px solid var(--color-border);
}
.comparison-table td:not(:first-child) {
  text-align: center;
}
.comparison-table .feature-name {
  font-weight: var(--font-medium);
  color: var(--color-text);
}
```

---

### Label-value hierarchy in dashboards

```css
.stat-label {
  font-size: 0.75rem;
  color: #888;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  font-weight: 500;
}
.stat-value {
  font-size: 2rem;
  font-weight: 700;
  color: #1a1a2e;
  line-height: 1.2;
}
```

### Button hierarchy

```css
.btn-primary {
  background: var(--accent);
  color: white;
  padding: 10px 20px;
  font-weight: 600;
  border: none;
  border-radius: 8px;
}
.btn-secondary {
  background: white;
  color: #333;
  padding: 8px 16px;
  font-weight: 400;
  border: 1px solid #ddd;
  border-radius: 8px;
}
.btn-danger {
  background: #fee2e2;
  color: #dc2626;
  border: 1px solid #fca5a5;
}
```

### Card content differentiation

```css
.card-title {
  font-size: 0.95rem;
  font-weight: 600;
  color: #1a1a2e;
  margin-bottom: 0.5rem;
}
.card-description {
  font-size: 0.85rem;
  font-weight: 400;
  color: #666;
  line-height: 1.5;
}
.card-meta {
  font-size: 0.75rem;
  color: #999;
  margin-top: 0.75rem;
}
```

---

## 10. Design System and Coherence

These patterns support the 3-phase implementation approach. Use them during Phase 1 (establish foundation) and Phase 3 (coherence pass).

### Extract and define design tokens

```css
/* Audit existing CSS values → consolidate to a token system */
:root {
  /* Spacing — consistent scale */
  --space-xs: 0.25rem;    /* 4px — icon to label */
  --space-sm: 0.5rem;     /* 8px — within component */
  --space-md: 1rem;       /* 16px — between related components */
  --space-lg: 1.5rem;     /* 24px — between sections */
  --space-xl: 2.5rem;     /* 40px — between major regions */
  --space-2xl: 4rem;      /* 64px — page section padding */

  /* Typography */
  --text-xs: 0.75rem;     /* meta, captions */
  --text-sm: 0.875rem;    /* secondary text */
  --text-base: 1rem;      /* body */
  --text-lg: 1.25rem;     /* card titles, h3 */
  --text-xl: 1.5rem;      /* section headings */
  --text-2xl: 2rem;       /* page title */
  --text-3xl: 2.5rem;     /* hero headline */

  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;

  /* Colors — limited palette */
  --color-primary: #6366f1;
  --color-primary-hover: #5558e6;
  --color-primary-subtle: #eef2ff;

  --color-text: #1a1a2e;
  --color-text-secondary: #64748b;
  --color-text-muted: #94a3b8;

  --color-bg: #ffffff;
  --color-bg-subtle: #f8fafc;
  --color-border: #e2e8f0;

  --color-success: #22c55e;
  --color-error: #ef4444;
  --color-warning: #f59e0b;

  /* Shadows — consistent depth scale */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 6px rgba(0,0,0,0.07);
  --shadow-lg: 0 10px 25px rgba(0,0,0,0.1);

  /* Radii — 1-2 values */
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;

  /* Transitions — one standard timing */
  --transition-fast: 150ms ease;
  --transition-base: 250ms ease;
}
```

### Consolidate icon usage

```html
<!-- Before: Mixed icon sources — looks unprofessional -->
<i class="fa fa-home"></i>                    <!-- Font Awesome -->
<span class="material-icons">mail</span>     <!-- Material Icons -->
<img src="arrow.svg">                         <!-- Custom SVG -->
★★★★☆                                        <!-- Unicode -->

<!-- After: One consistent source (e.g., Lucide via CDN) -->
<script src="https://unpkg.com/lucide@latest"></script>

<i data-lucide="home"></i>
<i data-lucide="mail"></i>
<i data-lucide="arrow-right"></i>
<div role="img" aria-label="4 out of 5 stars">
  <i data-lucide="star" class="star-filled"></i>
  <i data-lucide="star" class="star-filled"></i>
  <i data-lucide="star" class="star-filled"></i>
  <i data-lucide="star" class="star-filled"></i>
  <i data-lucide="star" class="star-empty"></i>
</div>
```

### Consistent component patterns

```css
/* Define once, use everywhere — not different styles per page */

/* Cards */
.card {
  background: var(--color-bg);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: var(--space-lg);
  transition: box-shadow var(--transition-fast);
}
.card:hover {
  box-shadow: var(--shadow-md);
}

/* Section containers */
.section {
  padding: var(--space-2xl) 0;
}
.section + .section {
  border-top: 1px solid var(--color-border);
}

/* Consistent interactive states for ALL buttons */
.btn {
  border-radius: var(--radius-sm);
  font-weight: var(--font-semibold);
  transition: all var(--transition-fast);
  cursor: pointer;
}
.btn:hover { filter: brightness(0.95); }
.btn:active { transform: translateY(1px); }
.btn:focus-visible { outline: 2px solid var(--color-primary); outline-offset: 2px; }
.btn:disabled { opacity: 0.5; cursor: not-allowed; }
```

### Coherence pass — consolidate random values

```css
/* Before: Every section has different spacing, every card different radius */
.hero { padding: 60px 20px; }
.about { padding: 40px 30px; }
.projects { padding: 50px 20px; }
.contact { padding: 35px 25px; }

.project-card { border-radius: 8px; padding: 20px; }
.testimonial-card { border-radius: 12px; padding: 24px; }
.skill-card { border-radius: 4px; padding: 16px; }

/* After: Tokens applied consistently — UI feels intentional */
.hero, .about, .projects, .contact {
  padding: var(--space-2xl) var(--space-lg);
}

.project-card, .testimonial-card, .skill-card {
  border-radius: var(--radius-md);
  padding: var(--space-lg);
}
```

### Interaction consistency

```css
/* Before: Different hover patterns on similar elements */
.project-card:hover { transform: translateY(-8px); box-shadow: 0 20px 40px rgba(0,0,0,0.3); }
.skill-card:hover { background: #f0f0f0; }
.testimonial-card:hover { border-color: blue; }

/* After: One hover pattern for all cards */
.project-card:hover,
.skill-card:hover,
.testimonial-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

/* Before: Inconsistent link/button transitions */
.nav-link { transition: color 0.3s; }
.btn { transition: background 0.15s ease-in-out; }
.card { transition: all 0.5s; }

/* After: One transition timing */
.nav-link, .btn, .card {
  transition: all var(--transition-fast);
}
```
