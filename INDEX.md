# T Web Knowledge Base

This folder is the React web knowledge base for rebuilding the current Thamili React Native app as a Vite SPA. It is intentionally separate from `PROJECT_DOCUMENTATION.md`.

## Confirmed Project Decisions

- Vite SPA, not Next.js.
- React + TypeScript strict mode.
- Single app with role-based routing.
- Supabase JS v1 to match the mobile app.
- react-map-gl + Mapbox GL JS for maps.
- react-hook-form + Yup for forms.
- shadcn/ui + Radix primitives for accessible UI.

## Architecture

- [[architecture/folder-structure|folder-structure]]
- [[architecture/routing|routing]]
- [[architecture/state-management|state-management]]
- [[architecture/api-client|api-client]]
- [[architecture/package-plan|package-plan]]
- [[architecture/nfr-compliance|nfr-compliance]]

## Build-Ready Specs

These files are extracted mostly from the existing React Native app and translated into the React web build plan.

- [[build-ready/source-extraction-map|source-extraction-map]]
- [[build-ready/data-model-and-types|data-model-and-types]]
- [[build-ready/api-contracts|api-contracts]]
- [[build-ready/validation-schemas|validation-schemas]]
- [[build-ready/route-guards|route-guards]]
- [[build-ready/env-vars|env-vars]]
- [[build-ready/i18n-and-errors|i18n-and-errors]]
- [[build-ready/screen-layouts|screen-layouts]]
- [[build-ready/test-plan|test-plan]]
- [[build-ready/ci-cd|ci-cd]]
- [[build-ready/deployment-security|deployment-security]]

## Frontend

- [[frontend/design/theme|theme]]
- [[frontend/design/typography|typography]]
- [[frontend/design/responsive|responsive]]
- [[frontend/design/loading-skeletons|loading-skeletons]]
- [[frontend/components/component-catalog|component-catalog]]
- [[frontend/templates/AuthLayout|AuthLayout]]
- [[frontend/templates/AppLayout|AppLayout]]
- [[frontend/templates/DeliveryLayout|DeliveryLayout]]
- [[frontend/templates/ScreenTemplate|ScreenTemplate]]

## Screens

### Auth

- [[screens/auth/LoginPage|LoginPage]]
- [[screens/auth/RegisterPage|RegisterPage]]
- [[screens/auth/VerifyEmailPage|VerifyEmailPage]]
- [[screens/auth/ForgotPasswordPage|ForgotPasswordPage]]
- [[screens/auth/ResetPasswordPage|ResetPasswordPage]]
- [[screens/auth/OtpPage|OtpPage]]

### Onboarding

- [[screens/onboarding/CountrySelectionPage|CountrySelectionPage]]
- [[screens/onboarding/OnboardingPage|OnboardingPage]]
- [[screens/onboarding/WelcomePage|WelcomePage]]

### Customer

- [[screens/customer/HomePage|HomePage]]
- [[screens/customer/ProductsPage|ProductsPage]]
- [[screens/customer/ProductDetailPage|ProductDetailPage]]
- [[screens/customer/CartPage|CartPage]]
- [[screens/customer/CheckoutPage|CheckoutPage]]
- [[screens/customer/OrderConfirmationPage|OrderConfirmationPage]]
- [[screens/customer/OrdersPage|OrdersPage]]
- [[screens/customer/OrderDetailPage|OrderDetailPage]]
- [[screens/customer/ProfilePage|ProfilePage]]
- [[screens/customer/EditProfilePage|EditProfilePage]]
- [[screens/customer/AddressesPage|AddressesPage]]
- [[screens/customer/PaymentsPage|PaymentsPage]]
- [[screens/customer/NotificationsPage|NotificationsPage]]
- [[screens/customer/NotificationSettingsPage|NotificationSettingsPage]]
- [[screens/customer/SettingsPage|SettingsPage]]
- [[screens/customer/ChangePasswordPage|ChangePasswordPage]]

### Admin

- [[screens/admin/DashboardPage|Admin DashboardPage]]
- [[screens/admin/ProductsPage|Admin ProductsPage]]
- [[screens/admin/ProductNewPage|ProductNewPage]]
- [[screens/admin/ProductEditPage|ProductEditPage]]
- [[screens/admin/OrdersPage|Admin OrdersPage]]
- [[screens/admin/OrderDetailPage|Admin OrderDetailPage]]
- [[screens/admin/DeliveryPage|DeliveryPage]]
- [[screens/admin/PickupPointsPage|PickupPointsPage]]
- [[screens/admin/PickupPointNewPage|PickupPointNewPage]]
- [[screens/admin/PickupPointEditPage|PickupPointEditPage]]
- [[screens/admin/DeliveryPartnersPage|DeliveryPartnersPage]]
- [[screens/admin/DeliveryPartnerNewPage|DeliveryPartnerNewPage]]
- [[screens/admin/TopProductsPage|TopProductsPage]]
- [[screens/admin/NotificationHistoryPage|NotificationHistoryPage]]
- [[screens/admin/SettingsPage|Admin SettingsPage]]

### Delivery

- [[screens/delivery/DashboardPage|Delivery DashboardPage]]
- [[screens/delivery/VanSalesPage|VanSalesPage]]
- [[screens/delivery/OrderDetailPage|Delivery OrderDetailPage]]

### Errors

- [[screens/errors/ForbiddenPage|ForbiddenPage]]
- [[screens/errors/NotFoundPage|NotFoundPage]]
- [[screens/errors/ErrorPage|ErrorPage]]

## Source References

- Mobile navigation: `src/navigation/AppNavigator.tsx`
- Services: `src/services/*Service.ts`
- Stores: `src/store/*Store.ts`
- Existing project documentation: `PROJECT_DOCUMENTATION.md`
