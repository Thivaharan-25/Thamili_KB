# i18n And Error Copy

> **Extracted from:** `src/i18n/locales/en.json`, `src/i18n/locales/ta.json`, validation files  
> **See also:** [[architecture/nfr-compliance|nfr-compliance]]

## Existing Namespaces

The RN app already has translation namespaces that should be reused and extended:

- `common`
- `auth`
- `navigation`
- `products`
- `cart`
- `orders`
- `profile`
- `admin`
- `delivery`
- `notifications`

## Required Web Additions

Add keys for web-only states:

```json
{
  "common": {
    "offlineReadOnly": "You are viewing cached data. Actions are temporarily unavailable.",
    "tryAgain": "Try again",
    "correlationId": "Reference ID: {{id}}"
  },
  "errors": {
    "pageLoadFailed": "We could not load this page.",
    "notFound": "Page not found",
    "forbidden": "You do not have permission to view this page.",
    "backendUnavailable": "Service is temporarily unavailable."
  },
  "auth": {
    "resetPassword": "Reset password",
    "otpVerification": "Verification code",
    "otpResendCooldown": "You can request another code in {{seconds}} seconds."
  }
}
```

## Error Handling Rules

- Validation messages come from Yup/i18n keys, not hardcoded page strings.
- API errors map to safe user messages.
- Technical errors are logged to Application Insights with correlation ID.
- User-facing errors may show correlation ID but never secrets, tokens, raw SQL, or stack traces.

## Known Issue

The Tamil locale file appears mojibake/encoding-corrupted in this checkout. Before web release, re-import Tamil translations as valid UTF-8.

