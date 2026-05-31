## Introduction

Open any browser and load a blank HTML page with nothing but a heading and a paragraph. The browser renders them. The heading is large, the paragraph is smaller, both in black on a white background. You did not write a single line of CSS, and yet they have a size.

Every browser ships with a built-in stylesheet called the user agent stylesheet. It gives HTML elements their default visual appearance: headings get progressively smaller sizes from `<h1>` down to `<h6>`, paragraphs get a standard text size, links get an underline and a blue color. These defaults exist so that a page with no CSS at all is still readable.

But visit any real website and the first thing you notice is that nothing looks like those defaults. The heading on a product landing page might be enormous and bold, filling a third of the screen. The paragraph text on a documentation site might be smaller and tighter than the browser default. A navigation bar might stretch the full width of the viewport. A sidebar might be exactly 280 pixels wide. A hero section might fill the entire screen height precisely.

None of those sizes come from the browser. A developer defined them. And in CSS, every size is expressed as a number followed by a unit. The unit is what gives the number its meaning: 16 of what? 50 of what? 100 of what?

That question is what this article answers.

### The Properties We Will Use to Understand Units

To keep things grounded, we will learn units through two concrete CSS properties that appear in nearly every stylesheet.

`font-size` controls how large text is rendered. Every browser has a default font size, typically 16 pixels for body text. Headings are larger multiples of that. CSS lets us override both.

`width` controls how wide an element is. Block elements like `<div>` and `<p>` take the full width of their parent by default. CSS lets us constrain that to any size we choose.

These two properties touch almost every unit concept we will cover. Once you understand units through them, applying the same logic to height, padding, margin, and everything else follows naturally.

### What Is a CSS Unit?

A CSS unit is the measurement system attached to a numeric value. Just as you cannot say a room is "5 large" without specifying whether you mean meters, feet, or something else, you cannot say an element is "5" without a unit. The unit defines the scale of measurement.

```css
h1 {
  font-size: 32px;   /* 32 pixels */
  width: 50%;        /* 50% of the parent's width */
}
```

All CSS units fall into two categories: absolute units and relative units. The difference between them is the most important idea in this article.

### Absolute Units

An absolute unit has a fixed, physical meaning. It does not change based on context, screen size, or any other element on the page. A measurement of 100px means the same thing everywhere, on every device, in every context.

#### px (Pixels)

The pixel is the only absolute unit you will use regularly in web CSS. One CSS pixel is a device-independent unit, roughly corresponding to what the eye perceives as a pixel on a standard-density screen. On high-density screens (like Retina displays), the browser handles the scaling automatically.

```css
h1 {
  font-size: 32px;
}

.sidebar {
  width: 280px;
}

.message-bubble {
  border-radius: 12px;
  border: 1px solid #334155;
}
```

Pixels are precise and predictable. When you say 280px, you get exactly 280 pixels regardless of what is around it. This makes pixels the right choice for:

- Borders and outlines (a 1px border is always exactly one pixel thin)
- Box shadows
- Border radius values where you want a specific corner shape
- Fine measurements where the exact size matters and should not vary

The limitation of pixels is that they are inflexible. A font size of 16px does not adapt if the user has set their browser's base font size to 20px for accessibility reasons. Pixels ignore user preferences. For typography and most spacing, this is where relative units do better.

Other absolute units like `cm`, `mm`, `in`, and `pt` exist in CSS but are intended for print stylesheets, not screen design. You will almost never encounter them in web development.

### Relative Units

A relative unit derives its size from something else: the parent element, the root element, or the dimensions of the viewport. The same relative value can mean different pixel sizes in different contexts, which is what makes them powerful for responsive, scalable design.

#### % (Percentage)

A percentage value is relative to the corresponding property of the parent element.

For `width`, a percentage is relative to the parent's width:

```css
.chat-container {
  width: 800px;
}

.message-bubble {
  width: 75%;  /* 75% of 800px = 600px */
}
```

For `font-size`, a percentage is relative to the parent's font-size:

```css
body {
  font-size: 16px;
}

.small-label {
  font-size: 75%;  /* 75% of 16px = 12px */
}
```

Percentages are most useful for widths in layout: a sidebar that takes 30% of the page, a main content area that takes 70%, a card that fills 100% of its container. They create fluid, proportional layouts that adapt to whatever size the parent element is.

#### em

The `em` unit is relative to the `font-size` of the current element itself. If an element's font size is 16px, then `1em` equals 16px within that element. If the font size is 20px, `1em` equals 20px.

```css
.message-text {
  font-size: 18px;
  padding: 1em;     /* 1em = 18px, because font-size is 18px */
  border-radius: 0.5em;  /* 0.5em = 9px */
}
```

When `em` is used for `font-size` itself, it becomes relative to the parent element's font-size. This is where things can become unpredictable.

Consider this nesting scenario:

```css
body {
  font-size: 16px;
}

.outer {
  font-size: 1.2em;  /* 1.2 × 16px = 19.2px */
}

.outer .middle {
  font-size: 1.2em;  /* 1.2 × 19.2px = 23.04px */
}

.outer .middle .inner {
  font-size: 1.2em;  /* 1.2 × 23.04px = 27.65px */
}
```

Each nested element multiplies by 1.2em again. The same written value, `1.2em`, produces a different pixel size at every level of nesting. This is called the **em compounding problem**. If your HTML has several levels of nesting, using `em` for font sizes becomes extremely difficult to predict or maintain.

`em` is still useful for padding and margin on components, where you want the spacing to scale proportionally with the component's own text size. But for font sizes specifically, there is a better option.

<!-- 
IMAGE: A nesting diagram showing four boxes: body, .outer, .middle, .inner, each visually smaller than the parent container but with text getting progressively larger. Next to each box, a label shows the computed font-size: 16px, 19.2px, 23.04px, 27.65px. Caption: "All four rules say 1.2em. All four produce a different size. This is em compounding." Style: flat vector, nested boxes illustration, font-size labels with arrows, clean educational layout.
-->

#### rem (Root em)

`rem` stands for root em. It is relative to the font-size of the root element of the document, which is the `<html>` element. In CSS, we set this using the `:root` selector.

```css
:root {
  font-size: 16px;
}
```

Every `rem` value on the entire page is calculated from this single number. `1rem` is always 16px. `2rem` is always 32px. `0.75rem` is always 12px. It does not matter how deeply an element is nested or what its parent's font-size is.

```css
:root {
  font-size: 16px;
}

.outer {
  font-size: 1.2rem;  /* always 1.2 × 16px = 19.2px */
}

.outer .middle {
  font-size: 1.2rem;  /* still 1.2 × 16px = 19.2px, no compounding */
}

.outer .middle .inner {
  font-size: 1.2rem;  /* still 1.2 × 16px = 19.2px */
}
```

The same value produces the same size at every level. No compounding, no surprises.

There is an additional accessibility benefit. When a user increases their browser's default font size in their system or browser settings (a common accessibility practice), they are changing the browser's default root font size. A stylesheet using `rem` throughout will scale with that change. A stylesheet using `px` will not.

`rem` is the preferred unit for:

- Font sizes across the entire design
- Padding and margin in a design system built around a consistent spacing scale
- Any measurement that should scale with the user's text preferences

The general rule that separates `em` from `rem` in practice: use `rem` when you want something to scale with the global design scale. Use `em` when you want something to scale with that specific component's own font size.

<!-- 
IMAGE: A side-by-side comparison showing the same four-level nesting from the em diagram, but now using rem. All four boxes show font-size: 19.2px regardless of depth. A dividing line separates the two diagrams, labeled "em compounds" on the left and "rem stays constant" on the right. Caption: "rem breaks the compounding chain by always referring to one source: the root." Style: flat vector, split comparison diagram, consistent sizing on the rem side, escalating sizes on the em side.
-->

#### vw and vh (Viewport Units)

The viewport is the visible area of the browser window. Viewport units measure size as a percentage of that area.

`1vw` equals 1% of the viewport's width. `100vw` equals the full width of the viewport.

`1vh` equals 1% of the viewport's height. `100vh` equals the full height of the viewport.

```css
.hero-section {
  width: 100vw;
  height: 100vh;
  /* This section fills the entire visible screen */
}

.chat-window {
  height: 70vh;
  /* The chat window takes 70% of the screen height */
}
```

Viewport units are particularly useful for full-screen layouts: a hero section on a landing page that fills the entire first screen, a chat window that uses a specific proportion of the available vertical space, or a modal overlay that covers the entire viewport.

Unlike percentages, which are relative to the parent element's size, viewport units are always relative to the browser window itself. A `width: 50vw` on a deeply nested element is still 50% of the whole viewport, regardless of how small its parent container is.

```css
/* A container that is 60% of the viewport width, centered */
.chatbot-container {
  width: 60vw;
  max-width: 800px;
  margin: 0 auto;
}
```

The `max-width` alongside `vw` is a common pattern: the element fills 60% of the viewport on large screens but is capped at 800px so it does not become uncomfortably wide on very large monitors.

There are two additional viewport units worth knowing: `vmin` (the smaller of vw and vh) and `vmax` (the larger). These are less commonly used but useful for sizing elements that should respond to the more constrained dimension of the viewport.

<!-- 
IMAGE: A browser window illustration showing the viewport with labeled dimensions. A horizontal arrow across the full width is labeled "100vw". A vertical arrow across the full height is labeled "100vh". Inside the viewport, a div taking up 60vw × 70vh is highlighted in a different color, labeled with its dimensions. Caption: "Viewport units measure against the browser window itself, not any parent element." Style: flat vector, browser mockup with measurement annotations, clean educational layout.
-->

### When to Use Which Unit

With several units now in the toolkit, the practical question is when each one is the right choice.

**Use `px`** for borders, outlines, box shadows, and any fine detail where a specific, fixed pixel size is intentional and should not adapt to any context.

**Use `rem`** for font sizes, padding, margin, and any spacing that should scale consistently with the global design. This is the workhorse unit for most typography and spacing decisions.

**Use `%`** for widths in layout contexts where an element should fill a proportion of its parent container. Common in multi-column layouts and flexible card grids.

**Use `em`** for padding and margin on self-contained components where the spacing should scale proportionally with the component's own font size. Not recommended for font sizes due to compounding.

**Use `vw` and `vh`** for full-screen sections, viewport-filling containers, and any element whose size should respond to the browser window dimensions rather than its parent's dimensions.

In a real stylesheet, you will use all of them, each in its appropriate context. A single CSS file might use `px` for borders, `rem` for font sizes and padding, `%` for column widths, and `vh` for the chat window height.

### Let's Define Things Now...

> **User Agent Stylesheet:** The browser's built-in default stylesheet that gives HTML elements their baseline visual appearance before any author CSS is applied.

> **Absolute Unit:** A CSS unit with a fixed, physical meaning that does not change based on context. `px` is the standard absolute unit for screen design.

> **Relative Unit:** A CSS unit whose value is calculated from something else: a parent element, the root element, or the viewport.

> **px (Pixel):** A device-independent CSS pixel. Fixed and predictable. Best for borders, shadows, and fine details.

> **% (Percentage):** Relative to the corresponding property of the parent element. Commonly used for fluid widths in layout.

> **em:** Relative to the current element's `font-size`. Compounds when nested, making it unpredictable for font sizes across nesting levels. Useful for component-level padding that should scale with the component's text size.

> **rem (Root em):** Relative to the root element's `font-size`, set on `:root` or `html`. Does not compound regardless of nesting depth. Preferred for typography and spacing systems.

> **:root:** A CSS pseudo-class that matches the root element of the document (`<html>`). Used to define global custom properties and the base `font-size` for `rem` calculations.

> **vw (Viewport Width):** 1% of the browser viewport's width. `100vw` fills the full visible width.

> **vh (Viewport Height):** 1% of the browser viewport's height. `100vh` fills the full visible height.

### Activity: Units in Practice

Create a new HTML page with the following structure: a hero section, a content section with a heading and three paragraphs, and a narrow sidebar div alongside it.

Apply the following using the specified units:

1. Set `:root { font-size: 16px; }` at the top of the stylesheet
2. Set the hero section to `width: 100vw` and `height: 100vh` to fill the entire first screen
3. Set the main heading to `font-size: 2.5rem` and the body paragraphs to `font-size: 1rem`
4. Set padding on the content section using `rem` values
5. Set the sidebar to `width: 25%` and the content area to `width: 75%`
6. Add a `1px solid` border to the sidebar using `px`
7. Nest one `<div>` inside another and apply `font-size: 1.2em` to both. Observe the compounding. Then change both to `1.2rem` and observe the difference.

Inspect each element in DevTools and switch to the Computed tab. This shows the final pixel values the browser calculated for every property, making the em compounding effect and the rem consistency visible in real numbers.

## What's Coming Next...

We have covered how CSS measures size. But a webpage is not just a collection of correctly-sized boxes. It has color: backgrounds that set the mood, text in tones that guide the eye, borders that define boundaries, gradients that add depth.

In the next article we will look at everything CSS can do with color: the different formats for expressing colors, when to use each, and how to build a consistent color system for a real design. From there, the chatbot page will start to look like something a user would actually want to open.
