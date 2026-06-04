## Introduction

Open Amazon and add a product to your wishlist. The page does not reload. The heart icon fills in, a count updates, and your item appears in the list -- all without leaving the page. Open Notion and add a new item to a checklist. A new row appears instantly, ready for you to type. Delete it, and it disappears just as fast.

This is not HTML. HTML describes what is on the page when it first loads. Once the page is loaded, HTML cannot respond to you. CSS can change how things look based on states like hover or focus, but it cannot add new content or remove existing content based on what a user does.

What you are seeing in those examples is JavaScript at work. The user interacted with the page, JavaScript responded to that interaction, and the page changed.

When you think about how this happens, it always comes down to two steps:

**Step 1:** Select the element you want to change. In a to-do list, before you can add a new item, JavaScript needs to find the list on the page.

**Step 2:** Do something with it. Once the list is selected, JavaScript creates a new item and adds it, or finds an existing item and removes it.

This article is about step one. Before we can change anything on a page, we need to understand how the browser actually represents the page internally, and then how we can reach into that representation to select specific elements.

### How the Browser Sees Your HTML

When you write an HTML file and open it in a browser, the browser does not keep it as a text file. It reads the HTML and converts it into a live, structured representation that JavaScript can work with. This representation is called the **Document Object Model**, or the **DOM**.

The DOM is a tree. Every element in your HTML becomes a node in that tree, and the relationships between elements in your HTML become parent-child and sibling relationships in the tree.

Consider this simple HTML page:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Page</title>
  </head>
  <body>
    <h1>Welcome</h1>
    <ul>
      <li>Item One</li>
      <li>Item Two</li>
    </ul>
  </body>
</html>
```

The browser converts this into a tree that looks like:

```
document
  └── html
        ├── head
        │     └── title ("My Page")
        └── body
              ├── h1 ("Welcome")
              └── ul
                    ├── li ("Item One")
                    └── li ("Item Two")
```

<!-- 
IMAGE: A vertical tree diagram representing the DOM for the sample HTML above. At the top: "document" node. Below it: "html" node. Branching from html: "head" on the left and "body" on the right. Under head: "title" with text "My Page". Under body: "h1" with text "Welcome", and "ul". Under ul: two "li" nodes with text "Item One" and "Item Two". Lines connect each parent to its children. Caption: "Every HTML element becomes a node in the DOM tree. The browser builds this tree from your HTML when the page loads." Style: flat vector tree diagram, monospace node labels, clean connecting lines.
-->

`html` is the root. `head` and `body` are its children. `ul` is a child of `body`, and the two `li` elements are children of `ul`.

### What Is a Node?

Every item in the DOM tree is called a **node**. The browser represents each node as a JavaScript object -- which means every element on your page has properties you can read and methods you can call.

The most common type of node is an **element node**: a node that corresponds to an HTML tag like `<ul>`, `<li>`, or `<h1>`. There are also text nodes (the actual text content inside a tag) and a few others, but for most of what you will do in web development, you are working with element nodes.

When you select an element using JavaScript, you get back one of these node objects. And because it is a JavaScript object, you can do things like read its text content, change its style, add children to it, or remove it from the page entirely.

This is the key idea: **the DOM is not a copy of your HTML. It is a live, interactive representation of the page.** When you change a node in the DOM, the page updates immediately. No reload required.

### The Entry Point: document

To work with the DOM, you start with a global object called `document`. It represents the entire page and is your entry point into the tree.

```js
console.log(document)    // the entire DOM tree
```

From `document`, you can reach every element on the page. It is the root from which all selection methods work.

### The Four Primary Ways to Select Elements

#### 1. getElementById()

**What it selects:** A single element with a specific `id` attribute.

**What it returns:** The element object if found, `null` if no element with that id exists.

**Syntax:**

```js
document.getElementById("idName")
```

```html
<button id="send-btn">Send</button>
```

```js
let sendButton = document.getElementById("send-btn")
console.log(sendButton)    // <button id="send-btn">Send</button>
```

**Where to use it:** When you need one specific element and it already has an id. Because ids are unique on a page, this method is fast and unambiguous. It is the right choice for key elements like a submit button, a search bar, or the main chat input field -- elements you will reference repeatedly.

**Exception:** If the id does not exist on the page, you get `null`. Trying to call methods on `null` will throw a TypeError, so always make sure the element exists before acting on it.

#### 2. querySelector()

**What it selects:** The first element that matches any valid CSS selector.

**What it returns:** The first matching element, or `null` if nothing matches.

**Syntax:**

```js
document.querySelector("cssSelector")
```

```js
document.querySelector("#send-btn")          // by id
document.querySelector(".message-card")      // by class -- first match
document.querySelector("ul li")              // first li inside a ul
document.querySelector("input[type='text']") // by attribute
```

**Where to use it:** When you need one element and want to use the full power of CSS selectors to describe which one. This is the most flexible selection method and the one you will reach for most often. It works with every selector you already know from CSS -- class, id, tag, attribute, descendant, child.

**Relationship to CSS:** The selector string you pass to `querySelector` is the exact same syntax you use in a CSS stylesheet. If you know how to write `.card h2` in CSS, you can use it in `querySelector`.

#### 3. querySelectorAll()

**What it selects:** All elements that match a CSS selector.

**What it returns:** A static **NodeList** -- a list of all matching elements, ordered by their appearance in the document.

**Syntax:**

```js
document.querySelectorAll("cssSelector")
```

```js
let allCards = document.querySelectorAll(".product-card")
let allInputs = document.querySelectorAll("input")
let navLinks  = document.querySelectorAll("nav a")
```

```js
let items = document.querySelectorAll(".message")
console.log(items.length)    // number of matching elements

// Iterate over them
items.forEach(item => {
  console.log(item.textContent)
})
```

**Where to use it:** When you need to work with a collection of elements -- all the items in a list, all the cards in a grid, all the checkboxes in a form.

**Important distinction -- static vs live:** The NodeList that `querySelectorAll` returns is **static**. If elements are added or removed from the page after you call it, the NodeList does not update. It reflects the state of the page at the moment you called it.

#### 4. getElementsByClassName() and getElementsByTagName()

**What they select:**
- `getElementsByClassName("className")` -- all elements with that class
- `getElementsByTagName("tagName")` -- all elements with that tag

**What they return:** A live **HTMLCollection** -- a list that automatically updates if the DOM changes.

**Syntax:**

```js
document.getElementsByClassName("card")    // all elements with class "card"
document.getElementsByTagName("li")        // all <li> elements on the page
```

**Where to use them:** These are older methods from before `querySelector` existed. They are still valid, but in modern JavaScript you will mostly use `querySelector` and `querySelectorAll` instead, since those accept any CSS selector and are more expressive.

The one practical difference: the HTMLCollection returned here is **live**. If you add a new `<li>` to the page after calling `getElementsByTagName("li")`, that new element automatically appears in the collection. The NodeList from `querySelectorAll` would not include it.

**Quick comparison:**

| Method | Returns | How many | Live or Static |
|---|---|---|---|
| `getElementById` | Element or null | One | N/A |
| `querySelector` | Element or null | One (first match) | N/A |
| `querySelectorAll` | NodeList | All matches | Static |
| `getElementsByClassName` | HTMLCollection | All matches | Live |
| `getElementsByTagName` | HTMLCollection | All matches | Live |

### Traversing the Tree

Selecting by id or class gets you to a specific element. But sometimes you already have one element and need to move to a related one -- its parent, its children, or its siblings -- without writing a new selector from scratch.

Every DOM element has properties that let you navigate the tree relative to it.

#### parentElement

Returns the element directly above the current one in the tree.

```js
let item = document.querySelector(".delete-btn")
let listItem = item.parentElement    // the <li> that contains the button
```

**Real-world use:** On a to-do list, when the user clicks "Delete" on an item, the click handler receives the button element. To remove the entire list item (not just the button), you go up one level: `button.parentElement` gives you the `<li>`, and then you remove that.

#### children

Returns an HTMLCollection of all direct child elements.

```js
let ul = document.querySelector("ul")
console.log(ul.children)         // all <li> elements inside this <ul>
console.log(ul.children.length)  // how many items
console.log(ul.children[0])      // first child
```

**Real-world use:** On a shopping cart, `cartList.children` gives you all the cart items so you can loop over them and calculate the total.

#### firstElementChild and lastElementChild

The first and last direct child elements, accessed directly without needing an index.

```js
let ul = document.querySelector("ul")
console.log(ul.firstElementChild)    // first <li>
console.log(ul.lastElementChild)     // last <li>
```

**Real-world use:** On a chat window, the newest message is always at the bottom. `messageList.lastElementChild` gives you the most recent message without needing to know the total count.

#### nextElementSibling and previousElementSibling

The element immediately after or before the current one at the same level in the tree.

```js
let activeTab = document.querySelector(".tab.active")
let nextTab = activeTab.nextElementSibling
let prevTab = activeTab.previousElementSibling
```

**Real-world use:** On a tab bar (like the tabs in Gmail or Google Drive), when the user presses the right arrow key to move to the next tab, `currentTab.nextElementSibling` gives you the tab to activate next.

```js
let heading = document.querySelector("h2")
let nextEl = heading.nextElementSibling    // the element right after the heading
```

If there is no element in that direction (you are already at the first or last child), these return `null`.

### document.documentElement and document.body

Two shorthand properties on `document` give you direct access to the two most important elements on the page.

#### document.documentElement

Returns the root `<html>` element -- the top of the entire DOM tree.

```js
console.log(document.documentElement)    // <html>...</html>
```

**Use case:** Applying a class to the entire page, such as switching between light and dark mode. Adding a class to `<html>` lets your CSS target the entire document hierarchy:

```js
document.documentElement.classList.add("dark-mode")
```

#### document.body

Returns the `<body>` element directly, without needing to query for it.

```js
console.log(document.body)    // <body>...</body>
```

**Use case:** When you want to append a new element to the page, or read the scroll position of the page, or apply styles that should affect the full visible area. `document.body` is faster and more explicit than `document.querySelector("body")`.

```js
console.log(document.body.scrollTop)    // how far the user has scrolled
```

### The DOM Is the Interface for All Logic

Everything JavaScript does on a webpage goes through the DOM. When a user submits a form, JavaScript reads the input values from the DOM. When a notification appears at the top of the page, JavaScript added an element to the DOM. When a product is removed from the cart, JavaScript found that element in the DOM and removed it.

This is why understanding the DOM comes before anything else in web JavaScript. Selection is always the first step. You cannot change something you have not found.

In the next article, we look at step two: what you can actually do with an element once you have selected it -- reading and changing its text content, its HTML, its attributes, and its styles.
