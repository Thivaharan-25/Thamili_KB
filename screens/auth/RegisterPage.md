# RegisterPage

> **Screen file:** `src/pages/auth/RegisterPage.tsx`  
> **Route:** `/register`  
> **Template:** [[frontend/templates/AuthLayout|AuthLayout]]  
> **Architecture:** [[architecture/routing|routing]] | [[architecture/api-client|api-client]] | [[architecture/state-management|state-management]]  
> **Design:** [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]] | [[frontend/components/component-catalog|component-catalog]]

## Purpose

Create a customer account and public profile using the same username-first registration behavior as the current app.

## User Flow

1. User enters username, optional contact email, password, confirm password, and country.
2. Client validates username, password, confirm password, country, and validates email only when the email field has a value.
3. Supabase creates the auth user through the username registration helper.
4. Create or upsert `users` profile with role `customer`, username, country, and `email: null` when the optional email is not provided.
5. Send user to email verification only when a real email was provided and Supabase requires confirmation; otherwise continue into the authenticated app flow.

## API Endpoints And Data

- Auth endpoint: `auth.registerWithUsername(input)` exposed through `src/lib/api/endpoints/auth.ts` as `register(input)`.
- Supabase auth: `supabase.auth.signUp({ email: realEmailOrUsernameFallback, password })`.
- Write: `users` insert/upsert with username, country, role `customer`, and public email only when supplied.

## Validation

- Use react-hook-form with Yup resolver.
- `username`: required, 3-20 characters, letters/numbers/underscore.
- `email`: optional; if present, must be a valid email address.
- `password`: required, minimum 6 characters.
- `confirmPassword`: required and must match password.
- `country`: required.
- Show inline errors and an accessible error summary.

## Reusable Components

- [[frontend/components/component-catalog#FormShell|FormShell]], [[frontend/components/component-catalog#ConsentBanner|ConsentBanner]]

## Skeleton Template

- Use [[frontend/templates/AuthLayout|AuthLayout]] with stable loading placeholders.
- Use [[frontend/components/component-catalog#EmptyState|EmptyState]] for no data and [[frontend/components/component-catalog#Error State|ErrorState]] for failures.
- Do not create one-off components when shared catalogue components cover the need.

## NFR Checklist

- Performance: LCP <= 2.5s, INP <= 200ms, CLS <= 0.1, TTI <= 3.5s on p75 4G mobile.
- Bundle budget: initial JS <= 250 KB gzipped, per-route chunk <= 100 KB, initial CSS <= 50 KB.
- Resilience: cached read-only browsing during backend outage; writes disabled with clear messaging; no blank screens.
- Accessibility: WCAG 2.1 AA minimum, keyboard navigation, visible focus, logical tab order, semantic HTML, live regions, skip link.
- Security: HTTPS only, no client secrets, CSP, X-Frame-Options DENY, Referrer-Policy, safe token storage, Yup validation, XSS/CSRF protections.
- Privacy: cookie/analytics consent, data access flow, data deletion flow.
- i18n: default en-AU, no hardcoded UI strings in logic, dates DD/MM/YYYY, currency AUD ($, 2 decimals).
- Observability: Application Insights, route/page-load metrics, errors, user interactions, correlation IDs.
- Maintainability: strict TypeScript, shared components, ESLint/Prettier in CI, axe-core in CI, >=70% unit coverage for business logic.

## Wikilinks

- Route group: [[architecture/routing#Public Auth Routes|Public Auth Routes]]
- API reference: [[architecture/api-client#Auth|Auth]]
