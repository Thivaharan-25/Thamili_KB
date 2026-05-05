# Folder Structure

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/routing|routing]] | [[architecture/state-management|state-management]] | [[architecture/api-client|api-client]]

```text
src/
  main.tsx
  App.tsx
  router.tsx
  lib/
    api/
      client.ts
      errors.ts
      index.ts
      endpoints/
        auth.ts
        products.ts
        orders.ts
        addresses.ts
        pickup-points.ts
        delivery.ts
        notifications.ts
        payment-methods.ts
        users.ts
        stripe.ts
      interceptors/
        auth.interceptor.ts
        correlation.interceptor.ts
        error.interceptor.ts
    supabase/
      client.ts
      types.ts
    security/
      token-manager.ts
      sanitizer.ts
      permission-guard.tsx
      idle-timeout.ts
    observability/
      app-insights.ts
      correlation.ts
    utils/
      cn.ts
      format-date.ts
      format-currency.ts
      cart.ts
      cart-validation.ts
      rate-limiter.ts
      rbac.ts
    i18n.ts
    mapbox.ts
  stores/
    use-auth-store.ts
    use-cart-store.ts
    use-theme-store.ts
    use-country-store.ts
    use-filter-store.ts
  hooks/
    use-auth.ts
    use-products.ts
    use-orders.ts
    use-addresses.ts
    use-delivery.ts
    use-notifications.ts
    use-pickup-points.ts
    use-realtime-orders.ts
  components/
    ui/
      accordion.tsx
      alert-dialog.tsx
      avatar.tsx
      badge.tsx
      button.tsx
      calendar.tsx
      card.tsx
      checkbox.tsx
      dialog.tsx
      dropdown-menu.tsx
      form.tsx
      input.tsx
      label.tsx
      popover.tsx
      radio-group.tsx
      select.tsx
      separator.tsx
      sheet.tsx
      skeleton.tsx
      switch.tsx
      table.tsx
      tabs.tsx
      textarea.tsx
      toast.tsx
      tooltip.tsx
    shared/
      AddressCard.tsx
      AddressForm.tsx
      AuthPromptDialog.tsx
      ConfirmDialog.tsx
      ConsentBanner.tsx
      CountrySelector.tsx
      DataTable.tsx
      EmptyState.tsx
      ErrorState.tsx
      FilterBar.tsx
      FormShell.tsx
      MapPicker.tsx
      NotificationItem.tsx
      OrderCard.tsx
      OrderDetails.tsx
      PageHeader.tsx
      PaymentMethodList.tsx
      PickupPointForm.tsx
      PriceDisplay.tsx
      ProductCard.tsx
      ProductForm.tsx
      QuantitySelector.tsx
      SearchInput.tsx
      SkeletonState.tsx
      skeletons/
        AuthFormSkeleton.tsx
        ProductGridSkeleton.tsx
        ProductDetailSkeleton.tsx
        CartListSkeleton.tsx
        CheckoutSkeleton.tsx
        OrderListSkeleton.tsx
        OrderDetailSkeleton.tsx
        ProfileSkeleton.tsx
        AdminDashboardSkeleton.tsx
        DataTableSkeleton.tsx
        MapPanelSkeleton.tsx
        NotificationListSkeleton.tsx
      StatusBadge.tsx
      StockBadge.tsx
      ThemeToggle.tsx
    forms/
      LoginForm.tsx
      RegisterForm.tsx
      ForgotPasswordForm.tsx
      ResetPasswordForm.tsx
      OtpForm.tsx
      AddressFields.tsx
      PaymentFields.tsx
      ProductFields.tsx
      PickupPointFields.tsx
    layout/
      AuthLayout.tsx
      AppLayout.tsx
      DeliveryLayout.tsx
      Topbar.tsx
      Sidebar.tsx
      BottomNav.tsx
      SkipToContent.tsx
      RouteAnnouncer.tsx
      OutageBanner.tsx
    feedback/
      GlobalErrorBoundary.tsx
      LoadingOverlay.tsx
      LiveRegion.tsx
      ToastProvider.tsx
      ValidationSummary.tsx
      InlineFieldError.tsx
  pages/
    auth/
      AuthLayout.tsx
      LoginPage.tsx
      RegisterPage.tsx
      VerifyEmailPage.tsx
      ForgotPasswordPage.tsx
      ResetPasswordPage.tsx
      OtpPage.tsx
    onboarding/
      CountrySelectionPage.tsx
      OnboardingPage.tsx
      WelcomePage.tsx
    customer/
      HomePage.tsx
      ProductsPage.tsx
      ProductDetailPage.tsx
      CartPage.tsx
      CheckoutPage.tsx
      OrderConfirmationPage.tsx
      OrdersPage.tsx
      OrderDetailPage.tsx
      ProfilePage.tsx
      EditProfilePage.tsx
      AddressesPage.tsx
      PaymentsPage.tsx
      NotificationsPage.tsx
      NotificationSettingsPage.tsx
      SettingsPage.tsx
      ChangePasswordPage.tsx
    admin/
      DashboardPage.tsx
      ProductsPage.tsx
      ProductNewPage.tsx
      ProductEditPage.tsx
      OrdersPage.tsx
      OrderDetailPage.tsx
      DeliveryPage.tsx
      PickupPointsPage.tsx
      PickupPointNewPage.tsx
      PickupPointEditPage.tsx
      DeliveryPartnersPage.tsx
      DeliveryPartnerNewPage.tsx
      TopProductsPage.tsx
      NotificationHistoryPage.tsx
      SettingsPage.tsx
    delivery/
      DashboardPage.tsx
      VanSalesPage.tsx
      OrderDetailPage.tsx
    errors/
      ErrorPage.tsx
      ForbiddenPage.tsx
      NotFoundPage.tsx
  validation/
    auth.schema.ts
    address.schema.ts
    checkout.schema.ts
    product.schema.ts
    pickup-point.schema.ts
    notification.schema.ts
  i18n/locales/en-AU.json
  styles/globals.css
```

## Rules

- Dedicated file for every screen.
- Auth screens stay separate. Shared auth calls live in `src/lib/api/endpoints/auth.ts`; shared auth form UI lives in `src/components/forms/`.
- Reuse shared components from [[frontend/components/component-catalog|component-catalog]] instead of screen-specific duplicates.
- Pages compose UI and call hooks; services own backend calls.
- Route guards and Supabase RLS both enforce access.

## Why These Files Exist

This section explains the responsibility of each file group so implementation does not drift into giant screen files or duplicated components.

| File or folder | Why it exists | What should not go here |
|---|---|---|
| `main.tsx` | Browser entry point. Mounts React and global CSS. | Business logic, routes, API calls. |
| `App.tsx` | Provider stack: QueryClient, theme, auth hydration, i18n, toast, router. | Page markup or feature logic. |
| `router.tsx` | Single route map using `createBrowserRouter()`, route guards, lazy route imports, error elements. | API calls, form logic, component styling. |
| `lib/api/client.ts` | Shared fetch/Supabase wrapper behavior, correlation IDs, common error mapping. | Screen-specific queries. |
| `lib/api/endpoints/*.ts` | Feature API modules. Screens/hooks call these instead of raw Supabase. | React components or DOM code. |
| `lib/api/endpoints/auth.ts` | All auth operations: login, register, forgot password, reset password, send OTP, verify OTP, logout, profile lookup. | Auth page JSX. |
| `lib/supabase/client.ts` | Supabase JS v1 singleton and env-based initialization. | Service role keys or server-only secrets. |
| `lib/security/*` | Token handling, sanitization, permission guards, idle timeout. | Visual UI except the guard wrapper. |
| `lib/observability/*` | Application Insights setup and correlation ID helpers. | User-facing error UI. |
| `lib/utils/*` | Pure helpers: formatting, cart math, RBAC, rate limiting. | React state or side effects unless explicitly named. |
| `stores/*` | Zustand client state: auth, cart, theme, country, filters. | Server-cache data that belongs in React Query. |
| `hooks/*` | React Query hooks and feature orchestration hooks. | Raw JSX screens or low-level fetch code. |
| `components/ui/*` | shadcn/Radix primitive building blocks. | Business-specific behavior. |
| `components/shared/*` | Reusable domain UI used by multiple screens. | Page-only layout that is used once. |
| `components/shared/SkeletonState.tsx` | Chooses the correct loading skeleton variant for each page. See [[frontend/design/loading-skeletons|loading-skeletons]]. | Business data fetching or mutation logic. |
| `components/shared/skeletons/*` | Reusable route/content skeleton shapes that prevent blank screens and layout shift. | Spinners as primary page loading UI. |
| `components/forms/*` | Reusable form bodies and field groups. Pages provide submit behavior. | Direct route navigation or API calls unless intentionally passed in as props. |
| `components/layout/*` | App shells, navigation, skip link, route announcer, outage banner. | Screen-specific content. |
| `components/feedback/*` | Error, loading, live-region, toast, validation feedback primitives. | Feature data fetching. |
| `pages/*` | Route-level composition. Each file maps to one screen. | Reusable components that other screens need. |
| `validation/*.schema.ts` | Yup schemas shared by forms and pages. | API calls or component state. |
| `i18n/locales/*.json` | UI strings. Required so logic does not hardcode text. | Business calculations. |
| `styles/globals.css` | Tailwind layers, CSS variables, focus styles, base accessibility styles. | Component-specific one-off styling. |

## Screen Implementation Mapping

Use this table when building each screen. The page file composes the screen; form/shared components hold reusable UI; endpoint modules hold backend calls; validation schemas hold Yup rules.

### Auth Screens

| Screen doc | Page file | Uses components | Uses API file | Uses validation |
|---|---|---|---|---|
| [[screens/auth/LoginPage]] | `pages/auth/LoginPage.tsx` | `forms/LoginForm.tsx`, `shared/FormShell.tsx`, `feedback/ValidationSummary.tsx` | `lib/api/endpoints/auth.ts` -> `login()`, `lookupUsername()` | `validation/auth.schema.ts` |
| [[screens/auth/RegisterPage]] | `pages/auth/RegisterPage.tsx` | `forms/RegisterForm.tsx`, `shared/FormShell.tsx`, `shared/ConsentBanner.tsx` | `lib/api/endpoints/auth.ts` -> `register()` | `validation/auth.schema.ts` |
| [[screens/auth/VerifyEmailPage]] | `pages/auth/VerifyEmailPage.tsx` | `shared/EmptyState.tsx`, `shared/FormShell.tsx` | `lib/api/endpoints/auth.ts` -> `getCurrentUser()` | `validation/auth.schema.ts` if resend form exists |
| [[screens/auth/ForgotPasswordPage]] | `pages/auth/ForgotPasswordPage.tsx` | `forms/ForgotPasswordForm.tsx`, `shared/FormShell.tsx` | `lib/api/endpoints/auth.ts` -> `forgotPassword()`, `lookupUsername()`, `claimAccountEmail()` | `validation/auth.schema.ts` |
| [[screens/auth/ResetPasswordPage]] | `pages/auth/ResetPasswordPage.tsx` | `forms/ResetPasswordForm.tsx`, `shared/FormShell.tsx` | `lib/api/endpoints/auth.ts` -> `resetPassword()` | `validation/auth.schema.ts` |
| [[screens/auth/OtpPage]] | `pages/auth/OtpPage.tsx` | `forms/OtpForm.tsx`, `shared/FormShell.tsx`, `feedback/LiveRegion.tsx` | `lib/api/endpoints/auth.ts` -> `sendOtp()`, `verifyOtp()` | `validation/auth.schema.ts` |

### Customer Screens

| Screen doc | Page file | Uses components | Uses API file | Uses validation |
|---|---|---|---|---|
| [[screens/customer/HomePage]] | `pages/customer/HomePage.tsx` | `shared/ProductCard.tsx`, `shared/SearchInput.tsx`, `shared/SkeletonState.tsx` | `endpoints/products.ts` | None, search params only |
| [[screens/customer/ProductsPage]] | `pages/customer/ProductsPage.tsx` | `shared/ProductCard.tsx`, `shared/SearchInput.tsx`, `shared/FilterBar.tsx`, `shared/QuantitySelector.tsx` | `endpoints/products.ts` | None, filter params only |
| [[screens/customer/ProductDetailPage]] | `pages/customer/ProductDetailPage.tsx` | `shared/ProductCard.tsx`, `shared/QuantitySelector.tsx`, `shared/StockBadge.tsx`, `shared/PriceDisplay.tsx` | `endpoints/products.ts` | Cart quantity rules in `validation/checkout.schema.ts` or cart utility |
| [[screens/customer/CartPage]] | `pages/customer/CartPage.tsx` | `shared/QuantitySelector.tsx`, `shared/ProductCard.tsx`, `shared/EmptyState.tsx` | `endpoints/products.ts`, cart store | `validation/checkout.schema.ts` |
| [[screens/customer/CheckoutPage]] | `pages/customer/CheckoutPage.tsx` | `forms/AddressFields.tsx`, `forms/PaymentFields.tsx`, `shared/AddressForm.tsx`, `shared/MapPicker.tsx`, `shared/PaymentMethodList.tsx` | `endpoints/orders.ts`, `endpoints/addresses.ts`, `endpoints/pickup-points.ts`, `endpoints/stripe.ts` | `validation/checkout.schema.ts`, `validation/address.schema.ts` |
| [[screens/customer/OrderConfirmationPage]] | `pages/customer/OrderConfirmationPage.tsx` | `shared/OrderDetails.tsx`, `shared/StatusBadge.tsx` | `endpoints/orders.ts` | None |
| [[screens/customer/OrdersPage]] | `pages/customer/OrdersPage.tsx` | `shared/OrderCard.tsx`, `shared/StatusBadge.tsx`, `shared/FilterBar.tsx` | `endpoints/orders.ts` | None, filter params only |
| [[screens/customer/OrderDetailPage]] | `pages/customer/OrderDetailPage.tsx` | `shared/OrderDetails.tsx`, `shared/StatusBadge.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/orders.ts`, `endpoints/stripe.ts` for refund/cancel paths | Optional cancellation reason schema |
| [[screens/customer/ProfilePage]] | `pages/customer/ProfilePage.tsx` | `shared/PageHeader.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/users.ts`, `endpoints/auth.ts` | Account deletion confirmation rules |
| [[screens/customer/EditProfilePage]] | `pages/customer/EditProfilePage.tsx` | `shared/FormShell.tsx`, `feedback/ValidationSummary.tsx` | `endpoints/users.ts` | `validation/auth.schema.ts` or profile schema if split later |
| [[screens/customer/AddressesPage]] | `pages/customer/AddressesPage.tsx` | `shared/AddressCard.tsx`, `shared/AddressForm.tsx`, `shared/MapPicker.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/addresses.ts` | `validation/address.schema.ts` |
| [[screens/customer/PaymentsPage]] | `pages/customer/PaymentsPage.tsx` | `shared/PaymentMethodList.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/payment-methods.ts`, `endpoints/stripe.ts` | Payment metadata validation |
| [[screens/customer/NotificationsPage]] | `pages/customer/NotificationsPage.tsx` | `shared/NotificationItem.tsx`, `shared/EmptyState.tsx` | `endpoints/notifications.ts` | None |
| [[screens/customer/NotificationSettingsPage]] | `pages/customer/NotificationSettingsPage.tsx` | `shared/FormShell.tsx`, `ui/switch.tsx` | `endpoints/notifications.ts` | `validation/notification.schema.ts` |
| [[screens/customer/SettingsPage]] | `pages/customer/SettingsPage.tsx` | `shared/ThemeToggle.tsx`, `shared/ConsentBanner.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/users.ts`, `endpoints/auth.ts` | Settings/privacy confirmation rules |
| [[screens/customer/ChangePasswordPage]] | `pages/customer/ChangePasswordPage.tsx` | `forms/ResetPasswordForm.tsx`, `shared/FormShell.tsx` | `endpoints/auth.ts` -> `resetPassword()` or `changePassword()` | `validation/auth.schema.ts` |

### Admin Screens

| Screen doc | Page file | Uses components | Uses API file | Uses validation |
|---|---|---|---|---|
| [[screens/admin/DashboardPage]] | `pages/admin/DashboardPage.tsx` | `shared/StatsCard.tsx`, `shared/DataTable.tsx`, `shared/StatusBadge.tsx` | `endpoints/orders.ts`, `endpoints/products.ts`, `endpoints/delivery.ts` | None |
| [[screens/admin/ProductsPage]] | `pages/admin/ProductsPage.tsx` | `shared/DataTable.tsx`, `shared/ProductCard.tsx`, `shared/FilterBar.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/products.ts` | Product status/delete confirmation rules |
| [[screens/admin/ProductNewPage]] | `pages/admin/ProductNewPage.tsx` | `shared/ProductForm.tsx`, `forms/ProductFields.tsx` | `endpoints/products.ts` | `validation/product.schema.ts` |
| [[screens/admin/ProductEditPage]] | `pages/admin/ProductEditPage.tsx` | `shared/ProductForm.tsx`, `forms/ProductFields.tsx` | `endpoints/products.ts` | `validation/product.schema.ts` |
| [[screens/admin/OrdersPage]] | `pages/admin/OrdersPage.tsx` | `shared/DataTable.tsx`, `shared/OrderCard.tsx`, `shared/StatusBadge.tsx`, `shared/FilterBar.tsx` | `endpoints/orders.ts` | None, filter params only |
| [[screens/admin/OrderDetailPage]] | `pages/admin/OrderDetailPage.tsx` | `shared/OrderDetails.tsx`, `shared/StatusBadge.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/orders.ts`, `endpoints/delivery.ts`, `endpoints/notifications.ts` | Status/assignment form rules |
| [[screens/admin/DeliveryPage]] | `pages/admin/DeliveryPage.tsx` | `shared/DataTable.tsx`, `shared/StatusBadge.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/delivery.ts`, `endpoints/users.ts` | Delivery assignment rules |
| [[screens/admin/PickupPointsPage]] | `pages/admin/PickupPointsPage.tsx` | `shared/DataTable.tsx`, `shared/MapPicker.tsx`, `shared/ConfirmDialog.tsx` | `endpoints/pickup-points.ts` | Delete confirmation rules |
| [[screens/admin/PickupPointNewPage]] | `pages/admin/PickupPointNewPage.tsx` | `shared/PickupPointForm.tsx`, `forms/PickupPointFields.tsx`, `shared/MapPicker.tsx` | `endpoints/pickup-points.ts` | `validation/pickup-point.schema.ts` |
| [[screens/admin/PickupPointEditPage]] | `pages/admin/PickupPointEditPage.tsx` | `shared/PickupPointForm.tsx`, `forms/PickupPointFields.tsx`, `shared/MapPicker.tsx` | `endpoints/pickup-points.ts` | `validation/pickup-point.schema.ts` |
| [[screens/admin/DeliveryPartnersPage]] | `pages/admin/DeliveryPartnersPage.tsx` | `shared/DataTable.tsx`, `shared/ConfirmDialog.tsx`, `shared/StatusBadge.tsx` | `endpoints/users.ts` | Delete/disable confirmation rules |
| [[screens/admin/DeliveryPartnerNewPage]] | `pages/admin/DeliveryPartnerNewPage.tsx` | `shared/FormShell.tsx`, `feedback/ValidationSummary.tsx` | `endpoints/users.ts`, `endpoints/auth.ts` or `POST /api/create-user` | Delivery partner account schema |
| [[screens/admin/TopProductsPage]] | `pages/admin/TopProductsPage.tsx` | `shared/DataTable.tsx`, `shared/StatsCard.tsx`, `shared/ProductCard.tsx` | `endpoints/orders.ts`, analytics helper | None, filter params only |
| [[screens/admin/NotificationHistoryPage]] | `pages/admin/NotificationHistoryPage.tsx` | `shared/DataTable.tsx`, `shared/NotificationItem.tsx`, `shared/FormShell.tsx` | `endpoints/notifications.ts` | `validation/notification.schema.ts` |
| [[screens/admin/SettingsPage]] | `pages/admin/SettingsPage.tsx` | `shared/FormShell.tsx`, `shared/ConsentBanner.tsx`, `shared/ConfirmDialog.tsx` | settings endpoint when available | Settings schema when available |

### Delivery Screens

| Screen doc | Page file | Uses components | Uses API file | Uses validation |
|---|---|---|---|---|
| [[screens/delivery/DashboardPage]] | `pages/delivery/DashboardPage.tsx` | `shared/DataTable.tsx`, `shared/MapPicker.tsx`, `shared/StatusBadge.tsx`, `shared/OrderCard.tsx` | `endpoints/delivery.ts` | Delivery status rules |
| [[screens/delivery/VanSalesPage]] | `pages/delivery/VanSalesPage.tsx` | `shared/ProductCard.tsx`, `shared/QuantitySelector.tsx`, `shared/FormShell.tsx` | `endpoints/delivery.ts`, `endpoints/products.ts` | `validation/checkout.schema.ts` |
| [[screens/delivery/OrderDetailPage]] | `pages/delivery/OrderDetailPage.tsx` | `shared/OrderDetails.tsx`, `shared/StatusBadge.tsx`, `shared/MapPicker.tsx` | `endpoints/delivery.ts`, `endpoints/orders.ts` | Delivery status rules |
