## Introduction

We have been thinking about webpages in two dimensions: horizontal (x) and vertical (y). Elements have a width, a height, and a position on the page. But the browser actually renders elements along a third axis too: depth into the screen, called the z-axis.

Most of the time this is invisible. Elements sit in the normal document flow, one after another, and there is no reason to think about depth at all. But the moment two positioned elements overlap the same area of the screen, the browser has to decide which one appears in front. That decision is controlled by `z-index`.

Think about opening a settings dialog on any website. The dialog appears on top of everything else. The rest of the page is still there behind a dark overlay, but you cannot interact with it. The dialog has been brought forward, visually and functionally, until you close it. That entire behavior is `z-index` at work.

### How the Browser Stacks Elements by Default

Without any `z-index` set, the browser stacks elements in the order they appear in the HTML. Elements that come later in the source code are painted on top of elements that come earlier.

```html
<div class="box-one">Box 1</div>
<div class="box-two">Box 2</div>
```

If these two elements overlap, Box 2 will appear on top of Box 1, because it comes later in the document.

<!-- 
IMAGE: Two overlapping rectangles. Box 1 is partially hidden behind Box 2. A label reads "Box 2 is later in the source, so it paints on top." Style: flat vector, dark background, two colored rectangles overlapping.
-->

This default ordering works for simple layouts, but real interfaces need more control. A dropdown menu written early in the HTML should still appear above the main content that comes after it. A modal dialog should appear above everything, regardless of where it sits in the source.

### The z-index Property

`z-index` sets an element's position along the z-axis. Higher values bring the element closer to the viewer (in front). Lower values push it further away (behind).

```css
.modal {
  z-index: 100;
}

.navbar {
  z-index: 50;
}

.page-content {
  z-index: 1;
}
```

With these values, the modal appears in front of the navbar, and the navbar appears in front of the page content, regardless of source order.

`z-index` values are just numbers. There is no universal scale. What matters is how they compare to each other within the same context. A `z-index: 2` beats a `z-index: 1`. A `z-index: 999` beats a `z-index: 998`. The numbers themselves are not special.

```css
/* A practical stacking scale for a typical interface */
.page-content  { z-index: 0;   }
.dropdown      { z-index: 10;  }
.navbar        { z-index: 50;  }
.overlay       { z-index: 100; }
.modal         { z-index: 200; }
.toast         { z-index: 300; }
```

Leaving gaps between values (10, 50, 100 rather than 1, 2, 3) is a practical habit. It gives room to insert new layers later without renumbering everything.

`z-index` also accepts negative values, which push an element behind the normal document flow.

```css
.background-decoration {
  z-index: -1;   /* renders behind everything else */
}
```

### The Prerequisite: z-index Only Works on Positioned Elements

This is the single most important rule about `z-index`, and the source of most confusion when it does not seem to work.

`z-index` only has an effect on elements with a `position` value other than `static`.

```css
/* This does nothing */
.my-element {
  z-index: 999;
  /* position: static (default) */
}

/* This works */
.my-element {
  position: relative;
  z-index: 999;
}
```

If `position` is still the default `static`, the `z-index` property is ignored entirely, no matter what number you give it. To use `z-index`, you must first set `position` to `relative`, `absolute`, `fixed`, or `sticky`.

In practice, the most common pattern is:

```css
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 50;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  z-index: 100;
}

.modal-box {
  position: fixed;
  top: 50%;
  left: 50%;
  z-index: 200;
}
```

The overlay sits above the navbar. The modal box sits above the overlay. The stacking order is explicit and predictable.

<!-- 
IMAGE: A layered diagram showing the browser z-axis as depth into the screen. Four layers visible from front to back: modal-box (z-index: 200) at the front, modal-overlay (z-index: 100) behind it, navbar (z-index: 50) behind that, and page-content (z-index: 0) at the back. Labels point to each layer with their z-index value. Caption: "z-index maps directly to visual depth. Higher values are closer to the viewer." Style: flat vector isometric or side-view stack diagram, dark UI aesthetic, each layer a different shade.
-->

### Where z-index Is Used in Practice

**Modals and dialogs**: A modal requires two layers: an overlay that dims everything behind it, and the dialog box itself that sits above the overlay. The overlay blocks interaction with the page. The dialog box is the only focusable surface.

```css
.modal-overlay {
  position: fixed;
  inset: 0;                          /* shorthand for top/right/bottom/left: 0 */
  background-color: rgba(0, 0, 0, 0.6);
  z-index: 100;
}

.modal-dialog {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);  /* centers the box on screen */
  z-index: 200;
}
```

**Dropdown menus**: A dropdown must appear above the content below the trigger button. Without `z-index`, the dropdown can slip behind adjacent cards or images.

```css
.dropdown-menu {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 10;
}
```

**Fixed navbars above page content**: A fixed navbar without `z-index` can be covered by a positioned card or image that has a higher default stacking order.

```css
.navbar {
  position: fixed;
  z-index: 50;
}
```

**Ecommerce overlays**: Product quick-view panels, promotional banners, size guide popups, and cart sidebars all use `position: fixed` with a high `z-index` to slide in above the current page without navigating away.

In all of these cases, the pattern is the same: the element that needs to appear on top gets a higher `z-index` than everything it needs to be in front of, and it must have a non-static position value for the `z-index` to take effect.

### Let's Define Things Now...

> **z-axis:** The third dimension in browser rendering, representing depth into the screen. Elements with higher z-axis values appear in front of elements with lower values.

> **z-index:** A CSS property that sets an element's position on the z-axis. Higher values bring the element in front. Only works on elements with a non-static `position` value.

> **Stacking Order:** The order in which overlapping elements are painted. Determined by source order by default, and overridden by `z-index` on positioned elements.

> **Overlay:** A semi-transparent fixed layer placed above all page content. Used to block interaction with the background while a modal or dialog is open.

### Activity: Building a Modal Dialog

Implement a full modal pattern on the chatbot page:

1. Create a `<div class="modal-overlay">` and a `<div class="modal-dialog">` inside it. Set the overlay to `position: fixed`, `inset: 0`, `background-color: rgba(0, 0, 0, 0.6)`, and `z-index: 100`. Set the dialog to `position: fixed`, `z-index: 200`.

2. Center the dialog box using `top: 50%`, `left: 50%`, and `transform: translate(-50%, -50%)`.

3. Give the fixed navbar from the previous activity a `z-index: 50`. Confirm the overlay appears above the navbar when the modal is visible.

4. Temporarily set the dialog's `z-index` lower than the overlay (for example, `z-index: 50`). Observe the dialog disappear behind the overlay, demonstrating the stacking order at work.

5. Remove the `position: fixed` from the overlay and keep `z-index: 100`. Observe that the `z-index` stops working entirely, confirming the positioning prerequisite.

## What's Coming Next...

That wraps up the core box model and positioning toolkit. We now know how every element is sized, spaced, and placed, and how they stack when they overlap.

Before we move into Flexbox and Grid, the two layout systems CSS provides for arranging groups of elements in rows, columns, and responsive patterns, we will take one article to look at some of the more visually expressive properties CSS offers. Things like shadows, transitions, opacity, and a few visual effects that are behind the polished look of modern interfaces. These are the properties that separate a page that is structurally correct from one that feels designed.
