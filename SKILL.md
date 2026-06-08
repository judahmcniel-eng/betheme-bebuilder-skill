---
name: betheme-bebuilder-builder
description: >-
  Provides guidelines, code patterns, and structural hacks for building robust layouts, HTML sections, tables, and scripts in WordPress using BeTheme's BeBuilder, avoiding sanitization issues and styling overrides.
---

# BeTheme BeBuilder Layout & Section Builder

## Overview
This skill provides comprehensive instructions, code templates, and design hacks for building custom layout sections, styling components, and publishing pages via the WordPress REST API for BeTheme and its page builder, **BeBuilder**.

---

## 1. BeBuilder Container Hierarchy & Targeting

### Container Hierarchy
When outlining or constructing layout structures, you must strictly adhere to BeTheme's native layout model:
1. **Section**: The top-level wrapper. Controls section backgrounds, full-width/boxed layout, padding, and divider styling.
2. **Wrap**: The sub-container within a section. Used to define columns (e.g., `1/2 + 1/2`, `1/3 + 1/3 + 1/3`).
3. **Element**: The actual widget inside the wrap. For custom styling, always use the **HTML** or **Text** element.

```
┌────────────────────────────────────────────────────────┐
│                        SECTION                         │
│  ┌──────────────────────────────────────────────────┐  │
│  │                       WRAP                       │  │
│  │  ┌───────────────┐            ┌───────────────┐  │  │
│  │  │    ELEMENT    │            │    ELEMENT    │  │  │
│  │  │ (Custom HTML) │            │ (Text Block)  │  │  │
│  │  └───────────────┘            └───────────────┘  │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

### Targeting & Scoping
To apply custom CSS rules or target elements with JavaScript:
- **IDs & Classes**: Assign highly specific IDs (e.g., `id="custom-dashboard-summary"`) or classes (e.g., `class="custom-metrics-card"`) to avoid conflicts.
- **User Instructions**: Explicitly instruct the user to configure these in the BeBuilder interface:
  > **To Apply Custom IDs/Classes in BeBuilder:**
  > 1. Click on the Section, Wrap, or Element in the editor.
  > 2. Open the **Advanced** tab in the sidebar.
  > 3. Click on the **Custom** settings sub-tab.
  > 4. Input the provided CSS ID or CSS Class.

---

## 2. Bypassing WordPress Content Sanitization

### Inline CSS (Preferred)
WordPress aggressively sanitizes page content and will silently strip out `<style>` tags from elements on save.
- **Rule**: Apply all styling directly via inline `style="..."` attributes on every HTML element.
- **Exception**: For responsive layouts, hover states, or pseudo-elements (e.g., `:hover`, `::after`), you must instruct the user to paste custom CSS into one of the designated areas:
  - **Page-specific CSS**: BeBuilder > Page Options > Custom CSS
  - **Global CSS**: WP Admin > Betheme > Theme options > Custom CSS & JS > CSS

> [!IMPORTANT]
> If custom CSS is placed in the Theme Options or Page Options, prefix all selectors with your unique wrapper ID or class to prevent styling leakage:
> ```css
> /* Example of scoped hover styling */
> #custom-dashboard-summary .custom-metrics-card:hover {
>     transform: translateY(-5px);
>     box-shadow: 0 8px 20px rgba(0,0,0,0.15);
> }
> ```

### SVG and Script Tags Banned
WordPress content filters block inline `<svg>` and `<script>` elements.
- **SVG Workaround**: Upload the SVG to the WordPress Media Library. Retrieve its URL and use a standard `<img>` tag:
  ```html
  <img src="https://example.com/wp-content/uploads/my-icon.svg" style="width: 48px; height: 48px;" alt="Metric Icon" />
  ```
  *Alternatively, construct native CSS shapes using borders, gradients, and flexbox alignment.*
- **Script Workaround**: Paste custom JS under `WP Admin > Betheme > Theme options > Custom CSS & JS > JS`.
- **Scoping Global JS**: Because code pasted here executes globally, you must scope the code by checking for the target element's presence on the current page:
  ```javascript
  (function() {
      document.addEventListener('DOMContentLoaded', function() {
          var target = document.getElementById('custom-dashboard-summary');
          if (!target) return; // Exit silently if not on the page

          // Execute custom logic safely
          console.log('Dashboard summary initialized.');
      });
  })();
  ```

### Gutenberg Block Comments
When generating raw custom HTML for pasting into a standard WordPress Gutenberg block (rather than BeBuilder), wrap the entire structure in `<!-- wp:html -->` block comments:
```html
<!-- wp:html -->
<div style="display: flex; gap: 15px; padding: 20px; background: #ffffff;">
    <p>Custom Gutenberg HTML block...</p>
</div>
<!-- /wp:html -->
```

---

## 3. BeTheme Structural Hacks (Dos and Don'ts)

### Table Overrides (CSS Table Layout)
BeTheme's global stylesheet overrides table styles, stripping padding and borders from `<table>`, `<tr>`, and `<td>` elements.
- **Rule**: Avoid standard HTML table tags. Use `div` elements with `display: table-cell` styling instead.
- **Headers**: Style headers directly on the row-representing `div` container.

```html
<!-- Div-based Table Mockup -->
<div style="display: table; width: 100%; border-collapse: collapse; margin: 20px 0;">
    <!-- Table Header Row -->
    <div style="display: table-row; background-color: #0073aa; color: #ffffff; font-weight: 600;">
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd;">Metric</div>
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd;">Value</div>
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd;">Status</div>
    </div>
    <!-- Table Body Row -->
    <div style="display: table-row; background-color: #f9f9f9;">
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd;">Conversion Rate</div>
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd;">3.45%</div>
        <div style="display: table-cell; padding: 12px; border: 1px solid #dddddd; color: #4caf50;">Good</div>
    </div>
</div>
```

### Alignment and Spacing
- **No `&nbsp;` or Box-Drawing Characters**: BeTheme fonts (and browser defaults) render whitespace and box-drawing elements (e.g., `┌`, `─`) at variable, non-uniform widths.
- **Rule**: Rely on CSS flexbox or grid for all positioning, margins, and layout alignment:
  ```html
  <div style="display: flex; align-items: center; justify-content: space-between; gap: 16px;">
      <div>Left-aligned element</div>
      <div>Right-aligned element</div>
  </div>
  ```

### Vertical Connector Lines
For diagram connections, trees, or timelines:
- **Don't**: Apply `border-top` or `border-bottom` borders on table cells.
- **Do**: Use a parent container with `position: relative` and an absolutely positioned child container as the line:
  ```html
  <!-- Vertical connector line container -->
  <div style="position: relative; padding-left: 24px; min-height: 50px;">
      <!-- Absolute Line -->
      <div style="position: absolute; left: 0; top: 0; bottom: 0; width: 2px; background-color: #cccccc;"></div>
      
      <!-- Content Block -->
      <div style="font-size: 14px; color: #333333;">
          Timeline point content...
      </div>
  </div>
  ```

---

## 4. WordPress MCP Connector Publishing Rules

When using WordPress MCP connector tools, follow this strict publishing flow to prevent data corruption and duplicate resources:

### 1. Pre-Publishing Checks
Before creating tags or categories, always list existing ones to check for duplicates:
- Call `categories.list` and `tags.list` and inspect the list.
- Only call `categories.create` or `tags.create` if no matching taxonomy term exists. Use the existing term ID otherwise.

### 2. Lowercase Parameters
Ensure all query parameter values in MCP API calls are in lowercase to prevent API-level validation errors:
- Use `"order": "desc"` instead of `"order": "DESC"`.
- Use `"status": "draft"` instead of `"status": "Draft"`.

### 3. Verification Workflow
- Always set the post/page status to `"status": "draft"` on initial insertion.
- Retrieve the `preview_link` or `link` from the API response payload.
- Return this preview URL directly to the user so they can manually check visual rendering and BeBuilder compatibility.
- Perform updates (`"status": "publish"`) only after successful user review.

---

## Common Mistakes

- **Writing `<style>` blocks**: Under BeBuilder/WordPress content sanitization, `<style>` blocks will save initially but disappear on subsequent edits or saves. Stick strictly to inline styles and Page/Theme options.
- **Pasting raw scripts in HTML elements**: Setting inline events (`onclick="..."`) or scripts inside BeBuilder elements will fail to execute or get deleted. Use the Theme Options JS manager and query target nodes instead.
- **Using standard HTML tables**: Global table selectors in BeTheme will override borders, font sizes, background colors, and cell padding. Div-based tables are mandatory for predictable layouts.
- **Publishing immediately**: Generating and immediately publishing a page/post can lead to broken layouts on live pages. Always use draft state first.
