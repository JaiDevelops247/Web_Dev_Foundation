## Introduction

We have spent the last two modules building the structure and the visual layer of a webpage. HTML gave us the skeleton: the elements, the content, the meaning. CSS gave us the appearance: the colors, the layout, the animations. Open the chatbot we have been building and it looks like a real product.

But try clicking the Send button. Nothing happens.

The input field accepts text. The button looks pressable. The layout is responsive. And yet the page does nothing when you interact with it. That is because HTML and CSS are declarative: they describe what the page should look like. Neither of them can answer questions like:

- Was the message field empty when the user hit Send?
- Which button did the user just click?
- Should the settings panel be visible or hidden right now?
- What should the bot reply with?

Every question like these requires logic. And logic requires a programming language.

That language is JavaScript.

Before we bring JavaScript into our web pages to wire up the chatbot, we will spend this module getting comfortable with it as a language on its own. Variables, data types, functions, arrays, objects: the building blocks of any program. Understanding these first, without the complexity of the browser's buttons and events, makes the later wiring much more intuitive.

This first article is about what JavaScript is, where it came from, and how to start running it.

### What is JavaScript?

JavaScript is the programming language built into every web browser. It is not something you install separately or link to from an external server. Every browser in use today -- Chrome, Firefox, Safari, Edge -- ships with a JavaScript engine that can run JavaScript code.

When a browser loads a webpage, it processes three things:

- HTML: parsed into the DOM, the tree of elements
- CSS: parsed into style rules applied to those elements
- JavaScript: executed by the JavaScript engine, which can read and modify the DOM and respond to user actions

HTML and CSS are processed once when the page loads. JavaScript runs as the user interacts with the page, continuously, for as long as the page is open.

<!-- 
IMAGE: Three-column diagram. Left column labeled "HTML" with an icon of a document showing nested tags -- caption: "Structure. Parsed once into the DOM." Center column labeled "CSS" with a paintbrush icon -- caption: "Appearance. Applied to the DOM." Right column labeled "JavaScript" with a gear/lightning icon -- caption: "Behaviour. Runs in response to user actions." Below all three, a single row labeled "Browser" shows an arrow flowing from all three into one webpage output. Caption: "The browser processes all three. HTML and CSS set up the page. JavaScript keeps it alive." Style: flat vector, three distinct color columns (blue for HTML, purple for CSS, yellow for JS), unified at the bottom into a single browser window.
-->

### A Brief History: Why JavaScript Exists

In 1994, the web was a collection of static pages. You clicked a link, a new page loaded, you read it, you clicked another link. There was no interactivity because there was no language running in the browser. The server generated the HTML and sent it. That was the entire cycle.

Netscape, the dominant browser company at the time, recognised that static pages were a ceiling. Web applications would need to validate forms before sending them to the server, respond to clicks, and update parts of the page without reloading the entire thing.

In 1995, Netscape hired Brendan Eich and gave him ten days to create a programming language for the browser. The result was JavaScript. Ten days is an extremely short time to design a language, and some of JavaScript's more confusing corners are a direct consequence of that speed. But the core of it -- dynamic typing, first-class functions, prototype-based objects -- was thoughtful and intentional.

JavaScript was not named after Java. The name was a marketing decision: Java was popular at the time and Netscape wanted to ride that recognition. The two languages have almost nothing in common.

Over the next decade, every browser shipped its own version of JavaScript with its own behaviour differences. Writing code that worked in both Internet Explorer and Netscape required constant workarounds. This era is the reason JavaScript has a reputation for inconsistency that no longer reflects the language as it is today.

In 2009, Node.js was released. For the first time, JavaScript could run outside the browser entirely, on servers. JavaScript became a general-purpose language, not just a browser tool.

#### ES6: The Modern Turning Point

In 2015, a major update called ES6 (also called ECMAScript 2015) was released. It introduced `let` and `const`, arrow functions, template literals, classes, and destructuring, among many other additions. ES6 is the version of JavaScript that most developers refer to when they say "modern JavaScript." Everything we write in this course uses ES6 syntax. You will see references to ES6 online constantly, and they all point to this 2015 update as the dividing line between old and modern JavaScript.

### If You Already Know Another Language

If you have written code in Python or C before, the foundational ideas will be immediately familiar: variables store values, functions group reusable logic, loops repeat operations, and conditionals branch on a condition. The control flow is the same.

The differences appear in the details. JavaScript's type system behaves differently from Python's and very differently from C's. How scope works, how objects and inheritance work, and how asynchronous code is handled are all specific to JavaScript. We will cover each of those differences precisely when they appear, with examples that show why JavaScript works the way it does.

### How JavaScript Runs in the Browser

When the browser receives JavaScript code, it does not execute it line by line like reading a script. It passes the code to a JavaScript engine, which compiles it to machine code and runs it.

Chrome uses an engine called V8. Firefox uses SpiderMonkey. Safari uses JavaScriptCore. They are all different implementations of the same specification, which is why code that follows the standard runs correctly in all of them.

The engine operates inside a single thread: it processes one thing at a time. But browsers are not frozen while JavaScript runs because certain operations (fetching data from a server, waiting for a timer) are handed off to the browser's background systems. When those finish, the results are queued for JavaScript to process. This is called the event loop, and it is the mechanism behind all asynchronous JavaScript. We will return to it in the JS Web Dev module when we start working with real API calls.

<!-- 
IMAGE: A simple two-box diagram. Left box labeled "Your JavaScript Code" with a small code snippet icon. An arrow points right to a box labeled "JavaScript Engine (V8 in Chrome)" with a gear icon. Below that box, an arrow points down to a box labeled "Machine Code (runs on your CPU)." Caption: "The browser does not run your JavaScript directly. The engine compiles it to machine code first." Style: flat vector, three boxes in a vertical-right-angled flow, clean and minimal.
-->

### Running JavaScript Outside the Browser: Node.js

JavaScript was originally a browser-only language. Node.js changed that.

Node.js is a runtime that packages the V8 engine and makes it available on your computer directly, without a browser. You can write a JavaScript file, run it in the terminal with `node filename.js`, and get output -- no browser tab required. This is how backend servers, command-line tools, and build systems are written in JavaScript today.

In this module, we will use both: the browser console for quick experiments, and Node.js for running JavaScript files directly when you want to work through larger programs without an HTML page.

### Three Ways We Will Use JavaScript in This Course

**1. The browser console**

Every browser has a built-in JavaScript console where you can type and run JavaScript directly. It is the fastest way to try a single expression, test a function, or check what a value is. We will use it throughout this module for quick experiments.

**2. A standalone .js file**

For anything beyond a one-liner, we will write JavaScript in a `.js` file and run it using Node.js in the terminal. This is how you will build the `chatbot-logic.js` file that grows across every article in this module.

**3. A script tag linked to an HTML page**

This is how JavaScript and the browser actually work together: a `<script src="script.js"></script>` tag in the HTML file tells the browser to load and execute the JavaScript file. We will use this in the next module, JS Web Dev, when we start wiring the chatbot interface to the logic we write here. We will not use it in this module so that we can focus on JavaScript as a language without the extra complexity of the browser environment.

### Running Your First JavaScript

Open your browser. The shortcut to open DevTools is:

- **Chrome / Edge**: `F12` on Windows, `Cmd + Option + J` on Mac
- **Firefox**: `F12` on Windows, `Cmd + Option + K` on Mac
- **Safari**: `Cmd + Option + C` (enable the Develop menu first in Safari preferences)

Click the **Console** tab. You will see a prompt that looks like this:

```
>
```

This is a live JavaScript environment. Type the following and press Enter:

```js
1 + 1
```

The console evaluates the expression and prints the result:

```
2
```

Now type:

```js
"Hello, JavaScript"
```

The console echoes the string back. Strings in JavaScript are text values wrapped in quotes.

Now type:

```js
typeof 42
```

```
'number'
```

`typeof` is an operator that returns the type of a value as a string. We will use it often when checking what kind of data a variable holds.

#### console.log()

The console prompt is useful for single expressions, but code in a `.js` file does not automatically print anything. To produce output from a file, you use `console.log()`.

Type this in the console:

```js
console.log("My first JavaScript output")
```

```
My first JavaScript output
```

`console.log()` is a function that sends its argument to the console. It accepts multiple values separated by commas:

```js
console.log("The answer is", 42)
```

```
The answer is 42
```

You will type `console.log()` more times than any other thing in this module. It is the primary tool for checking what your code is doing.

<!-- 
IMAGE: A screenshot of the browser DevTools console tab open. The > prompt is visible with "console.log('My first JavaScript output')" typed in. Below it, the output line shows: My first JavaScript output. The DevTools panel is docked to the bottom of the browser window with a simple webpage in the background. Caption: "The browser console is a live JavaScript environment. Any code you type here runs immediately." Style: realistic browser screenshot with DevTools open, annotated with a callout pointing to the console tab.
-->

#### Running a .js File with Node.js

If Node.js is installed on your machine, you can run JavaScript files directly from the terminal. Create a file called `hello.js` with the following content:

```js
console.log("Running JavaScript outside the browser")
console.log(1 + 1)
console.log(typeof "hello")
```

Then run it in the terminal:

```
node hello.js
```

Output:

```
Running JavaScript outside the browser
2
string
```

The output appears in the terminal exactly as it would in the browser console. For the rest of this module, we will use Node.js for running `.js` files and the browser console for quick checks.

### Let's Define Things Now...

> **JavaScript:** A programming language built into every web browser, used to add logic and interactivity to web pages. Also runs outside the browser using Node.js.

> **JavaScript Engine:** The component of the browser that compiles and executes JavaScript code. Chrome and Node.js use V8. Firefox uses SpiderMonkey. Safari uses JavaScriptCore.

> **ES6 (ECMAScript 2015):** A major update to JavaScript released in 2015 that introduced the modern syntax and features used in all current JavaScript development: `let`, `const`, arrow functions, template literals, classes, and more.

> **Node.js:** A runtime that allows JavaScript to run outside the browser, using the V8 engine. Used to run `.js` files from the terminal and to build server-side applications.

> **console.log():** A built-in function that outputs values to the browser console or terminal. The primary tool for inspecting values and debugging code.

> **Event Loop:** The mechanism by which the JavaScript engine handles asynchronous operations. JavaScript is single-threaded, but the event loop allows background tasks (timers, network requests) to complete and queue their results without blocking the main thread.

### Activity: Console Explorer

1. Open the browser console and run the following expressions one at a time. Before pressing Enter on each, predict what the output will be:

```js
10 + 3
10 - 3
10 * 3
10 / 3
10 % 3
typeof 10
typeof "hello"
typeof true
typeof undefined
typeof null
```

2. Use `console.log()` to print your name, your age as a number, and a boolean representing whether you are currently a student. Print all three in a single `console.log()` call separated by commas.

3. Create a file called `chatbot-logic.js`. Add the following as the first line of the file:

```js
console.log("Chatbot logic module loaded")
```

Run it with Node.js and confirm the message appears in the terminal. This file will grow across every article in this module into the complete JavaScript logic for the chatbot.

4. In the browser console, try the following and observe what happens:

```js
console.log(1 + "2")
console.log(true + 1)
console.log(typeof null)
```

Do not worry about why these produce the results they do. We will cover JavaScript's type system in the next article. For now, just observe that JavaScript behaves differently from what you might expect coming from another language.

## What's Coming Next...

That was the story of JavaScript: where it came from, how it runs, and how to start using it. We have the console open and a `chatbot-logic.js` file ready to grow.

From here we go into the language itself, one concept at a time. The next article covers variables: how JavaScript stores values, the difference between `let`, `const`, and `var`, and how the choice of keyword affects where a variable can be used.
