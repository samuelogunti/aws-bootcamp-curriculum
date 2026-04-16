# Frontend Best Practices

## Component Design
- Build small, reusable, composable components
- Separate presentational components from container/logic components
- Use props for configuration, events/callbacks for communication upward
- Co-locate component styles, tests, and stories with the component file
- Prefer functional components over class components

## State Management
- Keep state as local as possible — lift only when necessary
- Use context or lightweight stores for shared UI state (theme, auth, locale)
- Use server state libraries (React Query, SWR, TanStack Query) for API data
- Avoid duplicating server state in client state
- Normalize complex relational data in global stores

## Performance
- Lazy-load routes and heavy components
- Use code splitting to reduce initial bundle size
- Optimize images (WebP, responsive sizes, lazy loading)
- Memoize expensive computations and prevent unnecessary re-renders
- Monitor bundle size in CI — set budgets and alert on regressions
- Use a CDN (CloudFront) for static assets

## Responsive Design
- Design mobile-first, enhance for larger screens
- Use CSS Grid and Flexbox for layout — avoid fixed pixel widths
- Test on real devices, not just browser dev tools
- Support common breakpoints: mobile (< 768px), tablet (768–1024px), desktop (> 1024px)

## Forms
- Validate on both client and server — client validation is for UX, server validation is for security
- Show inline validation errors near the relevant field
- Disable submit buttons during submission to prevent double-submits
- Preserve form state on validation failure

## Error Handling
- Show user-friendly error messages — not raw API errors
- Implement error boundaries to catch rendering failures gracefully
- Provide retry options for transient failures
- Log client-side errors to a monitoring service (Sentry, CloudWatch RUM)

## Testing
- Unit test business logic and utility functions
- Use component testing (Testing Library) for UI behavior
- Use E2E tests (Playwright, Cypress) sparingly for critical user flows
- Test accessibility with automated tools (axe-core) in CI
