# Transferring a Local Web Project to Webflow

Step-by-step guide for replicating a local web project (React, Vue, HTML/CSS, Next.js, etc.) in the Webflow Designer.

## Overview

1. Analyze the local project (structure, styles, content)
2. Map CSS variables to concrete values
3. Map breakpoints from mobile-first to desktop-first
4. Build elements in Webflow
5. Apply styles at each breakpoint
6. Set text content
7. Handle SVGs and custom code
8. Create reusable components
9. QA and publish

## Step 1: Analyze the Local Project

Read both the component file (TSX/JSX/HTML) and its stylesheet (CSS/SCSS/module.css):

- **Element hierarchy** — What wraps what? Identify section → container → grid → content structure
- **Conditional rendering** — Are some elements only shown on certain breakpoints?
- **CSS custom properties** — List all `var(--xxx)` references and their resolved values
- **Dynamic content** — Identify props/data vs. static text
- **SVG illustrations** — Note inline SVGs that need Code Embed treatment
- **JavaScript behaviors** — Hover effects, animations, scroll triggers

## Step 2: Map CSS Variables to Pixel Values

Read the project's global CSS (e.g., `globals.css`, `variables.css`, `:root` block) and resolve every `var()` reference:

**Example resolution table:**

| Variable | Mobile (base) | Tablet (≥768px) | Desktop (≥1024px) |
|----------|----------|--------|---------|
| `--font-size-h1` | 32px | 32px | 48px |
| `--font-size-body` | 16px | 16px | 18px |
| `--space-lg` | 24px | 24px | 24px |
| `--section-padding` | 48px | 56px | 64px |

Create this table before building anything — it prevents back-and-forth during styling.

## Step 3: Map Breakpoints

Convert mobile-first `min-width` queries to Webflow's desktop-first breakpoints:

1. **Collect all `@media` rules** from the component's CSS
2. **Desktop values** (`min-width: 1024px` or similar) → Webflow `main` breakpoint
3. **Tablet values** (`min-width: 768px`) → Webflow `medium` breakpoint
4. **Mobile base values** (no media query) → Webflow `medium` or `small`
5. **Small mobile** (`max-width: 640px` or similar) → Webflow `small` or `tiny`

**Important:** In Webflow, set `main` first (it cascades to all screens), then override downward on `medium`/`small`/`tiny`. Only set properties that actually change at smaller breakpoints.

## Step 4: Build Element Tree in Webflow

Map local component hierarchy to Webflow element types:

| Local Element | Webflow Type |
|--------------|-------------|
| `<section>` | `Section` or `DivBlock` |
| `<div>` (container/wrapper) | `DivBlock` |
| `<h1>`, `<h2>`, `<h3>` | `Heading` (set level via tag property) |
| `<p>` | `Paragraph` |
| `<span>` (inline text) | `Paragraph` or `Heading` (no inline span type) |
| `<a>` wrapping content | `LinkBlock` |
| `<a>` as text link | `Link` |
| `<img>` | `Image` |
| `<ul>/<ol>` | `List` |
| `<button>` | `LinkBlock` styled as button |
| Inline SVG | `HtmlEmbed` (Code Embed) |

Use `element_builder` to create each element:
```
element_builder:
  type: "DivBlock"
  parent: "<parent-element-id>"
  position: "inside-bottom"  # or "inside-top", "before", "after"
  styleName: "overview-grid"
```

**Naming convention:** Give each element a descriptive `styleName` matching its purpose (e.g., `hero-title`, `overview-grid`, `card-description`). This makes future style updates easy via `style_tool`.

## Step 5: Apply Styles

For each element, apply styles starting from `main` breakpoint, then override on smaller breakpoints:

```
# Desktop first
style_tool → set_styles:
  styleName: "overview-grid"
  breakpoint: "main"
  pseudo: "noPseudo"
  properties:
    display: "grid"
    grid-template-columns: "1fr 1fr"
    gap: "64px"

# Tablet override
style_tool → set_styles:
  styleName: "overview-grid"
  breakpoint: "medium"
  properties:
    grid-template-columns: "1fr"
    gap: "32px"
```

**Batch efficiently:** Group all `main` breakpoint styles together, then all `medium` overrides, etc.

## Step 6: Set Text Content

Use `element_tool` → `set_text` for Heading and Paragraph elements:

```
element_tool:
  action: "set_text"
  element_id: "<element-id>"
  text: "Your heading text here"
```

- Supports `\u00a0` for non-breaking spaces
- Only works on `Heading` and `Paragraph` types (NOT `TextBlock` or `DivBlock`)
- For multi-paragraph content, create separate Paragraph elements

## Step 7: Handle SVGs and Custom Code

Inline SVGs from local components cannot be directly created via `element_builder`. Use Code Embeds:

1. Select the parent element via `element_tool`
2. Add a Code Embed via browser automation (Add Panel → Advanced → Code Embed)
3. Inject the SVG/HTML content via JavaScript into CodeMirror editor
4. Convert JSX SVG attributes to standard HTML (see code-embeds-and-svgs.md reference)

## Step 8: Componentize Repeating Patterns

After styling a section that will be reused across pages:

1. Select the root element
2. Use `de_component_tool` → `transform_element_to_component` with a descriptive name
3. Insert instances on other pages via `element_builder` with `component: "<id>"`
4. Override text on instances via `set_text`

**Good candidates for components:** Navbar, Footer, CTA sections, card patterns, hero sections

## Step 9: QA and Publish

1. **Desktop QA** — Use `element_snapshot_tool` to capture each section
2. **Responsive QA** — Resize browser to 768px, 480px, 375px via browser automation
3. **Compare** — Open local dev site side-by-side and check alignment, spacing, typography
4. **Fix discrepancies** — Update `style_tool` properties at the affected breakpoint
5. **Publish** — Use `data_sites_tool` → `publish_site`
6. **Verify live** — Navigate to the published URL and check

## Tips

- **Work section by section** — Complete one section (build → style → content → QA) before moving to the next
- **Start with the simplest sections** — Build confidence with straightforward sections before tackling complex layouts
- **Use `element_tool` → `get_elements`** sparingly on complex pages (can return 100K+ chars) — filter by style name instead
- **Read the local CSS thoroughly** — Missing a single property (like `letter-spacing` or `text-transform`) breaks pixel-perfection
- **Check inherited styles** — Webflow cascades from `main` down, so verify `medium`/`small` aren't inheriting unwanted desktop values
