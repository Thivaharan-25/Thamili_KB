# Data Model And Types

> **Extracted from:** `src/types/index.ts`, `src/types/notifications.ts`  
> **See also:** [[architecture/api-client|api-client]]

Use these as the first-pass web TypeScript domain types. Generate stricter Supabase database types later if the DB schema is exported.

## User

```ts
type UserRole = 'customer' | 'admin' | 'delivery_partner';

interface User {
  id: string;
  email?: string;
  phone?: string;
  name?: string;
  username?: string;
  role: UserRole;
  country_preference?: 'germany' | 'denmark';
  photoURL?: string;
  created_at: string;
}
```

## Address

```ts
interface Address {
  id: string;
  user_id: string;
  type: 'Home' | 'Work' | 'Other';
  name: string;
  street: string;
  city: string;
  state?: string;
  postal_code: string;
  country: string;
  phone: string;
  instructions?: string;
  is_default: boolean;
  latitude?: number;
  longitude?: number;
  created_at?: string;
}
```

## Product

```ts
type ProductCategory = 'fresh' | 'frozen';

interface Product {
  id: string;
  name: string;
  description?: string;
  category: ProductCategory;
  price_germany: number;
  price_denmark: number;
  original_price_germany?: number;
  original_price_denmark?: number;
  discount_percentage?: number;
  stock_germany: number;
  stock_denmark: number;
  image_url?: string;
  active: boolean;
  active_germany: boolean;
  active_denmark: boolean;
  created_at: string;
  rating?: number;
  review_count?: number;
  sell_type: 'pack' | 'loose';
  unit: 'packet' | 'gram' | 'kg';
  pack_size_grams?: number;
  is_deleted?: boolean;
}
```

## Order

```ts
type OrderStatus = 'pending' | 'confirmed' | 'out_for_delivery' | 'delivered' | 'canceled';
type PaymentMethod = 'online' | 'cod';
type PaymentStatus = 'pending' | 'paid' | 'failed' | 'refunded';

interface Order {
  id: string;
  user_id: string;
  order_date: string;
  status: OrderStatus;
  total_amount: number;
  delivery_fee?: number;
  payment_fee?: number;
  country: 'germany' | 'denmark';
  payment_method: PaymentMethod;
  payment_status: PaymentStatus;
  pickup_point_id?: string;
  delivery_address?: string;
  delivery_method?: 'home' | 'pickup';
  payment_intent_id?: string;
  created_at: string;
}
```

## Delivery

```ts
type DeliveryStatus = 'scheduled' | 'accepted' | 'picked_up' | 'in_transit' | 'delivered' | 'canceled' | 'failed';

interface DeliverySchedule {
  id: string;
  order_id: string;
  delivery_date: string;
  status: DeliveryStatus;
  pickup_point_id?: string;
  estimated_time?: string;
  actual_delivery_time?: string;
  delivery_partner_id?: string;
  notes?: string;
  created_at: string;
  updated_at: string;
}
```

## Notifications

```ts
type NotificationType =
  | 'order'
  | 'delivery'
  | 'order_confirmed'
  | 'order_shipped'
  | 'order_delivered'
  | 'payment_received'
  | 'general'
  | 'stock_alert'
  | 'task_assigned'
  | 'ready_for_pickup'
  | 'delivery_failed'
  | 'order_canceled'
  | 'task_cancelled';
```

## Needs Confirmation

- NFR requested `en-AU` and AUD, but existing app model is Germany/Denmark with EUR-style product fields.
- Web should either keep the current DB country fields or create an Australian market model before implementation starts.

