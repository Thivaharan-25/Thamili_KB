# API Contracts

> **Extracted from:** `src/services/*Service.ts`, `api/*.ts`, `supabase/functions/*`  
> **See also:** [[architecture/api-client|api-client]]

These are first-pass web contracts. The implementation should keep the same behavior but convert React Native concerns to browser equivalents.

## Auth

| Function | Request | Response |
|---|---|---|
| `login` | `{ emailOrUsername, password }` | `{ user, profile }` |
| `register` | `{ username, password, confirmPassword, email?, country }` | `{ user, profile, verificationRequired? }` |
| `forgotPassword` | `{ emailOrUsername }` | `{ sent: boolean }` |
| `resetPassword` | `{ password }` | `{ success: boolean }` |
| `sendOtp` | `{ phoneOrEmail }` | `{ sent: boolean, cooldownSeconds }` |
| `verifyOtp` | `{ phoneOrEmail, otp }` | `{ verified: boolean }` |
| `logout` | none | `{ success: boolean }` |

Register follows the active app behavior: email is optional. If the user does not provide an email, the auth layer uses the username fallback email internally and stores the public profile email as `null`.

## Products

| Function | Source behavior |
|---|---|
| `getProducts(filters)` | Reads `products`, filters category/search/active, excludes `is_deleted`. |
| `getProductById(productId)` | Reads one non-deleted product. |
| `createProduct(product)` | Admin insert into `products`. |
| `updateProduct(productId, updates)` | Admin update. |
| `toggleProductActive(productId, active)` | Calls `admin_toggle_product_status`. |
| `deleteProduct(productId)` | Hard delete, fallback to soft delete when referenced. |
| `uploadProductImage(file)` | Uploads to `product-images` bucket and returns public URL. |

## Orders

| Function | Source behavior |
|---|---|
| `getOrders(userId)` | Customer order list with items and pickup point joins. |
| `getAllOrders(filters)` | Admin order list with joins. |
| `getOrderById(orderId)` | Order detail with user, schedule, items, products. |
| `createOrder(orderData)` | Uses `create_order_atomic`, idempotency key, stock decrement, notifications. |
| `updateOrderStatus(orderId, status)` | Updates status; cancel path triggers refund logic when applicable. |
| `cancelOrder(orderId, reason?)` | Validates cancellable status, handles refund and notifications. |
| `getTopProducts(period)` | Aggregates order items by product/country. |

## Delivery

| Function | Source behavior |
|---|---|
| `getDeliverySchedules(filters)` | Reads `delivery_schedule` with order and pickup point joins. |
| `createDeliverySchedule(data)` | Creates assignment and notifies partner/customer. |
| `updateDeliverySchedule(scheduleId, updates)` | Updates delivery status and related order status/notifications. |
| `confirmPickupPoint(pickupPointId, partnerId)` | Bulk confirms pickup point schedules. |
| `createVanSalesOrder(userId, items, country, paymentMethod)` | Calls `create_order_atomic` with van sale behavior. |

## Addresses, Pickup Points, Payments, Notifications, Users

Use service signatures from:

- `addressService`: get/add/update/delete addresses.
- `pickupPointService`: get/getById/create/update/delete/findNearest.
- `paymentMethodService`: get/add/delete payment methods.
- `notificationService`: get, mark read, preferences, templates, bulk notifications.
- `userService`: profile, update profile, country preference, delivery partners, account deletion.

## External APIs

| API | Request | Response |
|---|---|---|
| `POST /api/create-payment-intent` or Supabase function `create-payment-intent` | `{ orderId, amount, currency, metadata?, setupFutureUsage? }` | `{ clientSecret, paymentIntentId, customer?, ephemeralKey? }` |
| Supabase function `refund-payment` | `{ orderId, amount }` | `{ success, message?, error? }` |
| `POST /api/send-push` | `{ userId, title, body, data? }` | `{ success }` or send counts |
| `POST /api/create-user` | delivery/admin-created user payload | created user/profile |
