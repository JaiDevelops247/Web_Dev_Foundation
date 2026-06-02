## Introduction

We have a flex container. We have set the direction. The items line up. But look at what we actually have:

```html
<div class="container">
  <div class="item">Home</div>
  <div class="item">Features</div>
  <div class="item">Pricing</div>
  <div class="item">Login</div>
</div>
```

```css
.container {
  display: flex;
  flex-direction: row;
  background-color: #0f172a;
  padding: 16px;
  height: 80px;
}

.item {
  background-color: #1e293b;
  color: #f1f5f9;
  padding: 8px 16px;
  border-radius: 6px;
}
```

<!-- 
IMAGE: A dark navbar-like flex container with four items (Home, Features, Pricing, Login) rendered side by side but all clumped together at the top-left corner of the container. There is visible unused space on the right side and vertical space below the items. Caption: "The items are in a row. But they are stuck to the top-left corner with all the empty space on the right." Style: flat vector, dark container with dark item boxes, labeled to show unused space on the right and below.
-->

The items are in a row. That is progress. But they are all pushed to the top-left corner of the container, packed tightly together with no breathing room, and all the available space in the container sits unused to the right and below.

Now think about how a real navbar actually looks. The logo sits on the far left, and the navigation links cluster on the far right. Or every link is evenly distributed across the full width of the bar. Or everything is centered.

<!-- 
IMAGE: Four versions of the same navbar container shown in a vertical stack. Version 1: items clustered at the left edge (no alignment set). Version 2: items spread across the full width with equal space between them. Version 3: items centered horizontally in the container. Version 4: items clustered at the right edge. Each version is labeled with a short description. Caption: "Same container. Same items. Four different layouts achieved purely through alignment properties." Style: flat vector, four dark navbar bars each showing the four items in the described positions.
-->

The same container, the same items, four completely different layouts. None of these required changing the HTML or the item sizes. The only thing that changed was how the container distributes space along its axes.

This is what alignment properties in Flexbox control.

### Two Axes, Two Alignment Properties

Before introducing the properties, remember the two axes from the previous article. Every alignment property in Flexbox targets one of these two axes:

- Properties that align along the **main axis** control how items are spaced and distributed in the direction they flow (left to right in a row, top to bottom in a column).
- Properties that align along the **cross axis** control how items are positioned in the perpendicular direction (vertically in a row, horizontally in a column).

With that in mind, the two primary alignment properties are:

| Property | Axis it controls |
|---|---|
| `justify-content` | Main axis |
| `align-items` | Cross axis |

### justify-content: Alignment Along the Main Axis

`justify-content` controls how flex items are distributed and spaced along the main axis. It is set on the flex container.

```css
.container {
  display: flex;
  justify-content: value;
}
```

#### flex-start (default)

Items are packed toward the start of the main axis. For `flex-direction: row`, that is the left edge. No space is added between items or before the first item.

```css
.container {
  display: flex;
  justify-content: flex-start;
}
```

<!-- 
IMAGE: A wide flex container with four items packed to the left side. Large unused space on the right. An arrow labeled "Main Axis" points right. Caption: "justify-content: flex-start — items pack toward the start of the main axis. This is the default." Style: flat vector, dark container, four item boxes on the left with space on right.
-->

#### flex-end

Items are packed toward the end of the main axis. For `flex-direction: row`, that is the right edge.

```css
.container {
  display: flex;
  justify-content: flex-end;
}
```

<!-- 
IMAGE: The same flex container with four items pushed to the right side. Large unused space on the left. Caption: "justify-content: flex-end — items pack toward the end of the main axis." Style: flat vector, dark container, four item boxes on the right with space on left.
-->

Use case: a row of action buttons that should sit on the right side of a panel header, or user messages pushed to the right in a chat interface.

#### center

Items are grouped and centered along the main axis. Equal space appears before the first item and after the last item.

```css
.container {
  display: flex;
  justify-content: center;
}
```

<!-- 
IMAGE: The same flex container with four items centered horizontally. Equal space on both sides. Caption: "justify-content: center — items are centered along the main axis." Style: flat vector, dark container, four item boxes centered with equal space on both sides.
-->

Use case: centering a group of navigation links, a button group, or a set of icons horizontally within their container.

#### space-between

Items are distributed so that the first item is at the start edge, the last item is at the end edge, and all remaining space is distributed equally between the items. No space before the first item or after the last item.

```css
.container {
  display: flex;
  justify-content: space-between;
}
```

<!-- 
IMAGE: The same flex container with four items. Item 1 is flush against the left edge. Item 4 is flush against the right edge. Items 2 and 3 are evenly distributed in between. Equal gaps between all adjacent items, no gap at the outer edges. Caption: "justify-content: space-between — outer items touch the edges. Space is distributed only between items." Style: flat vector, dark container, four items with labeled equal gaps between them.
-->

Use case: the classic navbar layout where the logo sits at the far left and the navigation links sit at the far right. One container, two children (logo and nav group), `justify-content: space-between` does the rest.

```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

#### space-around

Equal space is placed around each item. Each item gets the same amount of space on both its left and right sides. Because adjacent items each contribute their own space, the space between items is double the space at the outer edges.

```css
.container {
  display: flex;
  justify-content: space-around;
}
```

<!-- 
IMAGE: The same flex container with four items. Each item has equal space on both sides. The gaps between items are visibly wider than the gaps at the outer edges (because each adjacent item contributes one unit of space, resulting in two units between items). Caption: "justify-content: space-around — equal space around each item. The between-item gaps are double the edge gaps." Style: flat vector, dark container, items with labeled space units showing the 1:2 ratio.
-->

#### space-evenly

Equal space is distributed between all items and at the outer edges. Unlike `space-around`, every gap including the edges is exactly the same size.

```css
.container {
  display: flex;
  justify-content: space-evenly;
}
```

<!-- 
IMAGE: The same flex container with four items. All gaps including the left edge, between items, and the right edge are exactly equal. Caption: "justify-content: space-evenly — truly equal spacing everywhere, including the outer edges." Style: flat vector, dark container, five equal-sized gaps labeled between and around all four items.
-->

#### The Full justify-content Reference

```
flex-start   → [1][2][3][4]                    items packed left
flex-end     →                    [1][2][3][4]  items packed right
center       →        [1][2][3][4]              items centered
space-between → [1]    [2]    [3]    [4]         edges touch, gaps between
space-around  →  [1]   [2]   [3]   [4]          gaps around each item
space-evenly  →  [1]   [2]   [3]   [4]          equal gaps everywhere
```

### align-items: Alignment Along the Cross Axis

`align-items` controls how flex items are positioned along the cross axis (vertically when `flex-direction: row`). It is set on the flex container and applies to all items at once.

```css
.container {
  display: flex;
  align-items: value;
}
```

#### stretch (default)

Items stretch to fill the full cross-axis size of the container. If the container is 80px tall and the items have no explicit height set, they will each become 80px tall to fill it.

```css
.container {
  display: flex;
  align-items: stretch;
  height: 120px;
}
```

<!-- 
IMAGE: A tall flex container (height: 120px) with four items. The items have no height set but stretch to fill the full 120px height of the container. Caption: "align-items: stretch (default) — items expand to fill the container's cross-axis size." Style: flat vector, dark container, four item boxes stretching the full container height.
-->

#### flex-start

Items are packed to the start of the cross axis (the top edge for `flex-direction: row`). Items only take up as much height as their content requires.

```css
.container {
  display: flex;
  align-items: flex-start;
  height: 120px;
}
```

<!-- 
IMAGE: A tall flex container with four items of varying content heights. All items are aligned to the top edge. Unused space appears below each item. Caption: "align-items: flex-start — items align to the top of the container (the start of the cross axis)." Style: flat vector, dark container, items of different heights all touching the top edge.
-->

#### flex-end

Items are packed to the end of the cross axis (the bottom edge for `flex-direction: row`).

```css
.container {
  display: flex;
  align-items: flex-end;
  height: 120px;
}
```

<!-- 
IMAGE: A tall flex container with four items of varying content heights. All items are aligned to the bottom edge. Caption: "align-items: flex-end — items align to the bottom of the container (the end of the cross axis)." Style: flat vector, dark container, items of different heights all touching the bottom edge.
-->

#### center

Items are centered along the cross axis. This is one of the most used values in Flexbox: vertically centering content inside a container.

```css
.container {
  display: flex;
  align-items: center;
  height: 120px;
}
```

<!-- 
IMAGE: A tall flex container with four items of varying content heights. All items are vertically centered within the container. Equal space appears above and below each item. Caption: "align-items: center — items are centered along the cross axis. This is how vertical centering is done in Flexbox." Style: flat vector, dark container, items centered vertically with labeled equal space above and below.
-->

The combination of `justify-content: center` and `align-items: center` on a flex container perfectly centers its children both horizontally and vertically. This two-line pattern replaces what used to require complex positioning tricks:

```css
.hero-section {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

#### baseline

Items are aligned so that their text baselines line up. This is useful when items have different font sizes and you want the text within each item to read on the same horizontal line.

```css
.container {
  display: flex;
  align-items: baseline;
}
```

<!-- 
IMAGE: A flex container with four items that have different font sizes (one with a large heading, one with small body text, one with medium text, one with a label). With align-items: baseline, a horizontal reference line runs through all items showing all text baselines aligned. Caption: "align-items: baseline — items align along the bottom of their text, not the top or center of their box." Style: flat vector, dark container, items with visibly different font sizes, a dashed horizontal baseline line running through all text.
-->

Use case: a message row where the sender name is larger and bold and the timestamp is small, but both should read on the same baseline.

### align-self: Overriding Alignment for One Item

`align-items` applies the same cross-axis alignment to all flex items in the container. `align-self` is set on an individual flex item and overrides `align-items` for that item only.

```css
.container {
  display: flex;
  align-items: center;   /* default for all items */
  height: 120px;
}

.special-item {
  align-self: flex-end;  /* this item overrides the container rule */
}
```

<!-- 
IMAGE: A flex container with four items. Three items are vertically centered (following align-items: center). The fourth item has align-self: flex-end applied and sits at the bottom edge. Caption: "align-self overrides align-items for a single item. All other items follow the container rule." Style: flat vector, dark container, three centered items and one bottom-aligned item clearly distinguishable.
-->

`align-self` accepts the same values as `align-items`: `stretch`, `flex-start`, `flex-end`, `center`, `baseline`, and additionally `auto` which reverts to the container's `align-items` value.

Use case: a chat message row where all items are top-aligned by default, but the "send" icon should be bottom-aligned to sit at the bottom of tall messages.

### gap: Spacing Between Items

Until now, any space between flex items has been a side effect of padding, margin, or `justify-content` distributing leftover space. None of these are ideal for consistent gutters.

The problem with using margin for item spacing in Flexbox:

```css
/* The margin approach: messy at the edges */
.item {
  margin-right: 16px;   /* adds space after every item, including the last one */
}

/* Fix? */
.item:last-child {
  margin-right: 0;
}
```

This works but requires extra selectors to remove the outer edge margin. In a wrapping grid, it gets worse: the last item in each row creates an unintended gap.

`gap` solves this cleanly. It adds space between flex items only, never on the outer edges.

```css
.container {
  display: flex;
  gap: 16px;
}
```

<!-- 
IMAGE: Two versions of the same four-item flex container. Left (margin approach): each item has a right margin, including the last item, creating unwanted space at the right edge. The outer space is highlighted in red. Right (gap approach): same visual spacing between items, but no space at the outer edges. The between-item gaps are highlighted in green. Caption: "gap adds space between items only. margin adds space around every item, including the outer edges." Style: flat vector, two-panel comparison, color-coded to show the difference in outer edge behavior.
-->

`gap` is a shorthand for two properties:

```css
/* Both axes the same */
gap: 16px;

/* row-gap and column-gap separately */
gap: 12px 24px;   /* row-gap: 12px, column-gap: 24px */

/* Or individually */
row-gap: 12px;
column-gap: 24px;
```

For a wrapping flex container (with `flex-wrap: wrap`), `row-gap` controls the space between rows and `column-gap` controls the space between items in each row.

```css
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 24px;   /* 24px between every card, in every direction */
}
```

This is why `gap` is preferred over margin for flex item spacing. One declaration, consistent gutters everywhere, no edge cleanup required.

### Putting It Together: Chatbot Layout Alignment

Here is what the three major areas of the chatbot page look like with alignment applied:

```css
/* Navbar: logo left, links right, all vertically centered */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 24px;
  height: 64px;
}

/* Message row: avatar and bubble side by side, avatar at the top */
.message-row {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  margin-bottom: 16px;
}

/* User message row: push everything to the right */
.message-row.user {
  justify-content: flex-end;
}

/* Message header: sender name left, timestamp right */
.message-header {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 4px;
}

/* Input bar: text input and send button side by side, vertically centered */
.input-bar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 16px;
}
```

Each layout problem that required a different approach before Flexbox is now solved with a combination of `justify-content`, `align-items`, and `gap`.

### Let's Define Things Now...

> **justify-content:** Controls alignment and spacing of flex items along the main axis. Set on the flex container. Values: `flex-start` (default), `flex-end`, `center`, `space-between`, `space-around`, `space-evenly`.

> **align-items:** Controls alignment of all flex items along the cross axis. Set on the flex container. Values: `stretch` (default), `flex-start`, `flex-end`, `center`, `baseline`.

> **align-self:** Overrides `align-items` for a single flex item. Set on the item, not the container. Accepts the same values as `align-items`, plus `auto`.

> **gap:** Sets the space between flex items without adding space at the outer edges. Shorthand for `row-gap` and `column-gap`. Preferred over margin for consistent item gutters.

> **Vertical Centering Pattern:** `display: flex` + `justify-content: center` + `align-items: center` perfectly centers content both horizontally and vertically within a container.

### Activity: Alignment Grid

Build a container with six boxes and systematically explore each alignment value:

1. Set `display: flex` with `height: 200px` and no alignment properties. Observe the default behavior: items pack at `flex-start` horizontally, and `stretch` fills the container height.

2. Apply each `justify-content` value one at a time: `flex-start`, `flex-end`, `center`, `space-between`, `space-around`, `space-evenly`. For each one, note where the first and last items sit and where the gaps appear.

3. Give the items different amounts of text content so they have different natural heights. Apply each `align-items` value: `stretch`, `flex-start`, `flex-end`, `center`, `baseline`. For `baseline`, vary the font sizes of the text inside each box.

4. Apply `align-items: center` to the container, then add `align-self: flex-end` to only the third box. Confirm it sits at the bottom while the others remain centered.

5. Add `gap: 24px` to the container. Then replace it with individual `margin-right: 24px` on each item. Observe the extra space that appears after the last item with margin. Remove the last item's margin with `:last-child`. Compare the two approaches and confirm why `gap` is cleaner.

6. Apply this to the chatbot navbar: `justify-content: space-between` and `align-items: center`. Observe the logo and links move to opposite ends while remaining vertically centered.

## What's Coming Next...

We now have the two most used sets of Flexbox properties: direction and flow from the last article, and alignment and spacing from this one. These alone handle the vast majority of layout work.

But Flexbox has a few more properties that are less frequently used and not immediately obvious. Properties that control how individual items grow or shrink when there is extra space or not enough space, how items can be reordered visually without changing the HTML, and some behaviors that explain why your layout sometimes acts in unexpected ways. In the next article, we will cover these properties and the understanding they provide for debugging flex layouts that are not behaving the way you expect.
