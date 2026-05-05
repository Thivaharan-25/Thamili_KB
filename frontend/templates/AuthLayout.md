# AuthLayout

> **Template for:** [[screens/auth/LoginPage|LoginPage]], [[screens/auth/RegisterPage|RegisterPage]], [[screens/auth/VerifyEmailPage|VerifyEmailPage]], [[screens/auth/ForgotPasswordPage|ForgotPasswordPage]]

## Skeleton

- Skip-to-content link first.
- Centered card with brand/logo.
- Max width around 420px.
- Language/theme controls keyboard accessible.
- Error summary uses live region and receives focus on submit failure.

## NFRs

- No layout shift between idle, loading, and error states.
- Submit locks while pending.
- Auth errors do not leak account existence.
