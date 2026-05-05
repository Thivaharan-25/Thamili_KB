# Test Plan

> **Derived from:** existing `App.test-*.tsx`, NFRs, screen map  
> **See also:** [[architecture/nfr-compliance|nfr-compliance]]

## Unit Tests

- Cart calculations and stock validation.
- Product price/stock by active country.
- Yup validation schemas.
- RBAC route guard helpers.
- Formatting utilities.
- API error mapping.

## Component Tests

- Auth forms submit valid data and show validation errors.
- ProductCard add-to-cart states.
- QuantitySelector pack/loose behavior.
- DataTable sort/filter/mobile fallback.
- OrderDetails role-specific actions.
- SkeletonState variants render stable dimensions.

## Accessibility Tests

- axe-core/jest-axe on every page shell and critical form.
- Keyboard navigation for auth, checkout, admin tables, dialogs.
- Focus reset on route change.
- Live region announcements for async saves/errors.

## E2E Tests

- Browse products as guest.
- Register/login/logout.
- Add to cart and checkout.
- View order history and detail.
- Admin product create/edit/toggle.
- Admin assign delivery.
- Delivery partner status update.
- Backend outage/read-only state.

