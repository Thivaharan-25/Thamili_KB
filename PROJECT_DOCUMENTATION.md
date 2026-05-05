# Thamili — Complete End-to-End Project Documentation

> **Generated:** 2026-05-04  
> **Status:** Production  
> **Package:** `com.thamili.thamili`  
> **Deep Link Scheme:** `thamili://`  
> **Markets:** Germany 🇩🇪 · Denmark 🇩🇰  
> **Currency:** EUR (€) for both markets

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [Architecture](#3-architecture)
4. [User Roles & Auth Flow](#4-user-roles--auth-flow)
5. [Navigation Structure](#5-navigation-structure)
6. [Screens — Onboarding & Welcome](#6-screens--onboarding--welcome)
7. [Screens — Auth](#7-screens--auth)
8. [Screens — Customer](#8-screens--customer)
9. [Screens — Admin](#9-screens--admin)
10. [Screens — Delivery Partner](#10-screens--delivery-partner)
11. [Supabase Tables — Full Reference](#11-supabase-tables--full-reference)
12. [Supabase RPCs — Full Reference](#12-supabase-rpcs--full-reference)
13. [Supabase Storage](#13-supabase-storage)
14. [Supabase Realtime Subscriptions](#14-supabase-realtime-subscriptions)
15. [Services Layer](#15-services-layer)
16. [State Management (Zustand Stores)](#16-state-management-zustand-stores)
17. [Client-Side Rate Limiter](#17-client-side-rate-limiter)
18. [Role-Based Access Control (RBAC)](#18-role-based-access-control-rbac)
19. [Offline Queue & Network Resilience](#19-offline-queue--network-resilience)
20. [Conflict Resolution](#20-conflict-resolution)
21. [Request Queue & Timeouts](#21-request-queue--timeouts)
22. [Form Validation (Yup Schemas)](#22-form-validation-yup-schemas)
23. [Multi-Country Product Model](#23-multi-country-product-model)
24. [Cart & Pricing Utilities](#24-cart--pricing-utilities)
25. [Regional Formatting](#25-regional-formatting)
26. [Haptic Feedback](#26-haptic-feedback)
27. [Product Discovery Utilities](#27-product-discovery-utilities)
28. [Local Storage Keys — Full Reference](#28-local-storage-keys--full-reference)
29. [Hooks — Full Reference](#29-hooks--full-reference)
30. [External Integrations](#30-external-integrations)
31. [Vercel Serverless API](#31-vercel-serverless-api)
32. [Environment Variables](#32-environment-variables)
33. [EAS Build Profiles](#33-eas-build-profiles)
34. [Key File Paths](#34-key-file-paths)

---

## 1. Project Overview

**Thamili** is a **multi-role, bilingual (English + Tamil) mobile commerce app** built with React Native / Expo. It is a full-stack order management and delivery platform for a fresh-produce distribution business operating in **Germany** and **Denmark**.

The app serves **three distinct user roles** from a single codebase:

| Role | What they do |
|---|---|
| **Customer** | Browse products (with country-specific pricing), manage a cart, place orders (cash or Stripe), track deliveries, manage saved addresses and payment methods, delete their own account |
| **Admin** | Manage the product catalogue (dual-country stock/price), view and update all orders, assign deliveries, manage pickup points, manage delivery staff, view top-selling products, send notifications |
| **Delivery Partner** | View assigned delivery schedules on a Mapbox map, mark deliveries complete, manage van sales |

The app targets **Germany (EUR, +49, DD.MM.YYYY)** and **Denmark (EUR, +45, DD/MM/YYYY)**. Products have separate stock and price fields per country. Currency is **EUR (€)** for both markets.

The app is localised into **Tamil and English** using i18next and supports **Google OAuth**, **username/email login**, **Stripe payments**, **Mapbox address autocomplete**, **Expo push notifications**, and a full **offline queue** for resilience.

---

## 2. Tech Stack

### Core Framework

| Layer | Technology | Version |
|---|---|---|
| Runtime | React Native + Hermes | 0.81.5 |
| Framework | Expo (EAS) | 54 |
| Language | TypeScript | 5.x |
| Node (build) | Node | 24 |

### Backend

| Service | Purpose | Version / SDK |
|---|---|---|
| **Supabase** | PostgreSQL DB, Auth, Storage, Realtime | JS SDK **v1.35.7** (NOT v2) |
| **Vercel Serverless** | Push notifications, Stripe PaymentIntents | Functions in `/api/` |

### UI & Styling

| Technology | Purpose |
|---|---|
| NativeWind v4 | Tailwind CSS utility classes in React Native |
| Tailwind CSS v3 | Config base |
| React Navigation (Stack) | Screen navigation |
| Custom bottom tabs | Role-aware tab switching |
| react-native-reanimated | Animations (with safe fallback if unavailable) |
| expo-linear-gradient | Gradient backgrounds on Welcome screen |
| expo-haptics | Haptic feedback on interactions |

### State & Data Fetching

| Technology | Purpose |
|---|---|
| **Zustand v5** | Client-side state (cart, auth session, theme, saved-for-later) |
| **TanStack React Query v5** | Server-state caching and invalidation |
| AsyncStorage | Persisted store hydration |

### Payments & Maps

| Technology | Purpose |
|---|---|
| Stripe React Native | `@stripe/stripe-react-native` v0.50.3 — in-app payment sheet |
| Mapbox RNMapbox | `@rnmapbox/maps` v10.x — address autocomplete + map picker |

### Validation

| Technology | Purpose |
|---|---|
| Yup | Schema-based form validation for all auth and profile forms |

### Internationalisation

| Technology | Purpose |
|---|---|
| i18next | Translation framework |
| react-i18next | React bindings |
| Languages | English (`en`), Tamil (`ta`) |

### Push Notifications

| Technology | Purpose |
|---|---|
| `expo-notifications` | Register device push token |
| Expo Push Service | Delivery via Vercel `/api/send-push` |

---

## 3. Architecture

```
┌─────────────────────────────────────────┐
│            React Native UI               │
│  Screens → Hooks → Components           │
└──────────────┬──────────────────────────┘
               │ calls
┌──────────────▼──────────────────────────┐
│  Cross-cutting Concerns                  │
│  rateLimiter · rbac · requireAuth        │
│  offlineQueue · requestQueue · timeout   │
│  conflictResolution · validation (yup)   │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         Services Layer                   │
│  orderService, productService,           │
│  authStore (Zustand), etc.              │
└──────────────┬──────────────────────────┘
               │ calls
┌──────────────▼──────────────────────────┐
│       Supabase JS SDK v1                 │
│    Lazy Proxy Singleton                  │
│    (src/services/supabase.ts)            │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         Supabase Cloud                   │
│  PostgreSQL + RLS + RPCs                 │
│  Auth + Storage + Realtime              │
└─────────────────────────────────────────┘
```

### Key Architectural Decisions

- **Supabase Lazy Proxy Singleton** — The Supabase client is wrapped in a JavaScript `Proxy` that initialises only on first property access. This prevents `"property is not configurable"` errors that Expo throws if Supabase initialises before the JS engine is ready.

- **All services use this pattern:**
  ```ts
  function getSupabase() { return require('./supabase').supabase; }
  ```

- **React Query + Zustand hybrid** — React Query caches server state (products, orders, delivery schedules). Zustand owns ephemeral client state (cart items, auth session, theme).

- **Fixed-slot Main screen** — Navigation uses a single Stack with a `Main` screen that never unmounts. The bottom tabs re-render based on the authenticated user's role. This prevents React Navigation from resetting the entire stack on login/logout.

- **Three-layer service pattern** — `Screen → Service → Supabase`. No screen directly calls `supabase.from(...)`. All database access goes through a typed service function.

- **Client-side rate limiting** — Every sensitive operation (`login`, `order_write`, `payment`, etc.) is guarded by a sliding-window rate limiter before the Supabase call is made.

- **Offline-first mutations** — Failed order creates are queued to AsyncStorage and retried on reconnect via `offlineQueue` + `useOfflineQueue` hook.

- **Dual-country product model** — Products have `stock_germany`, `stock_denmark`, `price_germany`, `price_denmark` columns. All pricing/stock logic is country-aware.

---

## 4. User Roles & Auth Flow

### Roles

| Role | Stored In | How Created |
|---|---|---|
| `customer` | `public.users.role` | Self-registration |
| `admin` | `public.users.role` | Manual DB assignment |
| `delivery_partner` | `public.users.role` | Admin creates them in-app |

### Login Flow

```
User enters username
       ↓
checkRateLimit('auth')  ← 5 attempts per 15 min
       ↓
RPC: get_email_by_username  → real email OR username@thamili.app dummy
       ↓
supabase.auth.signIn({ email, password })
       ↓  (tries signInWithPassword first, falls back to signIn for v1 compat)
Session persisted to AsyncStorage (USER_TOKEN, USER_DATA)
       ↓
authStore.user.role → determines which tab set to show
```

### Delivery Partner Special Case

Delivery partners are created by admins using a dummy `username@thamili.app` email address. They never see or interact with an email field. They log in with username + password only.

### Google OAuth

Deep link callback: `thamili://auth-callback`. `supabase.auth.signIn({ provider: 'google' })` opens the OAuth flow in the browser. On redirect, the token is parsed from the URL fragment and `supabase.auth.setAuth(token)` is called manually (required because Supabase v1 does not auto-detect OAuth callbacks in React Native).

### Account Recovery / Username-based Forgot Password

```
User enters username
       ↓
RPC: lookup_user_by_username → { exists, email }
       ↓
RPC: claim_account_email → updates @thamili.app dummy email to real email
       ↓
supabase.auth.api.resetPasswordForEmail
```

### Account Deletion (Customer self-delete)

Available **only to non-admin users** via the Settings screen. Guarded by a `ConfirmationModal` before execution:

```
Customer presses "Delete Account" (SettingsScreen)
       ↓
ConfirmationModal with title/message confirmation
       ↓
authStore.deleteAccount()
       ↓
RPC: delete_user_account  (SECURITY DEFINER)
     → deletes auth.users row + public.users row in one transaction
       ↓
AsyncStorage cleared, Zustand reset, navigate to Login
```

The delete button is **conditionally rendered**: `user?.role !== 'admin'` — admins cannot self-delete from the app.

### Session Resume

On app resume, `authStore.loadSession()` reads the token from AsyncStorage and calls `supabase.auth.setAuth(token)` to re-hydrate the Supabase client (Supabase v1 does not auto-refresh sessions across app restarts in React Native).

---

## 5. Navigation Structure

**File:** `src/navigation/AppNavigator.tsx`

```
Stack Navigator
├── Onboarding        (first launch: CountrySelection → Welcome, paginated)
├── Welcome           (standalone — animated brand intro)
├── CountrySelection  (initial screen if no country chosen)
├── Main              (fixed slot — never unmounts)
│   └── MainTabs (role-aware bottom tabs)
│       ├── [unauthenticated]  Home, Products
│       ├── [customer]         Home, Products, Cart (badge), Orders, Profile
│       ├── [admin]            Dashboard, Products, Orders, Delivery,
│       │                      PickupPoints, Profile
│       └── [delivery_partner] Dashboard, VanSales, Profile
│
├── Login
├── Register
├── VerifyEmail
├── ForgotPassword
├── ProductDetails
├── OrderDetails         (shared across roles)
├── Checkout
├── OrderConfirmation
├── EditProfile
├── ChangePassword
├── Settings
├── Addresses
├── Payments
├── AddProduct
├── EditProduct
├── AddPickupPoint
├── EditPickupPoint
├── AddDeliveryMan
├── ManageDeliveryMan
├── NotificationHistory
├── AdminTopProducts
├── DeliveryVanSales
└── DeliveryOrderDetails
```

---

## 6. Screens — Onboarding & Welcome

### 6.1 OnboardingScreen
**Path:** `src/screens/onboarding/OnboardingScreen.tsx`  
**Purpose:** Coordinator screen for the first-launch onboarding flow. Hosts a horizontal `FlatList` (scroll disabled, programmatic-only) of two sub-screens: CountrySelection then WelcomeScreen. Shows pagination dots animated via `react-native-reanimated` (with a fallback if Reanimated is unavailable). Tapping "Next" scrolls to the next screen; on the final screen navigates to `Main`.

**Supabase APIs:** None — purely local coordination.

---

### 6.2 WelcomeScreen
**Path:** `src/screens/onboarding/WelcomeScreen.tsx`  
**Purpose:** Animated brand introduction screen shown as the second step of onboarding. Shows the Thamili logo with spring animations (scale + opacity), feature highlights ("Shop Quality Products", "Fast Delivery", "Secure Payments"), and a "Get Started" button that navigates to CountrySelection.

On first launch, also shows an **OnboardingTutorial** overlay (4-step walkthrough):
1. Welcome to Thamili!
2. Explore Products
3. Easy Checkout
4. Track Your Order

Tutorial completion state is persisted to AsyncStorage via `onboardingStorage.ts`. Once completed, the tutorial never shows again.

**Supabase APIs:** None.  
**External:** `expo-linear-gradient` for the navy→primary gradient background.

---

### 6.3 CountrySelectionScreen
**Path:** `src/screens/onboarding/CountrySelectionScreen.tsx`  
**Purpose:** Mandatory first-launch screen. User picks **Germany** or **Denmark**. This choice determines which country's stock and prices are shown throughout the app, and which phone/postal-code format is enforced.

**Supabase APIs:** None — purely local.  
**State:** `cartStore.setSelectedCountry('germany' | 'denmark')` → persisted to AsyncStorage (`SELECTED_COUNTRY`).

---

## 7. Screens — Auth

### 7.1 LoginScreen
**Path:** `src/screens/auth/LoginScreen.tsx`  
**Purpose:** Username + password login and Google OAuth entry point. Validated with `usernameLoginSchema` (yup: 3–20 chars, alphanumeric + underscore).

**Supabase APIs:**

| Operation | RPC / Auth | Details |
|---|---|---|
| `RPC` | `get_email_by_username` | Resolves username → email (or dummy email) |
| `AUTH` | `supabase.auth.signIn` | Email + password sign-in (with v1 fallback chain) |
| `AUTH` | `supabase.auth.signIn({ provider: 'google' })` | OAuth redirect |
| `SELECT` | `users` | Post-login profile load via `authStore.loadSession()` |

**Rate limit:** `checkRateLimit('auth')` → 5 per 15 minutes. Throws `RateLimitError` with `retryAfterMs`.

---

### 7.2 RegisterScreen
**Path:** `src/screens/auth/RegisterScreen.tsx`  
**Purpose:** New customer self-registration. Validated with `registerSchema` (yup: name min 2, email valid, phone optional, password min 6, confirmPassword must match).

**Supabase APIs:**

| Operation | Auth / Table | Details |
|---|---|---|
| `AUTH` | `supabase.auth.signUp` | Creates auth user with email + password |
| `UPSERT` | `users` | `authStore.registerWithUsername` — inserts public profile row with `role = 'customer'` |

**Rate limit:** `checkRateLimit('auth')` — same 5/15 min bucket as login.

---

### 7.3 VerifyEmailScreen
**Path:** `src/screens/auth/VerifyEmailScreen.tsx`  
**Purpose:** Post-registration prompt. Instructs the user to check their email. Polls Supabase to detect when email confirmation completes.

**Supabase APIs:**

| Operation | Auth | Details |
|---|---|---|
| `CHECK` | `supabase.auth.user()` | `authStore.checkVerificationStatus(email, password)` — polls `email_confirmed_at` |

---

### 7.4 ForgotPasswordScreen
**Path:** `src/screens/auth/ForgotPasswordScreen.tsx`  
**Purpose:** Username-based password recovery. Resolves username → email via RPC, optionally claims (upgrades) a dummy `@thamili.app` email to a real one, then sends Supabase reset email.

**Supabase APIs:**

| Operation | RPC / Auth | Details |
|---|---|---|
| `RPC` | `lookup_user_by_username` | Returns `{ exists: bool, email: string, isDummy: bool }` |
| `RPC` | `claim_account_email` | Updates dummy email to real email before reset |
| `AUTH` | `supabase.auth.api.resetPasswordForEmail` | Sends password reset link |

---

## 8. Screens — Customer

### 8.1 HomeScreen
**Path:** `src/screens/customer/HomeScreen.tsx`  
**Purpose:** Landing screen for logged-in customers. Shows featured/active products for the selected country, promotional banners, trending products, recently viewed items, and recommended products.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `products` | `productService.getProducts()` — active products, country stock/price used for display |

**Local utilities used:**
- `getTrendingProducts(products, country)` — sorts by stock descending
- `getRecommendedProducts(products, excludeIds)` — uses recently viewed category history
- `addToRecentlyViewed(productId, category)` — writes to AsyncStorage on product tap

**Rate limit:** `checkRateLimit('product_read')` — 30 per minute.

---

### 8.2 ProductsScreen
**Path:** `src/screens/customer/ProductsScreen.tsx`  
**Purpose:** Full product catalogue with search (with search history suggestions) and category filter. Customers browse all active products for their country and add to cart.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `products` | `productService.getProducts()` — active products with name, price_germany, price_denmark, stock_germany, stock_denmark, image_url, sell_type |

**Local utilities:**
- `addToSearchHistory(query)` / `getSearchSuggestions(query)` — last 10 searches in AsyncStorage `@thamili_search_history`
- `addToRecentlyViewed(productId, category)` — on product tap

---

### 8.3 ProductDetailsScreen
**Path:** `src/screens/customer/ProductDetailsScreen.tsx`  
**Purpose:** Full detail view for a single product. Shows description, country-specific price (EUR), stock count for selected country, images. Shows related products (same category). Customer adjusts quantity (grams for loose items, packets for pack items) and adds to cart.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `products` | `productService.getProductById(id)` — single row by UUID |

**Auth guard:** `requireAuth({ navigation, isAuthenticated })` — if unauthenticated user tries to add to cart, shows alert: "Create Account to Order" with Login / Sign Up Free / Continue Browsing options.

**Local utilities:**
- `getRelatedProducts(products, productId, category, 5)` — same category, active only
- `addToRecentlyViewed(productId, category)` — on screen mount

---

### 8.4 CartScreen
**Path:** `src/screens/customer/CartScreen.tsx`  
**Purpose:** Review cart contents, adjust quantities, remove items (swipeable), select delivery address, and proceed to checkout. Shows subtotal, delivery fee, and total for selected country.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `addresses` | `addressService.getUserAddresses()` — lists saved addresses for address picker |

**State:** Cart items live in `cartStore` (Zustand + AsyncStorage). No server writes on this screen.

**Local utilities:**
- `validateCart(items)` — checks stock per country, quantity limits, product active status
- `removeOutOfStockItems(items)` — cleans cart if items went out of stock since last session
- `calculateCartSubtotal(items, country)` — country-aware price calculation
- `formatCartSummary(...)` — subtotal + delivery fee + Stripe fee breakdown

**Rate limit:** `checkRateLimit('address')` on address fetch — 15 per minute.

---

### 8.5 CheckoutScreen
**Path:** `src/screens/customer/CheckoutScreen.tsx`  
**Purpose:** Final order placement. Customer selects delivery method (home delivery or pickup point), confirms address, chooses payment (cash or Stripe card), and places the order.

**Auto-save:** `useCheckoutAutoSave(formData)` — saves form state to AsyncStorage `@thamili_checkout_data` with 1-second debounce. Restored on re-enter if user navigates away mid-checkout.

**Supabase APIs:**

| Operation | Table / RPC | Details |
|---|---|---|
| `RPC` | `create_order_atomic` | Atomically creates `orders` + `order_items` + deducts `products.stock_germany` or `stock_denmark`. Returns new order UUID. |
| `SELECT` | `pickup_points` | `pickupPointService.getPickupPoints()` — lists available pickup locations |
| `SELECT` | `addresses` | `addressService.getUserAddresses()` — customer's saved addresses |
| `SELECT` | `payment_methods` | `paymentMethodService.getUserMethods()` — saved Stripe card tokens |
| `INSERT` | `addresses` | If customer saves new delivery address post-order |

**External:** Stripe — `stripeService` calls Vercel `/api/create-payment-intent`. Order status set to `paid` on Stripe success.

**Local utilities:**
- `calculateDeliveryFee(pickupPoint, isHomeDelivery, customerLocation)` — haversine distance, free radius, per-km rate from pickup point settings
- `calculatePaymentFee(total, 'online')` — Stripe fee: 2.9% + €0.30
- `validateCart(items)` — final stock check before submit
- `clearCheckoutData()` — clears autosave after successful order

**Rate limits:** `checkRateLimit('order_write')` — 3 per minute. `checkRateLimit('payment')` — 3 per minute.

---

### 8.6 OrderConfirmationScreen
**Path:** `src/screens/customer/OrderConfirmationScreen.tsx`  
**Purpose:** Success screen shown after a successful order. Displays order ID, summary of items, estimated delivery, and total amount paid.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getOrderById(orderId)` — full order with pickup_point join |
| `SELECT` | `order_items` | `orderService.getOrderItems(orderId)` — line items with product name, qty, price |

**Polling:** `useOrderPolling({ orderId, pollInterval: 10000, onStatusChange })` — polls every 10 seconds, triggers callback when status changes.

---

### 8.7 OrdersScreen
**Path:** `src/screens/customer/OrdersScreen.tsx`  
**Purpose:** List of all past and active orders for the logged-in customer. Shows status badges (pending, confirmed, dispatched, delivered, cancelled). Supports filter by status and search by order ID.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getOrders(userId)` — all orders for customer, desc by created_at, joined with `pickup_points` |

**Local utilities:** `filterOrdersByStatus`, `searchOrders`, `sortOrdersByDate` from `orderUtils.ts`.

**Rate limit:** `checkRateLimit('order_read')` — 20 per minute.

---

### 8.8 SharedOrderDetailsScreen
**Path:** `src/screens/shared/SharedOrderDetailsScreen.tsx`  
**Purpose:** Detailed view of a single order — used by customers, admins, and delivery partners. Role determines what actions are available (customer can cancel, admin can update status, delivery partner can mark delivered).

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getOrderById(orderId)` |
| `SELECT` | `order_items` | `orderService.getOrderItems(orderId)` — with product name join |
| `UPDATE` | `orders` | `orderService.cancelOrder(orderId)` — sets `status = 'cancelled'` |

**Polling:** `useOrderPolling` active for pending/confirmed orders (auto-stops when delivered/cancelled).

---

### 8.9 ProfileScreen
**Path:** `src/screens/customer/ProfileScreen.tsx`  
**Purpose:** Customer's account hub. Shows display name, avatar, and links to: Edit Profile, Addresses, Payment Methods, Notifications, Notification Settings, Change Password, Settings, Logout.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `users` | `userService.getUserProfile(userId)` — name, avatar_url, email, phone |

**Auth:** Logout calls `authStore.logout()` → `supabase.auth.signOut()` + AsyncStorage clear + Zustand reset.

---

### 8.10 EditProfileScreen
**Path:** `src/screens/customer/EditProfileScreen.tsx`  
**Purpose:** Lets the customer update their display name, phone number, and profile avatar. Validated with `profileUpdateSchema` (yup: name min 2, phone optional regex). Avatar upload goes to Supabase Storage.

**Supabase APIs:**

| Operation | Table / Storage | Details |
|---|---|---|
| `UPDATE` | `users` | `userService.updateUserProfile({ name, phone, avatar_url })` |
| `UPLOAD` | `product-images` bucket | `productService.uploadProductImage(file)` — ArrayBuffer upload, returns public URL |

**Rate limit:** `checkRateLimit('user_write')` — 10 per minute.

---

### 8.11 AddressesScreen
**Path:** `src/screens/customer/AddressesScreen.tsx`  
**Purpose:** Full CRUD for saved delivery addresses. Customer can add, edit, delete, and set a default address. Postal code validated per country (Germany: 5 digits, Denmark: 4 digits).

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `addresses` | `addressService.getUserAddresses(userId)` |
| `INSERT` | `addresses` | `addressService.addAddress(data)` |
| `UPDATE` | `addresses` | `addressService.updateAddress(id, data)` |
| `DELETE` | `addresses` | `addressService.deleteAddress(id)` |

**Rate limit:** `checkRateLimit('address')` — 15 per minute.

---

### 8.12 PaymentsScreen
**Path:** `src/screens/customer/PaymentsScreen.tsx`  
**Purpose:** Manage saved payment methods (Stripe card tokens). Customer can add a new card or delete an existing one.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `payment_methods` | `paymentMethodService.getUserMethods(userId)` |
| `INSERT` | `payment_methods` | `paymentMethodService.addMethod(data)` — stores `stripe_payment_method_id`, last4, brand |
| `DELETE` | `payment_methods` | `paymentMethodService.deleteMethod(id)` |

---

### 8.13 NotificationsScreen
**Path:** `src/screens/customer/NotificationsScreen.tsx`  
**Purpose:** In-app notification inbox. Lists all notifications for the user (order updates, delivery alerts, promotional). Supports mark-as-read (single) and mark-all-as-read. Unread count shown as badge on Profile tab.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `notifications` | `notificationService.getNotifications(userId)` — ordered by created_at desc |
| `UPDATE` | `notifications` | `notificationService.markAsRead(id)` — sets `read = true` |
| `UPDATE` | `notifications` | `notificationService.markAllAsRead(userId)` — bulk update |

**Rate limit:** `checkRateLimit('notification')` — 20 per minute.

---

### 8.14 NotificationSettingsScreen
**Path:** `src/screens/customer/NotificationSettingsScreen.tsx`  
**Purpose:** Lets the customer toggle which types of notifications they receive.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `notification_preferences` | `notificationService.getPreferences(userId)` |
| `UPSERT` | `notification_preferences` | `notificationService.updatePreferences(userId, prefs)` — order_updates, delivery_alerts, promotions booleans |

---

### 8.15 SettingsScreen
**Path:** `src/screens/customer/SettingsScreen.tsx`  
**Purpose:** App-level settings. Contains:
- Language switcher (English / Tamil) — instant via `i18n.changeLanguage(lang)`
- User info display (email, name, role)
- Account section: links to Edit Profile, Change Password
- Theme toggle (light/dark)
- Performance indicator (network speed + cache status)
- App version display (1.0.0)
- **Delete Account** — destructive, shown only to non-admin users

**Delete Account flow (customer-only, `user?.role !== 'admin'`):**
1. Customer taps "Delete Account" (red, `account-remove-outline` icon)
2. `ConfirmationModal` appears with localised title/message/confirm/cancel labels
3. On confirm: `authStore.deleteAccount()` is called
4. This calls RPC `delete_user_account` (SECURITY DEFINER) → removes `auth.users` + `public.users`
5. On error: `Alert.alert` shown with error message
6. On success: session cleared, user navigated to Login

**Supabase APIs:**

| Operation | RPC | Details |
|---|---|---|
| `RPC` | `delete_user_account` | `authStore.deleteAccount()` — SECURITY DEFINER, deletes caller's own auth + profile rows |

**No Supabase call** for language switch or theme toggle — those are local only.

---

### 8.16 ChangePasswordScreen
**Path:** `src/screens/customer/ChangePasswordScreen.tsx`  
**Purpose:** Lets an authenticated customer change their password. Validated with `passwordChangeSchema` (yup: currentPassword required, newPassword min 6, confirmPassword must match newPassword).

**Supabase APIs:**

| Operation | Auth | Details |
|---|---|---|
| `UPDATE` | `supabase.auth.update` | `authStore.changePassword(currentPassword, newPassword)` |

---

## 9. Screens — Admin

### 9.1 AdminDashboardScreen
**Path:** `src/screens/admin/AdminDashboardScreen.tsx`  
**Purpose:** Admin command center. Shows key metrics: total orders, revenue, pending orders, low-stock products (per country). Live updates via Realtime.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getAllOrders()` — all orders across all customers |
| `SELECT` | `products` | `productService.getProducts()` — to compute low-stock items per country |
| **Realtime** | `orders` | `useOrderRealtime` hook — invalidates React Query cache on any order change |

---

### 9.2 AdminProductsScreen
**Path:** `src/screens/admin/AdminProductsScreen.tsx`  
**Purpose:** Full product catalogue management. Admin sees all products (including inactive), can toggle active/inactive, and navigate to Add or Edit.

**Supabase APIs:**

| Operation | Table / RPC | Details |
|---|---|---|
| `SELECT` | `products` | `productService.getProducts()` — no active filter for admin |
| `RPC` | `admin_toggle_product_status` | `productService.toggleProductActive(id, active)` — bypasses RLS |
| `DELETE` | `products` | `productService.deleteProduct(id)` |

**Rate limit:** `checkRateLimit('product_write')` — 10 per minute.

---

### 9.3 AddProductScreen
**Path:** `src/screens/admin/AddProductScreen.tsx`  
**Purpose:** Form to create a new product. Admin fills name, description, category, `sell_type` (loose/pack), `pack_size_grams`, `price_germany`, `price_denmark`, `stock_germany`, `stock_denmark`, and uploads a product image.

**Supabase APIs:**

| Operation | Table / Storage | Details |
|---|---|---|
| `INSERT` | `products` | `productService.createProduct(data)` — includes dual-country price + stock |
| `UPLOAD` | `product-images` bucket | `productService.uploadProductImage(file)` — returns public URL for `image_url` |

---

### 9.4 EditProductScreen
**Path:** `src/screens/admin/EditProductScreen.tsx`  
**Purpose:** Edit an existing product's details, dual-country prices, stock levels, and image.

**Supabase APIs:**

| Operation | Table / Storage | Details |
|---|---|---|
| `SELECT` | `products` | `productService.getProductById(id)` — pre-fills form |
| `UPDATE` | `products` | `productService.updateProduct(id, data)` |
| `UPLOAD` | `product-images` bucket | `productService.uploadProductImage(file)` — if image changed |

---

### 9.5 AdminOrdersScreen
**Path:** `src/screens/admin/AdminOrdersScreen.tsx`  
**Purpose:** All orders across all customers. Filter by status, update order status (pending → confirmed → dispatched → delivered), cancel orders.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getAllOrders()` — joined with `users` for customer name, `pickup_points` for location |
| `UPDATE` | `orders` | `orderService.updateOrderStatus(id, status)` |
| `UPDATE` | `orders` | `orderService.cancelOrder(id)` |
| `SELECT` | `order_items` | `orderService.getOrderItems(orderId)` — for detail view |

**Local utilities:** `filterOrdersByStatus`, `searchOrders`, `sortOrdersByDate` from `orderUtils.ts`.

---

### 9.6 AdminDeliveryScreen
**Path:** `src/screens/admin/AdminDeliveryScreen.tsx`  
**Purpose:** Assign deliveries to delivery partners. Admin creates, edits, and deletes delivery schedules, linking them to partners, pickup points, and orders.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `delivery_schedule` | `deliveryService.getDeliverySchedules()` — all schedules |
| `INSERT` | `delivery_schedule` | `deliveryService.createDeliverySchedule(data)` |
| `UPDATE` | `delivery_schedule` | `deliveryService.updateDeliverySchedule(id, data)` |
| `DELETE` | `delivery_schedule` | `deliveryService.deleteDeliverySchedule(id)` |
| `SELECT` | `orders` | Linking orders to delivery schedules |
| `SELECT` | `pickup_points` | Assigning pickup location to schedule |
| `SELECT` | `users` | `userService.getDeliveryPartners()` — lists delivery partners (`role = 'delivery_partner'`) |

---

### 9.7 AdminPickupPointsScreen
**Path:** `src/screens/admin/AdminPickupPointsScreen.tsx`  
**Purpose:** View all pickup points, delete them.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `pickup_points` | `pickupPointService.getPickupPoints()` |
| `DELETE` | `pickup_points` | `pickupPointService.deletePickupPoint(id)` |

---

### 9.8 AddPickupPointScreen
**Path:** `src/screens/admin/AddPickupPointScreen.tsx`  
**Purpose:** Create a pickup point with name, address, coordinates (lat/lng from Mapbox picker), delivery fee settings (`base_delivery_fee`, `free_delivery_radius`, `extra_km_fee`, `delivery_fee`).

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `INSERT` | `pickup_points` | `pickupPointService.createPickupPoint(data)` |

---

### 9.9 EditPickupPointScreen
**Path:** `src/screens/admin/EditPickupPointScreen.tsx`  
**Purpose:** Edit pickup point details and delivery fee configuration.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `UPDATE` | `pickup_points` | `pickupPointService.updatePickupPoint(id, data)` |

---

### 9.10 ManageDeliveryManScreen
**Path:** `src/screens/admin/ManageDeliveryManScreen.tsx`  
**Purpose:** Lists all delivery partners. Admin can view their details and delete their accounts (removes from auth + public.users via RPC).

**Supabase APIs:**

| Operation | Table / RPC | Details |
|---|---|---|
| `SELECT` | `users` | `userService.getDeliveryPartners()` — `role = 'delivery_partner'` |
| `RPC` | `delete_user_by_id` | `userService.deleteDeliveryPartner(id)` — deletes from auth + public |

---

### 9.11 AddDeliveryManScreen
**Path:** `src/screens/admin/AddDeliveryManScreen.tsx`  
**Purpose:** Create a new delivery partner account. Admin enters name, username, password, phone, country. A dummy `username@thamili.app` email is generated for Supabase auth.

**Supabase APIs:**

| Operation | Auth / Table | Details |
|---|---|---|
| `AUTH` | `supabase.auth.signUp` | `authStore.createDeliveryPartner(username, password, phone, country)` — creates auth user with dummy email |
| `UPSERT` | `users` | `authStore.updateDeliveryPartner(userId, { role: 'delivery_partner', name, phone, country })` |

---

### 9.12 AdminTopProductsScreen
**Path:** `src/screens/admin/AdminTopProductsScreen.tsx`  
**Purpose:** Analytics view showing best-selling products by total quantity sold. Helps with restocking and pricing decisions.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `order_items` | `orderService.getTopProducts()` — aggregates quantity per product_id |
| `SELECT` | `orders` | Joined to filter only completed/delivered orders |
| `SELECT` | `products` | Joined for product name and image |

---

### 9.13 NotificationHistoryScreen
**Path:** `src/screens/admin/NotificationHistoryScreen.tsx`  
**Purpose:** Admin view of all notifications sent through the system. Admin can also edit notification templates used for automated messages.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `notifications` | `notificationService.getNotifications()` — admin sees all (not filtered by userId) |
| `SELECT` | `notification_templates` | `notificationService.getTemplates()` — editable message templates |
| `UPDATE` | `notification_templates` | `notificationService.updateTemplate(id, content)` |

---

### 9.14 AdminSettingsScreen
**Path:** `src/screens/admin/AdminSettingsScreen.tsx`  
**Purpose:** Admin app settings — country preference update and logout. No delete account option (admins cannot self-delete from app).

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `UPDATE` | `users` | `authStore.updateCountryPreference(country)` |

---

## 10. Screens — Delivery Partner

### 10.1 DeliveryDashboardScreen
**Path:** `src/screens/delivery/DeliveryDashboardScreen.tsx`  
**Purpose:** Delivery partner's main screen. Shows today's assigned delivery schedule on a Mapbox map with a stop list. Partner marks stops complete and triggers bulk customer push notifications.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `delivery_schedule` | `deliveryService.getDeliverySchedules(partnerId)` — filtered by `delivery_partner_id` |
| `UPDATE` | `delivery_schedule` | `deliveryService.updateDeliverySchedule(id, { status, completed_at })` |
| `SELECT` | `pickup_points` | Joined to show location on map |
| **Realtime** | `delivery_schedule` | `useDeliveryRealtime` — invalidates query on any change to partner's schedule rows |

**External:**
- Mapbox: renders partner's delivery route on map
- Push: `notificationService.sendBulkNotification(userIds, message)` → Vercel `/api/send-push`

---

### 10.2 DeliveryVanSalesScreen
**Path:** `src/screens/delivery/DeliveryVanSalesScreen.tsx`  
**Purpose:** Handles on-the-spot van sales — delivery partner sells directly to customers who didn't place an app order. Logs these as special order records.

**Supabase APIs:**

| Operation | Table / RPC | Details |
|---|---|---|
| `SELECT` | `delivery_schedule` | Fetches partner's schedule context |
| `INSERT/RPC` | `orders` / `order_items` | Van sales orders recorded via delivery service RPC |

---

### 10.3 DeliveryOrderDetailsScreen
**Path:** `src/screens/delivery/DeliveryOrderDetailsScreen.tsx`  
**Purpose:** Delivery partner's view of a specific order — customer name, address, items, confirmation button.

**Supabase APIs:**

| Operation | Table | Details |
|---|---|---|
| `SELECT` | `orders` | `orderService.getOrderById(orderId)` |
| `SELECT` | `order_items` | `orderService.getOrderItems(orderId)` |
| `UPDATE` | `delivery_schedule` | `deliveryService.updateDeliverySchedule(id, { status: 'delivered' })` |

---

## 11. Supabase Tables — Full Reference

| Table | Purpose | Key Columns |
|---|---|---|
| `users` | Public user profiles + role | `id` (UUID FK auth.users), `username`, `name`, `email`, `phone`, `role` (customer/admin/delivery_partner), `avatar_url`, `country` |
| `products` | Product catalogue — dual-country | `id`, `name`, `description`, `category`, `sell_type` (loose/pack), `pack_size_grams`, `price_germany`, `price_denmark`, `stock_germany`, `stock_denmark`, `image_url`, `active`, `created_at` |
| `orders` | Customer orders | `id`, `user_id` (FK users), `status` (pending/confirmed/dispatched/delivered/cancelled), `total_amount`, `payment_method` (cash/stripe), `payment_status`, `delivery_type` (home/pickup), `address_id`, `pickup_point_id`, `created_at` |
| `order_items` | Line items per order | `id`, `order_id` (FK orders), `product_id` (FK products), `quantity`, `unit_price` |
| `pickup_points` | Pickup locations with delivery fee settings | `id`, `name`, `address`, `latitude`, `longitude`, `active`, `delivery_fee`, `base_delivery_fee`, `free_delivery_radius`, `extra_km_fee` |
| `delivery_schedule` | Delivery partner schedules | `id`, `delivery_partner_id` (FK users), `pickup_point_id` (FK pickup_points), `scheduled_date`, `status`, `completed_at`, `notes` |
| `addresses` | Customer saved addresses | `id`, `user_id` (FK users), `label`, `street`, `city`, `state`, `postal_code`, `is_default` |
| `payment_methods` | Saved Stripe payment methods | `id`, `user_id` (FK users), `stripe_payment_method_id`, `last4`, `brand`, `is_default` |
| `notifications` | In-app notification inbox | `id`, `user_id` (FK users), `title`, `body`, `type`, `read` (bool), `created_at` |
| `notification_preferences` | Per-user notification opt-ins | `user_id` (FK users), `order_updates` (bool), `delivery_alerts` (bool), `promotions` (bool) |
| `notification_templates` | Editable message templates | `id`, `name`, `title_template`, `body_template`, `type` |
| `user_push_tokens` | Expo push tokens per user | `user_id` (FK users), `token` (Expo push token), `platform` |

---

## 12. Supabase RPCs — Full Reference

All RPCs are PostgreSQL functions called via `supabase.rpc(name, params)`.

| RPC Name | Security | Purpose | Called From |
|---|---|---|---|
| `create_order_atomic` | SECURITY DEFINER | Atomically inserts `orders` + `order_items` + decrements country-specific stock column (`stock_germany` or `stock_denmark`). Returns new order UUID. Prevents race conditions. | `orderService.createOrder` |
| `admin_toggle_product_status` | SECURITY DEFINER | Sets `products.active = $active` for `id = $product_id`. Bypasses RLS. | `productService.toggleProductActive` |
| `delete_user_by_id` | SECURITY DEFINER | Deletes `auth.users` + `public.users` for given UUID. Admin deletes delivery partners. | `userService.deleteDeliveryPartner` |
| `delete_user_account` | SECURITY DEFINER | Self-deletion: removes caller's own auth user + public profile. **This is how customers delete their own account.** | `authStore.deleteAccount` (called from SettingsScreen) |
| `create_notification` | SECURITY DEFINER | Inserts into `notifications` for any `user_id`, bypassing RLS. Used for cross-user notification delivery. | `notificationService.createNotification` |
| `get_email_by_username` | PUBLIC | Returns the `email` for a given `username` from `public.users`. Returns dummy `@thamili.app` email if none registered. | `authStore.loginWithUsername` |
| `lookup_user_by_username` | PUBLIC | Returns `{ exists: boolean, email: string, isDummy: boolean }` for forgot-password flow. | `authStore.checkUsername` |
| `claim_account_email` | SECURITY DEFINER | Updates `auth.users.email` from dummy `@thamili.app` to a real email before sending password reset. | `authStore.recoverAccount` |

---

## 13. Supabase Storage

**Bucket:** `product-images`  
**Access:** Public (CDN URLs readable without auth).  
**Used For:**
- Product images (AddProduct / EditProduct)
- User avatar images (EditProfile)

**Upload Flow:**
```
1. expo-file-system reads file as ArrayBuffer
2. productService.uploadProductImage(buffer, filename) → uploads to bucket
3. supabase.storage.from('product-images').getPublicUrl(path) → CDN URL
4. URL stored in products.image_url or users.avatar_url
```
**Fallback:** If expo-file-system ArrayBuffer read fails, `fetch → blob → arrayBuffer` fallback is used.

---

## 14. Supabase Realtime Subscriptions

Supabase JS v1 Realtime API (`.on('*', callback)` pattern).

### useOrderRealtime
**File:** `src/hooks/useOrderRealtime.ts`  
**Channel:** `orders`  
**Trigger:** Any INSERT, UPDATE, or DELETE on the `orders` table.  
**Effect:** Invalidates React Query keys: `['ordersAll']`, `['allOrders']`, `['order', orderId]`.  
**Used By:** AdminDashboardScreen, AdminOrdersScreen.

### useDeliveryRealtime
**File:** `src/hooks/useDeliveryRealtime.ts`  
**Channel:** `delivery_schedule:delivery_partner_id=eq.{partnerId}`  
**Trigger:** Any change to `delivery_schedule` rows belonging to the logged-in delivery partner.  
**Effect:** Invalidates `['deliverySchedules']` React Query key; calls optional `onUpdate` callback.  
**Used By:** DeliveryDashboardScreen.

---

## 15. Services Layer

All services in `src/services/`. Each exports pure async functions. No service holds state.

| Service File | Responsibility |
|---|---|
| `supabase.ts` | Lazy Proxy singleton — exports `supabase` client |
| `authStore.ts` (Zustand) | All auth: login, register, logout, session hydration, OAuth, delivery partner management, account deletion |
| `orderService.ts` | Order CRUD: create (atomic RPC), get by user, get all, get by ID, update status, cancel, top products |
| `productService.ts` | Product CRUD, toggle active (RPC), Storage image upload |
| `deliveryService.ts` | Delivery schedule CRUD, van sales |
| `notificationService.ts` | In-app notifications (CRUD), push token registration, bulk push via Vercel, template management |
| `userService.ts` | User profile get/update, delivery partner list + delete (RPC), self-account delete (RPC) |
| `addressService.ts` | Customer address CRUD |
| `paymentMethodService.ts` | Saved Stripe payment method CRUD |
| `pickupPointService.ts` | Pickup point CRUD + haversine distance for nearest-pickup |
| `mapboxService.ts` | Mapbox Geocoding API for address autocomplete |
| `stripeService.ts` | Calls Vercel `/api/create-payment-intent`, returns clientSecret to Stripe SDK |
| `pushNotificationService.ts` | Registers Expo push token into `user_push_tokens` on login |

---

## 16. State Management (Zustand Stores)

All stores in `src/store/`. All persisted stores use AsyncStorage.

### authStore (`src/store/authStore.ts`) — ~1740 lines

| State | Type | Persisted | Description |
|---|---|---|---|
| `user` | `User \| null` | AsyncStorage `USER_DATA` | Full public.users profile |
| `token` | `string \| null` | AsyncStorage `USER_TOKEN` | Supabase JWT |
| `isAuthenticated` | `boolean` | Derived | True when user + token both non-null |
| `isLoading` | `boolean` | No | True during login/register/session load |
| `hasCompletedOnboarding` | `boolean` | AsyncStorage | True after CountrySelection completed |

**Key Actions:** `loginWithUsername`, `loginWithGoogle`, `registerWithUsername`, `logout`, `loadSession`, `changePassword`, `deleteAccount`, `createDeliveryPartner`, `updateDeliveryPartner`, `deleteDeliveryPartner`, `checkUsername`, `recoverAccount`, `updateCountryPreference`, `handleAuthCallback`, `setupAuthListener`

---

### cartStore (`src/store/cartStore.ts`)

| State | Type | Persisted | Description |
|---|---|---|---|
| `items` | `CartItem[]` | AsyncStorage `CART` | Array of `{ product, quantity, selectedCountry }` |
| `selectedCountry` | `'germany' \| 'denmark'` | AsyncStorage `SELECTED_COUNTRY` | Active market |
| `countrySelected` | `boolean` | AsyncStorage | True after onboarding |
| `hasLoadedCountry` | `boolean` | No | Hydration guard to prevent flicker |

**Concurrency:** Cart writes use a **mutex + 500ms debounce** to prevent AsyncStorage corruption on rapid add/remove.

---

### productStore (`src/store/productStore.ts`)
In-memory cache of product list. Invalidated when productService fetches fresh data.

---

### savedForLaterStore (`src/store/savedForLaterStore.ts`)
AsyncStorage key `@thamili_saved_for_later`. Products the customer saved but didn't add to cart.

---

### themeStore (`src/store/themeStore.ts`)
AsyncStorage-persisted dark/light mode toggle. Drives NativeWind `dark:` classes.

---

## 17. Client-Side Rate Limiter

**File:** `src/utils/rateLimiter.ts`  
**Algorithm:** In-memory sliding window. Timestamps stored per category in a `Map<category, number[]>`. On each request, timestamps outside the window are evicted. If `recent.length >= maxRequests`, throws `RateLimitError` with `retryAfterMs`.

**Called at the top of every sensitive service function** — before any Supabase call is made.

| Category | Max Requests | Window | Applies To |
|---|---|---|---|
| `auth` | 5 | 15 minutes | Login, Register |
| `order_write` | 3 | 1 minute | createOrder, cancelOrder |
| `order_read` | 20 | 1 minute | getOrders, getOrderById |
| `payment` | 3 | 1 minute | Stripe PaymentIntent creation |
| `product_write` | 10 | 1 minute | Admin create/update/delete product |
| `product_read` | 30 | 1 minute | getProducts, getProductById |
| `address` | 15 | 1 minute | Address CRUD + fetch |
| `user_write` | 10 | 1 minute | updateProfile, deleteAccount |
| `mapbox` | 30 | 1 minute | Geocoding API (paid, debounced input) |
| `notification` | 20 | 1 minute | Notification reads/writes |
| `default` | 30 | 1 minute | Any uncategorised operation |

**RateLimitError** class exposes `retryAfterMs` so the UI can display a countdown.  
`resetRateLimit(category?)` resets a specific or all categories — called on logout.

---

## 18. Role-Based Access Control (RBAC)

**File:** `src/utils/rbac.ts`

```ts
hasRole(userRole, requiredRole)  // admin always returns true
isAdmin(userRole)
isCustomer(userRole)
getAccessibleRoutes(userRole)    // returns allowed route names per role
canAccessRoute(route, userRole)  // point check
```

**Admin access** — admin has access to ALL routes (superuser).  
**Customer routes:** Main, Home, Products, Cart, Orders, Profile, Settings, ProductDetails, Checkout, OrderConfirmation, OrderDetails.  
**Unauthenticated routes:** Login, Register.

Used by `RouteGuard` component and navigation logic to prevent unauthorised screen access.

---

## 19. Offline Queue & Network Resilience

### offlineQueue (`src/utils/offlineQueue.ts`)

A persistent queue for failed mutations when the device is offline.

| Property | Value |
|---|---|
| AsyncStorage key | `@thamili:offline_queue` |
| Max queue size | 100 items |
| Max retries per item | 3 |
| Priority eviction | Evicts oldest `type: 'other'` items when full |
| Processing order | Oldest first (sorted by `timestamp`) |
| Batch save | Single AsyncStorage write at end of processing batch (not per-item) |

**Mutation types:** `'order'` · `'product'` · `'profile'` · `'other'`

**Operations currently handled:**
- `createOrder` — if order creation fails offline, queued and retried when back online

### useOfflineQueue (`src/hooks/useOfflineQueue.ts`)

React hook that subscribes to `offlineQueue` state changes and listens to `NetInfo` for connectivity events. When the device comes back online and the queue is non-empty, `processQueue()` is triggered automatically.

### networkUtils (`src/utils/networkUtils.ts`)

| Function | Description |
|---|---|
| `isOnline()` | Returns `boolean` via `@react-native-community/netinfo` |
| `getNetworkState()` | Returns `{ isConnected, isInternetReachable, type }` |
| `classifyError(error)` | Returns `{ type: 'network'/'server'/'client'/'unknown', isRetryable, userMessage }` |
| `retryWithBackoff(fn, maxRetries=3, initialDelay=1000)` | Exponential backoff retry wrapper (1s → 2s → 4s) |

**OfflineStatusIndicator component** shows a banner when offline.  
**NetworkErrorHandler component** wraps screens to catch and display network errors gracefully.

---

## 20. Conflict Resolution

**File:** `src/utils/conflictResolution.ts`

Handles concurrent update conflicts between local and remote state (e.g., cart synced across devices, profile updated from two places).

| Strategy | Behaviour |
|---|---|
| `last-write-wins` | Compares `updated_at` timestamps; most recent wins. Defaults to remote (server wins) if no timestamp. |
| `first-write-wins` | Oldest timestamp wins. Defaults to local (client wins). |
| `merge` | 3-way merge: if local changed but remote didn't → keep local. If both changed → remote wins. If only remote changed → use remote. |
| `manual` | Caller provides a custom `resolve(local, remote) => T` function. |

**Key functions:**
- `resolveConflict<T>(options)` — main entry point
- `hasConflict<T>(local, remote, base?)` — detect conflict
- `createConflictResolver<T>(strategy, customResolver?)` — factory for reusable resolver
- `resolveVersionConflict<T>(local, remote)` — for entities with a `version` number field

---

## 21. Request Queue & Timeouts

### requestQueue (`src/utils/requestQueue.ts`)

A priority queue for managing concurrent API requests to prevent overload.

| Config | Value |
|---|---|
| Max concurrent | 3 |
| Max queue size | 50 |
| Retry delay (initial) | 1000ms |
| Backoff | Exponential (1s → 2s → 4s) |
| Priority order | `high` → `medium` → `low` |
| Max retries | 3 (configurable per request) |

**Usage:** `requestQueue.enqueue(fn, { priority: 'high', maxRetries: 3 })`  
**Helper:** `queueRequest(fn, priority)` — shorthand wrapper.

### requestTimeout (`src/utils/requestTimeout.ts`)

Wraps any promise with a timeout using `Promise.race`.

| Constant | Value |
|---|---|
| `DEFAULT_TIMEOUTS.SHORT` | 5,000ms |
| `DEFAULT_TIMEOUTS.MEDIUM` | 15,000ms |
| `DEFAULT_TIMEOUTS.LONG` | 30,000ms |
| `DEFAULT_TIMEOUTS.VERY_LONG` | 60,000ms |

**Usage:** `await withTimeout(promise, { timeout: DEFAULT_TIMEOUTS.MEDIUM })`  
Throws `TimeoutError` (extends `Error`) with `timeout` property on expiry.

---

## 22. Form Validation (Yup Schemas)

**File:** `src/utils/validation.ts`

All schemas use `yup` and are validated via `validateForm(schema, data)` which returns `{ isValid, errors: Record<string, string> }`.

| Schema | Used On | Rules |
|---|---|---|
| `usernameLoginSchema` | LoginScreen | username: 3–20 chars, `[a-zA-Z0-9_]` only; password: min 6 |
| `loginSchema` | (email/password login variant) | email: valid format; password: min 6 |
| `registerSchema` | RegisterScreen | name: min 2; email: valid; phone: optional E.164-ish regex; password: min 6; confirmPassword: must match |
| `phoneUsernameRegisterSchema` | (phone+OTP variant) | name: min 2; phone: required; otp: 6 digits; username: 3–20 `[a-zA-Z0-9_]`; password: min 6; confirmPassword: must match |
| `profileUpdateSchema` | EditProfileScreen | name: min 2; phone: optional regex |
| `passwordChangeSchema` | ChangePasswordScreen | currentPassword: required; newPassword: min 6; confirmPassword: must match newPassword |

---

## 23. Multi-Country Product Model

Products have **separate stock and price fields per country**:

| Field | Type | Description |
|---|---|---|
| `price_germany` | `number` | Price in EUR for Germany market |
| `price_denmark` | `number` | Price in EUR for Denmark market |
| `stock_germany` | `number` | Stock in KG for Germany |
| `stock_denmark` | `number` | Stock in KG for Denmark |
| `sell_type` | `'loose' \| 'pack'` | How the product is sold |
| `pack_size_grams` | `number` | Grams per packet (pack items only) |

**Sell type rules:**

| Type | Cart quantity unit | Min quantity | Stock in DB | Display |
|---|---|---|---|---|
| `loose` | Grams | 100g | KG (`stock * 1000` for grams) | e.g., "500g", "1.2kg" |
| `pack` | Packets | 1 packet | Packet count (`Math.floor(stock)`) | e.g., "3 packets" |

The `create_order_atomic` RPC decrements the correct stock column (`stock_germany` or `stock_denmark`) based on the order's country context.

---

## 24. Cart & Pricing Utilities

**File:** `src/utils/cartUtils.ts` + `src/utils/cartValidation.ts`

### Price Calculation

```ts
calculateItemSubtotal(item, country)
// Loose: (price * quantity_grams) / 1000
// Pack:  price * quantity_packets

calculateCartSubtotal(items, country)   // sum of all item subtotals
```

### Delivery Fee Calculation

```ts
calculateDeliveryFee(pickupPoint, isHomeDelivery, customerLocation)
```

- **Pickup point delivery:** `pickupPoint.delivery_fee`
- **Home delivery:** `base_delivery_fee` + `(distance_beyond_free_radius_km * extra_km_fee)`
- Distance calculated via haversine formula from pickup point coordinates to customer coordinates
- If coordinates unavailable, falls back to `base_delivery_fee`

### Payment Processing Fee

```ts
calculatePaymentFee(amount, paymentMethod)
// Stripe: 2.9% + €0.30 (only for 'online' payment)
// Cash:   €0.00
```

### Cart Validation

```ts
validateCart(items)        // → { isValid, errors: string[] }
validateStock(product, quantity, country)   // checks country stock
validateQuantity(quantity, min, max)
removeOutOfStockItems(items)   // splits into { validItems, removedItems }
updateCartWithProductData(cartItems, freshProducts)  // syncs quantities to current stock
```

---

## 25. Regional Formatting

**File:** `src/utils/regionalFormatting.ts`  
**Markets:** Germany (`germany`) and Denmark (`denmark`).  
**Currency:** EUR (€) for both.

| Function | Germany | Denmark |
|---|---|---|
| `formatCurrency(amount, country)` | `€ 1,234.56` | `€ 1,234.56` (same) |
| `formatDate(date, country)` | `DD.MM.YYYY` (de-DE) | `DD/MM/YYYY` (da-DK) |
| `formatDateTime(date, country)` | `DD.MM.YYYY HH:MM` | `DD/MM/YYYY HH:MM` |
| `formatPhoneNumber(phone, country)` | `+49 170 1234567` | `+45 12 34 56 78` |
| `validatePhoneNumberForCountry` | Must start `+49`, 11 digits max | Must start `+45`, exactly 8 local digits |
| `validatePostalCode(code, country)` | 5 digits (e.g., 10115) | 4 digits (e.g., 2100) |
| `getCurrencySymbol(country)` | `€` | `€` |
| `getCurrencyCode(country)` | `EUR` | `EUR` |

---

## 26. Haptic Feedback

**File:** `src/utils/hapticFeedback.ts`  
**Library:** `expo-haptics` (disabled on web via `Platform.OS !== 'web'` guard).  
All functions fire-and-forget (non-blocking).

| Function | Haptic Type | Typical Use |
|---|---|---|
| `lightHaptic()` | `ImpactFeedbackStyle.Light` | Subtle interactions (tab switch) |
| `mediumHaptic()` | `ImpactFeedbackStyle.Medium` | Standard button presses |
| `heavyHaptic()` | `ImpactFeedbackStyle.Heavy` | Destructive actions |
| `successHaptic()` | `NotificationFeedbackType.Success` | Order placed, payment success |
| `warningHaptic()` | `NotificationFeedbackType.Warning` | Stock warning, rate limit |
| `errorHaptic()` | `NotificationFeedbackType.Error` | Failed payment, network error |
| `selectionHaptic()` | `selectionAsync()` | Pickers, toggles, segmented controls |

---

## 27. Product Discovery Utilities

### Recently Viewed (`src/utils/recentlyViewed.ts`)

| Detail | Value |
|---|---|
| AsyncStorage key | `@thamili_recently_viewed` |
| Max items | 20 (oldest removed when full) |
| Item shape | `{ productId, timestamp, category? }` |

Functions: `addToRecentlyViewed`, `getRecentlyViewed`, `removeFromRecentlyViewed`, `clearRecentlyViewed`.  
Called from ProductDetailsScreen and ProductsScreen on product tap.

### Search History (`src/utils/searchHistory.ts`)

| Detail | Value |
|---|---|
| AsyncStorage key | `@thamili_search_history` |
| Max items | 10 (oldest removed when full) |
| Item shape | `{ query, timestamp, category? }` |

Functions: `addToSearchHistory`, `getSearchHistory`, `removeFromSearchHistory`, `clearSearchHistory`, `getSearchSuggestions(query, limit=5)`.  
Suggestions filter history by prefix match and return up to 5 results.

### Product Recommendations (`src/utils/productRecommendations.ts`)

| Function | Algorithm |
|---|---|
| `getTrendingProducts(products, country, limit=10)` | Filters active + in-stock for country, sorts by stock descending |
| `getRecommendedProducts(products, excludeIds, limit=10)` | Gets recently viewed categories → finds products in same categories → fills gaps with trending |
| `getRelatedProducts(products, productId, category, limit=5)` | Same category, excludes current product, active only |

---

## 28. Local Storage Keys — Full Reference

| Key | Store/Util | Content |
|---|---|---|
| `USER_DATA` | authStore | JSON: `User` object |
| `USER_TOKEN` | authStore | Supabase JWT string |
| `CART` | cartStore | JSON: `CartItem[]` |
| `SELECTED_COUNTRY` | cartStore | `'germany'` or `'denmark'` |
| `@thamili_saved_for_later` | savedForLaterStore | JSON: saved products |
| `@thamili:offline_queue` | offlineQueue | JSON: `QueuedMutation[]` |
| `@thamili_checkout_data` | checkoutAutoSave | JSON: `CheckoutFormData` |
| `@thamili_recently_viewed` | recentlyViewed | JSON: `RecentlyViewedItem[]` (max 20) |
| `@thamili_search_history` | searchHistory | JSON: `SearchHistoryItem[]` (max 10) |
| onboarding tutorial flag | onboardingStorage | boolean — tutorial shown? |

---

## 29. Hooks — Full Reference

| Hook | File | Purpose |
|---|---|---|
| `useOrderRealtime` | `hooks/useOrderRealtime.ts` | Supabase v1 realtime — orders table → React Query invalidation |
| `useDeliveryRealtime` | `hooks/useDeliveryRealtime.ts` | Supabase v1 realtime — delivery_schedule filtered by partner |
| `useOfflineQueue` | `hooks/useOfflineQueue.ts` | Subscribes to offline queue, auto-processes on reconnect |
| `useOrderPolling` | `hooks/useOrderPolling.ts` | Polls `getOrderById` every 10s (configurable), fires `onStatusChange` callback on status transitions |
| `useProducts` | `hooks/useProducts.ts` | React Query wrapper for `productService.getProducts()` |
| `useOrders` | `hooks/useOrders.ts` | React Query wrapper for `orderService.getOrders()` |
| `useDeliveries` | `hooks/useDeliveries.ts` | React Query wrapper for `deliveryService.getDeliverySchedules()` |
| `usePickupPoints` | `hooks/usePickupPoints.ts` | React Query wrapper for `pickupPointService.getPickupPoints()` |
| `useTheme` | `hooks/useTheme.ts` | Reads from `themeStore`, returns current theme colors |

---

## 30. External Integrations

### Stripe

- **SDK:** `@stripe/stripe-react-native` v0.50.3
- **Flow:**
  1. Checkout screen: `checkRateLimit('payment')` (3/min)
  2. `stripeService.createPaymentIntent(amount, 'EUR')` → POST to Vercel `/api/create-payment-intent`
  3. Vercel uses `STRIPE_SECRET_KEY` to create PaymentIntent server-side
  4. Returns `{ clientSecret }`
  5. Native Stripe SDK `confirmPayment(clientSecret)` opens payment sheet
  6. On success: `orderService.updatePaymentStatus(orderId, 'paid')`

### Mapbox

- **SDK:** `@rnmapbox/maps` v10.x
- **Uses:**
  - `AddressAutocomplete` component — Geocoding API via `mapboxService` (debounced, rate-limited 30/min)
  - `MapboxAddressSelector` component — full-screen map picker with draggable pin
  - `DeliveryDashboardScreen` — renders delivery route on map
  - AddPickupPoint / EditPickupPoint — coordinate picker for admin

### Expo Push Notifications

- **Flow:**
  1. On login: `pushNotificationService.registerPushToken()` — `Expo.getExpoPushTokenAsync()`
  2. Token stored in `user_push_tokens` table via Supabase INSERT
  3. Delivery partner triggers bulk notification → `notificationService.sendBulkNotification(userIds, message)`
  4. This calls Vercel `/api/send-push` with `INTERNAL_API_SECRET` header
  5. Vercel fetches tokens for `userIds` from `user_push_tokens`, sends to Expo Push API

### Google OAuth

1. `supabase.auth.signIn({ provider: 'google' })` opens browser
2. On redirect: deep link `thamili://auth-callback` caught by app
3. `authStore.handleAuthCallback(url)` parses token from URL fragment
4. `supabase.auth.setAuth(token)` called manually to hydrate v1 client

### i18next

- **Languages:** English (`en`), Tamil (`ta`)
- **Usage:** All user-facing strings use `t('key')` via `useTranslation()` hook
- **Switching:** `i18n.changeLanguage(lang)` in SettingsScreen — no app restart required
- Keys include: auth, settings, profile, product, order, delivery, common, errors

---

## 31. Vercel Serverless API

Two serverless functions deployed on Vercel. Called from the mobile app via `fetch`.

### `POST /api/create-payment-intent`
- **Auth:** None (CORS origin restriction)
- **Input:** `{ amount: number, currency: string }`
- **Action:** Calls Stripe API server-side with `STRIPE_SECRET_KEY`
- **Output:** `{ clientSecret: string }`

### `POST /api/send-push`
- **Auth:** `Authorization: Bearer INTERNAL_API_SECRET` header
- **Input:** `{ userIds: string[], title: string, body: string, data?: object }`
- **Action:** Queries `user_push_tokens` for each userId, batches and sends to Expo Push API
- **Output:** `{ successCount: number, failureCount: number }`

---

## 32. Environment Variables

**File:** `.env` (not committed) / `.env.example` (template)  
**Loaded via:** `react-native-dotenv` → `src/config/env.ts`

| Variable | Where Used | Purpose |
|---|---|---|
| `EXPO_PUBLIC_SUPABASE_URL` | Mobile app | Supabase project URL |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | Mobile app | Supabase anonymous public key |
| `EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY` | Mobile app | Stripe publishable key (safe to expose) |
| `EXPO_PUBLIC_MAPBOX_ACCESS_TOKEN` | Mobile app | Mapbox public access token |
| `STRIPE_SECRET_KEY` | Vercel only | Stripe secret key — never shipped in app bundle |
| `INTERNAL_API_SECRET` | Vercel + mobile app | Shared secret for `/api/send-push` auth header |
| `SUPABASE_SERVICE_ROLE_KEY` | Vercel only | Supabase service role — for server-side token lookup |

---

## 33. EAS Build Profiles

**File:** `eas.json`

| Profile | Purpose | Mode |
|---|---|---|
| `development` | Local dev builds with dev client | Debug |
| `preview` | Internal testing, no store submission | Release |
| `production` | App store submission | Release + optimised |

**App identifiers:**
- iOS bundle ID: `com.thamili.thamili`
- Android package: `com.thamili.thamili`
- Deep link scheme: `thamili://`

---

## 34. Key File Paths

| Path | Description |
|---|---|
| `src/services/supabase.ts` | Supabase lazy Proxy singleton |
| `src/store/authStore.ts` | All authentication logic (~1740 lines) including `deleteAccount` |
| `src/store/cartStore.ts` | Cart with mutex + debounced AsyncStorage persistence |
| `src/navigation/AppNavigator.tsx` | Full navigation tree + role-based tab switching |
| `src/types/index.ts` | All TypeScript interfaces (User, Product, Order, CartItem, etc.) |
| `src/types/notifications.ts` | Notification-specific TypeScript types |
| `src/services/orderService.ts` | Order CRUD + atomic creation RPC |
| `src/services/productService.ts` | Product CRUD + Storage image upload |
| `src/services/notificationService.ts` | In-app + push notification system |
| `src/services/deliveryService.ts` | Delivery schedule management |
| `src/services/userService.ts` | User profile + delivery partner management |
| `src/services/addressService.ts` | Address CRUD |
| `src/services/pickupPointService.ts` | Pickup point CRUD + haversine nearest-pickup |
| `src/services/paymentMethodService.ts` | Saved payment method CRUD |
| `src/hooks/useOrderRealtime.ts` | Supabase v1 realtime hook for orders |
| `src/hooks/useDeliveryRealtime.ts` | Supabase v1 realtime hook for delivery schedule |
| `src/hooks/useOrderPolling.ts` | 10-second order status polling with change detection |
| `src/hooks/useOfflineQueue.ts` | Offline queue processor + NetInfo listener |
| `src/utils/rateLimiter.ts` | Sliding window client-side rate limiter |
| `src/utils/rbac.ts` | Role-based access control helpers |
| `src/utils/offlineQueue.ts` | Persistent offline mutation queue (AsyncStorage) |
| `src/utils/conflictResolution.ts` | last-write-wins / merge / manual conflict strategies |
| `src/utils/requestQueue.ts` | Priority-based concurrent request queue (max 3) |
| `src/utils/requestTimeout.ts` | Timeout wrapper with SHORT/MEDIUM/LONG/VERY_LONG presets |
| `src/utils/validation.ts` | All yup form validation schemas |
| `src/utils/cartUtils.ts` | Cart pricing, delivery fee, Stripe fee calculations |
| `src/utils/cartValidation.ts` | Cart stock/quantity validation per country |
| `src/utils/regionalFormatting.ts` | Currency, date, phone, postal code formatting for DE + DK |
| `src/utils/hapticFeedback.ts` | expo-haptics wrapper (light/medium/heavy/success/warning/error) |
| `src/utils/productRecommendations.ts` | Trending + recommended + related product algorithms |
| `src/utils/recentlyViewed.ts` | Recently viewed product history (max 20, AsyncStorage) |
| `src/utils/searchHistory.ts` | Search history + suggestions (max 10, AsyncStorage) |
| `src/utils/checkoutAutoSave.ts` | Checkout form auto-save with 1s debounce |
| `src/utils/requireAuth.ts` | Auth gate alert — shows Login/Register prompt for guests |
| `src/utils/networkUtils.ts` | isOnline, classifyError, retryWithBackoff |
| `src/config/env.ts` | ENV config loader (react-native-dotenv) |
| `app.json` | Expo project config (package name, scheme, version) |
| `eas.json` | EAS build profiles |
| `api/create-payment-intent.ts` | Vercel function — Stripe PaymentIntent creation |
| `api/send-push.ts` | Vercel function — Expo push notification delivery |
