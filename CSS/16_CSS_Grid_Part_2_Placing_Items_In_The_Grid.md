## Introduction

We have a grid. We know how to define columns, rows, and gaps. Items flow into cells automatically in source order. For a uniform card grid, that is all you need.

But most real layouts are not uniform grids. Consider this magazine-style layout:

<!-- 
IMAGE: A wireframe of a webpage layout with a 4-column grid. The hero banner at the top spans all 4 columns (full width). Below it, a sidebar spans 2 rows on the left (1 column wide). To the right of the sidebar are 3 article tiles in 2 rows. At the bottom, a footer spans all 4 columns. The overall structure is clearly asymmetric with items of different sizes. Caption: "A typical content layout: some items span multiple columns, some span multiple rows, some do both." Style: clean wireframe sketch, dark background, light-colored placeholder boxes labeled hero, sidebar, article, footer.
-->

The hero banner spans the full width. The sidebar is taller than a single row. The footer spans the entire bottom. Auto-placement cannot produce this: every cell would be the same size and every item would fill exactly one cell.

To place items at specific coordinates, spanning specific numbers of columns or rows, we need explicit placement. And CSS Grid gives us two ways to do it: line-based placement and named area placement.

### Understanding Grid Lines

Before placing items, understand what grid lines are. When you define a grid, the browser draws lines to create the tracks. A grid with 3 columns has 4 vertical lines. A grid with 2 rows has 3 horizontal lines. These lines are numbered starting from 1.

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;   /* 3 columns */
  grid-template-rows: 200px 200px;       /* 2 rows */
}
```

<!-- 
IMAGE: A 3x2 grid container with column lines and row lines clearly labeled. Column lines numbered 1, 2, 3, 4 from left to right at the top. Row lines numbered 1, 2, 3 from top to bottom on the left side. Six cells visible. A callout note: "3 columns = 4 column lines. 2 rows = 3 row lines. Lines always equal tracks + 1." Caption: "Grid lines are the boundaries of tracks. They are numbered starting from 1 on each axis." Style: flat vector, dark grid container, visible grid lines with numbered labels at the edges.
-->

Grid line numbers are the coordinates you use when placing items. You do not place items into columns: you place them between line numbers.

### grid-column: Placing Items Horizontally

`grid-column` tells a grid item where to start and end on the column axis using line numbers.

```css
.item {
  grid-column: start-line / end-line;
}
```

```css
/* Start at column line 1, end at column line 3 — spans 2 columns */
.hero {
  grid-column: 1 / 3;
}
```

<!-- 
IMAGE: A 3x2 grid. The first item (labeled "hero") is highlighted and spans from column line 1 to column line 3, occupying the first two columns of the first row. The remaining items are auto-placed after it. Column lines 1, 2, 3, 4 are labeled. A bracket below the hero shows "grid-column: 1 / 3". Caption: "grid-column: 1 / 3 — starts at line 1, ends at line 3. That is two column tracks." Style: flat vector, dark grid, hero item highlighted in a different color, grid lines labeled.
-->

```css
/* Span all 3 columns in a 3-column grid: line 1 to line 4 */
.full-width-header {
  grid-column: 1 / 4;
}
```

<!-- 
IMAGE: A 3x2 grid. The header item spans from column line 1 to column line 4, taking the full width of the grid across all three columns. The remaining items fill the second row. Caption: "grid-column: 1 / 4 — spans all three columns in a 3-column grid (line 1 to line 4)." Style: flat vector, header item in a highlighted color stretching the full grid width.
-->

Negative line numbers count from the opposite end. `-1` always refers to the last line, regardless of how many columns exist. This is useful for spanning to the end without knowing the exact column count:

```css
/* Always spans to the last column line, regardless of column count */
.footer {
  grid-column: 1 / -1;
}
```

### grid-row: Placing Items Vertically

`grid-row` works exactly the same way but on the row axis:

```css
.item {
  grid-row: start-line / end-line;
}
```

```css
/* Sidebar spans from row line 1 to row line 3 — occupies 2 rows */
.sidebar {
  grid-row: 1 / 3;
}
```

<!-- 
IMAGE: A 3x3 grid. The sidebar item is highlighted and placed in column 1, spanning from row line 1 to row line 3 (two full rows tall). The remaining items fill the space to the right of the sidebar. Row lines 1, 2, 3 are labeled on the left. A bracket on the right of the sidebar shows "grid-row: 1 / 3". Caption: "grid-row: 1 / 3 — starts at row line 1, ends at row line 3. The item is two rows tall." Style: flat vector, dark grid, sidebar item highlighted in a different color, row lines labeled.
-->

`grid-column` and `grid-row` can be used together to place an item at a precise coordinate in the grid:

```css
.featured-card {
  grid-column: 2 / 4;   /* columns 2 and 3 */
  grid-row: 1 / 3;      /* rows 1 and 2 */
}
```

<!-- 
IMAGE: A 4x3 grid. One item is highlighted spanning columns 2 to 4 and rows 1 to 3, occupying a 2x2 block in the upper-right area of the grid. Column and row lines are labeled. Caption: "Combining grid-column and grid-row places an item at a precise two-dimensional coordinate." Style: flat vector, highlighted item spanning 2 columns and 2 rows simultaneously.
-->

### The span Keyword: Relative Spanning

Line numbers require you to know the exact position of an item. The `span` keyword lets you specify how many tracks to span without naming the exact lines:

```css
/* Span 2 columns from wherever auto-placement puts this item */
.item {
  grid-column: span 2;
}
```

<!-- 
IMAGE: A 4-column grid with five items. Items 1, 2, and 3 are regular single-cell items. Item 4 has grid-column: span 2 and occupies two columns. Item 5 auto-places after item 4. Caption: "span 2 spans two columns from wherever the item is auto-placed, without needing to know line numbers." Style: flat vector, five items in a 4-column grid, item 4 clearly wider than the others.
-->

You can combine `span` with a starting line:

```css
/* Start at line 2, span 3 columns from there */
.item {
  grid-column: 2 / span 3;
}
```

`span` is particularly useful when items are auto-placed and you just want some of them to be wider or taller than a single cell:

```css
.featured-post {
  grid-column: span 2;   /* always twice as wide, wherever it lands */
  grid-row: span 2;      /* always twice as tall, wherever it lands */
}
```

### grid-template-areas: Naming the Layout

Line numbers are precise but not immediately readable. Looking at `grid-column: 2 / 4` tells you nothing about what that region represents in the page. For full page layouts, there is a more visual alternative: named grid areas.

`grid-template-areas` lets you define the layout as a visual map using quoted strings. Each string represents one row. Each word in the string represents one cell. Cells sharing the same name merge into a single area.

```css
.page {
  display: grid;
  grid-template-columns: 260px 1fr;
  grid-template-rows: 64px 1fr 48px;
  grid-template-areas:
    "header  header"
    "sidebar main"
    "footer  footer";
}
```

Read it like a map: the first row has two cells both named `header`, so the header spans both columns. The second row has `sidebar` on the left and `main` on the right. The third row has `footer` across both columns.

<!-- 
IMAGE: A visual diagram showing the grid-template-areas layout as a labeled grid. Three rows and two columns. Top row: one wide region labeled "header" spanning both columns. Middle row: left cell labeled "sidebar", right cell labeled "main". Bottom row: one wide region labeled "footer" spanning both columns. The CSS code is shown alongside with each area name color-coded to match the visual. Caption: "grid-template-areas reads like a map. The quoted strings mirror the actual page structure." Style: flat vector, color-coded grid regions matching the CSS strings, clean labels.
-->

A period (`.`) represents an empty cell in the grid:

```css
grid-template-areas:
  "header header header"
  "sidebar main   .    "
  "footer  footer footer";
```

The third column in the middle row is empty.

### grid-area: Placing Items into Named Areas

Once areas are defined on the container, each item is assigned to an area by name using `grid-area`:

```css
header  { grid-area: header;  }
aside   { grid-area: sidebar; }
main    { grid-area: main;    }
footer  { grid-area: footer;  }
```

That is the entire placement system. No line numbers, no calculations. The CSS reads like a description of the layout.

```html
<div class="page">
  <header>ChatAI</header>
  <aside>Settings</aside>
  <main>Chat Window</main>
  <footer>Status Bar</footer>
</div>
```

```css
.page {
  display: grid;
  grid-template-columns: 260px 1fr;
  grid-template-rows: 64px 1fr 48px;
  height: 100vh;
  grid-template-areas:
    "header  header"
    "sidebar main  "
    "footer  footer";
}

header { grid-area: header;  }
aside  { grid-area: sidebar; }
main   { grid-area: main;    }
footer { grid-area: footer;  }
```

<!-- 
IMAGE: A full-page browser mockup showing the chatbot page laid out using the CSS above. A dark header bar spans the full width at the top. Below it, a narrow dark sidebar (260px) on the left shows settings options. The main chat area fills the remaining space on the right. A thin footer spans the full width at the bottom. The layout matches the grid-template-areas map exactly. Caption: "The entire page layout defined in one grid-template-areas declaration. Each element placed by name." Style: dark themed UI mockup, labeled sections matching header/sidebar/main/footer areas.
-->

Changing the layout is as simple as editing the quoted strings. Moving the sidebar to the right requires only rewriting the area map and updating the column definition, without touching any item rules.

### grid-area as a Placement Shorthand

`grid-area` also works as a shorthand for full line-based placement, combining row-start, column-start, row-end, and column-end:

```css
/* grid-area: row-start / column-start / row-end / column-end */
.hero {
  grid-area: 1 / 1 / 2 / 4;
}

/* Equivalent to: */
.hero {
  grid-row: 1 / 2;
  grid-column: 1 / 4;
}
```

This shorthand is less commonly used than the named area approach for full layouts, but it is useful when you need precise placement on a grid that does not use `grid-template-areas`.

### Putting It Together: The Magazine Layout

Here is the magazine-style layout from the introduction, built with both techniques shown side by side:

**Using line numbers:**

```css
.magazine {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: 300px 200px 200px 60px;
  gap: 16px;
}

.hero    { grid-column: 1 / -1; }          /* full width */
.sidebar { grid-row: 2 / 4; }              /* spans rows 2 and 3 */
.footer  { grid-column: 1 / -1; }          /* full width */
```

**Using named areas:**

```css
.magazine {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: 300px 200px 200px 60px;
  gap: 16px;
  grid-template-areas:
    "hero    hero    hero    hero   "
    "sidebar article article article"
    "sidebar article article article"
    "footer  footer  footer  footer ";
}

.hero    { grid-area: hero;    }
.sidebar { grid-area: sidebar; }
.article { grid-area: article; }
.footer  { grid-area: footer;  }
```

<!-- 
IMAGE: The completed magazine layout rendered in a browser mockup. A wide hero image spans the full width at the top. Below it, a narrow sidebar on the left spans two rows. Three article tiles fill the right side in two rows. A footer bar spans the full width at the bottom. The layout is clean and structured. Caption: "The complete magazine layout using named grid areas. The CSS strings visually map to the final layout." Style: dark themed editorial layout, each area labeled with its grid-area name.
-->

The named area version is more readable, especially for layouts with multiple named regions. The line-based version is more flexible for dynamic or unknown grid sizes.

### Let's Define Things Now...

> **Grid Lines:** The boundaries between grid tracks, numbered from 1. A grid with N columns has N+1 column lines. A grid with N rows has N+1 row lines. Negative numbers count from the opposite end.

> **grid-column:** Sets the horizontal span of a grid item using start and end line numbers. `grid-column: 1 / 3` starts at line 1 and ends at line 3.

> **grid-row:** Sets the vertical span of a grid item using start and end line numbers. `grid-row: 1 / 3` starts at row line 1 and ends at row line 3.

> **span:** A keyword used inside `grid-column` or `grid-row` to specify how many tracks to span rather than exact line numbers. `grid-column: span 2` spans two columns from wherever the item is placed.

> **grid-template-areas:** A container property that defines named regions of the grid using quoted strings. Cells with the same name merge into one area. Periods represent empty cells.

> **grid-area:** Set on a grid item to assign it to a named area defined by `grid-template-areas`. Also works as a shorthand for `row-start / column-start / row-end / column-end`.

### Activity: Magazine Layout and Chatbot Page

1. Build a 4-column grid with `repeat(4, 1fr)` and four rows. Place a hero element spanning all four columns using `grid-column: 1 / -1`. Confirm it fills the full width regardless of how many columns exist.

2. Add a sidebar element and set `grid-row: span 2`. Observe it occupies two row heights while remaining in its auto-placed column position.

3. Rebuild the same layout using `grid-template-areas`. Write the quoted strings for the grid map. Assign each element using `grid-area`. Confirm the visual output is identical to the line-based version.

4. Add a period to one cell in the `grid-template-areas` map. Observe the corresponding cell becomes empty in the rendered layout.

5. Build the complete chatbot page layout with `grid-template-areas`: header across the top, aside on the left (260px), main content filling the rest, footer across the bottom. Assign each semantic HTML element to its area. Confirm the layout matches by resizing the browser.

6. Move the aside to the right side by changing only the `grid-template-areas` strings and the `grid-template-columns` order. Observe that no item rules need to change.

## What's Coming Next...

We now have both layout systems fully in hand. Flexbox for one-dimensional flow along a single axis. Grid for two-dimensional layouts where rows and columns both need to be controlled. These two tools together cover every layout problem in modern web development.

But knowing both raises a natural question: which one do you reach for in a given situation? The answer is not always obvious. A navigation bar can be built with both. A card grid can be built with both. In the next article, we will look at how professionals decide between Flexbox and Grid given the design they are working with, what signals in a design tell you which system fits better, and why the two are often used together in the same interface rather than one replacing the other.
