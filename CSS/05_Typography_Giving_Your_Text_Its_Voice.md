## Introduction

Look at these two websites side by side.

<!-- 
IMAGE: A side-by-side screenshot comparison. Left side: a clean, modern blog or news website (like Medium or The Verge) showing an article page with a readable sans-serif font, generous line spacing, a clear heading hierarchy, and comfortable paragraph width. Right side: a Paris fashion brand website (like Vogue, Celine, or a luxury fashion house) showing dramatically large serif headings, tight letter spacing, sparing use of text, and an air of editorial sophistication. Both use text as their primary visual element but feel entirely different. Caption: "Same language. Same screen. Entirely different personalities." Style: actual representative screenshots or high-fidelity mockups side by side, with no annotations.
-->

Both pages are made of text. Both use CSS. Both work on the same browser. But they feel completely different, and not because of their colors or layouts. The differentiating factor is typography.

The blog is approachable and readable: a rounded sans-serif font designed for long-form reading, generous space between lines, paragraphs at a width that feels natural for the eye to scan. It says: this is a place to learn something, comfortably.

The fashion site is architectural and editorial: a refined serif font with tight spacing and restrained use of words. It says: this is a world of precision and taste. Text is used sparingly, like a label on a museum exhibit.

Typography is how text communicates personality before the reader processes meaning. A courier monospace font on a terminal-green background says hacker tool. A cursive handwritten font says personal invitation. A heavy, geometric sans-serif says confident technology brand. The same words in different typefaces, sizes, and spacing carry completely different emotional weight.

This article covers every CSS property that controls how text looks. Together, they form the typographic vocabulary you will use on every page you build.

### font-family

#### What It Does

`font-family` sets the typeface used to render the element's text. It accepts a comma-separated list of font names, tried in order from left to right.

```css
body {
  font-family: 'Inter', 'Helvetica Neue', Arial, sans-serif;
}
```

The browser looks for the first font in the list. If it is not available on the user's device, it tries the next, and so on. The last value should always be a generic family keyword (`serif`, `sans-serif`, `monospace`, `cursive`) as an absolute fallback that the browser can always satisfy.

#### The Built-In Fonts

A small set of fonts are available on virtually every device without needing to load anything. These are called web-safe fonts:

- **Arial, Helvetica**: neutral, clean sans-serif, used widely in interfaces
- **Georgia**: a readable serif designed for screens
- **Times New Roman**: the classic serif, commonly associated with print and academia
- **Courier New**: a monospace font, used for code and terminal aesthetics
- **Verdana, Trebuchet MS**: screen-optimized sans-serifs

Using web-safe fonts means no extra network request, which is good for performance. The trade-off is a very limited typographic palette.

#### Loading Web Fonts with Google Fonts

For custom typography, Google Fonts is the most widely used free service. It hosts hundreds of high-quality fonts that the browser downloads when a page loads.

**Step 1:** Go to fonts.google.com, pick a font (such as Inter, DM Sans, or Playfair Display), and select the specific weights you need.

**Step 2:** Add the provided `<link>` tag to the `<head>` of your HTML, before your own stylesheet:

```html
<head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="style.css">
</head>
```

**Step 3:** Use the font in your CSS exactly as you would any other font family:

```css
body {
  font-family: 'Inter', sans-serif;
}
```

The `&display=swap` in the URL tells the browser to show the fallback font immediately and swap to Inter once it has loaded, preventing invisible text during the load. Only request the weights you actually use. Requesting every weight of a font adds unnecessary loading time.

**Best Practice:** Use one or two font families per page at most. A single well-chosen typeface with a range of weights creates more visual cohesion than mixing four different fonts. Most professional interfaces use one typeface for everything.

### font-size

#### What It Does

`font-size` sets the size of the rendered text. As we covered in the units article, `rem` is the preferred unit because it is consistent, does not compound, and scales with the user's browser settings.

```css
:root {
  font-size: 16px;   /* base: 1rem = 16px */
}

h1 { font-size: 2.5rem; }   /* 40px */
h2 { font-size: 2rem; }     /* 32px */
h3 { font-size: 1.5rem; }   /* 24px */
p  { font-size: 1rem; }     /* 16px */
.caption { font-size: 0.875rem; }  /* 14px */
```

**Best Practice:** Establish a type scale. A type scale is a set of font size values with a consistent ratio between them (1.25, 1.333, 1.5 are common ratios). This creates visual harmony across the entire page. Do not set arbitrary sizes for individual elements.

### font-weight

#### What It Does

`font-weight` controls the thickness of the text strokes, commonly called the weight or boldness.

```css
.bot-name {
  font-weight: 700;
}

.message-text {
  font-weight: 400;
}

.section-label {
  font-weight: 600;
}
```

Values can be keywords (`normal` for 400, `bold` for 700) or numeric values from 100 (thin) to 900 (black) in steps of 100. The numeric scale gives more control: 300 is light, 400 is regular, 500 is medium, 600 is semi-bold, 700 is bold, 800 is extra bold, 900 is black.

A critical detail: **a font must actually include the requested weight.** Not every font ships with all nine weights. If you request `font-weight: 300` but the font only has 400 and 700, the browser picks the nearest available weight. When loading from Google Fonts, only the weights you explicitly request are downloaded, so only specify what you will actually use.

**Best Practice:** Most interfaces use two or three weights: regular (400) for body text, semi-bold or bold (600-700) for headings and emphasis, and occasionally light (300) for large display text where heavy weight would feel too dense.

### line-height

#### What It Does

`line-height` controls the vertical space between consecutive lines of text within the same block. It is the CSS equivalent of what designers call leading.

```css
body {
  line-height: 1.6;
}

h1 {
  line-height: 1.15;
}
```

Use unitless values. A unitless value is a multiplier of the current element's font-size: `line-height: 1.6` means the line height is 1.6 times the font size. If the font size changes, the line height scales automatically. Using `px` or `rem` for line-height breaks this relationship.

<!-- 
IMAGE: Two text blocks side by side showing the same paragraph. Left block has line-height: 1.2 (too tight, the letters from adjacent lines visually overlap). Right block has line-height: 1.6 (comfortable, each line has room to breathe). Caption: "Line-height is breathing room. Too tight and lines merge. Too loose and the reader loses the thread." Style: flat vector text illustration, same text content, same font size, clearly different line spacing.
-->

The browser's default `line-height` is approximately 1.2, which is comfortable for short headings but too tight for paragraph text. Long blocks of text with `line-height: 1.2` feel cramped and are tiring to read.

**Best Practice:** Set `line-height: 1.5` to `1.7` for body text and paragraphs. Set `line-height: 1.1` to `1.3` for headings, where tight stacking looks intentional and bold.

### letter-spacing

#### What It Does

`letter-spacing` adds or removes space between individual characters. Designers call this tracking.

```css
.chatbot-name {
  letter-spacing: -0.02em;   /* slightly tighter for large display text */
}

.section-label {
  letter-spacing: 0.1em;     /* wider for small caps labels */
  text-transform: uppercase;
}

.nav-item {
  letter-spacing: 0.05em;
}
```

Positive values add space between characters. Negative values remove it. Using `em` keeps the spacing proportional to the font size.

**Best Practice:** Large headings and display text often benefit from slightly tighter tracking (negative values) because at large sizes, the default spacing can feel loose. Short labels in uppercase benefit from wider tracking because all-caps text becomes harder to read without additional breathing room between letters. Body text generally needs no adjustment.

### word-spacing

#### What It Does

`word-spacing` adds or removes space between words, beyond the natural space character.

```css
.tagline {
  word-spacing: 0.2em;
}
```

This property is rarely used in most designs. The natural space between words is already calibrated by the typeface's designer, and most adjustments make text feel either airy or cramped in unnatural ways. Use it only for specific typographic effects in display contexts.

### text-transform

#### What It Does

`text-transform` changes the capitalization of text without altering the HTML content.

```css
.nav-link {
  text-transform: uppercase;
}

.section-heading {
  text-transform: capitalize;
}

.disclaimer {
  text-transform: lowercase;
}
```

`uppercase` converts all characters to capitals. `capitalize` capitalizes the first letter of each word. `lowercase` converts all characters to lowercase.

The key advantage is separating visual presentation from the actual content. A navigation label that reads "Features" in the HTML can be displayed as "FEATURES" in the interface through CSS alone. If the copy changes or the design changes, only one side needs to be updated. Writing the content in all-caps in the HTML means the text is semantically all-caps as well, which is how screen readers will announce it.

**Best Practice:** Use `text-transform: uppercase` for navigation labels, button text, and small section labels where the all-caps treatment signals UI rather than content. Never write UI labels in all-caps in the HTML itself.

### text-decoration

#### What It Does

`text-decoration` adds decorative lines to text: underline, overline (above the text), and line-through (strikethrough).

```css
/* Remove the default underline from links */
a {
  text-decoration: none;
}

/* Add it back selectively on hover */
a:hover {
  text-decoration: underline;
}

/* Strikethrough for deleted or unavailable items */
.unavailable {
  text-decoration: line-through;
  opacity: 0.5;
}
```

Browser default styles add an underline to all `<a>` elements. In real interfaces, navigation links and buttons built from anchor tags almost always have this removed and replaced with a color or style change on hover instead.

`text-decoration` is actually a shorthand for several sub-properties: `text-decoration-line` (underline, overline, line-through), `text-decoration-color` (any color value), and `text-decoration-style` (solid, dotted, dashed, wavy). These can create subtle, custom decorative effects when needed.

```css
.annotation {
  text-decoration: underline dotted #94a3b8;
}
```

### text-align

#### What It Does

`text-align` controls the horizontal alignment of inline content (text and inline elements) within its block container.

```css
.chatbot-title {
  text-align: center;
}

.message-text {
  text-align: left;
}

.timestamp {
  text-align: right;
}
```

`left` and `right` align text to the respective edge. `center` centers each line within the container. `justify` stretches lines to fill the full container width by distributing space between words.

**Best Practice:** Body text and paragraphs should almost always be `left` aligned (or `right` in right-to-left languages). Centered text works well for short headings, calls-to-action, and display elements but becomes uncomfortable to read at paragraph length. Avoid `justify` on the web: the algorithm for distributing space creates irregular "rivers" of whitespace through paragraphs that disrupt reading flow. Justified text is a print convention that does not translate well to variable-width screens.

<!-- 
IMAGE: A four-panel illustration showing the same three-line text block with each text-align value applied. Left panel: text-align: left (clean left edge, ragged right). Center panel: text-align: center (symmetrically centered, suitable for short content). Right panel: text-align: right (clean right edge). Fourth panel: text-align: justify showing uneven word spacing creating visible "rivers" of white space. Caption: "Justify looks intentional in print. On screen it creates distracting gaps. Left-align is almost always the correct choice for body text." Style: flat vector, four equal panels, clean text illustration.
-->

### Putting It Together: The Chatbot Typography System

Here is what a coherent typography system for the chatbot page looks like with all properties working together:

```css
:root {
  font-size: 16px;
}

body {
  font-family: 'Inter', 'Helvetica Neue', Arial, sans-serif;
  font-size: 1rem;
  font-weight: 400;
  line-height: 1.6;
  color: #f1f5f9;
}

h1 {
  font-size: 2rem;
  font-weight: 700;
  line-height: 1.2;
  letter-spacing: -0.02em;
}

.nav-link {
  font-size: 0.875rem;
  font-weight: 500;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  text-decoration: none;
}

.message-text {
  font-size: 0.9375rem;
  line-height: 1.6;
}

.timestamp {
  font-size: 0.75rem;
  font-weight: 400;
  color: #94a3b8;
}

.sender-name {
  font-size: 0.875rem;
  font-weight: 600;
}
```

Each element has a typographic role, and the system is built entirely from the properties in this article.

### Let's Define Things Now...

> **font-family:** Sets the typeface for an element's text. Accepts a comma-separated fallback list ending in a generic family.

> **Web-Safe Font:** A font available on virtually all devices without loading. A reliable fallback.

> **Google Fonts:** A free hosted font service. Fonts are loaded via a link tag and used in CSS like any other font-family.

> **font-size:** Sets the size of the text. Use rem for consistent, accessible scaling.

> **font-weight:** Controls the thickness of text strokes. Numeric values 100 to 900. Only weights loaded from the font source are available.

> **line-height:** The vertical space between lines. Use unitless values so it scales with font-size automatically. 1.5 to 1.7 for body text.

> **letter-spacing:** Space between individual characters. Positive values spread text apart, negative values tighten it. Use em for font-proportional tracking.

> **word-spacing:** Space between words. Rarely adjusted in practice.

> **text-transform:** Changes capitalization: uppercase, lowercase, capitalize. Separates visual style from HTML content.

> **text-decoration:** Adds lines to text. none removes default underlines from links. underline, line-through, and overline for specific effects.

> **text-align:** Horizontal alignment of text within its container. Left for body text. Center for short display elements. Avoid justify on screen.

### Activity: Type System for the Chatbot

Load Inter from Google Fonts with weights 400, 500, 600, and 700. Apply it to the chatbot body.

Then style the following elements using only the properties in this article:

1. The bot name in the header: large, bold, tight letter spacing
2. Navigation links: small, medium weight, uppercase, no underline, with an underline on :hover
3. Bot message text: comfortable size, 1.6 line height
4. User message text: same, but slightly differentiated weight
5. Timestamps: small, muted, right-aligned within the message row
6. Sender labels: small, semi-bold
7. The settings panel heading: centered, uppercase, wide letter spacing

Open the page after each change. Typography changes are often subtle individually but transformative as a system.

## What's Coming Next...

We can now define the color of any element, its text style, and its size. We have the vocabulary to style a single element as a self-contained unit.

But a webpage is not a collection of isolated elements. Each element exists in relationship to every other: next to it, inside it, above and below it. How much space separates a heading from its paragraph? How far does a sidebar sit from the main content? How wide is a button's clickable area beyond its text?

All of these questions come down to one model: the Box Model. In the next article, we step into how CSS thinks about space, positioning, and the relationship between every element on the page.
