# Breakpoints & Styles Reference

## Local → Webflow Breakpoint Mapping

Local (mobile-first) CSS uses `min-width` media queries. Webflow is desktop-first. Map as follows:

| Local CSS | Webflow Breakpoint | Applies To |
|-----------|-------------------|------------|
| `@media (min-width: 1024px)` values | `main` | Desktop default (≥992px, inherits to all) |
| `@media (min-width: 768px)` values | `medium` | ≤991px (tablet and below) |
| Base/default values (no media query) | `medium` or `small` | ≤991px or ≤767px depending on context |
| `@media (min-width: 1440px)` values | `xl` | ≥1440px (wide screens) |
| `@media (max-width: 640px)` values | `small` or `tiny` | ≤767px or ≤478px |

### Known Gap

Webflow breakpoints are **fixed** — cannot be customized:
- Webflow `main` starts at 992px, but many local projects use 1024px as desktop breakpoint
- The 32px overlap (992–1023px) will show desktop styles in Webflow
- Accept this and adapt local CSS per-component when differences are visible

### Mapping Process

1. Read the local CSS file and identify all `@media` queries
2. For each property, determine its value at each breakpoint
3. Set desktop values on `main`, tablet overrides on `medium`, mobile on `small`/`tiny`
4. Always start from `main` and work downward — Webflow cascades desktop-first

## style_tool Usage Patterns

### Setting Multiple Properties

```
style_tool → set_styles:
  styleName: "section-title"
  breakpoint: "main"
  pseudo: "noPseudo"
  properties:
    font-family: "'Public Sans', sans-serif"
    font-size: "32px"
    font-weight: "700"
    color: "#1A2332"
    line-height: "1.3"
    margin-bottom: "24px"
```

### Responsive Override (tablet)

```
style_tool → set_styles:
  styleName: "section-title"
  breakpoint: "medium"
  properties:
    font-size: "24px"
    margin-bottom: "16px"
```

Note: Only set properties that DIFFER from the `main` breakpoint. Webflow inherits the rest.

### Using Variables in Styles

```
properties:
  color: "var(--variable-f8edca52-1fd8-80a3-4324-6fe259f12eff)"
  font-family: "var(--variable-7a17e383-b5cf-ff8d-8d1c-5ca61fae13db)"
```

Variables are referenced by their Webflow variable ID, not CSS custom property names.

### Hover States

```
style_tool → set_styles:
  styleName: "card"
  breakpoint: "main"
  pseudo: "hover"
  properties:
    border-color: "rgba(255, 71, 51, 0.2)"
    transform: "translateY(-2px)"
```

### Grid Layouts

```
properties:
  display: "grid"
  grid-template-columns: "1fr 1fr"
  gap: "64px"
  align-items: "center"
```

### Flexbox Layouts

```
properties:
  display: "flex"
  flex-direction: "row"
  align-items: "center"
  gap: "32px"
```

## Common Typography Scale

A typical professional typography scale for reference:

| Element | Desktop (main) | Tablet/Mobile (medium) |
|---------|---------------|----------------------|
| H1 | 48px, weight 700, line-height 1.2 | 32px |
| H2 | 32px, weight 700, line-height 1.3 | 24px |
| H3 | 24px, weight 600, line-height 1.3 | 20px |
| Body | 18px, weight 400, line-height 1.6 | 16px |
| Small | 16px, weight 400, line-height 1.6 | 15px |
| Meta/Badge | 14px, weight 600, line-height 1.5 | 13px |

## Common Spacing Scale

| Token | Value | Use Case |
|-------|-------|----------|
| xs | 4px | Tight gaps, icon padding |
| sm | 8px | Between badge and title, small gaps |
| md | 16px | Paragraph margins, element gaps |
| lg | 24px | Section internal spacing |
| xl | 32px | Medium section gaps |
| 2xl | 48px | Section padding (mobile) |
| 3xl | 64px | Section padding (desktop), large gaps |

## Section Padding Pattern

Standard section padding across breakpoints:

| Breakpoint | Padding Top/Bottom | Gap Between Sections |
|-----------|-------------------|---------------------|
| main (desktop) | 64px | 64px |
| medium (tablet) | 56px | 56px |
| small (mobile) | 48px | 48px |

## Badge / Subtitle Pattern

Commonly used for section labels:

```
properties:
  display: "inline-block"
  font-size: "14px"        # 13px on mobile
  font-weight: "600"
  color: "#FF4733"          # Or brand color
  text-transform: "uppercase"
  letter-spacing: "0.5px"
  margin-bottom: "8px"
```
