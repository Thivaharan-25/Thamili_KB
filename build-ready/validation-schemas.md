# Validation Schemas

> **Extracted from:** `src/utils/validation.ts`, `src/utils/fieldValidation.ts`, `src/utils/checkoutValidation.ts`  
> **See also:** [[architecture/folder-structure|folder-structure]]

Use Yup with react-hook-form resolver.

## `validation/auth.schema.ts`

Current default registration must follow the active React Native `RegisterScreen.tsx` flow, not the older unused `registerSchema` alone. The app registers with username, password, country, and an optional contact email.

```ts
loginSchema = {
  email: string().email().required(),
  password: string().min(6).required()
}

usernameLoginSchema = {
  username: string().min(3).max(20).matches(/^[a-zA-Z0-9_]+$/).required(),
  password: string().min(6).required()
}

registerWithUsernameSchema = {
  username: string().min(3).max(20).matches(/^[a-zA-Z0-9_]+$/).required(),
  email: string().email().optional().nullable(),
  password: string().min(6).required(),
  confirmPassword: string().oneOf([ref('password')]).required(),
  country: mixed<'germany' | 'denmark'>().required()
}

otpSchema = {
  otp: string().length(6).matches(/^[0-9]+$/).required()
}

resetPasswordSchema = {
  password: string().min(6).required(),
  confirmPassword: string().oneOf([ref('password')]).required()
}
```

Notes:

- Email is optional on register.
- If email is provided, validate email format before submit.
- If email is absent, the app behavior is to use the username-based fallback email internally and store the public profile email as `null`.
- The older email-required `registerSchema` can only be used if product explicitly reintroduces email-first registration.

## `validation/address.schema.ts`

Rules from field validators:

- `street`: required, minimum 5 characters.
- `city`: required, minimum 2 characters.
- `postal_code`: Germany 5 digits, Denmark 4 digits in existing app.
- `phone`: optional in profile, required in checkout; basic phone regex plus country checks when country is known.
- `type`: `Home | Work | Other`.

## `validation/checkout.schema.ts`

Rules from `validateCheckout()`:

- Delivery method is required.
- If pickup: `pickupPointId` is required.
- If home delivery: `street`, `city`, `postalCode` are required.
- Phone is required and must contain at least 8 digits.
- Payment method is required.
- Stripe card details are not validated by app forms because Stripe owns secure collection.

## `validation/product.schema.ts`

Derived from `Product` type and admin product service:

- `name`: required, minimum 2 characters.
- `category`: `fresh | frozen`.
- `price_germany`, `price_denmark`: required numbers >= 0 unless market model changes.
- `stock_germany`, `stock_denmark`: required integers >= 0 unless market model changes.
- `sell_type`: `pack | loose`.
- `unit`: `packet | gram | kg`.
- `pack_size_grams`: required when `sell_type === 'pack'`.
- Image URL or uploaded file is optional but recommended.

## `validation/pickup-point.schema.ts`

Derived from `PickupPoint` type and pickup point service:

- `name`: required.
- `address`: required.
- `country`: required.
- `delivery_fee`: required number >= 0.
- `latitude`, `longitude`: optional but required when map-based pickup selection is enabled.
- `active`: boolean.

## `validation/notification.schema.ts`

Derived from notification types:

- Preference toggles are booleans.
- Template title/message are required.
- Notification type must match `NotificationType`.
