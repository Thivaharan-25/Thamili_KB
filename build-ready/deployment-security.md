# Deployment Security

> **Extracted from:** `vercel.json`, NFR security requirements  
> **See also:** [[architecture/nfr-compliance|nfr-compliance]]

Existing `vercel.json` only defines a product share rewrite. The web app needs security headers.

## Required Headers

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" },
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "Permissions-Policy", "value": "camera=(), microphone=(), geolocation=(self)" },
        { "key": "Strict-Transport-Security", "value": "max-age=31536000; includeSubDomains" }
      ]
    }
  ]
}
```

## CSP

Add CSP after confirming exact domains:

- Supabase project URL.
- Stripe scripts/connect endpoints.
- Mapbox scripts/styles/tiles/geocoding.
- Application Insights ingestion endpoint.
- Vercel API base URL.

## Secret Rules

- `SUPABASE_SERVICE_ROLE_KEY`, `STRIPE_SECRET_KEY`, Twilio secrets, and `INTERNAL_API_SECRET` stay server-only.
- Client uses only `VITE_SUPABASE_ANON_KEY`, `VITE_STRIPE_PUBLISHABLE_KEY`, and `VITE_MAPBOX_PUBLIC_KEY`.

