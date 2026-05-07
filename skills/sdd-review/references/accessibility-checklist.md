# Accessibility Checklist

Companion to **`skills/sdd-review/SKILL.md`**. Quick reference for WCAG 2.1 AA. Use alongside frontend UI changes or when usability and inclusive design deserve explicit review beyond the five-axis SKILL.

## Table of Contents

- [Essential Checks](#essential-checks)
- [Common HTML Patterns](#common-html-patterns)
- [Testing Tools](#testing-tools)
- [ARIA Live Regions](#aria-live-regions)
- [Anti-Patterns](#anti-patterns)

## Essential Checks

### Keyboard Navigation

- All interactive elements are reachable with Tab
- Focus order is logical and visible
- Modals trap focus and restore it on close
- No keyboard traps
- Skip links are available when the page is long or navigation-heavy
- Custom widgets expose keyboard equivalents for activation and dismissal

### Screen Readers

- Images have useful `alt` text
- Inputs have labels
- Buttons and links have descriptive text
- Headings are structured logically
- Dynamic updates are announced when needed
- Tables use headers for relationships

### Visual

- Text contrast meets WCAG AA
- Color is not the only signal
- Layout still works at 200% zoom
- Touch targets are large enough
- Motion does not block core task completion

### Forms

- Every field has a visible label
- Required state is not color-only
- Errors are specific and associated with the field
- Submission errors are easy to find
- Autocomplete is enabled where it helps users

### Content

- Set the page language
- Use a descriptive title
- Provide meaningful empty states
- Avoid jargon that only makes sense to insiders

## Common HTML Patterns

- Use `<button>` for actions and `<a>` for navigation
- Prefer native controls before custom widgets
- Use ARIA only when semantic HTML is not enough
- Announce status updates with the least disruptive live region available
- Use `aria-label` only when visible text is not practical

### Button vs Link

```html
<button type="button">Delete</button>
<a href="/tasks/123">View task</a>
```

### Labels

```html
<label for="email">Email address</label>
<input id="email" type="email" />
```

### Live Regions

```html
<div role="status" aria-live="polite">Saved</div>
<div role="alert">Error: title is required</div>
```

## Testing Tools

- Use axe, pa11y, and browser accessibility trees
- Test with a screen reader when needed
- Check keyboard-only flows before merging UI changes
- Compare the accessibility tree before and after major UI changes

## ARIA Live Regions

| Value | Behavior | Use For |
|-------|----------|---------|
| `polite` | Announced at the next pause | Status updates, saved confirmations |
| `assertive` | Announced immediately | Errors, time-sensitive alerts |
| `status` | Same as polite | Non-disruptive updates |
| `alert` | Same as assertive | Validation and blocking errors |

## Review Questions

- Can every interaction be completed without a mouse?
- Does the UI still make sense at 200% zoom?
- Are errors understandable without seeing color?
- Does focus go where a keyboard user expects?

## Anti-Patterns

- `div` as a button
- Missing labels or alt text
- Removing focus outlines
- Color-only states
- Empty links or buttons
- Custom controls without keyboard support
- Content announced out of order
- Dialogs that trap focus permanently
- Relying on screen-reader support that was never tested
