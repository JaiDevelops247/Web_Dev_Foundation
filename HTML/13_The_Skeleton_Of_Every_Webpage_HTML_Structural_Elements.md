## Introduction

In the last article we established what semantic HTML means and listed the structural elements HTML provides to label the distinct regions of a webpage. Now let us explore each one in detail: what it represents, when to use it, how it nests inside others, and where you encounter it on real websites every day.

Before diving into individual elements, let us look at the overall skeleton first. Understanding how these elements nest and relate to each other gives you a mental map that makes every individual element easier to place correctly.

### The Page Skeleton

A well-structured HTML document using semantic elements looks like this:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Aiden AI - Your Intelligent Assistant</title>
  </head>
  <body>

    <header>
      <nav>...</nav>
    </header>

    <main>

      <article>
        <header>...</header>

        <section>...</section>
        <section>...</section>

        <footer>...</footer>
      </article>

      <aside>...</aside>

    </main>

    <footer>...</footer>

  </body>
</html>
```

A few things to notice immediately. `<header>` and `<footer>` appear twice: once at the page level and once inside the `<article>`. This is intentional and valid. `<nav>` sits inside `<header>`. `<aside>` sits alongside `<article>` inside `<main>`. These elements nest like building blocks, each one fitting into a logical position relative to the others.

Keep this skeleton in mind as we go through each element.

### header

#### What It Is

`<header>` represents introductory content for its parent element. When it is a direct child of `<body>`, it is the site-wide page header. When it appears inside an `<article>` or a `<section>`, it is the introductory content for that article or section specifically.

#### When to Use It

Use `<header>` at the page level for the logo, site name, tagline, and top navigation. Use it inside an `<article>` to group the article title, author name, publication date, and category tags.

```html
<!-- Page-level header -->
<header>
  <img src="logo.png" alt="Aiden AI logo">
  <h1>Aiden AI</h1>
  <nav>...</nav>
</header>

<!-- Article-level header -->
<article>
  <header>
    <h2>How AI Chatbots Process Natural Language</h2>
    <p>By <span class="author">Arjun Mehta</span></p>
    <time datetime="2024-11-15">November 15, 2024</time>
  </header>
  ...
</article>
```

<!-- 
IMAGE: A screenshot of GitHub's homepage header. The dark top bar contains the GitHub logo on the left, the main navigation links in the centre (Product, Solutions, Resources, Open Source, Pricing), and Sign In / Sign Up buttons on the right. A red bracket on the left labels the entire bar as "header". Inside it, the navigation links are bracketed separately and labeled "nav". Caption: "Every website's header is the same structure. Logo, identity, and navigation." Style: actual webpage screenshot with minimal annotation brackets and labels in red.
-->

### nav

#### What It Is

`<nav>` marks a block of major navigation links. It communicates to browsers, screen readers, and search engines that this group of links is the site's navigation structure.

#### When to Use It

Use `<nav>` for primary site navigation (top menu), secondary navigation (sidebar links), breadcrumb trails, and pagination controls. Do not wrap every group of links in a `<nav>`. A footer that contains a few policy links does not need a `<nav>`. Use it for navigation that is significant enough to warrant landmark status on the page.

```html
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/features">Features</a></li>
    <li><a href="/pricing">Pricing</a></li>
    <li><a href="/contact">Contact</a></li>
  </ul>
</nav>
```

A nav's links are almost always inside a `<ul>`. Navigation menus are, structurally, unordered lists: a collection of links with no inherent ranking. The CSS removes the bullet points and arranges them horizontally. The underlying HTML remains semantically a list.

Screen readers offer users the ability to jump directly to the `<nav>` landmark or to skip it entirely. This is how users who navigate by keyboard avoid having to tab through every navigation link on every page load.

<!-- 
IMAGE: A screenshot of Wikipedia's left sidebar showing the "Contents" section with a list of article sections as links. The whole sidebar panel is bracketed and labeled "nav". Below it, a second screenshot shows Zomato's top navigation bar with category links. Both are labeled to show that nav can be a top bar or a sidebar depending on layout. Caption: "nav can be horizontal or vertical. Its position is irrelevant. What matters is that it is a major navigation block." Style: two-panel screenshot comparison with annotation brackets.
-->

### main

#### What It Is

`<main>` marks the dominant, unique content of the page. It is the area that changes from page to page, excluding repeated content like headers, footers, and sidebars.

#### When to Use It

There must be exactly one `<main>` per page. It should not be nested inside `<header>`, `<footer>`, `<aside>`, `<article>`, or `<nav>`. It is a direct child of `<body>`.

```html
<body>
  <header>...</header>

  <main>
    <!-- Everything that is unique to this page lives here -->
  </main>

  <footer>...</footer>
</body>
```

Screen readers expose `<main>` as a landmark. A visually impaired user can press a single keyboard shortcut to jump directly into the main content, skipping the header and navigation entirely. On a page with a lot of navigation, this is the difference between a usable and an unusable experience.

### article

#### What It Is

`<article>` marks a self-contained, independently distributable piece of content. The test for whether to use `<article>` is this: if you extracted just this element and read it somewhere else, would it still make complete sense on its own? If yes, it is an `<article>`.

#### When to Use It

Blog posts, news stories, product reviews, forum posts, user comments, individual chat messages in a conversation thread, and social media posts are all articles. Each one makes sense on its own, regardless of the page it appears on.

```html
<main>
  <article>
    <header>
      <h2>Getting Started with the OpenAI API</h2>
      <time datetime="2024-10-01">October 1, 2024</time>
    </header>

    <p>The OpenAI API gives you programmatic access to...</p>

    <section>
      <h3>Authentication</h3>
      <p>Every request requires an API key in the header...</p>
    </section>

    <section>
      <h3>Making Your First Request</h3>
      <p>Start with a simple completion endpoint...</p>
    </section>

    <footer>
      <p>Tags: API, OpenAI, Getting Started</p>
    </footer>
  </article>
</main>
```

An `<article>` can contain its own `<header>`, `<section>` elements, and `<footer>`. This is nested semantic structure: each layer uses the right element for its role.

<!-- 
IMAGE: A screenshot of a Medium blog post page. The article card showing the title, author photo, author name, publication date, and estimated read time is bracketed and labeled "article header". The body text below it is labeled "article content". The response/clap section at the bottom is labeled "article footer". To the right, a second example shows a single tweet with a similar breakdown: user info at top as header, tweet text as content, like/retweet/share buttons as footer. Caption: "An article contains its own header, content, and footer. It is a complete, standalone unit." Style: two-panel screenshot with annotation brackets.
-->

### section

#### What It Is

`<section>` marks a thematic grouping of content within a larger document or article. A section is a named chapter of something bigger. It almost always contains a heading.

#### When to Use It

Use `<section>` to divide a long page or a long article into distinct named parts. The product page for a SaaS tool is a clear example: a hero section, a features section, a pricing section, a testimonials section, a call-to-action section. Each is a `<section>`.

```html
<main>
  <section>
    <h2>Features</h2>
    <p>Aiden AI offers natural language understanding, multi-language support...</p>
  </section>

  <section>
    <h2>Pricing</h2>
    <p>Choose the plan that fits your team...</p>
  </section>

  <section>
    <h2>Testimonials</h2>
    <p>Hear from teams already using Aiden AI...</p>
  </section>
</main>
```

`<section>` should almost always have a heading inside it. If a grouping has no heading and no distinct thematic identity, it is probably not a `<section>`. It is a `<div>`.

<!-- 
IMAGE: A screenshot of Apple's iPhone product page scrolled to show three sections visible partially. Each major section (Design, Camera, Performance) has a large full-width heading at the top and its own visual treatment. Brackets surround each section with the label "section". Caption: "Apple's product pages are a textbook example of section usage. Each topic is its own named, thematic unit." Style: screenshot with annotation brackets and labels, clean educational overlay.
-->

### aside

#### What It Is

`<aside>` marks content that is related to the surrounding content but not part of it. Content that, if removed, would not break the flow or meaning of the main content.

#### When to Use It

Sidebars on news websites with trending articles and advertisements. Tip boxes and callout notes inside a documentation article. The Wikipedia information box on the right side of any article page. A "Related Articles" panel at the end of a blog post. A glossary definition that expands on a term used in the main text.

```html
<main>
  <article>
    <p>Large language models process text using tokens. Each token is approximately four characters...</p>

    <aside>
      <h3>What is a Token?</h3>
      <p>In the context of AI models, a token is the basic unit of text the model reads and generates. "chatbot" is one token. "I am learning HTML" is four tokens.</p>
    </aside>

    <p>The number of tokens a model can handle at once is called the context window...</p>
  </article>
</main>
```

<!-- 
IMAGE: A screenshot of a Wikipedia article page showing the main article text on the left taking up most of the width, and the information box (infobox) on the right showing key facts, an image, and structured data. The infobox is bracketed and labeled "aside". Caption: "Wikipedia's infobox is the canonical example of aside: related to the article, but not part of its narrative flow." Style: screenshot with annotation bracket on the aside/infobox area.
-->

### footer

#### What It Is

`<footer>` marks the closing content of its parent element. At the page level, it is the site-wide footer. Inside an `<article>`, it is the article's closing metadata: tags, author biography, share buttons, and related links.

#### When to Use It

Use `<footer>` at the page level for copyright notices, secondary links (Privacy Policy, Terms of Service, Sitemap), social media icons, and contact information. Use it inside articles for post metadata that appears after the content.

```html
<!-- Page footer -->
<footer>
  <p>&copy; 2024 Aiden AI. All rights reserved.</p>
  <nav>
    <a href="/privacy">Privacy Policy</a>
    <a href="/terms">Terms of Service</a>
  </nav>
</footer>
```

Notice a `<nav>` inside a `<footer>` is perfectly valid for a group of policy and site links significant enough to be a navigation landmark.

### figure and figcaption

#### What They Are

`<figure>` marks self-contained content that is referenced from the main content but could be moved to an appendix or a different position without breaking the reading flow. `<figcaption>` provides a visible caption for that figure, semantically linked to it.

#### When to Use Them

Images with captions, charts and graphs inside articles, code blocks that are referenced by the surrounding text, and diagrams pulled from the main content flow are all `<figure>` candidates.

```html
<figure>
  <img src="token-chart.png" alt="A bar chart showing token usage across three GPT models">
  <figcaption>Figure 1: Token consumption per API call across GPT-3.5, GPT-4, and GPT-4o.</figcaption>
</figure>
```

`<figcaption>` can be the first or last child of `<figure>`. The image's `alt` attribute still describes the image for screen readers. The `<figcaption>` is the visible caption for all readers. Both serve different purposes and should both be present.

### time

#### What It Is

`<time>` marks a date, time, or duration value. The `datetime` attribute provides a machine-readable version of the value that browsers, search engines, and calendar applications can parse and use.

```html
<p>This article was published on <time datetime="2024-11-15">November 15, 2024</time>.</p>

<p>Our next webinar is at <time datetime="2024-12-10T18:30">6:30 PM on December 10</time>.</p>
```

The visible text between the tags can be in any human-friendly format. The `datetime` attribute carries the precise, standardised format. Search engines use `<time>` to understand publication dates and display them in search results, and some browsers use it to offer "Add to Calendar" features.

### details and summary

#### What They Are

`<details>` and `<summary>` create a native browser disclosure widget. Clicking the `<summary>` text toggles the rest of the `<details>` content open and closed, with no JavaScript required.

```html
<details>
  <summary>What AI model does Aiden AI use?</summary>
  <p>Aiden AI uses GPT-4o by default, with options to switch to GPT-3.5 Turbo for faster, cost-efficient responses.</p>
</details>
```

FAQ sections, expandable footnotes, and optional configuration panels are all natural uses. The browser handles the open/close toggle natively. The `open` attribute on `<details>` can pre-expand it when the page loads.

<!-- 
IMAGE: A two-state illustration of a details/summary element. Left state shows the summary text "What AI model does Aiden AI use?" with a small triangle arrow pointing right, labeled "Collapsed: only summary visible." Right state shows the same summary with the triangle pointing down, and the answer paragraph visible below it, labeled "Expanded: full content revealed." Caption: "A native browser accordion with zero JavaScript. summary is always visible. Everything else inside details toggles." Style: flat vector, two-panel side-by-side, clean browser-render illustration.
-->

### Common Confusion Points to Avoid

These are the mistakes that trip up developers most often when working with structural semantic elements.

**section vs article vs div**: Ask the self-contained question first. Can this content make complete sense on its own, outside this page? If yes, use `<article>`. If it is a thematic chapter of something larger and has a heading, use `<section>`. If it is just a layout container with no semantic role, use `<div>`.

**section without a heading**: A `<section>` without a heading is almost always a `<div>`. The heading is what gives a section its thematic identity. If you cannot name what a section is about, it is not a section.

**header and footer are not page-exclusive**: Both can appear inside `<article>` and `<section>` elements. The page-level `<header>` and `<footer>` are those that are direct children of `<body>`. Every other occurrence is scoped to its parent element.

**aside is about relationship, not position**: `<aside>` is often rendered as a visual sidebar, but semantic meaning is about the content's relationship to the main content, not where it sits on the screen. A tip box in the middle of an article is semantically an `<aside>` even if it is not positioned to the side.

**nav is not for every group of links**: Footer policy links, breadcrumb links in a post, and tag clouds may not warrant a `<nav>`. Reserve it for primary and secondary navigation structures that are major landmarks on the page.

**one main per page**: A page with two `<main>` elements is invalid HTML. There is exactly one primary content area per document.

### Let's Define Things Now...

> **header:** Introductory content for a page or its parent section or article. At the page level: logo, site name, nav. At the article level: title, author, date.

> **nav:** A block of major navigation links. Used for primary site navigation, secondary menus, and breadcrumbs. Not every group of links.

> **main:** The primary, unique content of the page. Exactly one per document. Direct child of body.

> **article:** Self-contained, independently distributable content. Blog posts, news stories, comments, product cards. Passes the "makes sense on its own" test.

> **section:** A thematically grouped portion of a larger document or article. Almost always has a heading. Not a styling wrapper.

> **aside:** Content related to but not part of the surrounding content. Sidebars, tip boxes, related panels, pull quotes.

> **footer:** Closing content for a page or its parent section or article. Copyright, policy links, post metadata.

> **figure / figcaption:** Self-contained referenced content (image, chart, diagram, code) with a visible, semantically linked caption.

> **time:** A date or time value with a machine-readable datetime attribute for browsers, search engines, and calendar tools.

> **details / summary:** A native collapsible disclosure widget. summary is always visible. The rest of details toggles open and closed on click.

### Activity: Restructure the Chatbot Page

Take the HTML you have built for the chatbot project and restructure it using semantic elements:

1. Wrap the logo, chatbot name, and navigation links in a `<header>` containing a `<nav>`
2. Wrap the entire chat interface area in `<main>`
3. Wrap each individual chat message (bot and user) in an `<article>`
4. Inside each message article, use a `<header>` for the sender name and timestamp, and `<p>` for the message text
5. If you have a settings panel alongside the chat, wrap it in `<aside>`
6. Add a `<footer>` with the chatbot's version number, a privacy note, and any relevant links
7. Wrap the hero or intro section in a `<section>` with a heading

Open DevTools after making these changes. In the Accessibility tab or the Elements panel, observe that the browser now recognises these regions as landmarks. Screen reader software would present these landmarks in a navigation menu to users.

## What's Coming Next...

We are close to the end of our HTML journey.

The structural elements we covered in this article give the page its skeleton. They do not change how the page looks, but they transform how intelligently the browser, search engine, and assistive technology can understand and work with it.

The last piece of the HTML module is about optimisation: two specific areas where small, deliberate choices in your HTML have large consequences. The first is Search Engine Optimisation, the set of HTML signals that determine how and where your page appears in search results. The second is Accessibility, the practices that ensure your page works for users of every ability, including those who cannot see the screen, cannot use a mouse, or use assistive technologies to navigate.

Neither topic changes what a page looks like. Both are non-negotiable for any website that real people will use.
