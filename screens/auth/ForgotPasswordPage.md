# ForgotPasswordPage

> **Screen file:** `src/pages/auth/ForgotPasswordPage.tsx`  
> **Route:** `/forgot-password`  
> **Template:** [[frontend/templates/AuthLayout|AuthLayout]]  
> **Architecture:** [[architecture/routing|routing]] | [[architecture/api-client|api-client]] | [[architecture/state-management|state-management]]  
> **Design:** [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]] | [[frontend/components/component-catalog|component-catalog]]

## Purpose

Start password recovery, including username-based delivery partner recovery.

## User Flow

1. User enters email or username.
2. Lookup username when needed.
3. If the account uses a dummy delivery email, collect a real email claim.
4. Send password reset.
5. Show neutral confirmation without leaking account existence.

## API Endpoints And Data

- RPC: `lookup_user_by_username`.
- RPC: `claim_account_email`.
- Supabase auth: `supabase.auth.api.resetPasswordForEmail(email)`.

## Validation

- Use react-hook-form with Yup resolver.
- Client validation mirrors server/database rules.
- Show inline errors and an accessible error summary.

## Reusable Components

- [[frontend/components/component-catalog#FormShell|FormShell]], [[frontend/components/component-catalog#Error State|ErrorState]]

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
