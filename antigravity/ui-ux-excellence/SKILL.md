---
name: ui-ux-excellence
description: Enforce UI/UX consistency, reuse existing design systems/components, and ensure absolute user experience by handling all UI states (Loading, Empty, Error) and defensive design.
origin: custom
---

# UI/UX Excellence & Consistency Guard

Behavioral guidelines for implementing frontend interfaces. This skill ensures that the AI acts as a meticulous UX Engineer who respects the existing codebase design system and prioritizes the end-user experience above all else.

## When to Activate

- Creating new UI components, pages, or layouts.
- Refactoring frontend code (HTML, CSS, React, Angular, Vue, etc.).
- When the user asks to "make it look good", "improve UX", or "fix the UI".
- Integrating APIs into the frontend where loading/error states are required.

## Core Principles

### 1. Component & Design Token Reuse (Don't Reinvent)
**Match the codebase. If it exists, use it.**

- **Scan First:** Before writing raw HTML elements (like a custom `<button>` or a custom modal `<div>`), search the codebase for existing base components (e.g., `BaseButton`, `DataTable`, `AppDialog`).
- **Use Tokens, Not Magic Numbers:** NEVER hardcode colors (e.g., `#333333`), font sizes (`14px`), or spacing (`15px`). ALWAYS use the project's existing design tokens (Tailwind classes, SCSS variables, or CSS-in-JS theme objects).
- **Consistent Spacing:** Follow the project's existing spacing scale (e.g., 4px, 8px, 16px, 24px). Do not introduce arbitrary margins.

### 2. The 4-State UX Rule (Absolute Experience)
**A UI is never just the "Happy Path".** Every dynamic interface MUST handle 4 states:

1.  **Loading State:** Show skeletons, spinners, or disable buttons while fetching/submitting data. Never let the user click a button and wonder if it worked.
2.  **Empty State:** If a list or table has no data, show a clear, friendly empty state with an illustration/icon and a Call to Action (e.g., "No users found. Add a user").
3.  **Error State:** Catch API failures gracefully. Show localized, user-friendly error messages (not raw JSON or stack traces). Provide a "Retry" button.
4.  **Success State (Happy Path):** Provide immediate visual feedback (Toast, Snackbar, or checkmark) after a successful action.

### 3. Micro-Interactions & Polish
**Interfaces must feel alive and responsive.**

- **Interactive States:** Every clickable element MUST have defined `:hover`, `:focus`, `:active`, and `:disabled` styles.
- **Disabled Logic:** Dim the opacity and change the cursor (`cursor-not-allowed`) for disabled elements.
- **Transitions:** Use subtle transitions for state changes (e.g., `transition-colors duration-200` in Tailwind). Avoid abrupt visual jumps.

### 4. Defensive UI Design (Layout Stability)
**Assume data will break your layout.**

- **Text Truncation:** What if a user's name is 100 characters long? ALWAYS use text truncation (`text-ellipsis`, `overflow-hidden`, `whitespace-nowrap`) or line clamping for dynamic text.
- **Prevent CLS (Cumulative Layout Shift):** Reserve minimum heights or aspect ratios for images and content areas so the page doesn't jump when data loads.
- **Scrollbars:** Ensure containers with dynamic content have proper `overflow-y-auto` rules and don't break the parent layout.

### 5. Accessibility (a11y) by Default
**Make it usable for everyone.**

- **Semantic HTML:** Use `<button>` for actions, `<a>` for navigation. NEVER use a `<div>` with an `onClick` handler unless absolutely necessary (and if so, add `role="button"` and `tabIndex={0}`).
- **Labels:** Icon-only buttons MUST have `aria-label` or tooltip titles.
- **Focus Rings:** Never remove the default focus outline (`outline: none`) without providing a custom, highly visible focus ring for keyboard navigation.

## Anti-Patterns to Avoid

| Anti-Pattern | Correct Approach |
|--------------|------------------|
| **"Div Soup" & Inline Styles** | Use semantic tags and existing utility classes/theme tokens. |
| **Orphan Custom Components** | If you need a specific table, extend the existing `datatable.component` via inputs/configs, do not build a second table from scratch. |
| **Silent API Calls** | Always implement `isLoading` state and disable the submit button during the request. |
| **Assuming Perfect Data** | Always handle `null`, `undefined`, or empty arrays in the UI template. |

## Quality Gate (Pre-Delivery Checklist)

Before completing the UI task, verify:
- [ ] Are we reusing existing components and design tokens (colors, spacing)?
- [ ] Are Loading, Empty, and Error states implemented for dynamic data?
- [ ] Does every button/link have hover, focus, and disabled states?
- [ ] Is dynamic text protected against overflow/breaking the layout?
- [ ] Is the HTML semantic and accessible?
