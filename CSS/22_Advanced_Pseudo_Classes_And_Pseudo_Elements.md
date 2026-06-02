## Introduction

We have covered all the foundational concepts of a well-styled, well-laid-out, responsive webpage. At this point you can structure a page, style its elements, lay them out with Flexbox and Grid, and make the whole thing adapt to any screen size.

But open any modern AI product: Notion, Linear, Vercel, or Anthropic's own website. There is something beyond layout and color. Buttons highlight precisely when the cursor arrives. Form fields change their border color based on whether the input is valid. Every other row in a data table has a subtle alternate background. Quote blocks have decorative marks that exist nowhere in the HTML. Text selected by the user is highlighted in the brand's color rather than the default blue.

<!-- 
IMAGE: A collage of four UI details from modern AI product websites. Top-left: a text input field with a glowing colored border when it is valid. Top-right: a settings toggle switch with a custom checked state style. Bottom-left: an alternating-row table where odd rows have a subtle background tint. Bottom-right: a blockquote with a large decorative quotation mark that is purely a CSS element, not in the HTML. Caption: "Details that make an interface feel designed: valid input states, custom checkboxes, zebra tables, decorative elements. All built with pseudo-classes and pseudo-elements." Style: four-panel screenshot collage or high-fidelity UI mockups, dark themed.
-->

None of these effects require JavaScript. None require extra HTML markup. They are all CSS, built using two powerful systems: **pseudo-classes** (targeting elements based on state or position) and **pseudo-elements** (inserting virtual content into the DOM through CSS).

We covered the basic pseudo-classes (`:hover`, `:focus`, `:active`) in the selectors article. This article goes further into the ones that real interfaces depend on.

### Structural Pseudo-classes

Structural pseudo-classes target elements based on their position among their siblings in the document tree.

#### :first-child and :last-child

`:first-child` targets an element that is the first child of its parent. `:last-child` targets the last child.

```css
/* Remove the top border from the first item in a list */
.message-list .message:first-child {
  border-top: none;
}

/* Remove the bottom border from the last item */
.message-list .message:last-child {
  border-bottom: none;
}
```

<!-- 
IMAGE: A vertical list of message items, each separated by a thin border line. The first item has no top border and the last item has no bottom border, so the list floats cleanly within its container without border lines pressing against the edges. Caption: ":first-child and :last-child remove the outer borders, making the list look contained rather than cut off." Style: flat vector, dark card with a vertical list of items, clean edge treatment.
-->

**Real website use:** Every navigation sidebar with list items separated by dividers removes the divider from the first or last item using these selectors. Settings menus, dropdown lists, and chat message groups all use this pattern.

#### :nth-child()

`:nth-child()` is the most powerful structural selector. It targets elements based on a mathematical formula applied to their position among siblings.

```css
/* Every second row (2, 4, 6, ...) */
.data-table tr:nth-child(even) {
  background-color: rgba(255, 255, 255, 0.03);
}

/* Every odd row (1, 3, 5, ...) — same as :nth-child(odd) */
.data-table tr:nth-child(2n+1) {
  background-color: rgba(255, 255, 255, 0.05);
}
```

The formula notation `An+B` where `A` is the interval and `B` is the offset:

```css
:nth-child(2n)    /* every 2nd: 2, 4, 6, 8 ... — same as "even" */
:nth-child(2n+1)  /* every 2nd starting at 1: 1, 3, 5 ... — same as "odd" */
:nth-child(3n)    /* every 3rd: 3, 6, 9, 12 ... */
:nth-child(3n+2)  /* every 3rd starting at 2: 2, 5, 8, 11 ... */
:nth-child(4)     /* only the 4th element */
:nth-child(-n+3)  /* first 3 elements: 3, 2, 1 (n goes 0, -1, -2, ...) */
```

<!-- 
IMAGE: A table with 8 rows. The even-numbered rows (2, 4, 6, 8) have a subtly different background tint from the odd rows. Row numbers are labeled on the left. Caption: ":nth-child(even) creates the zebra-stripe pattern. A classic readability technique for data tables." Style: flat vector, dark themed data table, alternating subtle background tints on even rows.
-->

**Real website use:** Linear and Notion use alternating row backgrounds on their task and page list views. Analytics dashboards universally use zebra striping on data tables.

<!-- 
IMAGE: A screenshot reference of a project management tool (like Linear or Notion) showing a task list with alternating row backgrounds. Caption: "Alternating row backgrounds in Linear's issue list — a direct application of :nth-child(even)." Style: actual screenshot or high-fidelity mockup of a dark-themed task management interface.
-->

#### :only-child

`:only-child` targets an element that is the only child of its parent. Useful for showing a different style when there is just one item in a list.

```css
/* If there is only one message, center it differently */
.message-list .message:only-child {
  margin: 0 auto;
  text-align: center;
}
```

#### :not()

`:not()` targets elements that do not match a given selector. It accepts any valid selector as its argument.

```css
/* Style all buttons except the one with class .primary */
.button:not(.primary) {
  background-color: transparent;
  border: 1px solid #334155;
}

/* Style all list items except the last one */
.nav-item:not(:last-child) {
  margin-right: 24px;
}

/* Remove border from all inputs except disabled ones */
input:not(:disabled) {
  border: 1px solid #334155;
}
```

<!-- 
IMAGE: A row of four buttons: three ghost-style buttons (transparent background, border) and one solid primary button (filled background). The CSS rule shown: .button:not(.primary) with the ghost style applied. Caption: ":not(.primary) styles every button except the designated primary action." Style: flat vector, dark button row, three ghost buttons and one filled primary button clearly differentiated.
-->

**Real website use:** Stripe's dashboard styles all form controls except disabled ones with an active border. Ghost buttons on hero sections are almost always styled using `:not(.primary)` to avoid overriding the main call-to-action.

### Form-State Pseudo-classes

These pseudo-classes target form elements based on their current state: whether they are checked, disabled, valid, or focused.

#### :checked

`:checked` targets checkboxes and radio buttons that are in their checked state. Since browsers render checkboxes with default OS styles that are difficult to customize, `:checked` combined with a hidden native input and a custom sibling element is the standard technique for custom checkbox design.

```css
/* Hide the native checkbox */
.custom-checkbox input[type="checkbox"] {
  position: absolute;
  opacity: 0;
}

/* The visible custom box */
.custom-checkbox .box {
  width: 18px;
  height: 18px;
  border: 2px solid #334155;
  border-radius: 4px;
  background-color: transparent;
}

/* When checked: fill the box with the brand color */
.custom-checkbox input[type="checkbox"]:checked + .box {
  background-color: #3b82f6;
  border-color: #3b82f6;
}
```

<!-- 
IMAGE: Two rows of custom checkboxes. Top row: three unchecked checkboxes — dark bordered squares with no fill. Bottom row: three checked checkboxes — filled blue squares. The transition from unchecked to checked state shows the :checked selector in action. Caption: ":checked enables fully custom checkbox styling without JavaScript." Style: flat vector, dark themed form, custom checkboxes in both states clearly visible.
-->

**Real website use:** Every product with custom-designed forms: Notion's task checkboxes, Linear's issue filters, Vercel's settings toggles. All custom checkbox UIs are built on `:checked`.

<!-- 
IMAGE: A screenshot reference from Notion or Linear showing their custom-styled checkboxes in a task list. Caption: "Notion's task checkboxes are custom-styled using :checked. The native input is hidden; the visual checkbox is a CSS-controlled sibling." Style: actual screenshot or close mockup.
-->

#### :disabled

`:disabled` targets form elements with the `disabled` attribute. It is used to visually distinguish interactive controls from ones that are not currently available.

```css
.button:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

input:disabled {
  background-color: #0f172a;
  color: #475569;
  border-color: #1e293b;
}
```

#### :placeholder-shown

`:placeholder-shown` targets an input that is currently displaying its placeholder text, meaning the user has not yet typed anything into it.

```css
/* Show a label differently when the field is empty vs filled */
.input-label {
  transform: translateY(0);
  font-size: 0.875rem;
}

input:not(:placeholder-shown) + .input-label {
  transform: translateY(-24px);
  font-size: 0.75rem;
}
```

This is the technique behind floating labels: form labels that sit inside the input field as placeholder text, then float up above the field when the user starts typing.

#### :valid and :invalid

`:valid` and `:invalid` target form inputs based on whether their current value passes the browser's built-in validation rules (required fields, email format, min/max values, pattern matching).

```css
/* Green border when the input is valid */
input:valid {
  border-color: #22c55e;
}

/* Red border when the input has content but is invalid */
input:invalid:not(:placeholder-shown) {
  border-color: #ef4444;
}
```

The `:not(:placeholder-shown)` combination is important. Without it, empty required fields show as `:invalid` immediately on page load before the user has interacted with them, which is visually confusing.

<!-- 
IMAGE: A contact form with two input fields. The first (email) has a green border and a small checkmark icon — it is valid. The second (phone) has a red border and a small warning icon — the user has entered content but it does not match the expected format. Caption: ":valid and :invalid provide immediate visual feedback without any JavaScript." Style: flat vector, dark form fields with colored border feedback states.
-->

**Real website use:** Stripe's payment form shows green validation on the card number and expiry fields as the user types. Every well-designed form that provides inline validation without a form submission uses `:valid` and `:invalid`.

#### :focus-visible

`:focus` applies when an element receives focus (click, tab, or programmatic). The problem with `:focus`: it shows the focus ring on mouse clicks too, which feels like a visual glitch to mouse users.

`:focus-visible` shows the focus ring only when the browser determines the user is navigating by keyboard (tab key). Mouse clicks do not trigger `:focus-visible`.

```css
/* Remove the default focus ring for mouse users */
button:focus {
  outline: none;
}

/* Show a clear, branded focus ring for keyboard users */
button:focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 3px;
}
```

<!-- 
IMAGE: Two button states. Left: the same button focused by a mouse click — no visible focus ring. Right: the same button focused by pressing Tab — a clearly visible blue outline around the button. Caption: ":focus-visible shows the focus indicator only to keyboard users, not mouse users." Style: flat vector, two identical dark buttons, one with no visible ring and one with a clear blue outline ring.
-->

**Real website use:** Every accessibility-conscious product site (Stripe, GitHub, Vercel) uses `:focus-visible` instead of `:focus` for interactive elements. Removing focus rings entirely is an accessibility failure. `:focus-visible` is the correct balance.

### Pseudo-elements

Pseudo-elements are different from pseudo-classes. A pseudo-class selects an existing element in a particular state. A pseudo-element creates a virtual element that does not exist in the HTML and styles it through CSS alone.

Pseudo-elements use the double-colon prefix `::` (though single-colon still works in most browsers for legacy reasons).

#### ::before and ::after

`::before` inserts a virtual element as the first child of the selected element. `::after` inserts one as the last child. Both require the `content` property to render, even if it is an empty string.

```css
.section-heading::before {
  content: "";           /* required, even when empty */
  display: inline-block;
  width: 4px;
  height: 1em;
  background-color: #3b82f6;
  margin-right: 12px;
  border-radius: 2px;
  vertical-align: middle;
}
```

<!-- 
IMAGE: A section heading with a small vertical blue bar to its left. The heading text sits to the right of the bar. A callout annotation points to the bar: "This element exists only in CSS via ::before. There is no span or div in the HTML." Caption: "::before inserts a virtual first child. The accent bar is a pure CSS element." Style: flat vector, dark background, heading with the colored accent bar labeled.
-->

`content` can also be a string, inserting text into the virtual element:

```css
blockquote::before {
  content: "\201C";   /* Unicode for opening double quote " */
  font-size: 4rem;
  color: #3b82f6;
  line-height: 0;
  display: block;
  margin-bottom: 8px;
}
```

<!-- 
IMAGE: A blockquote element on a dark background. A large decorative opening quotation mark appears above the quoted text. The quotation mark is clearly a visual embellishment, not part of the actual text. A callout notes: "Generated by ::before with content: '\201C'." Caption: "Decorative content without HTML markup. ::before adds the quotation mark purely through CSS." Style: flat vector, dark card with a large decorative quotation mark above indented text.
-->

**Real website use:** Linear uses `::before` to add the dot indicators next to issue status labels. Notion uses `::after` for tooltip arrows on hoverable elements. The decorative accent lines next to section headings on marketing sites are almost always `::before` elements.

<!-- 
IMAGE: A screenshot reference from a modern product site (Stripe or similar) showing section headings with small accent bars or decorative marks clearly generated by CSS, with no extra HTML markup visible in the source. Caption: "Decorative accents on headings across modern product sites are ::before or ::after elements." Style: actual screenshot or faithful mockup.
-->

#### ::placeholder

`::placeholder` styles the placeholder text inside input fields.

```css
input::placeholder {
  color: #475569;
  font-style: italic;
  font-size: 0.875rem;
}
```

The native placeholder text color is a medium gray by default. `::placeholder` lets it match the design's muted text color, which on dark themes is often a lighter or differently styled gray.

#### ::selection

`::selection` styles the text that the user highlights by clicking and dragging. The default selection color is blue (or the OS accent color). On a dark-themed interface with a blue brand color, changing this to the brand color creates a cohesive experience.

```css
::selection {
  background-color: rgba(59, 130, 246, 0.3);
  color: #f1f5f9;
}
```

<!-- 
IMAGE: A text block where some text has been selected (highlighted) by the user. Instead of the default blue browser highlight, the selection uses a brand-colored highlight matching the site's accent color. Caption: "::selection replaces the browser's default text highlight with a brand-matching color. A small detail that feels polished." Style: flat vector, dark background with text, brand-colored selection highlight visible on a portion of the text.
-->

**Real website use:** Stripe's website uses a purple selection color that matches their brand. The Vercel website uses a white selection on dark backgrounds. Any branded website that has noticed and fixed their selection color has used `::selection`.

#### ::first-line and ::first-letter

`::first-line` styles only the first rendered line of a block of text, regardless of where it wraps. `::first-letter` styles only the first character.

```css
/* Slightly larger and bolder first line of an article */
.article-intro::first-line {
  font-weight: 600;
  letter-spacing: 0.02em;
}

/* Drop cap on the first letter */
.article-body::first-letter {
  font-size: 3rem;
  font-weight: 700;
  float: left;
  line-height: 1;
  margin-right: 8px;
}
```

<!-- 
IMAGE: An article text block showing two typographic treatments. Top: the first line of a paragraph is visibly bolder than the rest. Bottom: the first letter of a paragraph is a large decorative drop cap that sits alongside the first few lines of text. Caption: "::first-line and ::first-letter target specific typographic positions without wrapping elements in the HTML." Style: flat vector, dark background, editorial text layout with visible typographic effects.
-->

**Real website use:** Editorial content sites and long-form writing platforms (Substack, Medium) use `::first-letter` for drop caps on feature articles. The technique is a direct CSS equivalent of traditional print typography.

### Let's Define Things Now...

> **Structural Pseudo-class:** A pseudo-class that targets elements based on their position in the document tree relative to their siblings. Examples: `:first-child`, `:last-child`, `:nth-child()`, `:not()`.

> **:nth-child(An+B):** Targets elements at positions defined by the formula. `A` is the interval, `B` is the starting offset. `even`, `odd`, `3n`, `3n+2` are common expressions.

> **:not():** Targets elements that do not match the selector inside the parentheses. Accepts any valid selector.

> **Form-state Pseudo-class:** A pseudo-class that targets form elements based on their interactive state. Examples: `:checked`, `:disabled`, `:valid`, `:invalid`, `:placeholder-shown`.

> **:focus-visible:** Applies focus styles only when the browser determines keyboard navigation is being used. Allows focus rings for accessibility without showing them on mouse clicks.

> **Pseudo-element:** A virtual element created by CSS using the `::` prefix. Does not exist in the HTML. `::before` and `::after` are the most used and require the `content` property to render.

> **content property:** Required on `::before` and `::after` pseudo-elements. Can be an empty string, a text string, or a Unicode character. The pseudo-element does not render without it.

> **::selection:** Styles the text highlighted by the user. Most commonly used to replace the browser's default selection color with a brand color.

### Activity: Pseudo-class and Pseudo-element Practice

1. Build a settings form with checkboxes. Hide the native checkboxes using `opacity: 0` and `position: absolute`. Create custom checkbox boxes as sibling elements and style their `:checked` state using the adjacent sibling combinator (`+`).

2. Add an email input to the form. Apply `:valid` (green border) and `:invalid:not(:placeholder-shown)` (red border). Type a valid email and an invalid one and observe the border switching.

3. Add a submit button. Apply `button:focus { outline: none }` and `button:focus-visible { outline: 2px solid #3b82f6 }`. Click the button with the mouse and confirm no ring appears. Press Tab to focus it and confirm the ring appears.

4. Create a table with 8 rows. Apply `:nth-child(even)` to give alternate rows a subtle different background. Try changing `even` to `3n+1` to see a different pattern.

5. Add a `blockquote` to the page. Use `::before` with `content: "\201C"` and a large font size to create a decorative quotation mark. Confirm nothing was added to the HTML.

6. Apply `::selection` to the page with a brand color background and white text. Select some text with the mouse and observe the custom highlight color.

## What's Coming Next...

Structural positions, form states, keyboard focus, decorative virtual elements — these small details are what separate interfaces that feel assembled from interfaces that feel designed. A custom checkbox, a glowing valid state, a decorative accent line, a branded selection color: none of these are structurally necessary but all of them make the user feel like the product was built with care.

The next layer of that crafted feel is motion. Buttons that smoothly transition their color on hover. Elements that slide into view. Icons that rotate when a menu opens. These effects are not about decoration, they are about giving the user feedback: communicating that an interaction happened, that a state changed, that the interface is responding.

In the next article, we will cover CSS transitions and transforms — the tools behind every smooth state change and every subtle movement in a well-built interface.
