## Introduction

Everything we have covered so far produces something the user can see: headings, paragraphs, lists, links, images, tables, forms, video, and audio. Open the browser, load the page, and the content is there.

But a webpage has audiences beyond the person sitting in front of the screen.

The browser itself reads your HTML to understand how the page is structured and which parts are important. Search engines crawl through your HTML to decide how to rank and describe your page in results. Screen readers used by visually impaired users navigate through your HTML to read content aloud in a meaningful sequence. Automated tools and crawlers parse your HTML to extract information.

For all of these audiences, what something looks like on screen is irrelevant. What matters is what the HTML communicates about the meaning and role of each piece of content.

This is where semantics enters the picture. And it is where many developers, even experienced ones, fall short.

### What Does Semantic Mean?

The word semantic comes from the Greek word for meaning. In language, semantics is the study of what words mean, not just how they sound or look. A sentence can be grammatically correct but semantically empty. It can be structured perfectly yet communicate nothing.

HTML works the same way. You can structure a page with perfectly valid HTML that renders exactly as intended on screen, but communicates nothing meaningful to the browser, search engine, or screen reader about what any section of the page actually is.

Imagine walking into a large building for the first time. In one version of this building, every room looks identical: same door, same size, no labels anywhere. You have to open each door and look inside to figure out whether it is the kitchen, the conference room, the storage closet, or the main office. Everything functions, but navigation is slow, confusing, and inefficient.

In a second version of the same building, every room has a clear sign on the door: "Reception," "Server Room," "Meeting Room A," "Cafeteria." You know exactly where to go before you open any door. A new visitor, a delivery person, even a fire department emergency responder can navigate the building intelligently without ever having been there before.

Your HTML page is that building. The elements you choose are the signs on the doors. Semantic HTML is the version where every section is labeled so that anyone, or any system, entering the building knows exactly what each part is and how to work with it.

### Looking Back at What We Already Know

Before introducing new elements, consider the ones we have already used. Some of them already carry meaning about the type of content they contain. Some do not.

`<h1>` through `<h6>` tell the browser this is a heading, and what level of importance it holds. `<p>` tells the browser this is a paragraph of prose. `<strong>` communicates that the text inside is important. `<em>` communicates that it requires emphasis. `<a>` tells the browser and search engines this is a navigational link to another resource. `<ul>` and `<ol>` communicate that what follows is a list of items. `<table>` communicates that the data inside is organised in rows and columns. `<form>` communicates that this section collects input from the user. `<img>`, `<video>`, and `<audio>` communicate the type of media being embedded.

Every one of those elements tells the browser something about the nature of its content. They carry meaning beyond their visual output.

Now look at the two elements we introduced for grouping: `<div>` and `<span>`. A `<div>` says: I am a block container. That is all. It communicates nothing about what it holds. Is it a navigation bar? A product card? A message bubble? An advertisement? The `<div>` has no idea, and neither does anything reading the HTML. The same applies to `<span>`: an inline container with no information about the nature of its content.

This distinction gives us two precise categories.

**Semantic elements** are elements that carry inherent meaning about the role or nature of their content. Any browser, search engine, or assistive technology reading the HTML knows what that element is for without needing to look at what is inside it.

**Non-semantic elements** are elements that group or wrap content purely for structural, styling, or scripting purposes. They communicate nothing about what they contain. The content inside has to speak for itself.

### A Webpage Already Has Sections. HTML Should Reflect Them.

Look at any real website right now, not as a developer, but as a regular user.

At the top, there is a logo, a site name, and possibly a tagline. That is a header. Below it, or sometimes inside it, is a row of navigation links: Home, About, Products, Blog, Contact. That is a navigation bar. The bulk of the page is the main content: articles, product listings, a video, a blog post. Off to the side on a news site there might be trending topics or advertisements. That is a sidebar. And at the very bottom, copyright information, social links, additional navigation, a privacy policy link. That is a footer.

These sections exist on nearly every website you visit. They are so universally recognizable that any user, anywhere, knows immediately which part of a page is the navigation and which part is the footer.

But in the HTML we have written so far, none of those sections have names. They are all `<div>` elements. The navigation is a `<div>`. The footer is a `<div>`. The main content is a `<div>`. Every section is identical from the HTML's perspective, even though they play entirely different roles on the page.

HTML5 introduced a set of elements that fix exactly this. They are structural elements that work like `<div>` in every way, grouping content and applying no default visual changes, except that each one has a specific name that communicates what it contains. The browser, screen reader, and search engine can now read your HTML and immediately understand the architecture of the page.

### Semantic Structural Elements

Here are the primary semantic elements that define page-level sections. We will explore each in detail in the next article. For now, read each as a label: a meaningful sign on a room's door.

`<header>` marks the introductory content of a page or a section. Typically contains the site logo, site name, and a tagline.

`<nav>` marks a group of navigation links. Screen readers use this to let users jump directly to or skip over navigation. Search engines recognise it as the site's link structure.

`<main>` marks the primary, unique content of the page. There should be exactly one `<main>` on any given page. Assistive technologies use it to let users jump directly to what the page is actually about, skipping headers and navigation.

`<section>` marks a thematically grouped portion of content, typically one that could stand as its own named part of the page: a features section, a testimonials section, a pricing section.

`<article>` marks a self-contained, independently distributable piece of content: a blog post, a news story, a product listing, a forum post, or, in our project, a single chat message in the conversation. Something that would still make complete sense if you lifted it out of the page and read it elsewhere.

`<aside>` marks content that is tangentially related to the main content but not part of it: a sidebar, a tip box, a related articles panel, an advertisement, a pull quote.

`<footer>` marks the closing content of a page or section. Typically contains copyright information, secondary navigation links, contact details, and legal notices.

Beyond these structural ones, HTML5 added several semantic elements for specific types of content:

`<figure>` marks self-contained content that is referenced from the main content, typically an image, diagram, or code block. `<figcaption>` provides a caption for that figure, tied to it semantically.

`<time>` marks a date or time value, with a `datetime` attribute that provides a machine-readable format.

`<address>` marks contact information for the nearest `<article>` or the overall page.

`<details>` and `<summary>` create a native expand-and-collapse disclosure widget: `<summary>` is the visible heading that can be clicked, and `<details>` wraps the content that toggles open and closed.

`<mark>` marks text that is highlighted for reference purposes, like a search result with the matching term highlighted.

<!-- 
IMAGE: A webpage mockup of a news website broken into clearly labeled regions. The top band is outlined and labeled "header". Inside it, the row of navigation links is outlined and labeled "nav". The wide column of articles in the centre is outlined and labeled "main". Inside main, one article card is outlined and labeled "article", and a group of articles on a theme is outlined and labeled "section". The narrow right column is outlined and labeled "aside". The bottom band is outlined and labeled "footer". Style: flat vector, clean wireframe-style with coloured borders for each region, each label in a matching colour, educational layout.
-->

### Non-Semantic Elements: Still Useful, Just Contextless

With all these semantic elements available, `<div>` and `<span>` have not become obsolete. They remain the right choice for two situations.

The first is when a group of elements has no semantic identity. The container that positions the avatar and username side by side inside a message card is not a header, a section, an article, or a nav. It is just a layout container for those two elements. A `<div>` with a descriptive `class` name is exactly right here.

The second is when CSS or JavaScript needs a handle on a specific element or group that does not correspond to any semantic concept. A `<span>` wrapping a character count inside a paragraph, or a `<div>` wrapping a group of elements that animate together, are valid uses.

The rule of thumb: reach for a semantic element first. If no semantic element fits, use a `<div>` or `<span>` with a meaningful `class` name.

### Let's Define Things Now...

> **Semantics (in HTML):** The meaning conveyed by the choice of HTML element, beyond its visual output. A semantic element communicates what its content is, not just how it should look.

> **Semantic Element:** An HTML element that carries inherent meaning about the role or nature of its content. Examples: `<h1>`, `<p>`, `<strong>`, `<nav>`, `<article>`, `<footer>`.

> **Non-Semantic Element:** An HTML element that groups or wraps content without communicating anything about its nature. Examples: `<div>`, `<span>`.

> **header:** Marks introductory or heading content for a page or section.

> **nav:** Marks a set of navigation links.

> **main:** Marks the primary content of the page. One per page.

> **section:** Marks a thematically grouped portion of content within the page.

> **article:** Marks a self-contained, independently distributable piece of content.

> **aside:** Marks content tangentially related to the surrounding content.

> **footer:** Marks closing content for a page or section.

> **figure / figcaption:** Mark self-contained referenced content and its caption.

> **time:** Marks a date or time value with a machine-readable `datetime` attribute.

> **details / summary:** Create a native collapsible disclosure widget.

### What's Coming Next...

Semantic elements do not change a single pixel of what the user sees. In the browser, a `<nav>` and a `<div>` with the same class look identical. A `<footer>` and a `<div>` at the bottom of the page look identical.

But to the browser, the screen reader, and the search engine, they are entirely different. Choosing semantic elements is not optional. It is what separates a page that is merely rendered from a page that is understood.

These elements are not something to add later as an afterthought when the page "looks right." They are the skeleton you build first, and every other element lives inside them.

In the next article, we will go through each structural semantic element one by one: what it is for, when to use it, when not to, and how the full skeleton of a real webpage comes together using these as the foundation.
