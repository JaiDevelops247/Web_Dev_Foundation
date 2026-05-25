## Introduction

Now that the distinction between the Internet and the World Wide Web is clear, it is time to zoom in. We know the Web is a collection of Websites sitting on top of the Internet. But what exactly is a Website? What is it actually made of?

This is the question that opens the door to Web Development. Because once you see what a Website is under the hood, you will realise that building one is far less mysterious than it sounds. Every Website you have ever visited, from Google to Netflix to the smallest local bakery's Website, is made of the same three things.

Let us find out what those three things are.

<!-- 
IMAGE: A magnifying glass zooming into a browser window. Inside the magnification, instead of a polished website, you see three plain colored layers stacked like sheets of paper, labeled "HTML", "CSS", and "JavaScript" from bottom to top. The overall feel is a reveal moment. Style: flat vector, clean and modern, bright background.
-->

### Why Did We Need the Web in the First Place?

The Internet existed by the late 1980s. Data was moving across Networks, Emails were being sent, Files were being transferred. So why was a new system needed on top of it?

The problem was that the Internet had no structure for linking information together. If you were a Scientist at CERN and you wanted to share a Research Document with a colleague in another country, you could send it over the Internet. But if that Document referenced five other Documents, your colleague would need to request each one separately. There was no way to simply click and jump from one piece of information to another.

Information existed in silos. Finding it and navigating between it was painful.

**The need was a system where Documents could link to each other freely, and anyone with an Internet connection could access any of them.**

### How the World Wide Web Was Introduced

Tim Berners-Lee's solution in 1989 was built on one powerful idea: **Hyperlinks.**

A Hyperlink is simply a piece of text or an image that, when clicked, takes you to another Document. That Document could be on the same Computer or on a Computer on the other side of the world.

String millions of these linked Documents together across millions of Computers, all accessible over the Internet, and you get a massive web of connected information stretching across the entire world.

That is exactly why it is called the **World Wide Web.** The "web" refers to this interconnected web of links. "World Wide" means it spans the entire globe. Every page linking to other pages, every site connected to other sites, forming one enormous, interlocking web of information.

<!-- 
IMAGE: A visual of glowing dots (representing Websites) scattered across a dark globe. Thin glowing lines connect the dots to each other, creating a web-like pattern that looks like a spiderweb stretched across the world. Some clusters of dots are denser (representing popular sites). Label at top: "The World Wide Web." Style: dark background, neon blue and white, data-visualization aesthetic.
-->

### So What Is a Website, Really?

Here is the honest answer: a Website is just a **collection of Files sitting on a Computer somewhere.**

That Computer, the one storing and serving those Files to anyone who asks for them, is called a **Server.** When you type a URL into your Browser, your Browser sends a request to that Server, the Server sends back those Files, and your Browser uses those Files to display the page on your screen.

That is it. A Website is Files on a Server.

Now the important question: what kind of Files?

Every Website, no matter how complex, is ultimately built from three types of Files:

1. **HTML Files** that define the Structure and Content
2. **CSS Files** that define the Visual Appearance
3. **JavaScript Files** that define the Behaviour and Interactivity

These are the three building blocks of every Website on the Web.

<!-- 
IMAGE: A Server (visualized as a tall machine in a data center) with three glowing file icons floating out of it toward a Browser window. The three files are color-coded: orange for HTML, blue for CSS, yellow for JavaScript. An arrow flows from the Server to the Browser. Caption: "A Website = Files on a Server." Style: clean isometric illustration, tech aesthetic.
-->

### The Analogy: A Building Under Construction

Think of any building you have seen being constructed.

First, the workers lay down the **structure**: the foundation, the walls, the floors, the roof. This gives the building its shape and tells you how many rooms there are, where the doors are, where the windows go. Without this, nothing else can exist.

Then come the **designers**: they paint the walls, choose the flooring, add lighting, pick the furniture, and make the building look beautiful. The same structure can be made to look like a modern office, a cosy cafe, or a luxury hotel depending on how it is designed.

Finally, the **engineers** wire up the electricity, install the plumbing, set up the lifts, and add the alarm systems. These are the systems that make the building functional and interactive.

**HTML is the structure.** The walls, floors, and framework.
**CSS is the design.** The paint, furniture, and aesthetics.
**JavaScript is the engineering.** The electricity, lifts, and interactive systems.

Take away the structure and nothing stands. Take away the design and it looks like a grey concrete block. Take away the engineering and none of the interactive parts work.

<!-- 
IMAGE: Three-panel illustration of the same building in three stages. Panel 1: bare concrete skeleton structure labeled "HTML - Structure". Panel 2: the same building now painted, with windows and signage added, labeled "CSS - Style". Panel 3: lights glowing through windows, a lift moving, people interacting with the entrance, labeled "JavaScript - Behaviour". Style: flat vector, warm and approachable colors.
-->

### A Real Website Example: Wikipedia

Let us use Wikipedia because almost everyone has visited it.

Open Wikipedia and look at any article. Here is what you are actually seeing:

**The HTML layer** is responsible for the fact that there is a heading at the top, a table of contents on the side, paragraphs of text below, images placed in specific spots, and links you can click. HTML is the reason those elements exist on the page at all. Without HTML, there is nothing to see.

**The CSS layer** is responsible for the fact that the Heading is large and bold, the text is a readable size in a clean font, the background is white, the links are blue, and the table of contents sits neatly in a box on the right. Without CSS, all of those HTML elements would be stacked in a plain, unstyled list from top to bottom.

**The JavaScript layer** is responsible for the fact that clicking "show" on a collapsed section expands it, that the search bar gives you live suggestions as you type, and that some interactive elements respond to your clicks without the whole page reloading. Without JavaScript, the page would be entirely static.

<!-- 
IMAGE: A side-by-side breakdown of a Wikipedia-style article page. The same page is shown three times. Version 1 is labeled "HTML only" and shows raw unstyled text and links stacked vertically. Version 2 is labeled "HTML plus CSS" and shows the page looking clean, styled, and readable but with no interactivity. Version 3 is labeled "HTML plus CSS plus JavaScript" and shows the full, interactive page with a live search bar and expandable sections. Style: UI mockup illustration, clean and educational.
-->

### Let's Define Things Now...

> **HTML (HyperText Markup Language):** The standard language used to create and structure content on the Web. It defines what is on a page: headings, paragraphs, images, links, buttons, and forms.

> **CSS (Cascading Style Sheets):** A language used to describe how HTML elements should look. It controls layout, colors, fonts, spacing, and visual presentation.

> **JavaScript:** A programming language that runs in the Browser and makes Web pages interactive and dynamic. It responds to user actions and can update content without reloading the page.

### Everything Else Builds on These Three

Here is something important to understand before this course goes deeper.

You will hear a lot of names in the world of Web Development: React, Angular, Vue, Next.js, Spring, Django, Laravel, and dozens more. These can sound overwhelming when you are just starting out.

But here is the truth: **every single one of them either builds on top of HTML, CSS, and JavaScript, or exists to help create them more efficiently.**

React is a tool that helps you write JavaScript and HTML more powerfully. Django is a tool that helps a Server generate HTML to send to a Browser. CSS Frameworks like Tailwind or Bootstrap are just pre-written CSS. Everything in Web Development, no matter how advanced, is ultimately serving these three building blocks.

This course will teach you to write real HTML, CSS, and JavaScript from scratch. Once you understand the foundation, everything built on top of it will make sense.

<!-- 
IMAGE: A pyramid diagram. The base layer (widest) is labeled "HTML, CSS, JavaScript - The Foundation." Above it, progressively smaller layers show: "CSS Frameworks (Bootstrap, Tailwind)", then "JavaScript Frameworks (React, Vue, Angular)", then "Backend Frameworks (Django, Spring, Laravel)" at the top. The pyramid is solid and stable-looking. Caption: "Every Web technology builds on the same three foundations." Style: clean infographic, bold colors, flat design.
-->

### Activity: Web Archaeology

Before writing a single line of code, let us observe how these layers work together in the real world.

1. Go to [web.archive.org](https://web.archive.org) (the Wayback Machine)
2. Search for **amazon.com** or **wikipedia.org**
3. Pick a version from around **2005** and open it
4. Compare it with how the same site looks today

Look closely and note **3 differences** across these categories:

- Something about the **Structure**: What sections or elements exist now that did not exist then, or vice versa? (This is HTML changing)
- Something about the **Visual Design**: Colors, fonts, spacing, layout. (This is CSS changing)
- Something about the **Interactivity**: What interactive elements work differently now? (This is JavaScript changing)

You do not need to touch any code. This is purely observation. The goal is to see that every change you notice, visual or structural, was made by someone editing HTML, CSS, or JavaScript files.

### What's Coming Next...

You now know that the Web is a collection of Websites, each one made of HTML, CSS, and JavaScript Files sitting on a Server. You know the three building blocks of every page you have ever visited.

But before we start building anything with these three languages, there is a fascinating journey to understand first. When you type a URL into your Browser and press Enter, what actually happens? How does your request travel across the Internet, find the right Server, and come back? And once those Files arrive, how does your Browser turn them into the polished page you see on your screen?

That is exactly what the next chapters will walk you through, step by step, before we pick up our first tool and start writing code.
