# State Management

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/routing|routing]] | [[architecture/api-client|api-client]]

## Server State

Use TanStack Query for products, orders, pickup points, notifications, delivery schedules, admin analytics, and profile reads. Cached stale data remains visible during read failures.

## Zustand Stores

| Store | Owns | Used By |
|---|---|---|
| `use-auth-store` | user, role, session, token expiry, auth actions | [[screens/auth/LoginPage]], [[architecture/routing]] |
| `use-cart-store` | cart items, selected country, cart persistence | [[screens/customer/CartPage]], [[screens/customer/CheckoutPage]] |
| `use-theme-store` | light/dark/system theme | [[frontend/design/theme]], [[screens/customer/SettingsPage]] |
| `use-country-store` | active market/country | [[screens/onboarding/CountrySelectionPage]], product pricing |
| `use-filter-store` | per-module filters and table state | admin tables and product lists |

## Persistence

- Cart and non-sensitive preferences may use localStorage.
- Avoid localStorage for sensitive tokens.
- Persisted stores should include schema versions.

## Realtime

- `orders` invalidates order lists/details.
- `delivery_schedule:delivery_partner_id=eq.{id}` updates delivery tasks.
- `notifications:user_id=eq.{id}` updates inbox/badges.
