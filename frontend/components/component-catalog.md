# Component Catalog

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/folder-structure|folder-structure]] | [[frontend/design/theme|theme]] | [[frontend/design/responsive|responsive]]

Components should be reused across screens. Do not create a new screen-specific component when one of these fits with props or slots.

## UI Primitives

These are shadcn/ui-owned files under `src/components/ui/`.

| File | Purpose |
|---|---|
| `accordion.tsx` | Collapsible settings/help sections. |
| `alert-dialog.tsx` | Destructive confirmations such as delete product, cancel order, delete account. |
| `avatar.tsx` | User/profile avatar display. |
| `badge.tsx` | Low-level badge primitive used by StatusBadge and StockBadge. |
| `button.tsx` | Accessible button variants and loading/disabled states. |
| `calendar.tsx` | Date picking for delivery schedules and reports. |
| `card.tsx` | Individual repeated item cards only; not nested page sections. |
| `checkbox.tsx` | Consent and multi-select fields. |
| `dialog.tsx` | Modal and form dialogs. |
| `dropdown-menu.tsx` | User menu and compact table actions. |
| `form.tsx` | Form field primitives for react-hook-form. |
| `input.tsx` | Text input primitive. |
| `label.tsx` | Form labels. |
| `popover.tsx` | Lightweight overlays such as date picker containers. |
| `radio-group.tsx` | Payment method, delivery method, country options. |
| `select.tsx` | Status, country, category, sort controls. |
| `separator.tsx` | Visual grouping. |
| `sheet.tsx` | Mobile filters, edit panels, side drawers. |
| `skeleton.tsx` | Primitive loading blocks used by SkeletonState. |
| `switch.tsx` | Settings toggles and active states. |
| `table.tsx` | DataTable primitive. |
| `tabs.tsx` | Profile/settings/admin tabbed views. |
| `textarea.tsx` | Notes, descriptions, support messages. |
| `toast.tsx` | Toast surface. |
| `tooltip.tsx` | Icon button labels and short help. |

## Layout Components

## AuthLayout

`src/components/layout/AuthLayout.tsx` and `src/pages/auth/AuthLayout.tsx`. Auth-only wrapper with skip link, centered form area, brand, theme/language controls, and `<Outlet />`.

## AppLayout

`src/components/layout/AppLayout.tsx`. Customer/admin shell with topbar, desktop sidebar, mobile bottom nav, outage banner, consent banner, route announcer, and main landmark.

## DeliveryLayout

`src/components/layout/DeliveryLayout.tsx`. Delivery partner shell with compact navigation, sync/outage state, large touch targets, and route outlet.

## Topbar

`src/components/layout/Topbar.tsx`. Logo, account menu, notifications, search slot, theme toggle.

## Sidebar

`src/components/layout/Sidebar.tsx`. Desktop role-aware navigation.

## BottomNav

`src/components/layout/BottomNav.tsx`. Mobile role-aware navigation with icon labels.

## SkipToContent

`src/components/layout/SkipToContent.tsx`. Required on every layout.

## RouteAnnouncer

`src/components/layout/RouteAnnouncer.tsx`. Live region announcing route changes.

## OutageBanner

`src/components/layout/OutageBanner.tsx`. Global backend outage/read-only warning.

## Shared Components

## PageHeader

`src/components/shared/PageHeader.tsx`. Route title, subtitle, breadcrumbs, and action slot.

## DataTable

`src/components/shared/DataTable.tsx`. Sortable/filterable table with mobile card fallback.

## ProductCard

`src/components/shared/ProductCard.tsx`. Product image, name, price, stock, and add-to-cart action.

## OrderCard

`src/components/shared/OrderCard.tsx`. Order summary with status, total, date, destination.

## OrderDetails

`src/components/shared/OrderDetails.tsx`. Shared order detail display for customer, admin, and delivery pages. Role-specific actions are passed as slots.

## StatusBadge

`src/components/shared/StatusBadge.tsx`. Shared badge for order, payment, delivery, product active, and notification status.

## StockBadge

`src/components/shared/StockBadge.tsx`. Product stock status by selected country.

## PriceDisplay

`src/components/shared/PriceDisplay.tsx`. Currency formatting with AUD two decimals.

## FormShell

`src/components/shared/FormShell.tsx`. Standard title, fields, error summary, submit row, and live region wrapper.

## AddressCard

`src/components/shared/AddressCard.tsx`. Saved address display and default badge.

## AddressForm

`src/components/shared/AddressForm.tsx`. Shared address create/edit form.

## PaymentMethodList

`src/components/shared/PaymentMethodList.tsx`. Saved payment method list and selector.

## EmptyState

`src/components/shared/EmptyState.tsx`. Accessible empty state with optional action.

## Error State

`src/components/shared/ErrorState.tsx`. Accessible failure state with retry and correlation id.

## Skeleton State

`src/components/shared/SkeletonState.tsx`. Stable loading placeholders matching final layout dimensions. It selects reusable skeleton variants from `src/components/shared/skeletons/`.

Required variants are documented in [[frontend/design/loading-skeletons|loading-skeletons]]:

- `AuthFormSkeleton.tsx`
- `ProductGridSkeleton.tsx`
- `ProductDetailSkeleton.tsx`
- `CartListSkeleton.tsx`
- `CheckoutSkeleton.tsx`
- `OrderListSkeleton.tsx`
- `OrderDetailSkeleton.tsx`
- `ProfileSkeleton.tsx`
- `AdminDashboardSkeleton.tsx`
- `DataTableSkeleton.tsx`
- `MapPanelSkeleton.tsx`
- `NotificationListSkeleton.tsx`

## MapPicker

`src/components/shared/MapPicker.tsx`. Lazy-loaded react-map-gl Mapbox picker with autocomplete and fallback.

## ProductForm

`src/components/shared/ProductForm.tsx`. Admin create/edit product form.

## PickupPointForm

`src/components/shared/PickupPointForm.tsx`. Admin create/edit pickup point form.

## NotificationItem

`src/components/shared/NotificationItem.tsx`. Notification row/card with read state and deep-link action.

## StatsCard

`src/components/shared/StatsCard.tsx`. Admin dashboard metric card.

## SearchInput

`src/components/shared/SearchInput.tsx`. Debounced search with clear button and keyboard support.

## FilterBar

`src/components/shared/FilterBar.tsx`. Shared filter row/sheet trigger for product, order, and admin lists.

## QuantitySelector

`src/components/shared/QuantitySelector.tsx`. Pack/loose quantity control for product, cart, checkout, and van sales.

## ConsentBanner

`src/components/shared/ConsentBanner.tsx`. Cookie/analytics consent banner.

## CountrySelector

`src/components/shared/CountrySelector.tsx`. Active country/market selector.

## ConfirmDialog

`src/components/shared/ConfirmDialog.tsx`. Reusable confirmation wrapper around AlertDialog.

## AuthPromptDialog

`src/components/shared/AuthPromptDialog.tsx`. Login/register prompt when guest tries protected action.

## ThemeToggle

`src/components/shared/ThemeToggle.tsx`. Light/dark/system theme control.

## Form Components

## LoginForm

`src/components/forms/LoginForm.tsx`. Login fields only; calls a submitted handler from [[screens/auth/LoginPage|LoginPage]].

## RegisterForm

`src/components/forms/RegisterForm.tsx`. Registration fields only.

## ForgotPasswordForm

`src/components/forms/ForgotPasswordForm.tsx`. Email/username recovery fields.

## ResetPasswordForm

`src/components/forms/ResetPasswordForm.tsx`. New password and confirm password fields.

## OtpForm

`src/components/forms/OtpForm.tsx`. OTP entry and resend cooldown UI.

## AddressFields

`src/components/forms/AddressFields.tsx`. Reusable field group used by AddressForm and CheckoutPage.

## PaymentFields

`src/components/forms/PaymentFields.tsx`. Delivery/payment selector fields for checkout.

## ProductFields

`src/components/forms/ProductFields.tsx`. Product create/edit fields used by ProductForm.

## PickupPointFields

`src/components/forms/PickupPointFields.tsx`. Pickup point create/edit fields used by PickupPointForm.

## Feedback Components

## GlobalErrorBoundary

`src/components/feedback/GlobalErrorBoundary.tsx`. Non-route global fallback.

## LoadingOverlay

`src/components/feedback/LoadingOverlay.tsx`. Blocking loading state for rare app-wide operations.

## LiveRegion

`src/components/feedback/LiveRegion.tsx`. Shared async announcement component.

## ToastProvider

`src/components/feedback/ToastProvider.tsx`. Toast provider and project-wide toast helper.

## ValidationSummary

`src/components/feedback/ValidationSummary.tsx`. Accessible form error summary.

## InlineFieldError

`src/components/feedback/InlineFieldError.tsx`. Field-level error display with accessible ids.

## ProtectedRoute

`src/components/shared/ProtectedRoute.tsx` or `src/lib/security/permission-guard.tsx`. Route guard for auth, role, and permission checks.
