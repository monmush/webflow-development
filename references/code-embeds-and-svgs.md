# Code Embeds & SVGs Reference

## Adding a Code Embed to Webflow

Code Embeds (HtmlEmbed elements) allow custom HTML/CSS/JS inside Webflow. They are essential for:

- Inline SVG illustrations
- Custom CSS (hover effects, animations, complex layouts)
- Third-party script snippets

### Method 1: Browser Automation (Recommended)

1. Select the target parent element via `element_tool`
2. Open the Add Panel: press `A` key via browser automation
3. Scroll to "Advanced" section and click "Code Embed"
4. The Code Embed editor (CodeMirror) opens automatically
5. Inject content via JavaScript (see CodeMirror injection below)
6. Click "Save & Close"

### Method 2: Element Builder

```
element_builder:
  type: "HtmlEmbed"
  parent: "<parent-id>"
  position: "inside-bottom"
```

Note: This creates the element but you still need browser automation to edit its content.

## CodeMirror Content Injection

The Webflow Code Embed editor uses CodeMirror. Set content programmatically:

```javascript
// Find the CodeMirror instance
const cm = document.querySelector('.CodeMirror');
if (cm && cm.CodeMirror) {
  cm.CodeMirror.setValue(`<YOUR HTML/SVG/CSS CONTENT HERE>`);
}
```

Execute this via browser automation's JavaScript execution capability.

**Tips:**
- Escape backticks in the content string (use `\``)
- For large SVGs, the JS string may need to be broken into chunks
- Always verify the content was set correctly by reading back: `cm.CodeMirror.getValue()`

## JSX SVG → HTML SVG Conversion

When converting React/JSX SVG to standard HTML SVG for Code Embeds:

### Attribute Renames

| JSX (camelCase) | HTML (kebab-case) |
|-----------------|-------------------|
| `strokeWidth` | `stroke-width` |
| `strokeLinecap` | `stroke-linecap` |
| `strokeLinejoin` | `stroke-linejoin` |
| `fillRule` | `fill-rule` |
| `clipRule` | `clip-rule` |
| `clipPath` | `clip-path` |
| `viewBox` | `viewBox` (unchanged — it's standard) |
| `className` | `class` |
| `htmlFor` | `for` |
| `tabIndex` | `tabindex` |
| `xlinkHref` | `xlink:href` |

### Other JSX→HTML Changes

- Remove `{/* comments */}` — use `<!-- comments -->` or remove entirely
- Replace `style={{ prop: 'val' }}` with `style="prop: val"`
- Replace CSS variable references with hex values (CSS vars don't exist in Code Embeds)
- Remove React-specific attributes (`key`, `ref`, `onClick`, etc.)
- Replace `{expression}` with static values

### Common Color Mapping

Map CSS custom properties to their hex/rgba values:

```
var(--brand-red)        → #FF4733
var(--text-primary)     → #1A2332
var(--text-secondary)   → #6B7280
var(--border)           → #E5E7EB
var(--bg-main)          → #FAFAF9
var(--bg-surface)       → #FFFFFF
```

Always read the project's actual CSS variable definitions — these are examples.

### Example Conversion

**JSX (React):**
```jsx
<svg width="24" height="24" viewBox="0 0 24 24" fill="none"
     xmlns="http://www.w3.org/2000/svg">
  <path d="M12 2L22 12L12 22"
        stroke={color}
        strokeWidth="2"
        strokeLinecap="round"
        strokeLinejoin="round"
        fill="none" />
</svg>
```

**HTML (Code Embed):**
```html
<svg width="24" height="24" viewBox="0 0 24 24" fill="none"
     xmlns="http://www.w3.org/2000/svg">
  <path d="M12 2L22 12L12 22"
        stroke="#FF4733"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        fill="none" />
</svg>
```

## Custom CSS via Code Embed

For effects that can't be achieved with Webflow's native style panel (pseudo-elements, complex hover chains, animations):

```html
<style>
/* Button arrow hover animation */
.btn-arrow {
  transform: translateX(0) !important;
  transition: transform 0.15s ease;
}

.btn-primary:hover .btn-arrow,
.btn-outline:hover .btn-arrow {
  transform: translateX(3px) !important;
}
</style>
```

### When to Use `!important`

Use `!important` in Code Embed CSS when:
- Overriding stuck inline styles left by Webflow interactions/animations
- Ensuring your CSS takes priority over Webflow's generated styles
- Working around specificity issues with Webflow's class-based system

### CSS Animation Example

```html
<style>
@keyframes marquee-scroll {
  from { transform: translateX(0); }
  to { transform: translateX(-50%); }
}

.marquee-track {
  animation: marquee-scroll 30s linear infinite;
}
</style>
```

## Code Embed Placement

- Place CSS Code Embeds at the **page level** (top of the body or inside a wrapper div) so styles apply globally
- Place SVG Code Embeds **inside the element** where the illustration should appear
- Place JavaScript Code Embeds at the **bottom of the page** (before closing body)

## Debugging Code Embeds

- Code Embed content is only visible in **Preview mode** or on the **published site**, not in the Designer canvas
- Use browser DevTools on the published site to inspect rendered output
- Check for unclosed tags, missing quotes, or HTML syntax errors
- CodeMirror in Webflow does NOT validate HTML — errors fail silently
