# ResetPasswordPage

> **Screen file:** `src/pages/auth/ResetPasswordPage.tsx`  
> **Route:** `/reset-password`  
> **Template:** [[frontend/templates/AuthLayout|AuthLayout]]  
> **Architecture:** [[architecture/routing|routing]] | [[architecture/api-client|api-client]] | [[architecture/state-management|state-management]]  
> **Design:** [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]] | [[frontend/components/component-catalog|component-catalog]]

## Purpose

Complete token-based password reset after the user opens a reset link.

## User Flow

1. User opens reset link from email.
2. App validates that the Supabase recovery session/token is present.
3. User enters new password and confirmation.
4. Form validates strength and match.
5. Submit updates the password and routes to login or authenticated home.
6. If token is invalid or expired, show a safe error and link to [[screens/auth/ForgotPasswordPage|ForgotPasswordPage]].

## API Endpoints And Data

- Supabase auth: `supabase.auth.update({ password })`.
- Optional auth session check: `supabase.auth.session()`.
- Observability: track reset success/failure without logging password or token.

## Validation

- Use `ResetPasswordForm` with `auth.schema.ts`.
- Password must meet the project password policy.
- Confirmation must match.
- Token/session errors must not expose sensitive data.

## Reusable Components

- [[frontend/components/component-catalog#FormShell|FormShell]]
- [[frontend/components/component-catalog#Error State|ErrorState]]
- [[frontend/components/component-catalog#ValidationSummary|ValidationSummary]]

## Skeleton Template

- Use [[frontend/templates/AuthLayout|AuthLayout]].
- No blank screen while token/session is checked.
- Submit button locks while updating.

## NFR Checklist

- Accessibility: focus the first error or success heading after submit.
- Security: never log reset tokens or passwords.
- Resilience: expired token path must offer a retry flow.

## Wikilinks

- Route group: [[architecture/routing#Public Auth Routes|Public Auth Routes]]
- API reference: [[architecture/api-client#Auth|Auth]]
