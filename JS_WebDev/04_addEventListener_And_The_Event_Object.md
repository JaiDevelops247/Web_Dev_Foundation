## Introduction

In the previous two articles, you learned how to find elements on the page and change them -- update text, toggle a class, create a new item, remove an old one. You wrote code like this:

```js
let list = document.querySelector(".task-list")
let newItem = document.createElement("li")
newItem.textContent = "Renew passport"
list.append(newItem)
```

This code runs immediately when the script loads. The item is added to the list the moment the page opens, whether the user wanted it or not. But that is not how real websites work. Real websites wait. They do nothing until the user does something first -- clicks a button, types in a field, submits a form -- and then they respond.

Think about the "Add" button on a to-do list. Nothing happens when the page loads. The user types a task, presses the Add button, and only then does the new item appear. Or think about the search bar on Google: as you type each character, suggestions update in real time. Or a "Delete" button on a cart item: it waits until the user explicitly clicks it.

The browser needs to know two things to make this work: what the user did (the **event**), and what code to run in response (the **handler**). This is event-driven programming -- instead of code running from top to bottom once, your code sits idle and waits to be triggered.

### Two Parts of Every Interaction

Every interaction on a web page breaks into exactly two parts:

**Part 1: The event.** The user does something -- clicks a button, presses a key, moves the mouse, submits a form. The browser recognises this as an event and gives it a name like `"click"`, `"keydown"`, or `"submit"`.

**Part 2: The handler.** A function you write that runs when that event happens. It contains the code that should execute in response -- create the new item, update the suggestion list, remove the cart entry.

Your job is to connect the two: tell the browser "when this event happens on this element, run this function."

### Event Types: How the User Can Interact

Before you can listen for an event, you need to know its name. The browser has a specific string for each type of interaction.

#### Mouse Events

| Event | When it fires |
|---|---|
| `click` | User clicks (mousedown + mouseup on the same element) |
| `mouseenter` | Mouse pointer enters the element -- does not bubble |
| `mouseleave` | Mouse pointer leaves the element -- does not bubble |
| `mouseover` | Mouse enters the element or any of its children -- bubbles |
| `mouseout` | Mouse leaves the element or any of its children -- bubbles |

`click` is the most-used event on the web. Nearly every button, link, card, and toggle uses it.

`mouseenter` and `mouseleave` are the correct events for hover effects in JavaScript because they fire only when the mouse crosses the element's own boundary, not when it moves between children inside the element. The difference between these and `mouseover`/`mouseout` becomes important in the next article when we cover bubbling.

#### Keyboard Events

| Event | When it fires |
|---|---|
| `keydown` | A key is pressed down (fires repeatedly if held) |
| `keyup` | A key is released |

The event object (covered shortly) carries two useful properties for keyboard events:

- `event.key` -- the character or key name: `"a"`, `"Enter"`, `"Escape"`, `"ArrowUp"`
- `event.code` -- the physical key on the keyboard: `"KeyA"`, `"Enter"`, `"ArrowUp"`

Use `event.key` when you care about what character was typed. Use `event.code` when you care about the physical position of the key (for games or keyboard shortcuts where layout matters).

#### Input and Form Events

| Event | When it fires |
|---|---|
| `input` | Fires on every change to an input field's value, in real time |
| `change` | Fires when an input loses focus after its value was modified |
| `submit` | Fires when a form is submitted (button click or Enter in a field) |
| `focus` | Fires when an element gains focus (user clicks or tabs into it) |
| `blur` | Fires when an element loses focus |

`input` fires on every single keystroke -- use it for live updates like character counters or search suggestions. `change` fires only when the user is done editing and moves away -- use it for dropdowns or checkboxes where you only care about the final value.

`submit` fires on the form element, not the button. This is important -- always listen for `submit` on the `<form>` tag rather than `click` on the submit button, because users can also submit by pressing Enter.

### addEventListener: Connecting Events to Handlers

`addEventListener` is the method that connects an event type to a handler function. You call it on the element you want to watch.

**Syntax:**

```js
element.addEventListener("eventType", handlerFunction)
```

**Basic example -- a button that adds a task:**

```js
let addBtn = document.querySelector("#add-btn")

addBtn.addEventListener("click", function() {
  let list = document.querySelector(".task-list")
  let li = document.createElement("li")
  li.textContent = "New task"
  list.append(li)
})
```

The function passed as the second argument runs every time the button is clicked. Nothing runs until the user clicks.

#### Named vs Anonymous Handler Functions

The handler can be an anonymous function (written inline, as above) or a named function reference:

```js
// Named function -- defined separately, passed by reference
function handleAddClick() {
  let list = document.querySelector(".task-list")
  let li = document.createElement("li")
  li.textContent = "New task"
  list.append(li)
}

addBtn.addEventListener("click", handleAddClick)
// Note: no parentheses -- you are passing the function, not calling it
```

Both work. The important difference is that named functions can be removed later (covered next), while anonymous functions cannot. A common mistake is writing `addEventListener("click", handleAddClick())` with parentheses -- this calls the function immediately and passes its return value (usually `undefined`) as the handler. Always pass the function reference without parentheses.

**Real-world use -- live character counter:**

```js
let input = document.querySelector("#message-input")
let counter = document.querySelector(".char-count")

input.addEventListener("input", function() {
  let remaining = 280 - input.value.length
  counter.textContent = `${remaining} characters remaining`
})
```

Every keystroke triggers the handler and the counter updates instantly.

<!-- 
IMAGE: A text compose box (similar to Twitter's tweet composer) showing a partially typed message. Below the textarea, a character counter displays "214 characters remaining". Caption: "The input event fires on every keystroke. Each time it fires, the handler reads the current value and updates the counter." Style: realistic browser screenshot of a social media compose area with character count.
-->

**Real-world use -- form submission:**

```js
let form = document.querySelector("#login-form")

form.addEventListener("submit", function(event) {
  event.preventDefault()    // stop the page from reloading
  // validate and process the form
})
```

The `event` parameter here is the event object -- more on this in a moment.

**Attaching the same event type multiple times:**

You can call `addEventListener` multiple times on the same element. Each handler runs independently when the event fires:

```js
btn.addEventListener("click", logClick)
btn.addEventListener("click", updateUI)
btn.addEventListener("click", sendAnalytics)
// all three run on each click
```

### removeEventListener: Detaching a Handler

`removeEventListener` removes a previously attached handler. It requires the exact same event type string and the exact same function reference that was used when adding:

```js
element.removeEventListener("eventType", handlerFunction)
```

```js
function handleClick() {
  console.log("clicked")
}

btn.addEventListener("click", handleClick)

// Later, when you no longer need the handler:
btn.removeEventListener("click", handleClick)
```

**Why the function reference must match:**

JavaScript identifies functions by reference, not by content. Two functions that contain identical code are still two different functions. This is why anonymous inline functions cannot be removed -- every time you write `function() { ... }`, it creates a brand new function object, and there is no way to pass that same reference to `removeEventListener` later.

```js
// This does NOT work -- the two anonymous functions are different objects
btn.addEventListener("click", function() { console.log("clicked") })
btn.removeEventListener("click", function() { console.log("clicked") })  // no effect
```

**When to use removeEventListener:**

- A one-time action: a tutorial tooltip that should stop listening after the user has seen it once
- A timer that should stop responding after it expires
- Cleaning up event listeners when a component is removed from the page (important in frameworks like React to prevent memory leaks)

```js
function handleFirstClick() {
  showWelcomeMessage()
  btn.removeEventListener("click", handleFirstClick)    // only fires once
}

btn.addEventListener("click", handleFirstClick)
```

### The Event Object: Everything Is an Object

We have already established in the JS Basics module that almost everything in JavaScript is an object. This applies to events too.

Every time an event fires and calls your handler, the browser automatically creates an **event object** describing exactly what happened -- what element was involved, what key was pressed, where the mouse was, and more. This object is passed as the first argument to your handler function.

```js
element.addEventListener("click", function(event) {
  // event is the event object the browser created
  console.log(event)
})
```

You can name this parameter anything (`event`, `e`, `evt` are all common), but `event` is the most readable.

<!-- 
IMAGE: A browser DevTools console showing the logged event object expanded. Visible properties include: type: "click", target: button#submit-btn, currentTarget: button#submit-btn, key: undefined, clientX: 412, clientY: 280. Caption: "The event object is a full JavaScript object with properties describing every detail of what happened." Style: realistic DevTools console screenshot with the event object expanded.
-->

The properties you will use most often:

#### event.type

The name of the event that fired:

```js
element.addEventListener("click", function(event) {
  console.log(event.type)    // "click"
})
```

Useful when one handler is attached to multiple event types and you need to know which one triggered it.

#### event.target

The specific element the user actually interacted with -- the element that originated the event.

```js
document.addEventListener("click", function(event) {
  console.log(event.target)    // the exact element the user clicked
})
```

If you click a `<button>` inside a `<div>`, `event.target` is the `<button>`. This becomes particularly powerful in the next article when we look at how events travel up the tree.

#### event.currentTarget

The element that the event listener is attached to -- which may be different from `event.target`.

```js
let card = document.querySelector(".product-card")

card.addEventListener("click", function(event) {
  console.log(event.target)          // whatever inside the card was clicked
  console.log(event.currentTarget)   // always the .product-card
})
```

Inside a regular function handler, `this` also refers to `event.currentTarget`.

#### event.preventDefault()

Stops the browser from performing its default action for the event.

The browser has built-in behaviour for many events: clicking a link navigates to the `href`, submitting a form reloads the page, right-clicking shows a context menu. Calling `event.preventDefault()` cancels that default behaviour without stopping the event itself.

```js
// Stop a form from reloading the page on submit
form.addEventListener("submit", function(event) {
  event.preventDefault()
  // Now handle the submission with JavaScript instead
})

// Stop a link from navigating
link.addEventListener("click", function(event) {
  event.preventDefault()
  // Handle the click in JavaScript instead
})
```

**Real-world use:** Every single-page application (Gmail, Notion, Figma) uses `event.preventDefault()` on link clicks and form submissions to handle navigation and data without ever reloading the page.

#### event.stopPropagation()

Events in the browser do not just fire on the element you interacted with -- they travel upward through the DOM tree. Clicking a button also triggers a click on the button's parent, and then its grandparent, all the way up to the document. This is called **event bubbling**.

`event.stopPropagation()` stops the event from travelling further up the tree after the current handler runs.

```js
button.addEventListener("click", function(event) {
  event.stopPropagation()    // the click stops here, does not bubble up
  doSomething()
})
```

This is something you will understand much better in context. The next article covers exactly how and why bubbling happens, and when stopping it is the right call.

#### Keyboard Event Properties

For `keydown` and `keyup` events, two properties identify which key was pressed:

```js
document.addEventListener("keydown", function(event) {
  console.log(event.key)     // "Enter", "a", "Escape", "ArrowUp"
  console.log(event.code)    // "Enter", "KeyA", "Escape", "ArrowUp"

  if (event.key === "Escape") {
    closeModal()
  }

  if (event.key === "Enter") {
    submitForm()
  }
})
```

**Real-world use -- pressing Enter to send a message:**

In WhatsApp Web and Slack, pressing Enter sends the message. This is a `keydown` listener on the input field:

```js
let messageInput = document.querySelector("#message-input")

messageInput.addEventListener("keydown", function(event) {
  if (event.key === "Enter") {
    event.preventDefault()    // stop Enter from creating a new line
    sendMessage(messageInput.value)
    messageInput.value = ""
  }
})
```

### load and DOMContentLoaded: Knowing When the Page Is Ready

Two events tell you when different stages of the page loading process are complete.

#### DOMContentLoaded

Fires on the `document` object when the HTML has been fully parsed and the DOM is ready to be queried and modified -- before images, stylesheets, and other external resources have finished loading.

```js
document.addEventListener("DOMContentLoaded", function() {
  // Safe to query and manipulate the DOM here
  let btn = document.querySelector("#submit-btn")
  btn.addEventListener("click", handleSubmit)
})
```

**Why this matters:** If your JavaScript runs before the DOM is fully parsed, `document.querySelector` finds nothing -- the elements do not exist yet. Wrapping your setup code in `DOMContentLoaded` guarantees the DOM is ready before you try to work with it.

In practice, placing your `<script>` tag at the bottom of `<body>` (just before `</body>`) achieves the same thing and is the simpler approach. `DOMContentLoaded` is the explicit programmatic alternative.

#### load

Fires on the `window` object when the entire page is fully loaded -- HTML, images, stylesheets, fonts, and all external resources.

```js
window.addEventListener("load", function() {
  // Everything is loaded, including images and stylesheets
  console.log("Page fully loaded")
})
```

Use `load` when your code depends on images being present (reading their dimensions, for example). For most DOM manipulation work, `DOMContentLoaded` fires earlier and is the right choice.

### Something Interesting About How Events Travel

You may have noticed that `event.target` is the exact element the user clicked, but event listeners can be attached to parent elements and still receive those clicks. A click on a button inside a `<div>` triggers not just the button's listeners but also the `<div>`'s listeners, and the parent above that, and so on.

This is event bubbling -- events do not stay on the element where they fired. They rise up through the DOM tree, triggering listeners on every ancestor along the way.

This behaviour has a particularly useful application in web development: instead of attaching a listener to every item in a list, you can attach one listener to the list itself and handle all item interactions from there. It is called event delegation, and it is one of the most practical patterns you will use when building real interfaces.

That is exactly what the next article covers.
