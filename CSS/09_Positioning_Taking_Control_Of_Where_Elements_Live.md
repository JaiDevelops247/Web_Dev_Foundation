## Introduction

Every HTML page has a natural flow. Elements render in the order they are written in the document: top to bottom, each block element stacking beneath the previous one, each inline element following the one before it in the same line. This is called the normal document flow, and for most content — paragraphs, headings, images, lists — it is exactly what you want.

But not everything on a real webpage lives in this flow.

Think about the navigation bar on almost every website you have visited. As you scroll down through paragraphs of content, the navbar stays locked at the top of the viewport. It does not scroll away. It does not sit between the header and the first paragraph as the document flow would place it. It is pinned to the top of the screen, always visible, regardless of where you are in the page.

Now think about an AI chatbot widget. It sits in the bottom-right corner of the page, floating above all the other content, staying in that exact position no matter how long the page is or how far down you scroll. It is not between two sections of content. It does not push other elements around. It just floats there, always accessible.

Neither of these effects is possible with the normal document flow. They require a way to say to the browser: this element does not follow the usual rules. Put it here, and keep it here.

That is exactly what the `position` property does.

### The position Property

`position` changes how an element is placed in the layout and how the `top`, `right`, `bottom`, and `left` offset properties work on it.

```css
position: static;
position: relative;
position: absolute;
position: fixed;
position: sticky;
```

There are five values. Each one places the element differently and has distinct use cases in real products.

An important concept before we begin: when an element is positioned using `absolute`, `fixed`, or `sticky`, the `top`, `right`, `bottom`, and `left` properties control where it is placed. These are called offset properties, and they only work when a non-static position value is set.

```css
.element {
  position: absolute;
  top: 20px;     /* 20px from the top of its containing block */
  right: 16px;   /* 16px from the right */
}
```

Without a position value other than `static`, these offset properties have no effect at all.

### static: The Default

Every element starts with `position: static`. This means the element follows the normal document flow. It is placed where the document order puts it, and offset properties (`top`, `right`, `bottom`, `left`) have no effect.

```css
.message-bubble {
  position: static;   /* this is the default, writing it is redundant */
}
```

You will rarely write `position: static` explicitly. Its primary use is resetting a positioned element back to normal flow behavior when overriding a style from a parent or framework:

```css
/* A framework gave this element position: absolute — reset it */
.reset-position {
  position: static;
}
```

In industry, `static` is the invisible baseline. Everything that is not explicitly positioned is `static`.

### relative: Moving an Element Relative to Itself

`position: relative` does two things:

1. It keeps the element in the normal document flow.
2. It allows the element to be shifted from its natural position using the offset properties.

```css
.message-bubble {
  position: relative;
  top: 10px;    /* shift 10px down from where it would normally be */
  left: 20px;   /* shift 20px right from where it would normally be */
}
```

The critical behavior of `relative`: the space the element originally occupied is preserved. The layout around it does not change. The element visually moves, but its ghost stays in place.

<!-- 
IMAGE: A diagram showing three elements stacked vertically (Box 1, Box 2, Box 3). In the "Before" state, they are evenly stacked. In the "After" state, Box 2 has position: relative with top: 20px and left: 30px applied. Box 2 has visually shifted down and right, but the gap it left behind is still there — Box 3 has not moved up to fill it. Caption: "relative shifts the element visually but its original space remains reserved in the flow." Style: flat vector, two states side-by-side, clear annotation of the preserved space.
-->

```css
/* A subtle visual nudge — common for icon alignment */
.send-icon {
  position: relative;
  top: 2px;   /* nudge 2px down to align with text baseline */
}
```

In industry, `position: relative` is used for two main purposes:

**First**, for small visual adjustments — nudging an element a few pixels for fine alignment without disturbing the surrounding layout. Icon alignment next to text, badge positioning, decorative element placement.

**Second**, and more importantly: `position: relative` on a parent element creates a **positioning context** for any absolutely positioned children inside it. This is the more significant use, and it becomes clear in the next section.

### absolute: Removing an Element from the Flow

`position: absolute` removes the element from the normal document flow entirely. The element no longer occupies space in the layout — it is as if it does not exist to the surrounding elements, which close the gap it would have left.

The element is then placed at exact coordinates relative to its nearest ancestor that has a non-static position value. If no such ancestor exists, it is placed relative to the initial containing block (effectively the page itself).

```css
/* A notification badge on the chat header */
.unread-badge {
  position: absolute;
  top: -6px;
  right: -6px;
  width: 18px;
  height: 18px;
  background-color: #ef4444;
  border-radius: 50%;
}
```

For this badge to sit on the top-right corner of its parent button, the parent needs to be `position: relative`:

```css
.header-button {
  position: relative;   /* creates the positioning context */
}
```

Now `.unread-badge` with `top: -6px` and `right: -6px` positions itself relative to `.header-button`, not relative to the page.

<!-- 
IMAGE: A chat interface header showing a button (like a bell or chat icon). On the button's top-right corner sits a small red circular badge with a number. A callout annotation points to the button labeled "position: relative — creates the positioning context" and another points to the badge labeled "position: absolute — placed at top: -6px, right: -6px relative to the button." Caption: "absolute positioning is always relative to the nearest positioned ancestor." Style: flat vector, dark UI aesthetic, annotation lines clean and minimal.
-->

```css
/* A dropdown menu that appears below its trigger button */
.dropdown-menu {
  position: absolute;
  top: 100%;    /* exactly at the bottom edge of the parent */
  left: 0;
  width: 200px;
  background-color: #1e293b;
  border: 1px solid #334155;
  border-radius: 8px;
}

.dropdown-trigger {
  position: relative;   /* dropdown positions itself relative to this */
}
```

In industry, `position: absolute` is the backbone of a large set of UI patterns:

- Notification badges overlaid on icons
- Dropdown menus appearing below trigger buttons
- Tooltips appearing next to their target element
- Decorative elements layered over a card or banner
- Context menus that appear at the cursor position

The key advantage: absolute elements can be placed with pixel precision relative to a known parent, without disturbing the surrounding page layout in any way. The layout acts as if the absolutely positioned element is not there.

### fixed: Pinned to the Viewport

`position: fixed` removes the element from the document flow and pins it to the viewport — the visible area of the browser window. A fixed element does not move when the user scrolls. It stays locked at its declared position on screen.

```css
/* The navigation bar */
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background-color: #0f172a;
  border-bottom: 1px solid #1e293b;
  z-index: 100;
}
```

This navbar is pinned to the top of the viewport. As the user scrolls through the page content, the navbar stays at `top: 0` the entire time — always visible, always accessible.

```css
/* The floating chatbot widget in the bottom-right corner */
.chatbot-widget {
  position: fixed;
  bottom: 24px;
  right: 24px;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background-color: #3b82f6;
  cursor: pointer;
}
```

This chatbot button sits exactly 24px from the bottom and 24px from the right edge of the viewport. It stays there across every page, every scroll position, on every route. This is the exact technique used by every chatbot widget, cookie banner, floating action button, and live support button you have seen in the wild.

<!-- 
IMAGE: A full browser window mockup. At the top: a dark navbar with `position: fixed; top: 0` labeled with an annotation. The main content area shows a long scrolled page. At the bottom-right: a circular blue button (the chatbot widget) with `position: fixed; bottom: 24px; right: 24px` labeled. A faded scroll indicator on the right suggests the user has scrolled down. Caption: "fixed elements ignore the document flow and the scroll position entirely. They live in viewport coordinates." Style: flat vector browser mockup, dark UI aesthetic, clean annotations.
-->

In industry, `position: fixed` is used for:

- Navigation bars and top toolbars that stay visible as the page scrolls
- Floating action buttons (chatbot widgets, "back to top" buttons, help icons)
- Cookie consent banners at the top or bottom of the viewport
- Persistent sidebars in dashboard interfaces
- Progress bars or status indicators pinned to the top of the screen

The key advantage: the element exists in its own layer, completely independent of page scroll and document flow. Once placed, it stays there permanently.

One thing to be aware of: because a fixed element is no longer in the document flow, the content behind it is not pushed down. A fixed navbar at `top: 0` will overlap the first section of page content. The common fix is to add `padding-top` to the page body equal to the navbar height, so the first section of content starts below it.

```css
body {
  padding-top: 64px;   /* matches the navbar height */
}
```

### sticky: The Scroll-Aware Hybrid

`position: sticky` is the most nuanced of the five. It behaves like a relative element until the user scrolls to a specific threshold, at which point it becomes fixed — locking to the viewport until its parent scrolls out of view.

```css
/* A section heading that sticks to the top while its section is visible */
.section-header {
  position: sticky;
  top: 0;
  background-color: #0f172a;
  padding: 12px 0;
}
```

As the user scrolls down, `.section-header` moves with the document flow normally. The moment it would scroll off the top of the screen, it locks at `top: 0` and stays there — while the content beneath it continues to scroll. When the parent section scrolls out of view, the header unsticks and scrolls away with it.

<!-- 
IMAGE: A three-frame animation sequence showing a sticky header in action. Frame 1: the user is above the section, the section header is in normal document flow (labeled "scrolling normally — acts like position: relative"). Frame 2: the user has scrolled to the point where the header would leave the viewport — it is now pinned to the top (labeled "sticking — acts like position: fixed"). Frame 3: the user has scrolled past the section entirely, the header has scrolled away (labeled "unstuck — parent has left the viewport"). Caption: "sticky remembers where it came from. It sticks until its parent is gone." Style: flat vector, three panels showing scroll states, annotations pointing to the header position.
-->

A critical requirement: the `top` (or `bottom`) offset value is mandatory. Without it, `sticky` behaves exactly like `relative` and the sticking behavior never activates.

```css
/* sticky without top has no sticking effect */
.broken-sticky {
  position: sticky;
  /* missing: top: 0 — this element will never stick */
}
```

In industry, `position: sticky` is used for:

- Section labels in long scrollable lists (contact lists, settings menus, message group dates in chat applications)
- Table headers in long data tables that stay visible while rows scroll
- Sidebar navigation menus that stay in view as the main content scrolls
- "Now reading" indicators in long articles

The key advantage over `fixed`: a sticky element is still part of the document flow and is bounded by its parent container. It does not require manual padding adjustments to prevent content overlap. It sticks only while it is relevant, then naturally leaves the screen when its parent does.

### Choosing the Right Value

| Value | Stays in flow? | Scrolls with page? | Positioned relative to |
|-------|---------------|-------------------|------------------------|
| `static` | Yes | Yes | Normal flow |
| `relative` | Yes | Yes | Its own original position |
| `absolute` | No | Yes (unless ancestor is fixed) | Nearest positioned ancestor |
| `fixed` | No | No | Viewport |
| `sticky` | Yes (until threshold) | Until threshold, then no | Viewport (while stuck) |

When you need an element to stay in the layout but shift slightly: `relative`.

When you need an element to float over other content at a precise point inside a parent: `absolute`.

When you need an element to be permanently visible regardless of scroll: `fixed`.

When you need an element to scroll normally and then lock at a point while its section is visible: `sticky`.

### Let's Define Things Now...

> **position:** The CSS property that controls how an element is placed in the document layout and whether it responds to offset properties. Default is `static`.

> **Document Flow:** The default rendering order of HTML elements — top to bottom, block after block. Elements in normal flow affect the space around them.

> **Offset Properties:** `top`, `right`, `bottom`, and `left`. They specify the distance from the relevant edge. Only work on elements with a non-static position value.

> **Positioning Context:** A positioned ancestor (any element with a non-static position). Absolutely positioned descendants place themselves relative to their nearest positioning context.

> **static:** Default. Element is in normal document flow. Offset properties have no effect.

> **relative:** Element stays in normal flow but can be shifted using offset properties. Its original space is preserved. Creates a positioning context for children.

> **absolute:** Element is removed from normal flow. Placed at exact coordinates relative to the nearest positioned ancestor. Other elements ignore its space.

> **fixed:** Element is removed from normal flow and pinned to the viewport. Does not move on scroll. Placed relative to the browser window.

> **sticky:** Element behaves like `relative` until a scroll threshold is reached, then locks like `fixed` until its parent leaves the viewport. Requires a `top` or `bottom` offset value.

### Activity: Positioning the Chatbot Page

Implement the following on your chatbot page using each position value:

1. **Fixed navbar:** Wrap your navigation in a `<nav>` with `position: fixed`, `top: 0`, `left: 0`, `width: 100%`. Add `padding-top` to `<body>` equal to the nav height. Scroll the page and confirm the nav stays put.

2. **Fixed chatbot widget:** Create a circular `<button class="chatbot-widget">` with `position: fixed`, `bottom: 24px`, `right: 24px`. Scroll the page and confirm it stays in the corner.

3. **Absolute notification badge:** Add a small `<span class="badge">3</span>` inside a button in the navbar. Set the button to `position: relative` and the badge to `position: absolute` with `top: -6px` and `right: -6px`. Confirm the badge sits on the corner of the button.

4. **Relative nudge:** Find a small icon or label next to text that is slightly misaligned. Apply `position: relative` with a small `top` or `bottom` offset of 1–2px to align it precisely.

5. **Sticky section header:** In a long message list, add a date label (`<div class="date-label">Today</div>`) between messages with `position: sticky` and `top: 0`. Scroll through the messages and watch the date label stick while its group is visible.

After each step, open DevTools, click the element, and look at the Styles panel. Confirm the position value and offset properties are applied as expected.

## What's Coming Next...

We can now place elements exactly where we want them. But positioning introduces a new problem: what happens when two positioned elements occupy the same screen space?

Open any website with a fixed navbar and a dialog box or modal. When the modal opens, it appears on top of everything — on top of the navbar, on top of the content, on top of every other element. And the area behind the modal is not just visually covered — it is unreachable. You cannot click through to the elements behind it.

How does the browser decide which element appears in front? The answer is `z-index`, a property that controls the stacking order of positioned elements along the invisible third axis — depth into the screen. In the next article, we will look at how z-index works, why it only applies to positioned elements, and how to build the correct stacking structure for components like modals, dropdowns, tooltips, and overlays.
