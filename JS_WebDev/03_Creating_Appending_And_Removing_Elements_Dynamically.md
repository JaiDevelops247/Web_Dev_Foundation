## Introduction

You now know how to find an element and change what it contains. But changing existing content is only half the story. Most real web applications do not just update text -- they add entirely new pieces of the page and remove existing ones based on what the user does.

Consider a simple to-do list application. When the page first loads, it shows three tasks:

```
[ ] Buy groceries
[ ] Pay electricity bill
[ ] Call the dentist
```

<!-- 
IMAGE: A clean to-do list UI showing three items with checkboxes: "Buy groceries", "Pay electricity bill", "Call the dentist". An input field at the bottom with a placeholder "Add a new task..." and an "Add" button. Caption: "A to-do list rendered on page load. The three items exist in the HTML. New items and delete buttons need to be created by JavaScript." Style: realistic browser screenshot of a minimal task list app.
-->

When the user types "Renew passport" and clicks "Add", a fourth item should appear in the list. When the user clicks the delete icon on "Pay electricity bill", that item should disappear. Neither the new item nor the delete action was in the original HTML. JavaScript created and removed those elements at runtime.

This is the core of dynamic UI: your JavaScript creates, places, and removes DOM elements in response to what is happening in the application.

### The Three-Step Pattern for Adding Elements

Adding new content to a page always follows the same three steps:

1. **Create** the element
2. **Configure** it (set its text, classes, attributes)
3. **Attach** it to the DOM at the right position

Let us walk through this using the to-do list.

#### Step 1: Create

`document.createElement(tagName)` creates a new element in memory. It is not on the page yet -- it exists but is disconnected.

```js
let newItem = document.createElement("li")
```

The argument is the tag name as a string. You can create any valid HTML element: `"div"`, `"p"`, `"button"`, `"input"`, `"span"`, and so on.

#### Step 2: Configure

Before attaching the element to the page, set it up the way you need it -- its text content, classes, and any attributes.

```js
newItem.textContent = "Renew passport"
newItem.classList.add("task-item")
newItem.setAttribute("data-id", "4")
```

You can also create child elements and nest them. For a task item that has both a label and a delete button:

```js
let newItem = document.createElement("li")
newItem.classList.add("task-item")

let label = document.createElement("span")
label.textContent = "Renew passport"

let deleteBtn = document.createElement("button")
deleteBtn.textContent = "Delete"
deleteBtn.classList.add("delete-btn")

newItem.append(label, deleteBtn)    // attach children to the new li
```

The entire item is assembled in memory before touching the page.

#### Step 3: Attach

Now place the configured element into the live DOM. The browser immediately renders it on the page.

```js
let taskList = document.querySelector(".task-list")
taskList.append(newItem)    // adds the new item at the end of the list
```

<!-- 
IMAGE: The same to-do list from earlier, now showing a fourth item "Renew passport" appended at the bottom of the list. A dotted border around the new item with a label "Created by JavaScript". Caption: "After document.createElement, configuration, and append, the new item appears instantly at the bottom of the list." Style: realistic browser screenshot with the new item highlighted.
-->

The full three-step flow together:

```js
function addTask(text) {
  // Step 1: Create
  let li = document.createElement("li")

  // Step 2: Configure
  li.textContent = text
  li.classList.add("task-item")

  // Step 3: Attach
  document.querySelector(".task-list").append(li)
}

addTask("Renew passport")
```

### Insertion Methods: Controlling Where Elements Go

`append` places the new element at the end. But you often need more control over position. There are four modern insertion methods, each placing the element in a different spot relative to a reference element.

```js
parent.append(node)      // after the last child of parent
parent.prepend(node)     // before the first child of parent
reference.before(node)   // immediately before reference, as a sibling
reference.after(node)    // immediately after reference, as a sibling
```

Visually:

```
<ul>              ← prepend puts new node here (before first child)
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li> ← after puts new node here (after this sibling)
</ul>             ← append puts new node here (after last child)
```

**Real-world use -- pinned messages:**

In a chat application, a pinned message always appears at the top of the conversation. When a moderator pins a message, it is prepended to the message container so it sits above all other messages:

```js
let chatWindow = document.querySelector(".messages")
chatWindow.prepend(pinnedMessageEl)    // pins to the top
```

**Real-world use -- inserting a separator between items:**

On a news feed, a "You're all caught up" divider is inserted after the last item the user has already read:

```js
let lastReadItem = document.querySelector(".last-read")
let divider = document.createElement("div")
divider.classList.add("divider")
divider.textContent = "You're all caught up"

lastReadItem.after(divider)    // appears immediately after that item
```

**All four methods also accept plain strings** and multiple arguments at once:

```js
list.append("Some text", newElement1, newElement2)
```

#### appendChild() -- The Older Equivalent

Before the modern insertion methods existed, `appendChild` was the standard way to add a child element. It works like `append` but accepts only a single node:

```js
parent.appendChild(singleNode)
```

You will see this frequently in older code and tutorials. Both work -- `append` is more flexible for new code.

### insertAdjacentHTML(): Inserting HTML Strings at Specific Positions

Sometimes you have a string of HTML markup and want to insert it at a specific position without replacing what is already there. `insertAdjacentHTML` does exactly this.

```js
element.insertAdjacentHTML(position, htmlString)
```

The `position` argument is one of four string values that describe where the HTML goes relative to the element:

| Position | Where the HTML is inserted |
|---|---|
| `"beforebegin"` | Before the element itself (before its opening tag) |
| `"afterbegin"` | Inside the element, before its first child |
| `"beforeend"` | Inside the element, after its last child |
| `"afterend"` | After the element itself (after its closing tag) |

```html
<!-- beforebegin -->
<ul class="task-list">
  <!-- afterbegin -->
  <li>Item 1</li>
  <li>Item 2</li>
  <!-- beforeend -->
</ul>
<!-- afterend -->
```

```js
let list = document.querySelector(".task-list")
list.insertAdjacentHTML("beforeend", "<li class='task-item'>Renew passport</li>")
```

**Real-world use -- notification toasts:**

A notification that slides in from the corner is inserted at the end of the body without disturbing anything that is already rendered:

```js
document.body.insertAdjacentHTML("beforeend", `
  <div class="toast">
    <p>Your order has been placed!</p>
  </div>
`)
```

**Safety note:** `insertAdjacentHTML` parses the string as HTML, exactly like `innerHTML`. Apply the same rule: never use user-supplied text in the HTML string. Use `createElement` + `textContent` when content comes from a user.

### Removing Elements

#### element.remove()

The simplest way to delete an element from the page: call `remove()` on it directly.

```js
let taskToDelete = document.querySelector(".task-item.completed")
taskToDelete.remove()
// The element is gone from the DOM and the page immediately
```

**Real-world use -- deleting a to-do item:**

Each task item has a delete button. When the user clicks it, the button's parent `<li>` is removed:

```js
let deleteBtn = document.querySelector(".delete-btn")
deleteBtn.parentElement.remove()    // remove the whole li, not just the button
```

**Real-world use -- dismissing a notification:**

On any website with closable banners or cookie consent popups, the close button removes the banner element:

```js
let banner = document.querySelector(".cookie-banner")
banner.remove()
```

#### element.removeChild()

The older alternative, called on the parent rather than the element itself:

```js
parent.removeChild(childNode)
```

```js
let list = document.querySelector(".task-list")
let firstItem = list.firstElementChild
list.removeChild(firstItem)
```

This produces the same result as `firstItem.remove()`. You will see `removeChild` in older code; `remove()` is cleaner for new code.

### Replacing Elements

`element.replaceWith(newNode)` swaps an element out for a new one in a single step:

```js
let oldCard = document.querySelector(".product-card.outdated")

let newCard = document.createElement("div")
newCard.classList.add("product-card")
newCard.textContent = "Updated Product"

oldCard.replaceWith(newCard)
```

**Real-world use:** On a dashboard where a loading skeleton placeholder is replaced with real content once data arrives from the server.

### Cloning Elements: cloneNode()

When you need a copy of an existing element -- same structure, same classes, same attributes -- `cloneNode()` creates one without you having to rebuild it from scratch.

```js
element.cloneNode(false)    // clones the element only, not its children
element.cloneNode(true)     // clones the element and its entire subtree
```

**Real-world use -- duplicating a card:**

A project management board (like Trello) allows duplicating a card. The original card is cloned and appended to the same column:

```js
let originalCard = document.querySelector(".task-card")
let duplicate = originalCard.cloneNode(true)    // copies the card and all its content

originalCard.parentElement.append(duplicate)
```

`cloneNode(false)` gives you an empty copy of just the outer element. `cloneNode(true)` gives you the full tree including all nested elements and their text. Use `true` when you need a complete working copy, `false` when you want the shell but plan to fill it yourself.

### DocumentFragment: Inserting Many Elements Efficiently

Every time you attach an element to the live DOM, the browser may recalculate layout and repaint parts of the page. This is called a **reflow**. For one or two elements, this is not noticeable. But if you are rendering a list of 100 items in a loop, triggering 100 separate reflows makes the page visibly slow.

`DocumentFragment` is an in-memory container that holds a tree of elements. It is not part of the visible DOM. You build your entire structure inside it, and then attach it to the page in one operation -- causing only one reflow.

```js
let fragment = document.createDocumentFragment()

let tasks = ["Buy groceries", "Pay electricity bill", "Call the dentist"]

tasks.forEach(text => {
  let li = document.createElement("li")
  li.textContent = text
  li.classList.add("task-item")
  fragment.append(li)    // building inside fragment, not the live DOM
})

document.querySelector(".task-list").append(fragment)
// All three items inserted in a single DOM operation
```

The fragment itself does not appear on the page -- only its children are inserted. Think of it as a temporary staging area.

**Real-world use:** Any time you are rendering a list from a data array (a product catalogue, a message history, a search results page), build into a `DocumentFragment` and attach once. This is standard practice in performance-sensitive UI code.

### Let's Define Things Now...

> **document.createElement(tag):** Creates a new DOM element in memory. The element is not on the page until it is attached with an insertion method.

> **append(...nodes):** Inserts one or more nodes or strings at the end of a parent element's children.

> **prepend(...nodes):** Inserts at the beginning of a parent element's children.

> **before(node) / after(node):** Inserts a node immediately before or after the element itself (as a sibling, not a child).

> **appendChild(node):** Older single-node version of append. Still valid; append is preferred in new code.

> **element.remove():** Removes the element from the DOM entirely.

> **element.replaceWith(newNode):** Replaces the element with a new node in one step.

> **cloneNode(deep):** Creates a copy of an element. `cloneNode(false)` copies only the element; `cloneNode(true)` copies the full subtree.

> **insertAdjacentHTML(position, html):** Inserts an HTML string at a specific position relative to an element without replacing existing content. Never use with user input.

> **DocumentFragment:** A lightweight in-memory container for building a tree of elements before inserting them into the live DOM in a single operation, avoiding multiple reflows.

> **Reflow:** The browser recalculating layout when the DOM changes. Minimised by batching DOM insertions using DocumentFragment.

### Activity: Dynamic List Builder

1. Write a function `addTask(text)` that creates an `<li>` element with the given text and a "Delete" button inside it, then appends it to a `<ul>` on the page.

2. Make the Delete button inside each task item call `parentElement.remove()` when clicked (for now, you can call the handler manually -- the next article covers event listeners properly).

3. Use `prepend` to add a "pinned" task to the top of the list regardless of when it is added.

4. Build an initial list of five tasks from an array using a `DocumentFragment`. Compare this to appending each one directly in a loop -- the result is the same but the fragment version causes only one reflow.

5. Clone an existing task item using `cloneNode(true)` and append the clone to the list. Change the clone's text content to "Cloned Task".

6. Use `insertAdjacentHTML` with `"beforeend"` to insert a styled divider `<li class="divider">---</li>` between the third and fourth items.

7. Add the following to `chatbot-logic.js`:

```js
function renderMessage(container, sender, text) {
  let messageEl = document.createElement("div")
  messageEl.classList.add("message", sender)

  let textEl = document.createElement("p")
  textEl.textContent = text    // textContent -- never innerHTML for user text

  messageEl.append(textEl)
  container.append(messageEl)
}

function renderMessageHistory(container, messages) {
  let fragment = document.createDocumentFragment()

  messages.forEach(msg => {
    let el = document.createElement("div")
    el.classList.add("message", msg.sender)
    el.textContent = msg.text
    fragment.append(el)
  })

  container.append(fragment)    // one DOM operation for all messages
}
```

## What's Coming Next...

You can now build and modify the page structure with JavaScript. But everything so far has run immediately when the script loads -- none of it waits for the user. The missing piece is events: how does JavaScript know when a button was clicked, when a key was pressed, or when a form was submitted? The next article covers event listeners -- the mechanism that makes a website respond to the user rather than just display to them.
