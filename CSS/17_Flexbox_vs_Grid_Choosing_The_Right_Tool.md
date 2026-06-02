## Introduction

Two layouts. Both can technically be built with either Flexbox or Grid. But for each one, one tool fits better than the other.

**Scenario 1: A navigation bar.**

A logo on the left. Three navigation links on the right. All vertically centered inside a 64px-tall bar. The bar stretches the full width of the viewport.

<!-- 
IMAGE: A dark navigation bar spanning the full browser width. On the left: a logo mark and the text "ChatAI". On the right: three links labeled Features, Pricing, Login. Everything is vertically centered within the bar. Caption: "A navbar: one row of items, spaced and aligned along a single horizontal axis." Style: dark themed navbar, clean and minimal.
-->

**Scenario 2: A full page dashboard.**

A fixed-height header across the top. A narrow settings panel on the left. A main chat area filling the rest of the center. A status bar across the bottom.

<!-- 
IMAGE: A full-page browser mockup showing a dashboard layout. A dark header spans the full width at the top. Below it, a narrow sidebar (settings panel) is on the left. The large main chat area occupies the right. A thin footer bar spans the full width at the bottom. The layout is clearly a two-column, three-row structure. Caption: "A page layout: rows and columns both need to be controlled at the same time." Style: dark themed dashboard UI, clearly divided sections.
-->

Both layouts need CSS. Both involve alignment and positioning. But if you look at what each layout actually asks of you, the right tool becomes clear.

The navbar asks: arrange items along a single horizontal line, space them correctly, center them vertically. That is one axis. The items flow in a row and the job is to control that single dimension.

The dashboard asks: define a structure where the header always occupies the full top row, the sidebar holds its column, and the main content fills the remaining two-dimensional area. Rows and columns both matter simultaneously.

One axis: Flexbox. Two axes: Grid.

### The Practical Framework

Here is the decision rule you can apply to any layout problem:

**Use Flexbox when:**
- Items need to flow along a single axis (a row of buttons, a column of form fields)
- The number of items is dynamic or unknown and you want them to wrap naturally
- You are aligning items within a component (a card's internal structure, a message row)
- The primary concern is distributing space between items in one direction

**Use Grid when:**
- Both rows and columns need to be controlled simultaneously
- Items must align across multiple rows (a card grid where every card in a column lines up vertically)
- You are defining the structural skeleton of a page
- You need items to span specific numbers of columns or rows

```
Navbar links in a row?          → Flexbox
Column of sidebar links?        → Flexbox
Card internal layout?           → Flexbox
Page header + sidebar + main?   → Grid
Card grid (3 across, N rows)?   → Grid
Magazine layout?                → Grid
```

<!-- 
IMAGE: A split diagram. Left side labeled "Flexbox" shows four examples: a horizontal navbar, a vertical form column, a card with internal row layout, a horizontal tag list. Right side labeled "Grid" shows four examples: a 3-column card grid, a full page skeleton, a magazine layout, a dashboard. Each example is a small wireframe. Caption: "Flexbox for flow along one axis. Grid for structure across two axes." Style: flat vector, two-column diagram, each side with four small wireframe illustrations.
-->

### The Hybrid Approach: The Professional Standard

Here is the insight that connects both systems: professional interfaces use Grid and Flexbox together, at different levels of the layout hierarchy.

**Grid for the macro layout.** The overall page structure: where the header lives, where the sidebar is, where the main content fills. Grid defines the skeleton.

**Flexbox for the micro layout.** Inside each component: how items inside the header align, how the message rows are structured, how the input bar is laid out. Flexbox handles the internal arrangement of each region.

```css
/* MACRO: Grid defines the page skeleton */
.page {
  display: grid;
  grid-template-columns: 260px 1fr;
  grid-template-rows: 64px 1fr 48px;
  grid-template-areas:
    "header  header"
    "sidebar main  "
    "footer  footer";
  height: 100vh;
}

/* MICRO: Flexbox handles the inside of each region */
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 24px;
}

.message-row {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}

.input-bar {
  display: flex;
  align-items: center;
  gap: 8px;
}
```

<!-- 
IMAGE: The full chatbot page layout. The outer page structure is highlighted with a "Grid" label and dashed grid lines showing the header, sidebar, main, and footer areas. Inside the header, the logo and nav links are highlighted with a "Flexbox" label showing the flex row. Inside a message row, the avatar and bubble are highlighted with another "Flexbox" label. Caption: "Grid governs the page structure. Flexbox governs the internals of each component. Both in the same interface." Style: dark themed chatbot UI, grid lines overlaid on the outer structure, flexbox arrows overlaid inside components, color-coded labels.
-->

This is not a workaround. It is how the layout systems were designed to be used. Grid was built for two-dimensional structural layouts. Flexbox was built for one-dimensional component layouts. They complement each other naturally.

### auto-fit and minmax(): Responsive Grids Without Media Queries

There is one Grid technique that sits at the intersection of layout and responsiveness, and it is worth covering here because it demonstrates Grid's power over Flexbox in a specific scenario.

Consider a card grid. On a wide screen, three cards per row. On a narrower screen, two per row. On mobile, one per row. With Flexbox and media queries, this requires three separate breakpoint rules.

Grid can handle this in one declaration:

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 24px;
}
```

This is worth breaking down:

**`minmax(240px, 1fr)`** defines a column that is at minimum 240px wide and at maximum 1fr (as wide as the available space allows). No column can ever be narrower than 240px or wider than an equal share.

**`auto-fit`** tells the browser to create as many columns as will fit in the container at the minimum size. If the container is 800px wide and each column has a 240px minimum, the browser fits 3 columns. If the container is 500px wide, it fits 2. If it is 260px wide, it fits 1. The grid reflows entirely on its own.

<!-- 
IMAGE: Three browser viewport sizes showing the same card grid with repeat(auto-fit, minmax(240px, 1fr)). Large viewport (1200px): 4 cards per row. Medium viewport (700px): 2 cards per row. Small viewport (300px): 1 card per row. No media queries used. Caption: "auto-fit with minmax() creates a fully responsive grid with zero media queries." Style: three browser window sizes side by side, same card grid reflowing at each size.
-->

**`auto-fill`** vs **`auto-fit`**: the difference appears when there are fewer items than the maximum number of columns that could fit.

With `auto-fill`, the grid creates empty column tracks to fill the row even if there are no items to put in them. With `auto-fit`, those empty tracks collapse to zero width, allowing the existing items to stretch and fill the row.

```css
/* auto-fill: empty columns are preserved */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

/* auto-fit: empty columns collapse, items stretch to fill */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

<!-- 
IMAGE: Two rows showing three cards in a 5-potential-column container. Top row (auto-fill): three cards occupy three columns. Two empty column ghost tracks are visible on the right, the cards do not stretch to fill them. Bottom row (auto-fit): three cards each stretch to fill the row equally, the empty column slots have collapsed. Caption: "auto-fill preserves empty tracks. auto-fit collapses them and lets items fill the space." Style: flat vector, two container rows, top with visible empty tracks and bottom without.
-->

For most card grids, `auto-fit` is the right choice. Items stretch to fill the row cleanly regardless of how many there are.

### Grid Auto-Placement vs Flexbox Wrapping

Both Grid and Flexbox can wrap items onto new lines. The behavior is different in a way that matters for alignment.

With **Flexbox wrapping** (`flex-wrap: wrap`), each row is an independent flex line. Items in one row do not know about items in another row. If row 1 has three items and row 2 has two, the two items in row 2 stretch to fill their row independently. Columns do not align across rows.

```css
/* Flexbox: rows are independent, columns do not align across rows */
.card-grid {
  display: flex;
  flex-wrap: wrap;
}

.card {
  flex: 1;
  min-width: 240px;
}
```

<!-- 
IMAGE: A wrapping flex container with five cards. Row 1 has three cards of equal width. Row 2 has two cards that stretch to fill the full row width, so they are wider than the cards in row 1. The vertical edges of cards in row 2 do not align with the vertical edges of cards in row 1. Caption: "Flexbox wrapping: rows are independent. Items in row 2 stretch to fill their row, misaligning with row 1's columns." Style: flat vector, two rows of cards, vertical misalignment between rows highlighted with red dashed lines.
-->

With **Grid auto-placement**, the columns are defined once on the container. Every item in every row snaps to those same column tracks. The alignment is always consistent across rows.

```css
/* Grid: all rows share the same column tracks */
.card-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
}
```

<!-- 
IMAGE: A grid container with five cards. Row 1 has three equal-width cards. Row 2 has two cards, but they are the same width as the cards in row 1 because they snap to the same column tracks. The last cell in row 2 is empty. Vertical alignment between rows is perfect. Caption: "Grid auto-placement: all rows share the same column definitions. Alignment is always consistent." Style: flat vector, two rows of cards, vertical alignment between rows highlighted with green dashed lines.
-->

This is the most concrete reason to reach for Grid over Flexbox for multi-row card layouts. When vertical alignment across rows matters, only Grid guarantees it.

### A Layout Audit: The Chatbot Page

Here is how the tool decision plays out across every layout level of the chatbot project:

| Component | Tool | Reason |
|---|---|---|
| Page skeleton (header, aside, main, footer) | Grid | Two-dimensional structure |
| Header internal alignment (logo + links) | Flexbox | One row, items along one axis |
| Message list (vertical column of messages) | Flexbox column | One axis, content-driven height |
| Message row (avatar + bubble) | Flexbox | One row, align-items needed |
| Message header (name + timestamp) | Flexbox | One row, space-between |
| Input bar (textarea + button) | Flexbox | One row, flex-grow on input |
| Feature card grid | Grid | Multi-row, column alignment matters |
| Settings panel (form fields) | Flexbox column | Single axis, stacked fields |

No component in this list uses Grid and Flexbox interchangeably. Each one uses the system that matches its dimensional requirement.

### Let's Define Things Now...

> **Macro Layout:** The outer page structure: where major sections like header, sidebar, main, and footer are placed. Grid is the preferred tool.

> **Micro Layout:** The internal structure of individual components: how items inside a navbar or card are arranged. Flexbox is the preferred tool.

> **minmax(min, max):** A Grid function that sets a minimum and maximum size for a track. `minmax(240px, 1fr)` means never narrower than 240px, never wider than an equal share.

> **auto-fit:** A value for `repeat()` that creates as many columns as fit at the minimum size, then collapses empty column tracks so items stretch to fill the row.

> **auto-fill:** Like `auto-fit` but empty column tracks are preserved, preventing items from stretching beyond their fair share.

### Activity: Layout Classification and Audit

1. Given these eight layout problems, classify each as Flexbox or Grid and write one sentence justifying the choice: a navbar, a 3-column card grid, a settings panel with stacked form fields, a full page skeleton, a chat message row, a photo gallery, a pricing table with 3 plans side by side, a tag list below an article.

2. Build the `repeat(auto-fit, minmax(240px, 1fr))` card grid with six cards. Resize the browser from full width down to 320px and observe the columns reflow automatically with no media queries.

3. Build the same six-card layout using `flex-wrap: wrap` and `flex: 1 1 240px` on each card. Compare the behavior when the browser is resized. Notice that row 2 items stretch to fill the row independently of row 1's column positions.

4. Audit the chatbot page: verify Grid is used for the page skeleton and Flexbox is used inside each component. If any component is using the wrong tool for its layout, refactor it.

## What's Coming Next...

We have now covered both sides of CSS layout. From basic styling (color, typography, spacing) to positioning and decorative properties, through the two-dimensional toolkit of Flexbox and Grid. At this point, you have everything you need to build a beautifully designed, well-structured webpage.

But a webpage is not only experienced on a desktop or a large monitor. The same page is opened on phones, tablets, laptops, and wide screens, each with a different viewport size, a different amount of space, and different expectations for how the content should be arranged.

This is one of the most important aspects of modern web development: responsive design. In the next module, we will explore how to make a webpage that adapts to any screen size, from a 320px mobile screen to a 2560px wide monitor, using the same HTML and CSS.
