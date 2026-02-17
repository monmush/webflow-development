# Webflow Development Gotchas

Complete reference of known issues, error messages, and workarounds.

## Style Tool Issues

### "Property didn't change" — Missing `pseudo: "noPseudo"`

**Problem:** When updating existing properties on the `main` breakpoint, the value silently fails to update.

**Cause:** The `style_tool` requires `pseudo: "noPseudo"` explicitly when modifying existing properties on `main`.

**Fix:** Always include `pseudo: "noPseudo"` when setting styles on `main`:
```
style_tool → set_styles:
  styleName: "my-element"
  breakpoint: "main"
  pseudo: "noPseudo"    # ← Required
  properties:
    font-size: "32px"
```

This is NOT required when setting styles on `medium`, `small`, `tiny`, or `xl` — only `main`.

### Overflow Shorthand Not Supported

**Problem:** Setting `overflow: hidden` fails or behaves unexpectedly.

**Fix:** Use the longhand properties:
```
properties:
  overflow-x: "hidden"
  overflow-y: "hidden"
```

### Variables in Style Properties

**Problem:** CSS custom property names (e.g., `var(--my-color)`) don't work.

**Fix:** Use Webflow variable IDs:
```
properties:
  color: "var(--variable-<your-variable-id>)"
```

Discover variable IDs with `variable_tool` → `get_variables`.

## Element Builder Issues

### Timeout with Empty Component String

**Problem:** `element_builder` with `component: ""` causes the request to hang/timeout.

**Fix:** Either pass a valid component ID or omit the `component` parameter entirely.

### TextBlock Creates a DivBlock

**Problem:** Creating a `TextBlock` type results in a DivBlock that doesn't support `set_text`.

**Fix:** Use `Heading` (with appropriate tag level) or `Paragraph` for text elements:
```
element_builder:
  type: "Heading"
  tag: "h2"
  parent: "<parent-id>"
  styleName: "section-title"
```

### Element Ordering

**Problem:** New elements appear at the wrong position within their parent.

**Fix:** Use the `position` parameter:
- `"inside-top"` — First child
- `"inside-bottom"` — Last child (default)
- `"before"` — Before the reference element
- `"after"` — After the reference element

When using `"before"` or `"after"`, the `parent` should be the sibling element ID, not the parent container.

## Designer State Issues

### "User does not have permission to modify styles"

**Problem:** Style tool calls return permission errors.

**Cause:** The Webflow Designer is in Preview mode (URL contains `workflow=sitePreview`).

**Fix:** Navigate back to Design mode — remove the `sitePreview` parameter from the URL or click the Designer view toggle.

### "Unable to connect to Webflow Designer"

**Problem:** MCP tools return connection errors even though the Designer is open.

**Cause:** The MCP Bridge Chrome extension lost its connection to the Designer.

**Fix:** Navigate to the designer URL with the `?app=` query parameter to force MCP Bridge reconnection. If that fails, refresh the page and wait for the Bridge indicator to show connected status.

### Designer Shows Wrong Page

**Problem:** MCP tools operate on a different page than expected.

**Fix:** Use `data_pages_tool` to list all pages and find the correct page ID, then navigate to that page in the designer.

## Content Issues

### `set_text` Fails Silently

**Problem:** `set_text` doesn't update the element's text.

**Cause:** The element is a `DivBlock` or `TextBlock` type, which don't support `set_text`.

**Fix:** Only use `set_text` on `Heading` and `Paragraph` elements. If the element needs to be a different type, delete and recreate it as the correct type.

### Special Characters in Text

**Problem:** Non-breaking spaces, em dashes, or other special characters don't render.

**Fix:** Use Unicode escape sequences:
- Non-breaking space: `\u00a0`
- Em dash: `\u2014`
- En dash: `\u2013`

### Rich Text in Paragraphs

**Problem:** Need bold/italic/links within a paragraph.

**Limitation:** `set_text` only sets plain text. For rich text:
- Create separate elements (bold as a `Heading` with appropriate weight)
- Or use a `RichText` element type if available
- Or use a Code Embed with formatted HTML

## Large Response Issues

### Element Tree Too Large

**Problem:** `element_tool` → `get_elements` returns 100K+ characters, exceeding response limits.

**Fix options:**
1. Use `element_tool` with a specific element ID to get only that subtree
2. Search for elements by style name instead of getting the full tree
3. Save the full response to a file and parse with Python/jq
4. Use `element_snapshot_tool` to visually identify elements, then select them specifically

## Inline Style Conflicts

### Stuck Transform/Translate Inline Styles

**Problem:** Elements have inline styles like `transform: translate3d(3px, 0, 0)` that override CSS hover/transition rules.

**Cause:** Webflow's interaction system or manual property changes can bake inline styles into the HTML.

**Fix:** Add a Code Embed with `!important` CSS rules:
```html
<style>
.affected-element {
  transform: translateX(0) !important;
}
.parent:hover .affected-element {
  transform: translateX(3px) !important;
}
</style>
```

`!important` in stylesheets overrides inline styles (as long as the inline style doesn't also have `!important`).

## Component Issues

### Component Instances Don't Reflect Updates

**Problem:** After updating the source component, instances on other pages still show old content.

**Fix:** Component style changes should propagate automatically. If they don't:
1. Check that you're editing the source component, not an instance
2. Refresh the page showing the instance
3. Republish the site

### Text Overrides on Component Instances

**Problem:** Can't change text on a component instance.

**Fix:** Component text elements must be marked as "text overridable" in the source component. Use `set_text` on the specific text element within the instance (find it via `element_tool`).

## Publishing Issues

### Changes Not Visible After Publishing

**Problem:** Published site doesn't reflect recent changes.

**Possible causes:**
1. Browser cache — try incognito/hard refresh
2. CDN cache — wait 1-2 minutes for propagation
3. Changes were on staging only — verify with `data_sites_tool` → `publish_site`
4. Code Embed content only renders in preview/published mode, not in Designer canvas
