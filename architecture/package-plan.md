# Package Plan

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/folder-structure|folder-structure]] | [[architecture/api-client|api-client]] | [[architecture/nfr-compliance|nfr-compliance]]

| Area | Packages | Why |
|---|---|---|
| Build | `vite`, `@vitejs/plugin-react` | Vite SPA. |
| React | `react`, `react-dom` | Web UI. |
| Routing | `react-router-dom` | `createBrowserRouter`, guards, error boundaries. |
| Server state | `@tanstack/react-query` | Cache, retries, stale read-only outage browsing. |
| Client state | `zustand` | Auth, cart, theme, country, filters. |
| UI | `tailwindcss`, `class-variance-authority`, `clsx`, `tailwind-merge`, `lucide-react` | shadcn/ui foundation. |
| Primitives | `@radix-ui/react-*` | Accessible dialogs, menus, tabs, select, switch, labels. |
| Forms | `react-hook-form`, `yup`, `@hookform/resolvers` | User-selected validation stack. |
| Backend | `@supabase/supabase-js@1.35.7` | Match mobile app v1 syntax. |
| Payments | `@stripe/stripe-js`, `@stripe/react-stripe-js` | Stripe Elements and PaymentIntent flow. |
| Maps | `react-map-gl`, `mapbox-gl` | Web replacement for RN Mapbox. |
| i18n | `i18next`, `react-i18next`, `i18next-browser-languagedetector`, `i18next-http-backend` | en-AU default and future translations. |
| Dates | `date-fns` | DD/MM/YYYY formatting. |
| Sanitisation | `dompurify` | User-generated HTML safety. |
| Observability | `@microsoft/applicationinsights-web`, `@microsoft/applicationinsights-react-js` | Metrics, errors, interactions, correlation IDs. |
| Testing | `vitest`, `@testing-library/react`, `@testing-library/user-event`, `axe-core`, `jest-axe`, `playwright` | Unit, accessibility, and E2E. |

## Rules

- Lazy-load Stripe, Mapbox, and analytics charts.
- Do not introduce another component library unless shadcn/Radix cannot cover the requirement.
- No secrets in Vite env except public/anon keys intended for the browser.
