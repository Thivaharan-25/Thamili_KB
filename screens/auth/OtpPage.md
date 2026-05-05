# OtpPage

> **Screen file:** `src/pages/auth/OtpPage.tsx`  
> **Route:** `/otp`  
> **Template:** [[frontend/templates/AuthLayout|AuthLayout]]  
> **Architecture:** [[architecture/routing|routing]] | [[architecture/api-client|api-client]] | [[architecture/state-management|state-management]]  
> **Design:** [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]] | [[frontend/components/component-catalog|component-catalog]]

## Purpose

Handle one-time-password verification for email, phone, or future MFA-style auth flows.

## User Flow

1. User reaches OTP after login, registration, password recovery, or phone/email verification.
2. Page shows masked destination and OTP input.
3. User submits OTP.
4. App verifies code and routes to the next step.
5. User can resend OTP after cooldown.
6. Expired or invalid codes show inline errors without clearing all entered context.

## API Endpoints And Data

- Auth endpoint module: `sendOtp(input)`.
- Auth endpoint module: `verifyOtp(input)`.
- Optional Supabase auth OTP methods if enabled by backend config.
- Optional `otp_verifications` table or RPC if the project keeps custom OTP records.

## Validation

- Use `OtpForm` with `auth.schema.ts`.
- OTP must match the configured length and numeric/alphanumeric rule.
- Resend action must be rate limited and disabled during cooldown.

## Reusable Components

- [[frontend/components/component-catalog#FormShell|FormShell]]
- [[frontend/components/component-catalog#Error State|ErrorState]]
- [[frontend/components/component-catalog#LiveRegion|LiveRegion]]

## Skeleton Template

- Use [[frontend/templates/AuthLayout|AuthLayout]].
- Keep input dimensions stable between idle, error, cooldown, and pending states.

## NFR Checklist

- Security: never reveal full email/phone; rate-limit resend and verify.
- Accessibility: OTP fields must be keyboard-friendly and screen-reader labelled.
- Resilience: resend and verify failures preserve user context.

## Wikilinks

- Route group: [[architecture/routing#Public Auth Routes|Public Auth Routes]]
- API reference: [[architecture/api-client#Auth|Auth]]
