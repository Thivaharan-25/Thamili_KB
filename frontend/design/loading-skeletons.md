# Loading Skeletons

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[frontend/components/component-catalog#Skeleton State|Skeleton State]] | [[architecture/nfr-compliance|nfr-compliance]] | [[frontend/design/responsive|responsive]]

Loading skeletons are required so the web app never shows a blank screen and does not shift layout while data loads.

## Skeleton Rules

- Use `SkeletonState.tsx` for page-level loading.
- Use `ui/skeleton.tsx` only as the low-level primitive inside reusable skeleton components.
- Skeleton dimensions must match final content dimensions to protect CLS.
- Skeletons must work in light and dark theme through [[frontend/design/theme|theme]] tokens.
- Skeletons must be responsive across mobile, tablet, and desktop.
- Prefer route-specific skeleton variants over spinners for page content.
- Use `LoadingOverlay.tsx` only for rare blocking operations such as payment confirmation, never for normal page data loading.

## Skeleton Variants

| Variant | Use for | Expected shape |
|---|---|---|
| `auth-form` | Auth pages | Logo/title placeholder, 2-4 field rows, submit button row. |
| `product-grid` | Home and product catalogue | Filter/search bar plus responsive product card grid. |
| `product-detail` | Product detail | Image block, title/price/stock rows, quantity/action area. |
| `cart-list` | Cart and checkout line items | Item rows with image, text, quantity, price columns. |
| `checkout` | Checkout page | Form sections plus order summary; stacked on mobile. |
| `order-list` | Customer/admin order lists | Filter row plus order cards/table rows. |
| `order-detail` | Order detail pages | Header, status timeline, item list, summary/actions. |
| `profile` | Profile/settings pages | Header block and grouped setting rows. |
| `admin-dashboard` | Admin dashboard | Metric cards and recent table placeholders. |
| `data-table` | Admin management tables | Filter toolbar plus table row placeholders. |
| `map-panel` | Address, pickup point, delivery map screens | Fixed-height map rectangle plus detail/list panel. |
| `notification-list` | Notification inbox/history | Notification row placeholders. |

## Screen Skeleton Mapping

| Screen group | Screens | Skeleton variant |
|---|---|---|
| Auth | [[screens/auth/LoginPage]], [[screens/auth/RegisterPage]], [[screens/auth/ForgotPasswordPage]], [[screens/auth/ResetPasswordPage]], [[screens/auth/OtpPage]] | `auth-form` |
| Customer products | [[screens/customer/HomePage]], [[screens/customer/ProductsPage]] | `product-grid` |
| Product detail | [[screens/customer/ProductDetailPage]] | `product-detail` |
| Cart and checkout | [[screens/customer/CartPage]], [[screens/customer/CheckoutPage]] | `cart-list`, `checkout` |
| Customer orders | [[screens/customer/OrdersPage]], [[screens/customer/OrderDetailPage]], [[screens/customer/OrderConfirmationPage]] | `order-list`, `order-detail` |
| Profile and settings | [[screens/customer/ProfilePage]], [[screens/customer/EditProfilePage]], [[screens/customer/SettingsPage]], [[screens/customer/ChangePasswordPage]] | `profile`, `auth-form` for password form |
| Address/payment | [[screens/customer/AddressesPage]], [[screens/customer/PaymentsPage]] | `data-table`, `map-panel` where maps are present |
| Notifications | [[screens/customer/NotificationsPage]], [[screens/customer/NotificationSettingsPage]], [[screens/admin/NotificationHistoryPage]] | `notification-list`, `profile` |
| Admin dashboard | [[screens/admin/DashboardPage]] | `admin-dashboard` |
| Admin lists | [[screens/admin/ProductsPage]], [[screens/admin/OrdersPage]], [[screens/admin/PickupPointsPage]], [[screens/admin/DeliveryPartnersPage]], [[screens/admin/TopProductsPage]] | `data-table` |
| Admin forms/details | [[screens/admin/ProductNewPage]], [[screens/admin/ProductEditPage]], [[screens/admin/PickupPointNewPage]], [[screens/admin/PickupPointEditPage]], [[screens/admin/OrderDetailPage]], [[screens/admin/SettingsPage]] | `profile`, `order-detail`, `map-panel` where needed |
| Delivery | [[screens/delivery/DashboardPage]], [[screens/delivery/VanSalesPage]], [[screens/delivery/OrderDetailPage]] | `data-table`, `product-grid`, `order-detail`, `map-panel` |

## Example API

```tsx
<SkeletonState variant="product-grid" itemCount={8} />
<SkeletonState variant="order-detail" />
<SkeletonState variant="data-table" rows={10} columns={5} />
```

## Accessibility

- Skeletons should be marked with `aria-hidden="true"` when decorative.
- The containing region should use `aria-busy="true"` while loading.
- Do not announce every skeleton block to screen readers.
- When content finishes loading, move focus only if the user initiated the action.
