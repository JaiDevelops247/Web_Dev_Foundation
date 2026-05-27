## Introduction

In the last article we covered lists: unordered, ordered, and description lists for presenting collections of information in a vertical sequence.

But some information does not fit a list. Think about a cricket scorecard. You need to see a player's name alongside their runs, balls faced, fours, sixes, and strike rate, all on the same line, each value aligned under its column label. Change the player and the next row carries the same structure. The columns stay constant. The rows change.

That is not a list. That is a grid. A relationship between rows and columns, where both axes carry meaning at the same time.

<!-- 
IMAGE: A screenshot of a real cricket scorecard from ESPNcricinfo showing the classic two-dimensional layout: column headers (Batsman, Dismissal, R, B, 4s, 6s, SR) and multiple player rows beneath. Caption: "A scorecard is not a list. Every value belongs to a row and a column simultaneously." Style: actual scorecard screenshot with a subtle caption overlay, clean educational layout.
-->

You see this kind of data everywhere on the Web: flight and train schedules, pricing plan comparisons, stock dashboards, product spec sheets, leaderboard standings. Any time data has both a vertical and a horizontal dimension, a Table is the correct tool.

This article is about how HTML creates that two-dimensional structure.

### The Practical We Will Build

Rather than a generic example, let us build something real. We will create a simplified batting scorecard for a T20 match between India and Australia. Here is the data we are working with:

**India vs Australia, 1st T20I: India Batting**

| Batsman | R | B | 4s | 6s | SR |
|---------|---|---|----|----|----|
| Rohit Sharma | 45 | 34 | 5 | 1 | 132.35 |
| Virat Kohli | 72 | 55 | 7 | 2 | 130.90 |
| KL Rahul | 28 | 22 | 3 | 0 | 127.27 |

By the end of this article, this will be a fully structured HTML Table, with a caption, a proper header section, a body, a footer row for totals, and merged cells for summary rows. Let us build it piece by piece.

### The table Element

The foundation is the `<table>` element. It is the outer container, the boundary that tells the Browser: everything inside here is part of a table.

```html
<table>
  <!-- rows and cells go inside here -->
</table>
```

On its own, an empty `<table>` renders nothing visible. Its entire content is built from the elements that live inside it.

### tr: The Table Row

Every row in a table is created with a `<tr>` element (Table Row). Think of `<tr>` as a shelf in a cabinet. The cabinet is the `<table>`, and each shelf is a `<tr>`. The items that sit on each shelf are the cells.

```html
<table>
  <tr><!-- first row --></tr>
  <tr><!-- second row --></tr>
  <tr><!-- third row --></tr>
</table>
```

Each `<tr>` produces a horizontal row in the rendered table.

### th and td: The Two Types of Cells

A row can contain two kinds of cells.

`<th>` stands for Table Header. It marks a cell as a label for its row or column. Browsers render it **bold and centred** by default, and screen readers treat it semantically as a column or row heading. Use `<th>` for the top row of labels.

`<td>` stands for Table Data. It is a regular data cell, the information that belongs to a row under a particular column. Browsers render it in normal weight, left-aligned by default.

Here is the first row of our scorecard built in HTML:

```html
<table>
  <tr>
    <th>Batsman</th>
    <th>R</th>
    <th>B</th>
    <th>4s</th>
    <th>6s</th>
    <th>SR</th>
  </tr>
  <tr>
    <td>Rohit Sharma</td>
    <td>45</td>
    <td>34</td>
    <td>5</td>
    <td>1</td>
    <td>132.35</td>
  </tr>
  <tr>
    <td>Virat Kohli</td>
    <td>72</td>
    <td>55</td>
    <td>7</td>
    <td>2</td>
    <td>130.90</td>
  </tr>
  <tr>
    <td>KL Rahul</td>
    <td>28</td>
    <td>22</td>
    <td>3</td>
    <td>0</td>
    <td>127.27</td>
  </tr>
</table>
```

This already produces a recognisable table. A header row followed by three data rows. But it has no semantic structure yet. The Browser does not know which rows are headers, which are data, and which is the summary.

<!-- 
IMAGE: A rendered HTML table illustration showing the scorecard above. The first row has bold, centred text for column headers (Batsman, R, B, 4s, 6s, SR). The three player rows below show normal weight, left-aligned data. No CSS applied, just the default browser table rendering. Caption: "th cells are bold and centred by default. td cells are normal weight. Both are inside tr rows." Style: clean browser-render illustration, flat vector, monospace code on the left, rendered table on the right.
-->

### thead, tbody, and tfoot: Giving the Table Semantic Structure

A table can be divided into three logical sections using three container elements, each wrapping a group of `<tr>` rows.

`<thead>` wraps the header row or rows. It communicates to the Browser, screen readers, and search engines that these rows are the column labels.

`<tbody>` wraps the main data rows. This is where the actual records go.

`<tfoot>` wraps the footer row or rows, typically used for totals, averages, or summary values.

```html
<table>
  <thead>
    <tr>
      <th>Batsman</th>
      <th>R</th>
      <th>B</th>
      <th>4s</th>
      <th>6s</th>
      <th>SR</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Rohit Sharma</td>
      <td>45</td>
      <td>34</td>
      <td>5</td>
      <td>1</td>
      <td>132.35</td>
    </tr>
    <tr>
      <td>Virat Kohli</td>
      <td>72</td>
      <td>55</td>
      <td>7</td>
      <td>2</td>
      <td>130.90</td>
    </tr>
    <tr>
      <td>KL Rahul</td>
      <td>28</td>
      <td>22</td>
      <td>3</td>
      <td>0</td>
      <td>127.27</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>Total</td>
      <td>157</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tfoot>
</table>
```

Visually this renders identically to the version without these wrappers. The value is semantic and practical. Screen readers use `<thead>` to announce column headers when reading through data rows. CSS can target `thead`, `tbody`, and `tfoot` separately. When a long table is printed, the Browser can repeat the `<thead>` at the top of each printed page automatically.

Always use these three sections when your table has headers and summary rows. For a simple two-or-three-row table with no clear header/footer distinction, they are optional but still good practice.

### caption: Giving the Table a Title

Every table should have a clear title so the reader immediately knows what the data represents. HTML provides the `<caption>` element for this.

`<caption>` must be the **very first child** of the `<table>` element, placed before `<thead>`. The Browser renders it centred above the table by default.

```html
<table>
  <caption>India vs Australia, 1st T20I: India Batting</caption>
  <thead>
    ...
  </thead>
  <tbody>
    ...
  </tbody>
</table>
```

Do not use a heading element like `<h3>` above the table as a substitute for `<caption>`. A `<caption>` is semantically tied to the table. Screen readers announce it as the table's title when they enter the table. An `<h3>` has no relationship to the table at all.

### colspan: Merging Cells Horizontally

Our scorecard has a problem. The "Total" row in the footer currently has empty cells because the total score spans the concept of the entire innings, not just one column. In real scorecards, summary rows like "Total" and "Extras" stretch across the whole table visually.

This is what `colspan` solves. The `colspan` attribute on a `<td>` or `<th>` tells the Browser: this single cell should stretch across this many columns.

```html
<tfoot>
  <tr>
    <td>Extras</td>
    <td colspan="5">b 4, lb 2, w 6</td>
  </tr>
  <tr>
    <td>Total</td>
    <td colspan="5">157/3 (20 Overs)</td>
  </tr>
</tfoot>
```

The first `<td>` in each row takes up column 1 (Batsman). The second `<td>` with `colspan="5"` stretches across columns 2 through 6 (R, B, 4s, 6s, SR), merging all five into a single wide cell. No additional empty `<td>` elements are needed for those columns. The `colspan` value handles the space.

<!-- 
IMAGE: A rendered table illustration showing the footer section with colspan in action. The "Extras" row shows "b 4, lb 2, w 6" stretching across five merged columns. The "Total" row shows "157/3 (20 Overs)" in the same wide merged cell. An annotation arrow labels the merged cell with "colspan='5': one cell spans 5 columns." Style: flat vector, clean browser-render illustration, red bracket highlighting the merged cell area.
-->

### rowspan: Merging Cells Vertically

Where `colspan` merges horizontally across columns, `rowspan` merges vertically across rows. The `rowspan` attribute tells the Browser: this single cell should stretch down across this many rows.

Consider a match schedule where a session spans two time slots:

```html
<table>
  <caption>Day 1 Schedule</caption>
  <thead>
    <tr>
      <th>Time</th>
      <th>India</th>
      <th>Australia</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>9:00 AM</td>
      <td rowspan="2">Batting Practice</td>
      <td>Fielding Drills</td>
    </tr>
    <tr>
      <td>10:00 AM</td>
      <!-- no td here: the rowspan cell above already occupies this slot -->
      <td>Net Bowling</td>
    </tr>
    <tr>
      <td>11:00 AM</td>
      <td>Team Meeting</td>
      <td>Team Meeting</td>
    </tr>
  </tbody>
</table>
```

The "Batting Practice" cell has `rowspan="2"`. It occupies the India column for both the 9:00 AM and 10:00 AM rows. Notice the second `<tr>` has no `<td>` for the India column. It does not need one. The `rowspan` from the row above is already occupying that cell's position. Adding an extra `<td>` there would push everything out of alignment.

<!-- 
IMAGE: A rendered table illustration showing the Day 1 Schedule. The "Batting Practice" cell is visually taller, stretching across the 9:00 AM and 10:00 AM rows in the India column. An annotation arrow labels it "rowspan='2': one cell spans 2 rows." The Australia column shows "Fielding Drills" and "Net Bowling" in separate cells for those same rows, contrasting with the merged India cell. Style: flat vector, clean educational illustration, blue highlight on the rowspan cell.
-->

The rule with both `colspan` and `rowspan` is: once you declare a cell spans N rows or N columns, the cells it overlaps must be removed from the HTML entirely. Only one cell can occupy a given position. If you add cells anyway, the table breaks.

### A Critical Note: Tables Are Not for Layout

Before CSS existed, web developers used `<table>` elements as a layout tool: placing navigation in one column and content in another, building the entire page structure with rows and cells. You may still encounter this pattern in very old code.

**Do not do this.** Tables are for tabular data, full stop.

Using tables for layout is semantically incorrect, makes pages nearly impossible to maintain, produces terrible output for screen readers, and creates layouts that break on mobile. CSS Grid and CSS Flexbox are the correct tools for layout. HTML Tables are only for data that genuinely belongs in rows and columns.

If you cannot describe your table's data as "each row is a record and each column is a property of that record," it should not be a table.

### Let's Define Things Now...

> **table:** The outer container element for an HTML Table. Everything inside it belongs to the table structure.

> **tr (Table Row):** Defines a single horizontal row within a table. Rows contain cells.

> **th (Table Header):** A header cell within a row. Rendered bold and centred by default. Used for column and row labels. Has semantic meaning for screen readers.

> **td (Table Data):** A standard data cell. Holds the actual content of the table record.

> **thead:** A semantic wrapper for the table's header row or rows. Screen readers use it to announce column labels before reading data.

> **tbody:** A semantic wrapper for the main data rows of the table.

> **tfoot:** A semantic wrapper for footer rows such as totals, averages, or summary information.

> **caption:** An element placed as the first child of `<table>` that provides a visible, semantically linked title for the table. Read by screen readers as the table's label.

> **colspan:** An attribute on `<td>` or `<th>` that causes the cell to stretch horizontally across the specified number of columns.

> **rowspan:** An attribute on `<td>` or `<th>` that causes the cell to stretch vertically across the specified number of rows.

### Activity: Model Comparison Table

Build a table that compares three AI language models across four properties. Use the following data or modify it with your own research:

| Model | Developer | Context Window | Best For |
|-------|-----------|---------------|----------|
| GPT-3.5 Turbo | OpenAI | 16K tokens | Fast, cost-efficient tasks |
| GPT-4 | OpenAI | 128K tokens | Complex reasoning |
| GPT-4o | OpenAI | 128K tokens | Multimodal inputs |

**Requirements**:
- Use `<caption>` for the table title
- Use `<thead>`, `<tbody>`, and `<tfoot>`
- Add a footer row that uses `colspan` to display a note like "Data as of 2024. Specifications subject to change." spanning all four columns
- Add at least one `rowspan` somewhere that makes logical sense, such as grouping models by the same developer

Open the file in the Browser. Verify that the header row is bold and centred, the footer note stretches across all columns, and the structure looks correct in DevTools under the Elements tab.

## What's Coming Next...

That is how Tables work in HTML: a structured grid of rows and cells, organised semantically with `thead`, `tbody`, and `tfoot`, titled with `caption`, and given flexibility through `colspan` and `rowspan`.

So far, every page we have built presents information outward to the reader. But a real web application does something more: it collects information from the user. A search box, a login form, a message input, a settings panel. All of that user input flows through one HTML mechanism.

The next article is about HTML Forms. We will cover the `<form>` element, the `<input>` element and its many types, labels, buttons, and how a form packages user data and sends it to a server. This is where our pages stop being static documents and start becoming interactive applications.
