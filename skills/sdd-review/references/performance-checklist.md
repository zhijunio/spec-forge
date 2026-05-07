# Performance Checklist

Companion to **`skills/sdd-review/SKILL.md`**. Quick reference for web application performance. Use alongside performance-focused review work or when the Performance axis in the SKILL needs more depth.

## Table of Contents

- [Core Web Vitals Targets](#core-web-vitals-targets)
- [TTFB Diagnosis](#ttfb-diagnosis)
- [Frontend Checklist](#frontend-checklist)
- [Backend Checklist](#backend-checklist)
- [Measurement Commands](#measurement-commands)
- [Anti-Patterns](#anti-patterns)

## Core Web Vitals Targets

| Metric | Good | Needs Work | Poor |
|--------|------|------------|------|
| LCP | <= 2.5s | <= 4.0s | > 4.0s |
| INP | <= 200ms | <= 500ms | > 500ms |
| CLS | <= 0.1 | <= 0.25 | > 0.25 |

## TTFB Diagnosis

When TTFB is slow, check the path before changing code:

- DNS resolution
- TCP/TLS handshake
- Server processing time
- Cache hit rate
- Edge or CDN placement

Use `preconnect` or `dns-prefetch` for known origins when network setup is the bottleneck.

## Frontend Checklist

### Images

- Use modern formats where supported
- Size images responsively
- Add dimensions to avoid layout shift
- Lazy-load below-the-fold images
- Do not lazy-load the main LCP image

### JavaScript

- Split code by route or feature
- Avoid large unused dependency trees
- Keep blocking scripts out of the head
- Push non-critical work off the interaction path
- Avoid expensive re-renders in hot paths

### CSS

- Avoid render-blocking CSS for non-critical styles
- Keep critical styles small
- Prefer simpler selectors in hot paths

### Network

- Compress and cache static assets
- Preconnect to critical origins when needed
- Avoid unnecessary redirects
- Use CDN delivery where appropriate

### Rendering

- Avoid layout thrashing
- Use `content-visibility` for off-screen sections when appropriate
- Keep long lists virtualized when they are large
- Use transform and opacity for cheap animations

## Backend Checklist

- Remove N+1 queries
- Add indexes for filtered or sorted columns
- Paginate list endpoints
- Avoid synchronous heavy work in request handlers
- Use caching where freshness allows
- Compress responses when suitable
- Keep expensive repeated results behind a cache or materialized path

## Measurement Commands

```bash
npx lighthouse https://localhost:3000 --output json --output-path ./report.json
npx webpack-bundle-analyzer stats.json
npx bundlesize
```

## Measurement Guidance

- Measure before and after every change
- Check field data or RUM before guessing
- Use bundle analysis before optimizing bundle size
- Inspect the waterfall to separate network delay from app delay
- Keep a before/after record for each performance fix

## Anti-Patterns

- N+1 queries
- Missing indexes
- Large initial bundles
- Blocking main-thread work
- Unbounded queries or lists
- Optimizing without measurement
- Re-rendering the whole page for a small change
- Loading third-party scripts before core content
- Assuming one optimization helps every bottleneck
