# Accessibility (a11y) Best Practices

## General Principles
- Design for inclusivity from the start — retrofitting accessibility is expensive
- Follow WCAG 2.1 AA as the minimum standard
- Test with keyboard-only navigation
- Test with screen readers (VoiceOver, NVDA, JAWS)
- Ensure all interactive elements are reachable and operable without a mouse

## Semantic HTML
- Use native HTML elements for their intended purpose (`<button>`, `<nav>`, `<main>`, `<form>`)
- Use heading levels (`h1`–`h6`) in logical order — don't skip levels
- Use `<label>` elements associated with form inputs
- Use landmark elements (`<header>`, `<main>`, `<footer>`, `<aside>`) for page structure
- Prefer native elements over ARIA — use ARIA only when no native equivalent exists

## ARIA
- Add `aria-label` or `aria-labelledby` to elements that lack visible text labels
- Use `aria-live` regions for dynamic content updates (notifications, loading states)
- Set `role` attributes only when native semantics are insufficient
- Never use `aria-hidden="true"` on focusable elements

## Visual Design
- Maintain a minimum contrast ratio of 4.5:1 for normal text, 3:1 for large text
- Don't rely on color alone to convey information — use icons, patterns, or text as well
- Support user font size preferences — use relative units (`rem`, `em`) over fixed (`px`)
- Ensure focus indicators are clearly visible
- Support reduced motion preferences (`prefers-reduced-motion`)

## Forms
- Associate every input with a `<label>`
- Provide clear error messages linked to the relevant field (`aria-describedby`)
- Group related fields with `<fieldset>` and `<legend>`
- Mark required fields clearly (not just with color)

## Media
- Provide alt text for all meaningful images — use empty `alt=""` for decorative images
- Add captions or transcripts for video and audio content
- Ensure media players are keyboard accessible
