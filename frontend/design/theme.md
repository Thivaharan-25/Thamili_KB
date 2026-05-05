# Theme

> **Part of:** [[INDEX|T Web Knowledge Base]]  
> **See also:** [[frontend/design/typography|typography]] | [[frontend/design/responsive|responsive]] | [[frontend/components/component-catalog|component-catalog]]

Use CSS variables so every skeleton screen inherits light/dark colors.

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222 47% 11%;
  --card: 0 0% 100%;
  --primary: 166 84% 32%;
  --primary-foreground: 0 0% 100%;
  --secondary: 35 92% 51%;
  --muted: 210 40% 96%;
  --muted-foreground: 215 16% 35%;
  --destructive: 0 72% 45%;
  --border: 214 32% 88%;
  --ring: 166 84% 32%;
}
.dark {
  --background: 220 20% 8%;
  --foreground: 210 40% 98%;
  --card: 220 18% 12%;
  --primary: 166 74% 42%;
  --primary-foreground: 220 20% 8%;
  --secondary: 42 95% 58%;
  --muted: 220 16% 18%;
  --muted-foreground: 218 14% 78%;
  --destructive: 0 72% 55%;
  --border: 220 14% 24%;
  --ring: 166 74% 42%;
}
```

## Rules

- Use tokens, not hardcoded page colors.
- Avoid one-hue palettes. Use teal, amber, neutral, and semantic states.
- Meet WCAG AA contrast; AAA where possible.
- Theme toggle persists user preference and respects system theme.
