## Introduction

In the last module, we traced the entire journey of a Web request, from a URL typed into a Browser all the way to pixels painted on a screen. At the very end of that journey, we landed on one clear conclusion: every Website on the Web is ultimately built from three types of Files, HTML, CSS, and JavaScript, each playing a distinct role.

Now it is time to actually learn how to write them.

We are starting with HTML, and the reason is straightforward. HTML is the most elemental of the three. Before you can make something look beautiful with CSS, that something has to exist. Before JavaScript can make an element respond to a click, that element has to be on the page. Everything begins with HTML. It is not the most visually exciting of the three, but it is the one that makes everything else possible.

By the end of this article, you will know exactly what HTML is, what each word in its name actually means, and why it is categorically different from programming languages like Python or JavaScript. You will also understand what HTML5 is and why it is the version every professional Web Developer writes today.

<!-- 
IMAGE: A three-layer illustration representing a webpage. The bottom layer is a plain wireframe skeleton of boxes and lines, labeled "HTML - Structure." The middle layer shows the same layout now with colors, fonts, and spacing applied, labeled "CSS - Style." The top layer shows the same page with animated elements and interactive buttons highlighted, labeled "JavaScript - Behaviour." An arrow points down through the layers with the caption: "It all starts at the bottom." Style: flat vector, three distinct layers clearly separated with subtle depth.
-->

### The Need: Why Did the Web Need a Standard Language?

When Tim Berners-Lee proposed the World Wide Web in 1989, he had an idea: a system of linked documents that anyone with an Internet connection could access. But there was an immediate practical problem.

Computers in the world are radically different from each other. Different operating systems, different processors, different screen sizes. A document written for one computer might look completely broken on another. There was no guarantee that a File created on a computer in Switzerland would display correctly on a computer in Japan.

The Web needed a universal language for writing documents. A language that any Browser running on any Computer anywhere in the world could read and render consistently.

That language is HTML.

HTML solved the problem of universality. You write one HTML File, and every Browser on every device knows how to read it. A Chrome Browser on a Windows laptop, Safari on an iPhone, and Firefox on a Linux machine will all look at the same HTML and understand it the same way. The Browser handles the visual rendering. The developer just writes the structure.

This is still true today. HTML is the one language every Browser on the planet understands, and it has been since the Web began.

<!-- 
IMAGE: A world map with glowing dots representing different countries. From a single central point (labeled "One HTML File"), lines radiate outward to the dots. At each dot, a small Browser window shows the same webpage rendering correctly. Some devices shown are a laptop, a mobile phone, a tablet, and a desktop. Caption: "One HTML file. Every device. Every browser." Style: dark background, glowing connection lines, flat vector with a modern data-visualization aesthetic.
-->

### How HTML Was Introduced

HTML was created by Tim Berners-Lee in 1991, alongside the Web itself. The first version was extremely minimal: Just enough tags to write headings, paragraphs, and links. That was all the Web needed to begin.

Over the following decades, HTML grew as the Web grew. New tags were added to handle new kinds of content. Browsers started supporting new features. Standards organisations stepped in to formalise the rules so that every Browser would behave consistently.

This process of incremental development eventually produced the version of HTML that is in use today: **HTML5.**

HTML5 is not a dramatic reinvention. It is the result of decades of careful additions and improvements that gave HTML everything it needed to build any Website on the planet, from a simple personal page to a full video streaming platform. It introduced native support for embedding video and audio, semantic elements that give sections of a page real meaning, improved form controls, and much more.

Every Browser released in the past decade fully supports HTML5. Every professional Web Developer writes HTML5. Every Website you have visited recently was built with it. There are older versions of HTML, but they are historical artefacts. This course teaches HTML5, because that is the only version that matters in practice today.

### What HTML Is Not

Before going further, there is one very important thing to establish, because it is a source of genuine confusion for almost every beginner.

**HTML is not a programming language.**

This might sound strange, because it does have the word "language" in its name. But the distinction matters, and understanding it will shape how you think about writing HTML.

A programming language is a language that can compute, make decisions, and perform logic. Look at this snippet of Python:

```python
temperature = 38

if temperature > 37:
    print("You have a fever.")
else:
    print("Your temperature is normal.")
```

This code stores a value, checks a condition, and decides what to output based on that condition. It is doing something. It is executing logic. The computer runs this code and performs a computation.

Now look at this HTML:

```html
<h1>Patient Report</h1>
<p>Temperature: 38 degrees</p>
<p>Status: You have a fever.</p>
```

This HTML is not doing anything. It is not checking a condition. It is not computing a result. It is describing and structuring content. It is telling the Browser: this is a heading, this is a paragraph, this is a paragraph. The Browser reads these instructions and displays accordingly. HTML does not run, it is rendered.

This is the core distinction:

A **programming language** computes. It has variables, conditionals, loops, and functions. It makes decisions and produces results.

A **markup language** describes. It annotates content with structural or semantic meaning. It tells a reader, whether a human or a Browser, what content is and how it relates to other content.

HTML belongs firmly in the second category. And that is not a limitation. It is exactly what makes it the right tool for the job of structuring Web content. You would not use a hammer to write a letter, and you would not use a markup language to process a payment. Right tool, right job.

<!-- 
IMAGE: A two-column comparison card. Left column titled "Programming Language (Python / JavaScript)" shows four bullet points: Has variables, Has if/else conditions, Has loops and functions, Runs and computes logic. Right column titled "Markup Language (HTML)" shows four bullet points: Has tags that describe content, Tells the browser what something is, Has no conditions or loops, Is rendered, not executed. A large label at the bottom of each column: "Does something" vs "Describes something." Style: clean comparison card, contrasting background colors for each column, bold labels.
-->

### Breaking Down the Name: What Does HTML Actually Stand For?

HTML stands for **HyperText Markup Language.** Every word in that name carries a specific and significant meaning. Understanding each one will give you a clearer picture of what HTML is and why it works the way it does.

#### H and T: HyperText

The word **Hyper** comes from Greek, meaning "over," "beyond," or "exceeding normal limits." In the context of HTML, it refers to going beyond regular, linear text.

Think about how a physical book works. You start at page one and read forward. The text is linear. You go from one sentence to the next, one page to the next, in a fixed sequence. The text on page 50 has no direct connection to the text on page 200 unless you flip there yourself.

**HyperText** is text that breaks that linear model. It contains embedded connections to other pieces of text or other documents. These connections are called Hyperlinks. When you click a Hyperlink, you jump instantly to another page, another section, or even another Website entirely. The reading experience is non-linear and web-like, branching in every direction.

This is why every clickable link on a webpage is called a Hyperlink. The "hyper" prefix is not accidental. It is the defining characteristic that makes the Web work: documents that connect to other documents, forming the world-spanning web of linked information that Tim Berners-Lee envisioned.

#### M: Markup

The word **Markup** has its origins in traditional publishing and editorial work.

Before digital publishing, editors and typesetters would take a manuscript and physically mark it up with instructions. They would draw a circle around a word and write "bold" next to it. They would underline a line and write "this is the headline." They would add symbols indicating paragraph breaks, indentation, or font changes. These physical annotations told the typesetter how to format and print the final document.

HTML does exactly the same thing, digitally. Instead of a person drawing a circle, you wrap content in tags. Instead of writing "this is the headline" next to a line of text, you write `<h1>` before it and `</h1>` after it. You are marking up the content with instructions about what it is and what role it plays.

The Browser reads your markup and follows the instructions, just like a typesetter followed the editor's annotations.

<!-- 
IMAGE: A side-by-side illustration. Left side: an old printed manuscript page with handwritten editorial marks in red: circles, underlines, margin notes saying "bold," "headline," "new paragraph." Right side: the same content written in HTML with h1 and p tags, strong tags, and a paragraph break. A label connecting the two: "Same idea. Different medium." Style: warm editorial aesthetic on the left, clean code-card aesthetic on the right.
-->

#### L: Language

The word **Language** here means that HTML has a defined and consistent syntax, a vocabulary of elements, and a set of rules that must be followed for the markup to be valid and understood.

Just like English has grammar rules, punctuation rules, and a vocabulary of words, HTML has tag syntax rules, nesting rules, and a vocabulary of elements. Writing `<h1>` means something specific and universally agreed upon. Writing `<xyz>` means nothing and will be ignored by Browsers.

The "language" in HTML does not imply computation. It simply means there is a formal, agreed-upon system of symbols and rules that both writers (developers) and readers (browsers) understand.

### HTML5: The Version That Powers the Entire Modern Web

As mentioned earlier, HTML has gone through multiple versions since 1991. The current version, and the one this entire course is built on, is **HTML5.**

HTML5 is the result of years of incremental development that filled in every gap the earlier versions left. It gave HTML the ability to embed video and audio natively without needing a third-party plugin. It introduced a set of semantic elements like `<header>`, `<nav>`, `<main>`, `<article>`, and `<footer>` that give sections of a page real, meaningful names rather than generic containers. It improved form controls so browsers can natively validate email addresses, date pickers, and number ranges. It added APIs for things like offline storage, geolocation, and drawing graphics.

The result is a version of HTML capable of powering any Website imaginable, from a simple blog to a video platform to a full productivity application.

You do not need to think about the version number as you write. Open any text editor, write HTML, and you are writing HTML5. The `<!DOCTYPE html>` declaration that goes at the top of every HTML document is the signal that tells the Browser: this is HTML5. One line, and you are using the modern standard.

### Let's Define Things Now...

> **HTML (HyperText Markup Language):** The standard markup language used to create and structure content on the Web. Every webpage is built on an HTML foundation.

> **Markup Language:** A language that annotates content with tags or symbols to describe its structure, meaning, or presentation. It does not compute or execute logic.

> **HyperText:** Text that contains embedded links (Hyperlinks) to other documents or sections, enabling non-linear navigation across the Web.

> **Hyperlink:** A clickable connection embedded in Web content that takes the user to another page, section, or resource.

> **Markup:** The practice of wrapping content in tags to describe its role or structure, just as a traditional editor annotates a manuscript with formatting instructions.

> **HTML5:** The current and universally supported version of HTML, which includes semantic elements, native audio and video support, improved forms, and all the modern features needed to build any Website.

### Activity: Markup Hunt

Before writing a single line of HTML yourself, spend five minutes observing it in the wild.

1. Open any Website in Chrome or Firefox.
2. Right-click anywhere on the page and select **View Page Source.** A new tab opens showing the raw HTML the Browser received from the Server.
3. Scroll through it and find **10 HTML tags.** They will be the words wrapped in angle brackets, like `<div>`, `<p>`, `<a>`, `<img>`, `<h1>`.
4. For each tag you find, note it down and write one sentence guessing what it does based purely on its name.

You are not expected to understand all of it. The goal is to see that every webpage you have ever visited was built with exactly this kind of text, a set of tags wrapped around content, telling the Browser what to display and how to interpret it.

## What's Coming Next...

You now know what HTML is, what each word in its name means, why it is a markup language and not a programming language, and why HTML5 is the version that powers the entire modern Web.

The next article is where things get hands-on. You will write your very first HTML, open it in a real Browser, and see it render on your screen. Before you can write anything meaningful though, every HTML document needs a specific structure, a skeleton of its own, that tells the Browser how to read the file. That structure is the subject of the next article, and it is where every Web Developer begins.
