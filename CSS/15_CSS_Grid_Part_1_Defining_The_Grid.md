## Introduction

We have six feature cards to lay out on the chatbot product homepage. Each card has an icon, a heading, and a short description. Here is the HTML:

```html
<section class="features">
  <div class="card">Fast Responses</div>
  <div class="card">Context Aware</div>
  <div class="card">Always Online</div>
  <div class="card">Multilingual</div>
  <div class="card">Secure Chats</div>
  <div class="card">Easy to Embed</div>
</section>
```

Without any layout CSS, they stack top to bottom. But the design calls for this:

<!-- 
IMAGE: A clean product webpage mockup showing six feature cards arranged in a 3-column by 2-row grid. Each card is the same size. All three cards in the top row are the same width. All three cards in the bottom row are the same width. The columns are perfectly aligned vertically: the left edge of every card in column 1 is the same, column 2 the same, column 3 the same. The layout feels like a well-defined grid overlaid on the content. Caption: "Six cards, two rows, three columns. Every card aligns both horizontally and vertically." Style: dark themed product page, uniform dark cards with icons and text inside, clean and professional.
-->

Three columns, two rows. Every card is the same width. The columns align perfectly from top to bottom.

### Why Flexbox Is Not the Cleanest Tool Here

Flexbox arranges items along one axis at a time. To build this layout using only Flexbox, here is what you would need:

```html
<section class="features">

  <div class="row">              <!-- flex container 1 -->
    <div class="card">Fast Responses</div>
    <div class="card">Context Aware</div>
    <div class="card">Always Online</div>
  </div>

  <div class="row">              <!-- flex container 2 -->
    <div class="card">Multilingual</div>
    <div class="card">Secure Chats</div>
    <div class="card">Easy to Embed</div>
  </div>

</section>
```

```css
.row {
  display: flex;
  gap: 24px;
}

.card {
  flex: 1;
}
```

This works, but look at the cost. The HTML had to be restructured. Six cards inside a single container became six cards split across two artificial wrapper divs that exist only to satisfy the one-axis limitation of Flexbox. The HTML now reflects a layout decision, not a content decision.

And there is a deeper problem: the two rows are entirely separate flex contexts. They are not aware of each other. If the cards in row 1 happen to be taller than those in row 2 for any reason, the rows have no mechanism to coordinate their heights across the full layout.

<!-- 
IMAGE: Side-by-side diagram. Left: six cards in a single features container, all six as siblings. Right: the same six cards now split across two "row" divs, with a label pointing to the row divs reading "Artificial wrappers added only for layout — not meaningful in HTML." Caption: "Flexbox requires restructuring HTML to achieve 2D layout. The layout leaks into the markup." Style: flat vector, two HTML tree diagrams side by side, elements highlighted with color coding.
-->

When both rows and columns need to be controlled at the same time, a layout system that operates on both axes simultaneously is the cleaner tool. That is what CSS Grid provides.

### Imagine a Grid Flowing Over the Page

Before we look at any CSS, picture a transparent grid laid over the entire features section. Like graph paper placed on top of the webpage. The grid has rows and columns, and each intersection creates a cell. Each card sits inside one of those cells.

<!-- 
IMAGE: The same six-card layout from earlier, now with a visible grid overlay drawn on top. Thin lines divide the section into 3 columns and 2 rows, creating 6 equal cells. Each card sits inside one cell. The grid lines extend the full width and height of the section. Caption: "Grid is a coordinate system for the page. Define the tracks, place the items." Style: dark themed section, cards visible inside cells, grid lines drawn in a lighter contrasting color over the top.
-->

With CSS Grid, you tell the browser: draw this grid on the container. Define how many columns, how wide each one is, how tall each row is. Then the items inside the container fill the cells of that grid automatically, or you can place them at specific coordinates manually.

The result: the same six cards, still siblings in the HTML, no wrapper divs needed, both rows and columns controlled by a single container declaration.

### CSS Grid: A Formal Definition

**CSS Grid** is a two-dimensional layout system that lets you define a grid of rows and columns on a container element and place items precisely within that grid. Unlike Flexbox which controls one axis at a time, Grid controls both axes simultaneously from a single container.

### Grid Container and Grid Items

Just like Flexbox, CSS Grid works with two types of elements:

**The grid container**: the parent element with `display: grid` declared. You define the grid structure here: how many columns, how many rows, what sizes.

**The grid items**: the direct children of the grid container. They are automatically placed into the grid's cells. Only direct children are grid items, just like only direct children are flex items.

```css
.features {
  display: grid;   /* activates Grid layout for direct children */
}
```

```html
<section class="features">       <!-- grid container -->
  <div class="card">...</div>    <!-- grid item -->
  <div class="card">...</div>    <!-- grid item -->
  <div class="card">...</div>    <!-- grid item -->
  <div class="card">...</div>    <!-- grid item -->
  <div class="card">...</div>    <!-- grid item -->
  <div class="card">...</div>    <!-- grid item -->
</section>
```

Setting `display: grid` alone does not produce a visible grid. The container activates the grid context but the browser needs more information: how many columns to create and how wide each should be.

### grid-template-columns: Defining the Columns

`grid-template-columns` defines the number of columns in the grid and the width of each one. Each value you provide creates one column.

```css
/* Two columns, each 200px wide */
.container {
  display: grid;
  grid-template-columns: 200px 200px;
}
```

<!-- 
IMAGE: A grid container with four items. Two columns, each 200px wide. Two items in the first row, two items in the second row (auto-placed). The column lines are visible. Caption: "grid-template-columns: 200px 200px — two columns, each explicitly 200px wide." Style: flat vector, dark container, four item boxes in 2x2 arrangement, column lines visible.
-->

```css
/* Three columns, each 200px wide */
.features {
  display: grid;
  grid-template-columns: 200px 200px 200px;
}
```

<!-- 
IMAGE: A grid container with six items. Three columns, each 200px wide. Items fill in order: three in row 1, three in row 2. Column lines visible. Caption: "grid-template-columns: 200px 200px 200px — three columns, six items fill two rows automatically." Style: flat vector, dark container, six item boxes in 3x2 arrangement, column lines visible.
-->

Columns do not have to be equal:

```css
/* A sidebar layout: first column 260px, second takes remaining space */
.page-layout {
  display: grid;
  grid-template-columns: 260px auto;
}
```

<!-- 
IMAGE: A grid container with two columns. The left column is a narrow fixed-width sidebar (260px). The right column stretches to fill the rest of the container. Caption: "grid-template-columns: 260px auto — fixed sidebar and a fluid content column." Style: flat vector, two-column layout, left column narrow and labeled 260px, right column wide and labeled auto/remaining space.
-->

You can mix any unit: `px`, `%`, `auto`, or a new unit introduced specifically for Grid called `fr`.

### grid-template-rows: Defining the Rows

`grid-template-rows` defines the height of each row, in the same format as `grid-template-columns`.

```css
.features {
  display: grid;
  grid-template-columns: 200px 200px 200px;
  grid-template-rows: 150px 150px;
}
```

<!-- 
IMAGE: A grid container with six items, 3 columns and 2 rows. Both rows are explicitly 150px tall. Items are uniform in size. Column and row lines are visible. Caption: "grid-template-rows: 150px 150px — both rows locked to a 150px height." Style: flat vector, dark container, six equal-sized item boxes in 3x2 arrangement, grid lines visible.
-->

When `grid-template-rows` is not specified, row heights are determined by the content inside them. This is the most common setup: you define columns explicitly and let row heights adapt to content.

### The fr Unit: Fractional Space

Defining columns in fixed pixels creates a grid that does not adapt to different screen sizes. CSS Grid introduces a dedicated proportional unit for this: `fr`.

`fr` stands for fractional unit. One `fr` represents one fraction of the available space in the grid container after any fixed-size columns have been placed.

```css
/* Three equal columns, each taking one-third of the available space */
.features {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
```

<!-- 
IMAGE: A flex container showing three items in 3 equal columns. A label below shows the container width divided into three equal parts, each labeled "1fr". The columns resize if the container is wider or narrower. Caption: "1fr 1fr 1fr — three equal columns. Each fr is one-third of the available space." Style: flat vector, dark container with proportional arrows showing equal thirds.
-->

The ratio works the same way as `flex-grow`. `2fr 1fr` gives the first column twice the width of the second:

```css
/* Main content column is twice as wide as the sidebar */
.page-layout {
  display: grid;
  grid-template-columns: 2fr 1fr;
}
```

<!-- 
IMAGE: A two-column grid. The left column (2fr) takes two-thirds of the container. The right column (1fr) takes one-third. Labeled with their fr values and proportional arrows. Caption: "2fr 1fr — the first column is always twice the width of the second, regardless of the container size." Style: flat vector, two columns with labeled proportions.
-->

Mixed units work too. Fixed columns are placed first; the remaining space is then divided among `fr` columns:

```css
/* 260px sidebar, remaining space goes to main content */
.page-layout {
  display: grid;
  grid-template-columns: 260px 1fr;
}
```

<!-- 
IMAGE: A two-column grid. The left column is a fixed 260px. The right column stretches to fill whatever space remains. An arrow labels the remaining space as "1fr (everything left over after 260px is placed)." Caption: "Mixing fixed and fr units: fixed tracks are placed first, then fr divides what remains." Style: flat vector, narrow fixed left column and wide fluid right column.
-->

### The repeat() Function

Writing `1fr 1fr 1fr` is fine for three columns. For twelve it gets unreadable. `repeat()` is a shorthand for repeating a track definition:

```css
/* These two are identical */
grid-template-columns: 1fr 1fr 1fr;
grid-template-columns: repeat(3, 1fr);
```

```css
/* A 12-column grid (the classic design system grid) */
.layout {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
}
```

<!-- 
IMAGE: A wide grid container with 12 equal-width columns. Each column is labeled "1fr". A bracket below the entire row shows "repeat(12, 1fr)". Caption: "repeat(12, 1fr) — a twelve-column grid. The standard grid system used in most design frameworks." Style: flat vector, wide dark container with 12 visible columns of equal width, labeled.
-->

`repeat()` also works with multiple values as a pattern:

```css
/* Alternating 1fr and 2fr columns, repeated 3 times */
grid-template-columns: repeat(3, 1fr 2fr);
/* Result: 1fr 2fr 1fr 2fr 1fr 2fr — 6 columns */
```

For rows:

```css
/* Four rows each 120px tall */
grid-template-rows: repeat(4, 120px);
```

Here is the six-card feature grid using `repeat()`:

```css
.features {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
```

<!-- 
IMAGE: The six feature cards now laid out in a 3x2 grid using repeat(3, 1fr). All three columns are equal width. The cards fill both rows automatically. No wrapper divs. The design matches the original target layout from the introduction. Caption: "repeat(3, 1fr) on the container. Six cards, single HTML structure, no wrappers." Style: dark themed product page, six uniform cards in 3x2 arrangement.
-->

Three lines of CSS on the container. The six cards are in the correct layout. No restructuring of the HTML required.

### gap: Spacing Between Grid Cells

`gap` works exactly the same in Grid as in Flexbox: it adds space between grid cells without adding space at the outer edges of the container.

```css
.features {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
}
```

<!-- 
IMAGE: The same 3x2 card grid now with visible 24px gutters between every card in both directions. The outer edges of the container have no extra space. Caption: "gap: 24px — 24px between every cell in both directions. No space at the outer edges." Style: dark themed grid, cards with clearly labeled gutters between them.
-->

Row and column gaps can be set independently:

```css
.features {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  row-gap: 32px;      /* vertical space between rows */
  column-gap: 24px;   /* horizontal space between columns */
}

/* Or using the shorthand: gap: row-gap column-gap */
.features {
  gap: 32px 24px;
}
```

<!-- 
IMAGE: A 3x2 grid with clearly different row and column gaps. The vertical gap between rows is visibly larger than the horizontal gap between columns. Both gaps are labeled with their values. Caption: "row-gap and column-gap can be set independently for different horizontal and vertical spacing." Style: flat vector, dark container with labeled asymmetric gaps.
-->

### Auto-Placement: Items Fill the Grid Automatically

When grid items are not given specific placement instructions, the browser places them automatically in the cells of the grid, filling them in source order: left to right, top to bottom.

```css
.features {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
}

/* No placement rules on the cards — they fill automatically */
```

<!-- 
IMAGE: A grid container with repeat(3, 1fr) and eight items (more than six to show wrapping). The first three items fill row 1 (left to right). The next three fill row 2. Items 7 and 8 start filling row 3. Numbered items 1-8 make the order clear. Row heights are automatically sized to content. Caption: "Auto-placement: items fill cells in source order. New rows are created automatically as items overflow." Style: flat vector, dark grid, eight numbered item boxes in a 3-column layout, 3+3+2 distribution.
-->

New rows are created automatically as items overflow the defined columns. The height of each auto-created row is determined by the tallest item in that row.

This automatic behavior handles most cases cleanly. When you need an item to span multiple columns or rows, or to be placed at a specific position in the grid, that is done with item-level placement properties: the topic of the next article.

### Putting It Together: Feature Grid and Page Layout

Here is the complete feature section using everything from this article:

```css
/* Six-card feature grid */
.features {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 32px 24px;
}

/* Full chatbot page layout: settings aside + chat interface */
.page-layout {
  display: grid;
  grid-template-columns: 280px 1fr;
  gap: 0;
  height: 100vh;
}
```

The features section needs no wrapper divs. The page layout defines a fixed settings panel and a fluid chat area in two lines of CSS on a single container.

### Let's Define Things Now...

> **CSS Grid:** A two-dimensional CSS layout system that defines a grid of rows and columns on a container element and places items within that grid. Controls both axes simultaneously from a single container declaration.

> **Grid Container:** The parent element with `display: grid` declared. Defines the grid structure through `grid-template-columns`, `grid-template-rows`, and `gap`.

> **Grid Item:** A direct child of a grid container. Placed into grid cells automatically or explicitly.

> **grid-template-columns:** Defines the number of columns and their width. Each value creates one column track.

> **grid-template-rows:** Defines the number of rows and their height. Rows not defined here are sized automatically by content.

> **fr unit:** Fractional unit native to CSS Grid. Represents a share of the available space after fixed tracks are placed. `1fr 1fr 1fr` divides space equally among three columns.

> **repeat():** A Grid function for repeating track definitions. `repeat(3, 1fr)` is equivalent to `1fr 1fr 1fr`.

> **gap:** Adds space between grid cells in both directions. `row-gap` and `column-gap` control the axes independently.

> **Auto-placement:** The default behavior where grid items fill cells in source order without needing explicit placement rules. New rows are created automatically as items overflow.

### Activity: Grid Foundation

Build the feature card section using everything in this article:

1. Create a `<section class="features">` with six `<div class="card">` children. Apply `display: grid` only. Observe no visible change — the grid needs column definitions.

2. Add `grid-template-columns: 200px 200px 200px`. Observe three fixed-width columns appear. Add a seventh card and watch it auto-place into a new row.

3. Replace the three pixel values with `repeat(3, 1fr)`. Resize the browser and observe the columns scale proportionally with the container.

4. Add `grid-template-rows: 150px 150px`. Observe both rows lock to 150px. Remove it and observe rows return to content height.

5. Add `gap: 24px`. Observe gutters appear between every card. Change to `gap: 32px 16px` and observe the vertical and horizontal gaps become different.

6. Build a two-column page layout with `grid-template-columns: 280px 1fr`. Place a sidebar element and a main content element as the two direct children. Confirm the sidebar is always 280px and the content fills the rest.

## What's Coming Next...

Just like in Flexbox where one article established the direction and flow and the next covered alignment, Grid follows the same pattern. We have now defined the grid: its columns, its rows, its gaps.

In the next article, we will see how to place items at specific positions inside the grid. How to make one card span two columns. How to make a header span the entire row. How to use grid areas to name regions of the layout and assign elements to them by name. That is where the full power of Grid becomes visible.
