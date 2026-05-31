## Introduction

Now let us go onto how we decide if the padding and border are going to be part of our box's height or width or not.

In the last article we established the box model: every element is a rectangular box composed of content, padding, border, and margin. We saw how adding padding gives an element breathing room, and how adding a border gives it a visible edge.

But we glossed over something important in the closing tease. When we set `width: 400px` on an element, what exactly is 400 pixels wide?

The answer to that question is controlled by a single CSS property called `box-sizing`. And the default answer is one that surprises almost every developer the first time they encounter it.

### box-sizing: Who Owns the Width?

`box-sizing` determines what the browser includes when it calculates the dimensions declared by `width` and `height`.

There are two possible values. Let us go through each one.

### content-box: The Default (and the Surprise)

The default value is `content-box`.

With `content-box`, the `width` and `height` properties apply to the content area only. Padding and border are added on top of that declared width.

Here is what that looks like in practice:

```css
.message-bubble {
  width: 400px;
  padding: 12px 16px;
  border: 2px solid #334155;
  box-sizing: content-box;   /* this is the default, even without writing it */
}
```

With `content-box`, the actual rendered width of this element is:

```
Content:  400px
Padding:  16px (left) + 16px (right) = 32px
Border:   2px (left) + 2px (right) = 4px
─────────────────────────────────────────
Total:    436px
```

The element ends up being 436 pixels wide even though you wrote `width: 400px`. The padding and border are added outside the declared width, expanding the element beyond what you specified.

<!-- 
IMAGE: A diagram showing a single element with width: 400px, padding: 12px 16px, and border: 2px solid. Two rows: the top row shows the element as the developer expects it (400px wide box labeled "I wrote width: 400px"). The bottom row shows the actual rendered element (436px wide) with labeled sections: 2px border, 16px padding, 400px content, 16px padding, 2px border, with a total bracket showing 436px. Caption: "content-box: the default. The width you write and the width you get are different numbers." Style: flat vector diagram, annotated with dimension labels, clear contrast between expected and actual.
-->

#### The Problem This Creates

The `content-box` default creates real problems when building layouts.

Imagine a two-column layout where each column should take up exactly 50% of the page width:

```css
.column {
  width: 50%;
  padding: 20px;
}
```

With `content-box`, each column is actually 50% + 40px wide (the padding adds 20px on each side). Two columns at 50% + 40px each do not fit side by side in a container that is 100% wide. One wraps to the next line and the layout breaks.

This becomes even more unpredictable when mixing percentage widths with pixel padding:

```css
.sidebar {
  width: 30%;
  padding: 24px;
  border: 1px solid #334155;
}
```

The final rendered width is now a mix of relative and absolute units: 30% of the parent plus 48px of padding plus 2px of border. Calculating and predicting this gets painful fast.

The deeper problem: the number you write in `width` is not the number that actually controls the space the element takes up. That mental disconnect makes layout code hard to reason about.

### border-box: What You Write Is What You Get

`border-box` fixes this.

With `border-box`, the declared `width` includes the content, padding, and border combined. The browser calculates the content area by subtracting the padding and border from the declared width.

```css
.message-bubble {
  width: 400px;
  padding: 12px 16px;
  border: 2px solid #334155;
  box-sizing: border-box;
}
```

Now the math works the way you expect:

```
Total rendered width:   400px (exactly what you wrote)
  Minus border:         4px (2px each side)
  Minus padding:        32px (16px each side)
  ──────────────────────────────────────────
  Remaining for content: 364px
```

The element is 400 pixels wide. Period. Padding and border carve into that space rather than expanding beyond it.

<!-- 
IMAGE: A side-by-side comparison of the same element under both box-sizing values. Left (content-box): a box labeled "width: 400px" with padding and border drawn outside it, total width shown as 436px. Right (border-box): a box labeled "width: 400px" with padding and border drawn inside it, total width shown as 400px. Caption: "border-box: the declared width is the rendered width. Padding and border live inside it." Style: flat vector diagram, same element, same properties, different box-sizing values, clear labeled comparison.
-->

The two-column layout from before now works exactly as intended:

```css
.column {
  width: 50%;
  padding: 20px;
  box-sizing: border-box;   /* padding is now inside the 50%, not added to it */
}
```

Two columns at `width: 50%` with `border-box` will always fit side by side regardless of how much padding is added. The width you declare is the width you get.

### The Universal Reset: Apply border-box to Everything

Because `border-box` is almost always what developers want, and because `content-box` is still the browser's default for historical reasons, the standard practice is to apply `border-box` globally at the start of every stylesheet using the universal selector:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

The `*` selector targets every element on the page. The `*::before` and `*::after` targets pseudo-elements created with those selectors (we will cover these in a later article).

This one block of CSS is one of the first things written in virtually every professional stylesheet. It resets the box-sizing behavior globally so that every element on the page uses `border-box` from the start. You never have to think about the `content-box` problem again after writing this.

```css
/* The very first thing in your stylesheet */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Everything below this behaves predictably */
.message-bubble {
  width: 400px;
  padding: 12px 16px;
  border: 2px solid #334155;
  /* No need to declare box-sizing: border-box here — it is inherited from the reset */
}
```

This is a foundation rule, not a hack or workaround. It corrects an ergonomic problem in CSS's default behavior and is considered universal best practice.

### display: Changing How Elements Behave

In the HTML module, we learned that HTML elements come in two fundamental behaviors: block elements like `<div>`, `<h1>`, and `<p>` that take up the full available width and start on a new line, and inline elements like `<span>`, `<a>`, and `<strong>` that sit within the flow of text.

But CSS allows us to change that default behavior. The `display` property controls how an element participates in the page's layout flow.

```css
display: value;
```

Let us go through the values.

#### block

`display: block` makes an element behave like a block element: it starts on its own line and stretches to fill the available width of its parent by default.

```css
/* Make links behave like block elements (useful in navigation) */
.nav-link {
  display: block;
  padding: 12px 16px;   /* Now padding affects the full block area */
}
```

An `<a>` tag is normally inline. Setting `display: block` changes it to a full-width block, which means padding and border work predictably on all four sides and the element creates its own line in the layout.

#### inline

`display: inline` makes an element behave like an inline element: it sits in the flow of text and only takes up as much space as its content requires.

```css
/* Treat a div as inline content */
.badge {
  display: inline;
}
```

Inline elements do not respect `width`, `height`, or vertical margins. They only flow with the surrounding text.

#### inline-block

`display: inline-block` is the hybrid. An element with `inline-block` sits in the text flow like an inline element, but it respects width, height, and full box model properties like a block element.

```css
.timestamp-badge {
  display: inline-block;
  padding: 4px 8px;
  background-color: rgba(30, 41, 59, 0.8);
  border-radius: 4px;
}
```

This is commonly used for buttons, tags, badges, and any element that needs box model properties but should sit inline with text or other elements.

<!-- 
IMAGE: A three-row diagram showing the same element styled three ways. Row 1 (block): a full-width dark rectangle spanning the container width, labeled "display: block — starts on its own line, full parent width." Row 2 (inline): the element sitting within a line of text, no space above/below it, labeled "display: inline — flows in text, no vertical space control." Row 3 (inline-block): the element sitting within a line of text but with visible padding and defined shape, labeled "display: inline-block — sits in flow, respects full box model." Style: flat vector, three rows, dark UI aesthetic.
-->

#### none

`display: none` removes the element from the layout entirely. It is as if the element does not exist on the page. The space it would have occupied collapses.

```css
.loading-spinner {
  display: none;   /* hidden by default */
}

.loading-spinner.active {
  display: block;  /* shown when .active class is added */
}
```

This is the primary technique for hiding and showing elements with CSS (and later, JavaScript toggling CSS classes). It is different from `visibility: hidden`, which hides the element visually but still preserves the space it occupies.

#### flex and grid

`display: flex` and `display: grid` turn an element into a flex container or a grid container, unlocking powerful layout capabilities for arranging the children inside it.

These two values are significant enough that they each get their own chapter. We will build an entire chatbot layout with Flexbox in Chapter 9 and explore Grid in Chapter 10. For now, just note that `display` is the property that activates them.

### overflow: When Content Outgrows Its Box

Here is a situation you will run into: you give an element a fixed height, but the content inside it grows beyond that height.

```css
.chat-window {
  height: 500px;
  background-color: #0f172a;
}
```

If enough messages fill the chat window, the messages will push outside the 500-pixel boundary and visually overflow into whatever is below the element on the page. The content spills out and the layout breaks.

The `overflow` property controls what the browser does when content is too large for its container.

```css
overflow: visible;  /* default: content spills outside the box */
overflow: hidden;   /* content is clipped at the box boundary */
overflow: scroll;   /* a scrollbar appears, always */
overflow: auto;     /* a scrollbar appears only when content overflows */
```

#### visible

The default. Content that exceeds the container's bounds is rendered anyway, painting outside the element's box. The element does not grow to accommodate the overflow and the surrounding layout is not disturbed — the overflowing content simply paints on top of whatever is around it.

```css
.tag {
  overflow: visible;   /* default behavior */
}
```

#### hidden

The overflowing content is clipped at the element's boundary. Anything outside the box is invisible and inaccessible.

```css
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;   /* clips the image to the circular boundary */
}
```

`overflow: hidden` is commonly used with `border-radius` to clip images and child elements to a rounded or circular shape.

#### scroll

The element shows a scrollbar regardless of whether the content actually overflows.

```css
.code-block {
  overflow: scroll;
}
```

The scrollbar is always rendered even if the content fits. In practice, this is rarely what you want because the scrollbar takes up space and appears even when unnecessary.

#### auto

The most useful value for containers that might receive variable amounts of content. A scrollbar appears only when the content actually overflows. When content fits, no scrollbar is shown.

```css
.chat-window {
  height: 500px;
  overflow: auto;   /* scroll only when messages fill the window */
}
```

This is the standard choice for containers with dynamic content like message lists, code editors, and sidebars.

<!-- 
IMAGE: A four-panel illustration showing the same chat window with overflow content under each value. Panel 1 (visible): messages spill below the container boundary onto the content below. Panel 2 (hidden): messages are clipped sharply at the container edge, nothing shows beyond it. Panel 3 (scroll): a scrollbar is present even though the container is half-empty. Panel 4 (auto): a scrollbar appears because messages exceed the height, but only because there is actual overflow. Caption: "overflow decides what happens when content runs out of room." Style: flat vector, four equal panels, dark chat window UI.
-->

You can also control the horizontal and vertical axes separately:

```css
.chat-window {
  overflow-x: hidden;   /* no horizontal scroll */
  overflow-y: auto;     /* vertical scroll when content overflows */
}
```

This combination is common for vertically scrollable panels that should never scroll sideways.

### Putting It Together: The Chat Window Foundation

Here is what a well-structured chat window looks like with everything from the last two articles applied:

```css
/* Foundation reset */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Chat container: predictable width, scrollable messages */
.chat-window {
  width: 700px;
  height: 600px;
  margin: 0 auto;
  background-color: #0f172a;
  border: 1px solid #1e293b;
  border-radius: 12px;
  overflow: hidden;   /* clips child elements to the rounded corners */
}

/* Message area: scrolls when full */
.message-list {
  height: 500px;
  overflow-y: auto;
  padding: 16px;
}

/* Input area: fixed height at the bottom */
.input-bar {
  display: block;
  width: 100%;
  padding: 12px 16px;
  border-top: 1px solid #1e293b;
  background-color: #0f172a;
}
```

The `box-sizing: border-box` reset means all `width` and `padding` values work exactly as written. The `overflow-y: auto` on the message list handles the scrolling without overflowing into the input bar. The `overflow: hidden` on the outer container clips the rounded corners cleanly.

### Let's Define Things Now...

> **box-sizing:** Determines what the declared `width` and `height` include. The default `content-box` adds padding and border on top of the declared dimensions. `border-box` includes padding and border within the declared dimensions.

> **content-box:** The default `box-sizing` value. `width` applies to the content area only. Padding and border are added outside, making the rendered element wider than declared.

> **border-box:** A `box-sizing` value where `width` includes the content, padding, and border. The rendered element is exactly the declared width.

> **Universal Reset:** A CSS rule using `* { box-sizing: border-box }` applied at the top of every stylesheet to ensure all elements use border-box sizing globally.

> **display:** The CSS property that controls how an element participates in the page layout. Common values: `block`, `inline`, `inline-block`, `none`, `flex`, `grid`.

> **inline-block:** A `display` value that makes an element sit in the text flow like an inline element while respecting width, height, and full box model properties like a block element.

> **overflow:** Controls what happens when an element's content is larger than its container. `visible` (default) lets it spill out. `hidden` clips it. `scroll` always shows a scrollbar. `auto` shows a scrollbar only when needed.

### Activity: Building a Contained Chat Window

Build a fixed-size chat window that behaves correctly under all the concepts in this article:

1. Start with a `<div class="chat-window">` at `width: 600px` and `height: 500px`. Add padding and a border. Open DevTools and compare the rendered width with and without the `box-sizing: border-box` reset. Observe the difference.

2. Add the universal reset (`*, *::before, *::after { box-sizing: border-box }`) and verify the rendered width now matches your declared width exactly.

3. Inside the chat window, add a `<div class="message-list">` with `height: 400px` and `overflow-y: auto`. Fill it with enough `<div class="message-bubble">` elements to overflow the container. Watch the scroll behavior activate.

4. Add `overflow: hidden` to the outer chat window. Add `border-radius: 16px`. Observe how overflow hidden clips the child elements to the rounded corners, preventing the content from painting outside the rounded boundary.

5. Create a `<span class="badge">` element. Set it to `display: inline-block` and add padding, background, and border-radius. Observe that it sits inline with surrounding text but respects its box model properties fully.

6. Change the badge to `display: inline` and observe that padding no longer pushes surrounding elements away vertically. Then change to `display: block` and observe it takes its own line.

Each step builds the mental model of how these properties interact. They are not independent rules: they work as a system.

## What's Coming Next...

That wraps up the core box model. We now know how every element is sized, how its internal space is structured, and what happens when its content runs out of room.

But there is something we have not answered yet: why is the navigation bar always pinned to the top of the page as you scroll? Why does a chatbot widget always sit in the bottom-right corner of every website, floating above all the other content? How do elements escape the normal document flow and go exactly where you tell them?

In the next article, we will cover positioning: the CSS mechanism that lets elements break out of the normal flow, stack on top of each other, stick to the viewport as the user scrolls, and appear in precise locations regardless of what surrounds them. It is the tool behind every fixed header, every floating button, and every layered UI component you have ever seen.
