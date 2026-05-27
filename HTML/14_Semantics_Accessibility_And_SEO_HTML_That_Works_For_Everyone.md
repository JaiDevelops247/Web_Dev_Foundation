## Introduction

Over the last two articles, we established what semantic HTML is and mapped every structural element that gives a webpage its skeleton. We kept returning to a central idea: these choices do not change what the user sees. The page looks identical with a `<div>` or a `<nav>`.

So who exactly benefits?

Three distinct audiences read your HTML beyond the human in front of the screen. The browser, which uses your structure to understand what the page is about. Screen readers and assistive technologies, which use your structure to navigate the page on behalf of users who cannot see it. And search engines, which use your structure to decide how to rank and describe your page in results.

This article is about those three beneficiaries and the specific HTML choices that serve each of them well. Two of them, Accessibility and SEO, have their own fields of practice. Here we cover the HTML layer of each: the signals you embed directly in your markup that lay the foundation for both.

### Accessibility: Building for Every User

#### Who Uses Accessible Websites

Accessibility is the practice of building websites that work for users of every ability. It is easy to assume this means a narrow audience, but the numbers are significant. Roughly one in seven people globally lives with some form of disability. That includes visual impairment, blindness, colour blindness, motor impairment, hearing loss, and cognitive differences.

Beyond permanent disabilities, situational limitations apply to everyone at some point. A person driving uses voice control. A person in bright sunlight cannot read low-contrast text. A person with a broken hand navigates by keyboard. A person on a slow connection relies on text alternatives when images fail to load. Accessible design improves the experience for all of these users, not just those with permanent impairments.

There is also a legal dimension. Many countries have digital accessibility laws and standards (such as WCAG, the Web Content Accessibility Guidelines) that apply to public-facing websites. Building accessibly from the start is significantly easier than retrofitting it later.

#### How Screen Readers Use Your HTML

A screen reader is software that reads webpage content aloud to users who cannot see the screen. Popular screen readers include NVDA and JAWS on Windows, VoiceOver on macOS and iOS, and TalkBack on Android.

When a screen reader loads a page, it does not see pixels. It reads the HTML. It builds a list of landmarks, which are the semantic regions of the page: the `<header>`, `<nav>`, `<main>`, `<aside>`, and `<footer>`. A screen reader user can pull up this landmark list and jump directly to any region without listening to everything in between. A `<div>` with `class="header"` does not appear in this list. A `<header>` element does.

Screen readers also navigate by heading. A user can request a list of all headings on the page and use them to skim the page structure, exactly as a sighted user visually skims headings and subheadings. If your heading hierarchy skips levels or uses headings for visual styling rather than content structure, this navigation breaks.

This is why semantic HTML is inseparable from accessibility. The correct element is the correct semantic signal. No amount of CSS class naming substitutes for it.

#### ARIA: When HTML Alone Is Not Enough

ARIA stands for Accessible Rich Internet Applications. It is a set of HTML attributes that extend the semantic information available to assistive technologies, covering scenarios where native HTML elements do not express enough meaning on their own.

The most important rule about ARIA is when not to use it: never use ARIA to replace what a native HTML element already communicates. A `<button>` element already has an implicit role of "button." A `<nav>` element already tells the screen reader it is a navigation landmark. Adding ARIA to these adds nothing and increases complexity. Use native HTML first. Reach for ARIA only when native HTML cannot express the required meaning.

Three ARIA attributes you will use regularly:

**`aria-label`** provides an accessible name for an element that has no visible text label. A send button with only an icon and no visible text is meaningless to a screen reader. Adding `aria-label` gives it a spoken name.

```html
<button type="submit" aria-label="Send message">
  <img src="send-icon.svg" alt="">
</button>
```

**`aria-live`** tells the screen reader to watch a specific region of the page and announce changes to it automatically, without the user navigating to it. This is critical for dynamic content: chat messages, notifications, error alerts, and live search results that appear after the user has already moved their focus elsewhere.

```html
<ul role="log" aria-live="polite">
  <!-- New bot messages added here by JavaScript will be announced automatically -->
</ul>
```

`aria-live="polite"` waits for the user to finish their current action before announcing. `aria-live="assertive"` interrupts immediately. Use `polite` for most cases. Reserve `assertive` for genuine urgent alerts.

**`role`** explicitly declares the semantic role of an element when HTML alone does not express it. `role="log"` on the chat message list tells the screen reader that this region is a running log of messages, which it handles with specific behaviour.

```html
<ul id="chat-messages" role="log" aria-live="polite">
  <li>...</li>
</ul>
```

<!-- 
IMAGE: A split-screen illustration. Left side shows a standard-looking chat interface with a message list and send button, labeled "What the sighted user sees." Right side shows the same page as a screen reader would announce it: a text list of landmarks (Navigation, Main, Log: Chat Messages), then the message content, then a labeled "Send message" button. Caption: "The screen reader experiences your page entirely through its HTML structure. Every element choice you make is an accessibility decision." Style: flat vector, two-panel, left panel is a visual mockup, right panel shows a text/audio representation in a screen reader UI style.
-->

#### Basic Accessibility Practices

**Every image needs meaningful `alt` text.** A screen reader announces alt text when it encounters an image. "Image" or "photo" is not useful. A description of what the image shows and why it is there is useful.

**Every input needs a `<label>`.** The `for`-`id` connection between label and input is what allows a screen reader to announce the field's name when the user focuses on it. Placeholder text disappears on interaction and is not an alternative.

**Link text must be descriptive.** "Click here" and "Read more" are meaningless without visual context. "Download the API documentation PDF" tells the user exactly what will happen. Screen readers can generate a list of all links on a page. That list must be scannable on its own.

**Keyboard navigation must work.** Every interactive element on the page must be reachable and operable using the Tab key and Enter key alone. If a user cannot click, they must be able to keyboard-navigate. Native HTML elements like `<button>`, `<a>`, `<input>`, and `<select>` are keyboard-accessible by default. Custom interactive components built with `<div>` are not, and require additional work to make accessible.

### SEO: Building for Search Engines

#### How Search Engines Read Your Page

When a search engine like Google crawls a webpage, it sends a bot that reads the HTML. It cannot see CSS, it does not experience visual hierarchy, and it cannot infer meaning from styling. It reads the text and the structure.

From that reading, it tries to answer: what is this page about? How authoritative is it on that topic? How well does it match what a user searching for this topic actually wants?

Your HTML provides the signals that answer those questions.

#### Signals That Directly Affect Search Ranking

**The `<title>` tag.** The title in `<head>` is the single most important on-page SEO signal. It appears as the blue clickable link in search results. It should clearly describe the page's content and include the primary keyword the page is targeting. Keep it under 60 characters so search engines do not truncate it.

```html
<title>Aiden AI: Your Intelligent Chatbot Assistant</title>
```

**The meta description.** This does not directly affect ranking but determines the grey summary text shown beneath the title in search results. A well-written meta description increases click-through rate, which indirectly signals quality to the search engine. Keep it under 160 characters.

```html
<meta name="description" content="Aiden AI is a GPT-4 powered chatbot for natural language queries, built with HTML, CSS, and JavaScript.">
```

**The meta author.** Identifies the creator of the page content. Increasingly used as a signal for content credibility, particularly in the context of Google's E-E-A-T guidelines (Experience, Expertise, Authoritativeness, Trustworthiness).

```html
<meta name="author" content="Arjun Mehta">
```

**Heading hierarchy.** The `<h1>` is the primary topic declaration of the page. Search engines treat it as the main subject signal. There should be exactly one `<h1>` per page. `<h2>` elements are the subtopics. The hierarchy communicates the content structure of the document, and search engines use it to understand which topics the page covers and at what depth.

**Alt text on images.** Search engines cannot see images. The `alt` attribute is how they understand what an image contains and whether it is relevant to the page's topic. Image search results are populated entirely from `alt` text.

**Semantic structure.** A page built with `<article>`, `<section>`, `<header>`, and `<nav>` communicates its structure explicitly. Search engines use these signals to identify which part of the page is the primary content, which is navigation, and which is supplementary. A `<main>` element tells the crawler: start reading the important content here.

**Link anchor text.** The visible text of a hyperlink tells search engines what the linked page is about. "Visit our documentation" is more informative than "click here." When many sites link to a page using the same anchor text, that anchor text becomes a strong relevance signal for the linked page.

<!-- 
IMAGE: A Google search result card illustration for a fictional site showing: a blue title link labeled "title tag", a green URL below labeled "URL structure", and the grey description text labeled "meta description content." Below it, a second result showing a truncated title with "..." labeled "Title too long, truncated at 60 characters." Caption: "The title tag and meta description are the only parts of your HTML users ever see without opening your site." Style: flat vector replica of a Google search result card, clean annotation with labelled arrows, neutral background.
-->

#### What to Avoid

Skipping heading levels destroys hierarchy signals. Going from `<h1>` directly to `<h4>` communicates nothing coherent to a search engine or a screen reader.

Keyword stuffing, repeating the same words unnaturally to try to rank for them, is penalised. Search engines are sophisticated enough to detect it and demote pages that do it.

Using heading elements for visual size rather than structural meaning is a common mistake. If text needs to be large and bold, CSS handles that. A `<h3>` used as a styling choice where no heading is semantically appropriate degrades the document's heading structure.

### The HTML Best Practices Checklist

Every page you build should pass these before it is considered complete. These are the minimum baseline for an HTML page that serves its users, browsers, and search engines correctly.

**One `<h1>` per page.** It declares the primary topic of the document. Multiple `<h1>` elements dilute that signal.

**Heading levels do not skip.** `<h1>` to `<h2>` to `<h3>` in order. Jumping from `<h1>` to `<h4>` is invalid structure.

**Every `<img>` has an `alt` attribute.** Meaningful description for informational images. Empty `alt=""` for purely decorative images. Never missing.

**Every `<input>` has a paired `<label>`.** The `for`-`id` relationship must be in place on every field in every form.

**No link says "click here" or "read more" alone.** All link text must be descriptive enough to understand out of context.

**A `<meta name="description">` is in `<head>`.** Short, accurate summary of the page, under 160 characters.

**Landmark regions are present.** `<header>`, `<nav>`, `<main>`, and `<footer>` should be identifiable in the document structure.

**The page title is in `<title>` and is descriptive.** Under 60 characters, clearly describes the page content.

### Let's Define Things Now...

> **Accessibility:** The practice of building websites that work for users of every ability, using correct HTML structure, labels, alt text, and ARIA attributes so assistive technologies can navigate and present the content correctly.

> **Screen Reader:** Software that reads webpage content aloud for visually impaired users. Navigates by HTML landmarks (header, nav, main) and heading hierarchy.

> **ARIA (Accessible Rich Internet Applications):** A set of HTML attributes that extend semantic information for assistive technologies. `aria-label` names elements. `aria-live` announces dynamic changes. `role` declares an element's semantic purpose.

> **aria-live:** An ARIA attribute that tells a screen reader to monitor a region and announce content changes automatically as they happen.

> **role:** An ARIA attribute that explicitly declares an element's semantic role when native HTML cannot express it (for example, `role="log"` for a live message feed).

> **SEO (Search Engine Optimisation):** The practice of structuring HTML so search engines correctly understand, index, and rank the page. Affected by title, meta description, heading hierarchy, alt text, semantic structure, and link anchor text.

> **meta description:** A `<meta>` tag in `<head>` that provides the summary text shown beneath a page's title in search engine results.

> **E-E-A-T:** Experience, Expertise, Authoritativeness, and Trustworthiness. Google's framework for evaluating content quality and credibility. Semantic structure and clear authorship contribute to it.

### Activity: Accessibility and SEO Audit

Perform two tasks using any public website of your choice.

**Task 1: Accessibility Audit**
Open the website in Chrome. Open DevTools and go to the Accessibility tab (or use the Lighthouse tool under the Audits tab). Check the following manually using the Elements panel:
- Is there exactly one `<h1>` on the page?
- Do heading levels progress without skipping?
- Do all visible images have `alt` attributes?
- Do all form inputs have associated `<label>` elements?
- Is there at least one `<nav>` landmark?
- Is there a `<main>` region?

Note at least three things the site does correctly and one thing it fails.

**Task 2: Chatbot Accessibility and SEO Pass**
Apply the following to your chatbot HTML:
- Add `role="log"` and `aria-live="polite"` to the chat message list
- Add `aria-label="Type your message"` to the message input
- Add `aria-label="Send message"` to the send button
- Add a `<meta name="description">` and `<meta name="author">` to `<head>`
- Verify there is exactly one `<h1>` and that heading levels do not skip
- Confirm every image has descriptive `alt` text
- Confirm every input has a paired `<label>`

After applying these changes, the chatbot is not just visually functional. It is navigable by a screen reader user, announced to assistive technologies when new messages appear, and understood by search engines.

## What's Coming Next...

We have now covered everything on a structural level for creating websites. From the very first article that defined what HTML is, through elements for text, lists, links, images, tables, forms, media, and semantic structure, and finally through the accessibility and SEO practices that make those structures work for every user and every system that reads them.

HTML gives a webpage its bones. Every element we covered is part of that skeleton. But bones alone are invisible and unremarkable. What makes a website look like a website, the colours, the typography, the spacing, the layouts, the visual identity, none of that lives in HTML.

That is what comes next. In the following module, we will take the HTML structure we have built and style it using CSS, the language that transforms a semantic skeleton into a polished, visual, and responsive web interface. We will make our chatbot look like a real product.

Let us start styling.
