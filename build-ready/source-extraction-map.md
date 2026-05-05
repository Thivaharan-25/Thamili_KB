# Source Extraction Map

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[build-ready/data-model-and-types|data-model-and-types]] | [[build-ready/api-contracts|api-contracts]]

Most build-ready information can be extracted from the React Native app. Some visual decisions still need web design confirmation.

| Build spec | Can be extracted from RN app? | Source files | Notes |
|---|---|---|---|
| Screen list and routes | Mostly yes | `src/navigation/AppNavigator.tsx`, `src/types/index.ts` | Web routes are translated from stack/tab screens into URL paths. |
| Data model/types | Yes | `src/types/index.ts`, `src/types/notifications.ts` | Use as first-pass web TypeScript types. |
| API contracts | Mostly yes | `src/services/*Service.ts`, `api/*.ts`, `supabase/functions/*` | Request/response shapes come from service method signatures and Supabase calls. |
| Validation schemas | Yes | `src/utils/validation.ts`, `src/utils/fieldValidation.ts`, `src/utils/checkoutValidation.ts`, `src/utils/regionalFormatting.ts` | Convert to `validation/*.schema.ts` with Yup. |
| Route guards | Mostly yes | `src/utils/rbac.ts`, `src/navigation/AppNavigator.tsx` | Web needs stricter URL-level guards but roles come from RN. |
| Env vars | Yes | `.env.example`, `src/config/env.ts` | Convert public browser envs to `VITE_*`; keep secrets server-only. |
| Error/i18n copy | Mostly yes | `src/i18n/locales/en.json`, `src/i18n/locales/ta.json` | Existing Tamil file has encoding issues in this checkout and should be revalidated. |
| Test plan | Partly yes | `App.test-*.tsx`, docs test notes | Web test stack differs but business logic cases transfer. |
| CI/CD | Partly yes | `.github/workflows/deploy.yml`, `vercel.json` | Needs web build/test/lint/a11y steps added. |
| Deployment headers | Partly yes | `vercel.json`, NFRs | Existing `vercel.json` only has rewrite; security headers need adding. |
| Exact wireframes | Partly | RN screen files under `src/screens/**` | Layout intent can be inferred, but web desktop/tablet layouts still need design decisions. |

