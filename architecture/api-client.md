# API Client

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[architecture/folder-structure|folder-structure]] | [[architecture/state-management|state-management]] | [[architecture/package-plan|package-plan]]

The web app uses Supabase JS v1 plus Vercel functions. Screens call endpoint modules/hooks, not raw `supabase.from()`.

## Supabase v1

```ts
await supabase.auth.signIn({ email, password });
await supabase.auth.signUp({ email, password });
await supabase.auth.signOut();
const user = supabase.auth.user();
await supabase.auth.update({ password });
await supabase.auth.api.resetPasswordForEmail(email);
```

## Endpoint Modules

| Module | Tables/RPCs | Screens |
|---|---|---|
| Auth | auth, OTP, `get_email_by_username`, `lookup_user_by_username`, `claim_account_email` | [[screens/auth/LoginPage]], [[screens/auth/RegisterPage]], [[screens/auth/ForgotPasswordPage]], [[screens/auth/ResetPasswordPage]], [[screens/auth/OtpPage]] |
| Products | `products`, `product-images`, `admin_toggle_product_status` | [[screens/customer/ProductsPage]], [[screens/customer/ProductDetailPage]], [[screens/admin/ProductNewPage]] |
| Orders | `orders`, `order_items`, `create_order_atomic` | [[screens/customer/CheckoutPage]], [[screens/customer/OrdersPage]], [[screens/customer/OrderDetailPage]] |
| Addresses | `addresses` | [[screens/customer/AddressesPage]], [[screens/customer/CheckoutPage]] |
| Pickup Points | `pickup_points` | [[screens/admin/PickupPointsPage]], [[screens/customer/CheckoutPage]] |
| Delivery | `delivery_schedule`, van sale order RPC | [[screens/admin/DeliveryPage]], [[screens/delivery/DashboardPage]], [[screens/delivery/VanSalesPage]] |
| Notifications | `notifications`, `notification_preferences`, `notification_templates`, `create_notification` | [[screens/customer/NotificationsPage]], [[screens/customer/NotificationSettingsPage]] |
| Payment Methods | `payment_methods` | [[screens/customer/PaymentsPage]], [[screens/customer/CheckoutPage]] |
| Users | `users`, `delete_user_by_id`, `delete_user_account` | [[screens/customer/ProfilePage]], [[screens/admin/DeliveryPartnersPage]] |
| Stripe | `POST /api/create-payment-intent` | [[screens/customer/CheckoutPage]] |

## Auth

Authentication, username lookup, registration, password reset, and password update flows.

`src/lib/api/endpoints/auth.ts` should centralize auth API calls:

```ts
login(input)
register(input) // username-first; optional email
logout()
forgotPassword(input)
resetPassword(input)
sendOtp(input)
verifyOtp(input)
getCurrentUser()
getCurrentProfile()
lookupUsername(username)
claimAccountEmail(input)
```

Auth UI remains split by page: [[screens/auth/LoginPage|LoginPage]], [[screens/auth/RegisterPage|RegisterPage]], [[screens/auth/ForgotPasswordPage|ForgotPasswordPage]], [[screens/auth/ResetPasswordPage|ResetPasswordPage]], and [[screens/auth/OtpPage|OtpPage]].

Register must match the app's active `registerWithUsername` behavior: username, password, country, and optional email. Do not make email required unless product changes the app flow.

## Products

Product catalogue reads, admin product writes, product image storage, and product active-state RPCs.

## Orders

Customer order history, order detail, checkout, cancellation, idempotency, and atomic order creation.

## Addresses

Customer delivery address CRUD and default-address behavior.

## Pickup Points

Pickup point list, admin CRUD, country filtering, location, and delivery fee data.

## Delivery

Delivery schedule assignment, delivery partner task views, status transitions, and van sales.

## Notifications

Notification inbox, preferences, templates, cross-user notification RPC, and push fan-out.

## Payment Methods

Saved payment method metadata and Stripe-backed secure payment flows.

## Users

Profile reads/writes, country preference, delivery partner management, and account deletion.

## Stripe

PaymentIntent creation and any server-only refund/payment actions.

## Settings

Tenant, user, consent, privacy, and admin configuration data when backed by the API.

## Local State

Client-only state such as onboarding completion, theme, filters, and non-sensitive preferences.

## Observability

Application Insights page load, error, interaction, and correlation-id tracking.

## Important RPCs

- `create_order_atomic`: atomic order creation, stock decrement, idempotency.
- `admin_toggle_product_status`: admin product active toggle.
- `delete_user_by_id`: admin delivery partner deletion.
- `delete_user_account`: self-service deletion.
- `create_notification`: cross-user notification insert.
- `get_email_by_username`, `lookup_user_by_username`, `claim_account_email`: username auth/recovery.

## Vercel APIs

- `POST /api/create-payment-intent` for Stripe.
- `POST /api/send-push` for server-protected push fan-out.
- `POST /api/create-user` for server-side staff/delivery account creation if needed.

## Realtime

```ts
supabase.from('orders').on('*', callback).subscribe();
supabase.from(`delivery_schedule:delivery_partner_id=eq.${partnerId}`).on('*', callback).subscribe();
```

## Interceptors

- Auth interceptor attaches JWT and handles expiring state.
- Correlation interceptor creates/provides `X-Correlation-Id`.
- Error interceptor maps backend errors into user-safe states and read-only outage mode.
