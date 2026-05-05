# Route Guards

> **Extracted from:** `src/utils/rbac.ts`, `src/navigation/AppNavigator.tsx`  
> **See also:** [[architecture/routing|routing]]

## Roles

```ts
type UserRole = 'customer' | 'admin' | 'delivery_partner';
```

## Guard Rules

| Route group | Access |
|---|---|
| Public auth routes | Guest only where appropriate; redirect authenticated users to role home. |
| Product browsing | Public read access. |
| Cart | Public local cart allowed; checkout requires auth. |
| Customer account routes | Authenticated customer or admin where business allows. |
| Admin routes | `role === 'admin'`. |
| Delivery routes | `role === 'delivery_partner'`. |
| Order detail | Customer can access own order; admin can access all; delivery partner can access assigned order/schedule. |
| Write actions | Always require auth and role check; RLS/RPC remains authoritative. |

## Web Guard Components

- `ProtectedRoute`: requires auth.
- `RoleRoute`: requires one or more roles.
- `PermissionGate`: controls action visibility/disabled states.
- `ForbiddenPage`: shown for authenticated users without permission.
- `AuthPromptDialog`: shown for guest protected actions like checkout/add-to-cart if required.

