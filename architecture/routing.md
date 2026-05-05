# Routing

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/folder-structure|folder-structure]] | [[architecture/state-management|state-management]]

Use `createBrowserRouter()` in `src/router.tsx`. Each route has an error element using [[screens/errors/ErrorPage|ErrorPage]].

## Public Auth Routes

| Route | Screen |
|---|---|
| `/login` | [[screens/auth/LoginPage]] |
| `/register` | [[screens/auth/RegisterPage]] |
| `/verify-email` | [[screens/auth/VerifyEmailPage]] |
| `/forgot-password` | [[screens/auth/ForgotPasswordPage]] |
| `/reset-password` | [[screens/auth/ResetPasswordPage]] |
| `/otp` | [[screens/auth/OtpPage]] |

## Onboarding Routes

| Route | Screen |
|---|---|
| `/country` | [[screens/onboarding/CountrySelectionPage]] |
| `/onboarding` | [[screens/onboarding/OnboardingPage]] |
| `/welcome` | [[screens/onboarding/WelcomePage]] |

## Customer Routes

| Route | Screen |
|---|---|
| `/` | [[screens/customer/HomePage]] |
| `/products` | [[screens/customer/ProductsPage]] |
| `/products/:productId` | [[screens/customer/ProductDetailPage]] |
| `/cart` | [[screens/customer/CartPage]] |
| `/checkout` | [[screens/customer/CheckoutPage]] |
| `/orders/:orderId/confirmation` | [[screens/customer/OrderConfirmationPage]] |
| `/orders` | [[screens/customer/OrdersPage]] |
| `/orders/:orderId` | [[screens/customer/OrderDetailPage]] |
| `/profile` | [[screens/customer/ProfilePage]] |
| `/profile/edit` | [[screens/customer/EditProfilePage]] |
| `/addresses` | [[screens/customer/AddressesPage]] |
| `/payments` | [[screens/customer/PaymentsPage]] |
| `/notifications` | [[screens/customer/NotificationsPage]] |
| `/notifications/settings` | [[screens/customer/NotificationSettingsPage]] |
| `/settings` | [[screens/customer/SettingsPage]] |
| `/change-password` | [[screens/customer/ChangePasswordPage]] |

## Admin Routes

| Route | Screen |
|---|---|
| `/admin` | [[screens/admin/DashboardPage]] |
| `/admin/products` | [[screens/admin/ProductsPage]] |
| `/admin/products/new` | [[screens/admin/ProductNewPage]] |
| `/admin/products/:productId/edit` | [[screens/admin/ProductEditPage]] |
| `/admin/orders` | [[screens/admin/OrdersPage]] |
| `/admin/orders/:orderId` | [[screens/admin/OrderDetailPage]] |
| `/admin/delivery` | [[screens/admin/DeliveryPage]] |
| `/admin/pickup-points` | [[screens/admin/PickupPointsPage]] |
| `/admin/pickup-points/new` | [[screens/admin/PickupPointNewPage]] |
| `/admin/pickup-points/:pickupPointId/edit` | [[screens/admin/PickupPointEditPage]] |
| `/admin/delivery-partners` | [[screens/admin/DeliveryPartnersPage]] |
| `/admin/delivery-partners/new` | [[screens/admin/DeliveryPartnerNewPage]] |
| `/admin/top-products` | [[screens/admin/TopProductsPage]] |
| `/admin/notifications/history` | [[screens/admin/NotificationHistoryPage]] |
| `/admin/settings` | [[screens/admin/SettingsPage]] |

## Delivery Routes

| Route | Screen |
|---|---|
| `/delivery` | [[screens/delivery/DashboardPage]] |
| `/delivery/van-sales` | [[screens/delivery/VanSalesPage]] |
| `/delivery/orders/:orderId` | [[screens/delivery/OrderDetailPage]] |

## Error Routes

| Route | Screen |
|---|---|
| `/403` | [[screens/errors/ForbiddenPage]] |
| `*` | [[screens/errors/NotFoundPage]] |
| `errorElement` | [[screens/errors/ErrorPage]] |

## Guard Rules

- Admin routes require role `admin`.
- Delivery routes require role `delivery_partner`.
- Customer writes require authentication.
- UI guards are backed by Supabase RLS/RPC permissions.
- Focus resets to the page heading after route change.
