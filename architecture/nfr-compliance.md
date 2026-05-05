# NFR Compliance

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/package-plan|package-plan]] | [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]]

## Performance Budget

| Metric | Target | Rule |
|---|---:|---|
| LCP | <= 2.5 s | Optimize images, reserve dimensions, reduce blocking work. |
| INP | <= 200 ms | Avoid heavy synchronous work; debounce filters/search. |
| CLS | <= 0.1 | Skeletons match final layout; fixed media aspect ratios. |
| TTI | <= 3.5 s | Split routes; lazy-load maps, Stripe, charts. |
| Initial JS | <= 250 KB gzipped | Keep heavy libraries out of root bundle. |
| Per-route chunk | <= 100 KB gzipped | Split admin, delivery, checkout widgets. |
| Initial CSS | <= 50 KB gzipped | Tailwind content purge and minimal custom CSS. |

## Availability And Resilience

- Availability target: 99.9% monthly.
- During backend outages, keep cached read-only browsing active.
- Disable booking/order/admin/delivery write actions with clear messaging.
- Every route must render skeleton, empty, or error states, never a blank screen. See [[frontend/design/loading-skeletons|loading-skeletons]].

## Security And Privacy

- HTTPS only, CSP, X-Frame-Options DENY, Referrer-Policy, HSTS, Permissions-Policy.
- No service role keys, Stripe secrets, internal push secrets, or private Mapbox tokens in the browser.
- Prefer in-memory access token handling; avoid localStorage for sensitive auth tokens.
- Validate with Yup and server/database constraints.
- Sanitize HTML with DOMPurify.
- Show consent banner before non-essential analytics.
- Provide data access and deletion flows.

## Accessibility

- WCAG 2.1 AA minimum and AAA contrast where practical.
- Keyboard navigation, visible focus, semantic HTML, live regions, skip link on all layouts.
- axe-core in CI and manual audits per release against NVDA, JAWS, VoiceOver, and TalkBack.

## Browser Support

Chrome, Edge, Firefox latest two versions; Safari latest two versions; iOS Safari 15+; Android Chrome latest two versions. Internet Explorer is not supported.

## Responsive Device Support

The web app must be responsive across desktop computers, tablets, and mobile phones.

| Device | Requirement |
|---|---|
| Mobile phones | 320px+ width, touch-first, bottom navigation, single-column screens, sheet-based filters/actions. |
| Tablets | 768px+ width, touch and pointer friendly, wider forms, card/table hybrid lists, optional side panels. |
| Desktop/laptop | 1024px+ width, keyboard/mouse friendly, topbar plus sidebar, full data tables, multi-column layouts. |
| Large desktop | 1440px to 1920px+, constrained content width so text and controls do not stretch awkwardly. |

See [[frontend/design/responsive|responsive]] for exact layout rules.
