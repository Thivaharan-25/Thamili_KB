# Responsive

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[frontend/design/theme|theme]] | [[frontend/templates/AppLayout|AppLayout]] | [[frontend/components/component-catalog|component-catalog]]

Responsive means the same web app must work well on **desktop computers, tablets, and mobile phones**. It is not enough for the page to shrink; navigation, grids, tables, forms, maps, and action placement must adapt to the device.

## Supported Devices

| Device class | Width guide | Input | Layout expectation |
|---|---:|---|---|
| Mobile phone | 320-639px | Touch first | Single-column layout, bottom navigation, sheet-based filters, large tap targets. |
| Large phone / small tablet | 640-767px | Touch first | Two-column product grid where space allows, compact cards, full-width forms. |
| Tablet | 768-1023px | Touch and pointer | Wider forms, optional side panels, 2-3 column product grids, readable card/table hybrids. |
| Desktop / laptop | 1024-1439px | Mouse, keyboard, pointer | Topbar + sidebar, dense data tables, multi-column layouts, visible filters. |
| Large desktop | 1440-1920px+ | Mouse, keyboard, pointer | Constrained content width, richer split views, more visible columns without stretching text. |

## Layout Rules By Area

| Area | Mobile | Tablet | Desktop |
|---|---|---|---|
| Navigation | Bottom nav and compact topbar. | Topbar plus optional side drawer. | Topbar plus persistent sidebar. |
| Product grid | 1 column, sometimes 2 for compact cards. | 2-3 columns. | 3-5 columns based on content width. |
| Admin tables | Card list or labelled horizontal scroll. | Hybrid table/card layout. | Full data table with visible filters and actions. |
| Forms | Single column, full-width controls. | Single or two-column sections where readable. | Two-column sections for long forms, action bar aligned consistently. |
| Checkout | Step/section stack. | Two-column summary + form where space permits. | Main form plus sticky order summary. |
| Maps | Full-width with fallback list below. | Map plus detail panel. | Map/detail split view. |
| Modals | Prefer sheets/full-screen dialogs. | Dialog or sheet depending on task. | Dialogs, side panels, or sheets. |

## Breakpoints

| Breakpoint | Use |
|---|---|
| `sm` 640px | Large phone / small tablet adjustments. |
| `md` 768px | Tablet layouts begin. |
| `lg` 1024px | Desktop navigation and table layouts begin. |
| `xl` 1280px | Wider dashboard/admin layouts. |
| `2xl` 1536px | Large desktop content constraints. |

## Rules

- Every screen must be checked at mobile, tablet, and desktop widths.
- Use stable dimensions for cards, grids, toolbars, and maps.
- Tables become card lists or labelled horizontal scroll on small screens.
- Controls must not resize when loading, disabled, or labelled differently.
- Touch targets should be at least 44px high/wide on touch devices.
- Text must not overflow buttons, cards, table cells, or navigation items.
- Do not hide essential actions on mobile; move them to sheets, menus, or sticky action bars.
- Use CSS grid/flex and container-aware constraints, not fixed desktop widths.
