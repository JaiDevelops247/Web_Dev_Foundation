## Introduction

We know how to create a flex container, set the direction, and align items. But there is still a question we have not answered: what happens to the space itself?

Consider the input bar at the bottom of the chatbot. A text input on the left, a send button on the right. The button should always be its natural size. The text input should stretch to fill all the remaining horizontal space, expanding and contracting as the browser window resizes.

```html
<div class="input-bar">
  <input type="text" placeholder="Type a message..." />
  <button>Send</button>
</div>
```

```css
.input-bar {
  display: flex;
  gap: 8px;
  padding: 12px 16px;
}
```

<!-- 
IMAGE: A flex input bar showing a text input and a Send button side by side. The input only takes up as much space as its placeholder text requires. The Send button sits to its right. There is a large visible gap of unused space to the right of the button, making the input look too short and the layout incomplete. Caption: "Both items are only as wide as their content. The remaining space in the container sits unused." Style: flat vector, dark input bar, dark input field and blue send button, visible unused space labeled on the right.
-->

Both elements are only as wide as their content. The remaining space in the container sits unused. We need a way to tell the text input: take all the space that is left over after the button claims its share.

This is exactly what `flex-grow`, `flex-shrink`, and `flex-basis` control. They are set on the flex items themselves, not the container, and they determine how each item handles the available space in the layout.

### flex-basis: The Starting Size

Before the browser distributes any extra space, it first figures out how large each item wants to be. That starting size is `flex-basis`.

`flex-basis` sets the initial size of a flex item along the main axis, before any growing or shrinking happens. In a `flex-direction: row` layout, it behaves like `width`. In a `flex-direction: column` layout, it behaves like `height`.

```css
.item {
  flex-basis: 200px;   /* start at 200px before distributing leftover space */
}

.item {
  flex-basis: 30%;     /* start at 30% of the container width */
}

.item {
  flex-basis: auto;    /* use the item's content size as the starting point (default) */
}
```

The default value is `auto`, which means the item uses its own width (or content size) as the starting point.

`flex-basis: 0` means the item starts from zero and claims space only through `flex-grow`. This is the starting point used when you want all items to share space purely in proportion to their `flex-grow` values, ignoring their content sizes.

<!-- 
IMAGE: Three items in a flex row. Item A has flex-basis: 100px (narrow, clearly 100px). Item B has flex-basis: 200px (medium). Item C has flex-basis: auto (sized to its content, which is longer text). A label below shows remaining free space in the container after the three basis values are accounted for. Caption: "flex-basis sets each item's starting size. The remaining space after all basis values is what flex-grow distributes." Style: flat vector, dark container, three items with dimension labels and a labeled "remaining space" section.
-->

### flex-grow: Claiming Extra Space

After each item has been assigned its `flex-basis` size, the container calculates how much space remains. `flex-grow` determines how that remaining space is distributed among the items.

```css
.item {
  flex-grow: 0;   /* default: do not claim any extra space */
}
```

By default every flex item has `flex-grow: 0`, which means no item claims any leftover space. The unused space just stays unused, which is why our input bar has that gap on the right.

Setting `flex-grow: 1` on the text input tells it: take all the remaining space that no other item has claimed.

```css
.input-bar input {
  flex-grow: 1;   /* claim all remaining space */
}
```

<!-- 
IMAGE: The same input bar from the introduction, now with flex-grow: 1 on the text input. The text input now stretches to fill all horizontal space between the left edge and the Send button. The Send button remains its natural width. No unused space visible. Caption: "flex-grow: 1 on the input — it claims all the remaining space the button does not need." Style: flat vector, dark input bar, input stretched to fill remaining width, button naturally sized.
-->

The value in `flex-grow` is a ratio, not a size. When multiple items have `flex-grow` values set, the remaining space is divided proportionally.

```css
.sidebar {
  flex-grow: 1;
}

.main-content {
  flex-grow: 3;
}
```

Here the remaining space is split into 4 parts (1 + 3). The sidebar gets 1 part. The main content gets 3 parts. The result is a sidebar that is always one-quarter the width of the main content, regardless of the container size.

<!-- 
IMAGE: A two-column layout inside a flex container. The left column (sidebar) is labeled "flex-grow: 1" and takes up roughly one-quarter of the container width. The right column (main content) is labeled "flex-grow: 3" and takes up roughly three-quarters. An arrow below shows the total remaining space split into a 1:3 ratio. Caption: "flex-grow distributes the remaining space proportionally. 1 and 3 means a 1:3 ratio." Style: flat vector, two-column dark layout, labeled with proportion arrows.
-->

```
Container: 1000px
No flex-basis set on either item, so both start at 0

Remaining space: 1000px
Ratio: 1 : 3 (total 4 parts)
sidebar gets: 1000 ÷ 4 × 1 = 250px
main gets:    1000 ÷ 4 × 3 = 750px
```

If you give three items `flex-grow: 1` each, all three claim equal shares of the remaining space:

```css
.card {
  flex-grow: 1;   /* each card gets an equal share */
}
```

<!-- 
IMAGE: Three cards in a flex container, each with flex-grow: 1. The cards are equal width, evenly filling the container with no unused space. A note shows that the container width is divided equally among the three. Caption: "flex-grow: 1 on all items — equal sharing of the available space." Style: flat vector, three equal-width dark cards filling the container.
-->

### flex-shrink: Giving Up Space When Things Get Tight

`flex-grow` handles surplus space. `flex-shrink` handles the opposite: what happens when the total size of all items exceeds the container width.

By default every flex item has `flex-shrink: 1`, meaning all items shrink proportionally when there is not enough room.

```css
/* Default: all three items shrink equally when space runs out */
.item {
  flex-shrink: 1;
}
```

<!-- 
IMAGE: A flex container that is too narrow to fit three items at their natural width. With flex-shrink: 1 on all items, all three items are narrower than their natural size, compressed equally to fit within the container. Caption: "flex-shrink: 1 (default) — all items shrink proportionally when the container is too small." Style: flat vector, narrow container, three slightly squeezed item boxes.
-->

A higher `flex-shrink` value means the item gives up more space relative to its siblings when the container is too small.

```css
.flexible-item {
  flex-shrink: 2;   /* shrinks twice as fast as a flex-shrink: 1 sibling */
}
```

`flex-shrink: 0` prevents an item from shrinking at all. This is critical for the send button in the input bar: the button should never shrink to the point of becoming unusable.

```css
.input-bar input {
  flex-grow: 1;       /* expand to fill available space */
  flex-shrink: 1;     /* shrink if needed */
}

.input-bar button {
  flex-shrink: 0;     /* never shrink — always keep its natural width */
}
```

<!-- 
IMAGE: A narrow flex input bar where the container has been resized to be smaller. The text input has shrunk to give up space. The Send button remains exactly its natural width (flex-shrink: 0). Caption: "flex-shrink: 0 on the button — it never compresses regardless of how small the container gets." Style: flat vector, narrow dark input bar, input clearly narrower than before, button unchanged.
-->

### The flex Shorthand

Writing `flex-grow`, `flex-shrink`, and `flex-basis` separately gets repetitive. The `flex` shorthand combines all three in order:

```css
/* flex: grow  shrink  basis */
flex: 1  1  0;
```

This is common enough that CSS provides several named presets worth memorizing:

#### flex: 1

The most used preset. Equivalent to `flex: 1 1 0`. The item starts from zero size and grows and shrinks equally with its siblings. All items with `flex: 1` share the available space in equal proportions.

```css
.equal-column {
  flex: 1;   /* shorthand for: flex-grow: 1, flex-shrink: 1, flex-basis: 0 */
}
```

Because `flex-basis` is set to `0`, the content size of each item is ignored when distributing space. All space is divided purely by the `flex-grow` ratio. Three items with `flex: 1` will always be exactly equal width regardless of how much content each contains.

#### flex: auto

Equivalent to `flex: 1 1 auto`. The item grows and shrinks like `flex: 1`, but it starts from its content size (`auto` basis) instead of zero. Items with more content naturally start wider.

```css
.item {
  flex: auto;
}
```

#### flex: none

Equivalent to `flex: 0 0 auto`. The item does not grow and does not shrink. It is fixed at its content size or declared width. Used for items that should always remain at a specific size regardless of the container.

```css
.fixed-width-button {
  flex: none;
  width: 120px;   /* stays at 120px always */
}
```

#### Proportional layouts with the shorthand

The `flex` shorthand makes proportional layouts concise. A sidebar at one-quarter width and a main content area at three-quarters:

```css
.sidebar {
  flex: 1;
}

.main-content {
  flex: 3;
}
```

Because both items use `flex-basis: 0` (the default when using the single-number shorthand), the ratio is exactly 1:3 based purely on the `flex-grow` values, not on content sizes.

<!-- 
IMAGE: A three-panel layout showing different flex shorthand combinations. Panel 1: three items all with flex: 1 — equal width columns. Panel 2: a sidebar (flex: 1) and main content (flex: 3) — one-quarter and three-quarter split. Panel 3: a fixed item (flex: none, 120px) and a growing item (flex: 1) — fixed button and expanding input. Each panel labeled with the flex values applied. Caption: "The flex shorthand handles the three most common space-distribution patterns with a single value each." Style: flat vector, three side-by-side layout panels, dark containers.
-->

### Putting It Together: The Chatbot Input Bar

Here is the complete input bar for the chatbot, using everything from this article:

```css
.input-bar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 16px;
  border-top: 1px solid #1e293b;
  background-color: #0f172a;
}

.input-bar textarea {
  flex: 1;            /* grow to fill all remaining space */
  flex-shrink: 1;     /* can shrink if container gets narrow */
  min-width: 0;       /* allows shrinking below content width in flex context */
  resize: none;
  background-color: #1e293b;
  color: #f1f5f9;
  border: 1px solid #334155;
  border-radius: 8px;
  padding: 10px 14px;
  font-size: 0.9375rem;
}

.input-bar button {
  flex: none;         /* never grow, never shrink */
  width: 80px;
  padding: 10px 0;
  background-color: #3b82f6;
  color: #ffffff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}
```

The textarea expands to fill all horizontal space. The button stays exactly 80px wide at every viewport size. The gap between them is always 8px. The input bar adapts to any container width without breaking.

### Let's Define Things Now...

> **flex-basis:** The initial size of a flex item along the main axis before growing or shrinking. Default is `auto` (content size). `0` means the item starts from zero and claims space only through `flex-grow`.

> **flex-grow:** Controls how much of the remaining free space an item claims relative to its siblings. Default is `0` (no growth). Items with equal `flex-grow` values share extra space equally. A higher value means a proportionally larger share.

> **flex-shrink:** Controls how much an item shrinks relative to siblings when the container is too small. Default is `1` (shrinks proportionally). `0` prevents shrinking entirely.

> **flex shorthand:** Combines `flex-grow`, `flex-shrink`, and `flex-basis` in one declaration. Common presets: `flex: 1` (equal growth from zero basis), `flex: auto` (grows and shrinks from content size), `flex: none` (fixed, does not grow or shrink).

### Activity: Chatbot Input Bar and Proportional Layout

1. Build the input bar: a `<div>` with `display: flex` containing a `<textarea>` and a `<button>`. Apply no `flex` properties yet. Observe both elements sized only to their content.

2. Add `flex: 1` to the textarea. Watch it expand to fill all remaining space. Resize the browser window and observe the textarea resizing while the button stays the same width.

3. Add `flex: none` to the button. Now narrow the browser window significantly. The textarea should compress. The button should remain exactly the same size. Confirm this by checking the computed width in DevTools.

4. Build a two-column layout: a `.sidebar` and a `.main`. Apply `flex: 1` to the sidebar and `flex: 3` to main. Observe the 1:3 ratio. Change the ratio to `flex: 2` and `flex: 2` (equal). Then try `flex: 1` on one and `flex: 4` on the other.

5. Add three cards to a container with `flex-wrap: wrap`. Give each card `flex: 1` with a `min-width: 250px`. Resize the window and observe the cards wrap to new rows while stretching to fill the available width at each row size.

## What's Coming Next...

This is everything you need to know about the basics of Flexbox. We can now create a flex container, control the flow direction, align and distribute items along both axes, and define how each item grows and shrinks in relation to its siblings.

Flexbox is a one-dimensional layout system. It is excellent at arranging elements along a single axis at a time, and it handles the vast majority of layout problems in any interface. The chatbot navbar, the message rows, the input bar, the settings panel — all of it is solvable with what we have covered.

But there is a layout challenge where Flexbox starts to feel like a workaround: placing elements in a true grid, where both rows and columns need to be controlled simultaneously. When you need cards to align across both horizontal and vertical axes at the same time, or a full page layout where the header, sidebar, content, and footer all lock to a defined structure, that is where a two-dimensional system is the better tool.

CSS Grid was built for exactly this. In the next section, we will see what advantages a two-dimensional layout system provides over Flexbox, how it thinks about space differently, and how the two systems work together in a real interface.
