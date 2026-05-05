# Screen Layouts

> **Extracted from:** `src/screens/**`, `src/navigation/AppNavigator.tsx`, existing component names  
> **See also:** [[frontend/design/responsive|responsive]] | [[frontend/design/loading-skeletons|loading-skeletons]]

The RN app gives screen intent and mobile-first structure. The web app should translate that into responsive desktop/tablet/mobile layouts.

## Layout Source Rules

- Use RN screen names and navigation to determine screen purpose.
- Use existing RN components as component intent, not direct web implementation.
- Create desktop/tablet layouts using the same content hierarchy.
- Final visual wireframes still need design review before pixel-perfect build.

## Major Layout Patterns

| Pattern           | Screens                                            | Web layout                                                     |               |
| ----------------- | -------------------------------------------------- | -------------------------------------------------------------- | ------------- |
| Auth form         | Login, register, forgot/reset password, OTP        | Centered form in [[frontend/templates/AuthLayout               | AuthLayout]]. |
| Product discovery | Home, products                                     | Search/filter top area, responsive product grid, cart action.  |               |
| Product detail    | Product detail                                     | Image/details split on desktop, stacked on mobile.             |               |
| Checkout          | Cart, checkout                                     | Step/section stack on mobile, sticky order summary on desktop. |               |
| Order management  | Orders, order detail                               | List/detail or table/detail on desktop, cards on mobile.       |               |
| Admin management  | Products, orders, pickup points, delivery partners | DataTable desktop, card list mobile, filters in sheet.         |               |
| Delivery work     | Delivery dashboard, van sales                      | Task list and map/detail panels; touch-first actions.          |               |

