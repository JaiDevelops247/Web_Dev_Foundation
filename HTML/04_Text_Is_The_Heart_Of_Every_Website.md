## Introduction

Open Wikipedia and visit any article. Now try to ignore the layout completely. Forget the sidebar, the table of contents on the left, the colours, the fonts. Look only at the content itself.

What you see is text. A title at the top. Section headings breaking the article into chunks. Paragraphs of information. Bold terms when a concept is first introduced. Italicised titles of books or films. Footnote numbers in superscript. A copyright symbol at the bottom.

This is true of almost every Website on the internet. Behind the beautiful styling, the layouts, and the branding, the primary substance of most Websites is text. Articles, product descriptions, instructions, blog posts, chat messages, news stories: All of it is text. Websites exist to communicate ideas, and text is how ideas are communicated.

This means that knowing how to structure and format text in HTML is not a minor skill. It is the most fundamental thing you will do as a Web Developer.

This article covers every major text element HTML provides: the headings that give a page its hierarchy, the paragraph tag that organises content into readable blocks, the supporting tags that handle line breaks and thematic dividers, the subscript and superscript tags, and the formatting tags that let you mark text as important, emphasised, highlighted, or deleted. It also covers HTML Entities, the mechanism for displaying special characters that would otherwise confuse the Browser.

<!-- 
IMAGE: A screenshot-style illustration of a Wikipedia article page, but with the CSS and styling stripped away. All that remains is plain, unstyled text in a browser window: a large title at the top, section headings, paragraphs, bold words, and superscript footnote numbers. A label overlaid on the image reads: "Strip away the design. What remains is text." Style: flat editorial illustration, muted greyscale for the stripped page content, clean and educational.
-->
<div style="display: flex; justify-content: center; padding: 15px 0; width: 100%;">
  <img 
    src="https://s3.ap-south-1.amazonaws.com/static.bytexl.app/uploads/44jnaxga5/content/44qdhnb4j/image04.png" 
    alt="Image Description"
    style="
      width: 70%;
      max-width: 500px; 
      height: auto; 
      border: 4px solid #87CEEB; 
      border-radius: 20px; 
      padding: 10px; 
      background-color: white; 
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    "
  >

### Headings: Giving a Page Its Hierarchy

#### The Need

Imagine reading a textbook where every sentence looked identical. No chapter titles, no section headers, no sub-headings. Just one continuous block of text from cover to cover. It would be nearly unreadable. You would have no way to scan for what you need, no sense of where one topic ends and another begins.

Headings solve this problem. They establish a hierarchy of importance and give readers a map of the page's structure at a glance.

#### How Headings Work in HTML

HTML provides six levels of headings: `<h1>` through `<h6>`. The number indicates the level of importance in the hierarchy. `<h1>` is the most important, `<h6>` is the least.

```html
<h1>My AI Chatbot</h1>
<h2>Features</h2>
<h3>Natural Language Understanding</h3>
<h4>Handling Follow-Up Questions</h4>
```

By default, the Browser renders each heading at a different text size, with `<h1>` being the largest. But remember: headings are not just visual. They carry meaning.

```html
<h1>Photosynthesis</h1>
<h2>The Light-Dependent Reactions</h2>
<h3>Photosystem II</h3>
<h2>The Calvin Cycle</h2>
<h3>Carbon Fixation</h3>
```

This is exactly how Wikipedia structures its articles. The article title is `<h1>`. Major sections are `<h2>`. Subsections within those are `<h3>`. The hierarchy creates a clear, navigable structure.

<!-- 
IMAGE: A visual representation of a webpage structure showing heading levels as a tree. h1 at the top in the largest font, two h2 blocks below it, each with two h3 blocks beneath them, and one h4 beneath one of the h3s. Each level is progressively smaller and indented. Lines connect parent to child headings. Caption: "Heading levels create a navigable hierarchy." Style: clean tree diagram, flat vector, each heading level in a different shade of the same color.
-->
<div style="display: flex; justify-content: center; padding: 15px 0; width: 100%;">
  <img 
    src="https://s3.ap-south-1.amazonaws.com/static.bytexl.app/uploads/44jnaxga5/content/44qdhnb4j/image05.png" 
    alt="Image Description"
    style="
      width: 70%;
      max-width: 500px; 
      height: auto; 
      border: 4px solid #87CEEB; 
      border-radius: 20px; 
      padding: 10px; 
      background-color: white; 
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    "
  >

#### The Rules That Matter

**One `<h1>` per page.** The `<h1>` is the title of the page. Just like a book has one title, a Web page should have exactly one `<h1>`. Search Engines treat it as the primary subject of the page.

**Never skip heading levels.** Do not jump from an `<h2>` directly to an `<h4>`. Heading levels should nest in order: `<h1>` contains `<h2>`, which contains `<h3>`, and so on. Skipping levels confuses screen readers and breaks the structural logic of the page.

**Choose headings by hierarchy, not by visual size.** If you want smaller text, use CSS. Do not pick a lower heading level just because it looks smaller by default.

#### Why Headings Matter Beyond the Visual

Search Engines like Google read heading hierarchy to understand what a page is about and how it is organised. A page with a clear `<h1>` and well-structured `<h2>` and `<h3>` hierarchy ranks better and is easier for search engines to index accurately.

Screen readers, used by visually impaired users, rely on heading structure to navigate a page. A user can jump directly from heading to heading to find the section they need, the same way a sighted user scans visually.

Headings are not just for appearance. They are the skeleton of your page's meaning.

### The Paragraph: The Workhorse of Web Text

#### The Need

Plain text without any structure is a wall. Readers stop engaging. Information becomes hard to absorb. The paragraph is how we break that wall into digestible, meaningful chunks.

#### How Paragraphs Work

The `<p>` tag creates a paragraph element. Each `<p>` is a block-level element, meaning it starts on its own line and the Browser adds a small margin above and below it by default.

```html
<p>HTML is the foundation of every webpage on the Web.</p>

<p>It provides the structure and meaning that Browsers use to render content correctly on any device.</p>
```

These two paragraphs will render with visible spacing between them, making the text easy to read.

One important rule: do not nest paragraphs inside each other. An HTML paragraph is a flat container for inline text content. Placing a `<p>` inside another `<p>` produces invalid HTML.

### Supporting Text Elements

#### The Line Break: `<br>`

Sometimes you want content to appear on a new line but you do not want to start a new paragraph. The most common real-world examples are postal addresses and lines of poetry, where line breaks carry meaning but the content still belongs to a single block.

```html
<p>
  Anthropic<br>
  548 Market Street<br>
  San Francisco, CA 94104
</p>
```

`<br>` is a Void Element. It inserts a line break at that point in the text flow without creating a new paragraph or adding paragraph spacing.

One important note: `<br>` is for meaningful line breaks. It is not a tool for adding vertical spacing between sections. If you find yourself writing `<br><br><br>` to push content down the page, that is CSS's job, not HTML's.

#### The Horizontal Rule: `<hr>`

The `<hr>` element creates a horizontal dividing line across the page. It signals a thematic break between content, the HTML equivalent of a scene break in a novel.

```html
<p>End of the first section.</p>
<hr>
<p>Start of the second section.</p>
```

`<hr>` is a Void Element. It renders as a horizontal line by default, but like everything else, its exact appearance can be controlled with CSS.

#### Subscript and Superscript: `<sub>` and `<sup>`

Some text needs to sit below or above the normal line of text. This is essential for scientific notation, chemical formulas, mathematical expressions, and footnote references.

**`<sub>`** renders its content below the baseline of the surrounding text.

```html
<p>Water is written as H<sub>2</sub>O in chemistry.</p>
<p>Carbon dioxide is CO<sub>2</sub>.</p>
```

**`<sup>`** renders its content above the baseline.

```html
<p>The speed of light squared is c<sup>2</sup>.</p>
<p>This was first proposed in 1905.<sup>1</sup></p>
```

Both are inline elements. They sit within the surrounding text without breaking the line.

<!-- 
IMAGE: A single rendered paragraph showing both sub and sup in context. The text reads: "H2O is formed when hydrogen combines with oxygen. The formula E=mc2 explains mass-energy equivalence." The "2" in H2O is visually subscript (below the baseline) and the "2" in mc2 is visually superscript (above the baseline). Labels point to each one. Style: clean browser-render style illustration, clear baseline shown as a horizontal guide line, flat and educational.
-->
<div style="display: flex; justify-content: center; padding: 15px 0; width: 100%;">
  <img 
    src="https://s3.ap-south-1.amazonaws.com/static.bytexl.app/uploads/44jnaxga5/content/44qdhnb4j/image07.png" 
    alt="Image Description"
    style="
      width: 70%;
      max-width: 500px; 
      height: auto; 
      border: 4px solid #87CEEB; 
      border-radius: 20px; 
      padding: 10px; 
      background-color: white; 
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    "
  >

### Text Formatting Tags

Text formatting is where HTML lets you go beyond plain text and add meaning to individual words and phrases. HTML provides a set of inline elements specifically for this purpose.

Before covering them, there is an important distinction to understand.

Some formatting tags are **Semantic**: they carry meaning beyond their visual appearance. The Browser and screen readers understand them as meaningful markers.

Other formatting tags are **Visual**: they produce a visual change but carry no inherent meaning. They are presentational only.

This distinction matters because a screen reader that reads a page aloud will treat a `<strong>` tag differently from a `<b>` tag, even though both appear bold visually.

#### `<strong>`: Importance

`<strong>` marks text as important. The Browser renders it bold by default, but its meaning goes beyond appearance. A screen reader may read `<strong>` content with added emphasis in its voice.

```html
<p><strong>Do not share your API key with anyone.</strong></p>
```

Use `<strong>` when the text carries genuine importance or urgency.

#### `<em>`: Stress Emphasis

`<em>` marks text as stressed or emphasised. Browsers render it italic by default. Screen readers may subtly change the tone or emphasis of the reading.

```html
<p>I said the chatbot was <em>surprisingly</em> accurate, not perfect.</p>
```

The emphasis changes the meaning of the sentence. That is exactly what `<em>` is designed for.

#### `<b>`: Visual Bold

`<b>` renders text bold but carries no semantic importance. Use it when you want visual boldness without implying that the text is critically important.

```html
<p>The model options are <b>GPT-3.5</b>, <b>GPT-4</b>, and <b>GPT-4o</b>.</p>
```

This is bold for clarity and scannability, not urgency. `<b>` is the right choice here.

#### `<i>`: Visual Italic

`<i>` renders text italic without implying stressed emphasis. It is conventionally used for technical terms, foreign-language words, titles of creative works, and internal thoughts.

```html
<p>This model uses a <i>transformer</i> architecture.</p>
<p>The French phrase <i>je ne sais quoi</i> means a certain indescribable quality.</p>
```

#### `<mark>`: Highlighted Text

`<mark>` highlights text, the HTML equivalent of dragging a yellow marker across text in a textbook. It is used to indicate text that is relevant in the current context, such as a search result match.

```html
<p>Your search for "API" matched the word <mark>API</mark> in this article.</p>
```

#### `<del>` and `<ins>`: Deleted and Inserted Text

`<del>` renders text with a strikethrough, indicating that it has been removed or is no longer valid.

`<ins>` renders text with an underline, indicating that it has been added or inserted.

```html
<p>The response time is <del>500ms</del> <ins>120ms</ins> after optimisation.</p>
```

These are commonly used in version history, changelogs, and document editing tools.

#### `<small>`: Fine Print

`<small>` renders text at a smaller font size. It is used for disclaimers, legal footnotes, copyright notices, and any secondary or fine-print information.

```html
<p><small>Responses are generated by AI and may not always be accurate.</small></p>
```

<!-- 
IMAGE: A clean visual reference card showing all text formatting tags. Each row shows the tag name on the left, a rendered example in the middle, and whether it is "Semantic" or "Visual" on the right. Rows: strong (bold, Semantic), em (italic, Semantic), b (bold, Visual), i (italic, Visual), mark (yellow highlight, Visual), del (strikethrough, Semantic), ins (underline, Semantic), small (small text, Visual). Style: clean table card, white background, two-column color coding for Semantic vs Visual.
-->
<div style="display: flex; justify-content: center; padding: 15px 0; width: 100%;">
  <img 
    src="https://s3.ap-south-1.amazonaws.com/static.bytexl.app/uploads/44jnaxga5/content/44qdhnb4j/image08.png" 
    alt="Image Description"
    style="
      width: 70%;
      max-width: 500px; 
      height: auto; 
      border: 4px solid #87CEEB; 
      border-radius: 20px; 
      padding: 10px; 
      background-color: white; 
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    "
  >

#### A Note on Screen Readers and CSS

The semantic formatting tags, `<strong>`, `<em>`, `<del>`, `<ins>`, exist first and foremost for meaning. A screen reader interprets them. A search engine reads them. Assistive technologies rely on them. Their visual appearance (bold, italic, strikethrough) is just the default Browser rendering.

When we reach the CSS module, you will see that there are no limits to how text can be styled. You can make text any colour, any size, any weight, any spacing, with or without semantic tags. CSS separates the visual from the structural completely. But the semantic meaning that `<strong>` and `<em>` carry is something only HTML provides. So use the right tag for the right reason.

### HTML Entities: When Characters Need Special Handling

#### The Need

Consider this situation. You are writing an article about HTML and you want to display the less-than sign on the page:

```
The syntax for a tag is: < tagname >
```

If you type a `<` directly into your HTML, the Browser reads it as the beginning of a Tag. Everything that follows gets interpreted as HTML syntax, not as visible text. Your content breaks.

Similarly, the `&` character has special meaning in HTML. The `>` character can also confuse the Browser in certain contexts.

And then there are characters that simply are not on any standard keyboard: copyright symbols, trademark signs, arrows, accented letters.

HTML Entities solve both problems: reserved characters that need to be displayed literally, and special characters that have no keyboard key.

#### How Entities Work

An HTML Entity is a short code that represents a single character. Every Entity starts with an ampersand `&` and ends with a semicolon `;`.

```
&name;
```

For example: `&lt;` displays as `<`. `&amp;` displays as `&`. `&copy;` displays as ©.

Think of it like a pronunciation guide. If you are writing a script and a character needs to say the word "ampersand" out loud, you write it so the actor knows: say the actual word, do not treat it as a symbol. HTML Entities work the same way: they tell the Browser "display this character, do not interpret it."

Here are the ones you will actually use:

| Entity Code | Displays As | When to Use |
|---|---|---|
| `&lt;` | < | Displaying HTML code as visible text |
| `&gt;` | > | Displaying HTML code as visible text |
| `&amp;` | & | Displaying a literal ampersand |
| `&nbsp;` | (non-breaking space) | A space that will never wrap onto a new line |
| `&copy;` | © | Copyright notices in footers |
| `&trade;` | ™ | Trademark symbols |
| `&reg;` | ® | Registered trademark symbols |
| `&euro;` | € | Euro currency symbol |

The `&nbsp;` entity deserves a mention. It creates a space that the Browser will not collapse or wrap across lines. Useful for situations like `10&nbsp;km` where you never want "10" and "km" to be separated onto different lines.

You do not need to memorise all HTML Entities. The ones above cover the vast majority of practical cases. When you need an obscure symbol, a quick search for "HTML entity [character name]" will give you the code instantly.

### Let's Define Things Now...

> **Heading Elements (h1 through h6):** Block-level elements that define a hierarchy of headings on a page. h1 is the most important and should appear only once per page. Each level nests logically within the one above it.

> **Paragraph Element (p):** A block-level element that groups a section of text into a readable paragraph. The Browser adds default spacing above and below each paragraph.

> **Line Break (br):** A Void Element that inserts a line break within the current block of content, without creating a new paragraph.

> **Horizontal Rule (hr):** A Void Element that renders a horizontal dividing line, signaling a thematic break between sections of content.

> **Subscript (sub):** An inline element that renders its content below the normal text baseline. Used for chemical formulas and mathematical notation.

> **Superscript (sup):** An inline element that renders its content above the normal text baseline. Used for exponents, footnote references, and mathematical notation.

> **Semantic Formatting Tags:** Formatting elements that carry meaning beyond their visual appearance: strong (important), em (stressed), del (removed), ins (inserted). Screen readers and search engines interpret these.

> **Visual Formatting Tags:** Formatting elements that produce a visual effect but carry no inherent meaning: b (bold), i (italic), mark (highlighted), small (smaller text).

> **HTML Entity:** A special code starting with & and ending with ; that represents a single character in HTML. Used for reserved characters like < and &, and for symbols not available on a keyboard.

### Activity: Formatting a Text Block

Below is a block of plain text. Mark it up using appropriate HTML text elements. There is no single correct answer, but every formatting decision should have a reason behind it.

```
AI Chat Assistant Documentation

Introduction
Welcome to the Aiden AI documentation. This guide explains how to use the chatbot effectively. Important: Never share your API key with anyone.

How It Works
Aiden uses a transformer architecture to process your messages. The response time is typically under 200ms, down from the original 500ms after recent optimisations. The model currently runs on GPT-4o.

Chemical Formula Note
Water (H2O) and carbon dioxide (CO2) are used in internal examples to test subscript rendering.

Copyright Notice
All responses are generated by artificial intelligence and may not always be accurate. Use at your own discretion.
Copyright 2024 Aiden AI.
```

Your markup should use: at least three heading levels, paragraphs, at least one `<strong>`, at least one `<em>`, a `<del>` and `<ins>` pair, `<sub>` for the chemical formulas, `<small>` for the copyright, and the `&copy;` entity.

## What's Coming Next...

You can now structure and format text in HTML. That is a significant part of what building for the Web involves.

But think about how humans actually process information. A page of flowing paragraphs, however well-formatted, can only do so much. When information naturally comes in distinct, enumerable pieces, like a list of features, a sequence of steps, or a set of definitions, the most effective way to present it is as a list.

Lists are one of the most used structures in all of Web Development. Navigation menus are lists. Blog post feeds are lists. Feature comparisons are lists. The next article covers ordered lists, unordered lists, and description lists: what they are, when to use each one, and how to nest them to represent more complex hierarchies.
