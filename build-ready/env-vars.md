# Environment Variables

> **Extracted from:** `.env.example`, `src/config/env.ts`  
> **See also:** [[architecture/nfr-compliance|nfr-compliance]]

For Vite web, browser-exposed variables must use `VITE_*`. Server-only secrets stay in Vercel/Supabase functions and must never be imported into the client bundle.

## Browser Variables

| Web env var | Source RN env var | Purpose |
|---|---|---|
| `VITE_SUPABASE_URL` | `SUPABASE_URL` | Supabase project URL. |
| `VITE_SUPABASE_ANON_KEY` | `SUPABASE_ANON_KEY` | Supabase public anon key. |
| `VITE_STRIPE_PUBLISHABLE_KEY` | `STRIPE_PUBLISHABLE_KEY` | Stripe Elements publishable key. |
| `VITE_MAPBOX_PUBLIC_KEY` | `MAPBOX_PUBLIC_KEY` | Mapbox GL JS public token. |
| `VITE_API_URL` | `API_URL` / `EXPO_PUBLIC_VERCEL_API_URL` | Vercel API base URL. |
| `VITE_APP_INSIGHTS_CONNECTION_STRING` | new | Application Insights browser telemetry. |

## Server-Only Variables

| Env var | Purpose |
|---|---|
| `SUPABASE_SERVICE_ROLE_KEY` | Server-only user/admin actions. Never bundled. |
| `STRIPE_SECRET_KEY` | Server-only payment/refund functions. |
| `INTERNAL_API_SECRET` | Server-only push/internal API protection. |
| `TWILIO_ACCOUNT_SID` | Server-only OTP/WhatsApp if retained. |
| `TWILIO_AUTH_TOKEN` | Server-only OTP/WhatsApp if retained. |
| `TWILIO_WHATSAPP_NUMBER` | Server-only OTP/WhatsApp sender. |
| `RNMAPBOX_MAPS_DOWNLOAD_TOKEN` | React Native build-only token; not needed in web client. |

## Required Web Rule

No file in `src/` may import server-only env vars. If the web app needs an action requiring a secret, call a Vercel/Supabase server function.

