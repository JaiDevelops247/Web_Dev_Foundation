## Introduction

Open Amazon and add three items to your cart. Look at the page carefully. The cart icon in the navbar shows "3". The cart sidebar (if open) lists all three items. The subtotal at the bottom updates to reflect all three prices. If you remove one item, all three of those places update simultaneously -- the badge becomes "2", the item disappears from the list, and the subtotal recalculates.

<!-- 
IMAGE: An e-commerce page showing a cart interaction. Top-right navbar has a cart icon with a badge showing "3". A cart sidebar is open on the right showing three product items with names, prices, and a remove button each. At the bottom of the sidebar, a subtotal "₹6,499" is shown. Caption: "The same cart data appears in three places simultaneously: the navbar badge, the item list, and the subtotal. All three must stay in sync." Style: realistic browser screenshot of an e-commerce cart sidebar.
-->

Now think about how a beginning developer might build this. They add an item and manually update each part of the UI:

```js
function addToCart(product) {
  // update the badge
  let badge = document.querySelector(".cart-badge")
  badge.textContent = parseInt(badge.textContent) + 1

  // add item to the list
  let cartList = document.querySelector(".cart-items")
  let li = document.createElement("li")
  li.textContent = product.name
  cartList.append(li)

  // update the subtotal
  let subtotal = document.querySelector(".cart-subtotal")
  subtotal.textContent = "₹" + (parseFloat(subtotal.textContent.slice(1)) + product.price)
}
```

This works for one action. But when the user removes an item, you have to reverse all three updates. When they change a quantity, you update the badge and the subtotal but not the list. When you want to show the item count on the checkout page too, you add a fourth place to update. Every new feature means remembering every place the cart data appears and writing code to sync each one manually.

The DOM is now serving two roles at once: it is both the display of the data and the source of truth for the data. The badge is not just showing the count -- it *is* the count. When you need to know how many items are in the cart, you read `badge.textContent`. When there is a bug, you cannot tell whether the display is wrong or the underlying data is wrong, because they are the same thing.

This breaks down quickly. There is a better approach.

### The State-First Pattern

**The core idea:** Define your application data as a plain JavaScript object (the **state**). Write a single function that reads the state and builds the UI from it (the **render function**). When something changes, update the state and call render again.

The DOM becomes a consequence of the state -- it always reflects the state, never defines it.

```js
// The state: the actual source of truth
let cartState = {
  items: [],
  total: 0
}

// The render function: builds the UI from the state
function renderCart() {
  // badge
  let badge = document.querySelector(".cart-badge")
  badge.textContent = cartState.items.length

  // item list
  let cartList = document.querySelector(".cart-items")
  cartList.innerHTML = ""    // clear the current list

  cartState.items.forEach(function(item) {
    let li = document.createElement("li")
    li.textContent = `${item.name} -- ₹${item.price}`
    cartList.append(li)
  })

  // subtotal
  let subtotal = document.querySelector(".cart-subtotal")
  subtotal.textContent = `₹${cartState.total}`
}
```

Now adding an item is simple:

```js
function addToCart(product) {
  cartState.items.push(product)
  cartState.total += product.price
  renderCart()    // re-render everything from the updated state
}
```

And removing an item:

```js
function removeFromCart(productId) {
  let item = cartState.items.find(i => i.id === productId)
  cartState.items = cartState.items.filter(i => i.id !== productId)
  cartState.total -= item.price
  renderCart()    // the same render function handles this too
}
```

You only write `renderCart` once. Every action -- add, remove, change quantity -- just modifies the state object and calls the same function. The function always builds a correct UI because it always reads from the state, never from the DOM.

<!-- 
IMAGE: A diagram showing two boxes on the left: "addToCart()" and "removeFromCart()" both pointing with arrows to a middle box "cartState { items, total }". From cartState, a single arrow points to "renderCart()" on the right, which points to three DOM elements: the badge, the item list, and the subtotal. Caption: "State is the single source of truth. Any action updates the state. render() always rebuilds the UI from state." Style: flat vector flow diagram, left-to-right, clear arrows and labeled boxes.
-->

#### Why This Is Better

When a bug appears, you look at the state object first. Is the data correct? If yes, the bug is in the render function. If no, the bug is in whichever function modified the state. The two concerns -- data and display -- are now separate and independently debuggable.

When you need to display cart data in a new place (a checkout summary, an order confirmation), you just read from `cartState` in a new function. You do not touch `renderCart`.

When a new action needs to change the cart (apply a discount code, clear on checkout), it modifies `cartState` and calls `renderCart`. One new function, one call. No hunting through the UI to remember what else needs updating.

This pattern -- state as a JavaScript object, render as a pure function of that state -- is the foundation of every modern frontend framework. React, Vue, and Svelte all build on this same idea. Learning it now means when you encounter those frameworks, the underlying logic will already be familiar.

### Re-rendering: Rebuilding vs Patching

The simplest re-render approach is what the example above does: clear the container and rebuild it from scratch on every state change.

```js
cartList.innerHTML = ""    // clear
cartState.items.forEach(...)    // rebuild
```

This is easy to reason about and correct in all cases. The downside is efficiency: on every change, every item is destroyed and recreated, even the ones that did not change. For a cart with five items, this is not noticeable. For a list with thousands of rows that updates many times per second, it becomes a problem.

The alternative is a surgical update -- only change the specific DOM element that corresponds to the changed data:

```js
function updateSubtotalOnly() {
  document.querySelector(".cart-subtotal").textContent = `₹${cartState.total}`
}
```

In practice, start with full re-render. It is correct, simple, and fast enough for most real applications. Move to surgical updates only if you observe actual performance problems.

### localStorage: Persisting State Across Page Refreshes

The state pattern solves the sync problem. But `cartState` is a JavaScript object in memory. When the user closes the tab or refreshes the page, memory is cleared. The cart is gone.

Earlier in the course, we saw how browsers cache website resources -- HTML files, CSS, JavaScript, images -- so that on a return visit, those files do not need to be downloaded again. The browser stores them locally, and subsequent visits load faster.

`localStorage` is a different kind of storage in the browser, but the idea of "keeping something locally instead of fetching it from a server every time" is the same. The difference is that `localStorage` is storage your JavaScript controls directly. You decide what to put in, what to read, and when to clear it. It persists even after the tab is closed and survives page refreshes.

**The key limitation:** `localStorage` stores only strings. Objects must be serialised to JSON before storing and parsed back after reading. This is exactly what `JSON.stringify` and `JSON.parse` are for.

#### The Four Operations

```js
localStorage.setItem("key", value)    // store -- value must be a string
localStorage.getItem("key")           // read -- returns the string, or null if key does not exist
localStorage.removeItem("key")        // delete one key
localStorage.clear()                  // delete everything in localStorage
```

**Storing the cart state:**

```js
function saveCart() {
  localStorage.setItem("cart", JSON.stringify(cartState))
}
```

**Reading it back:**

```js
let stored = localStorage.getItem("cart")

if (stored !== null) {
  cartState = JSON.parse(stored)
} else {
  cartState = { items: [], total: 0 }    // default if nothing is saved yet
}
```

Always check for `null` before parsing. `localStorage.getItem` returns `null` (not an empty string) when the key has never been set. Calling `JSON.parse(null)` returns `null`, which would replace your state object with `null` and break everything. The `null` check makes the code safe on first visit.

#### Putting It Together: Save on Update, Load on Start

The complete pattern:

**Save every time state changes:**

```js
function addToCart(product) {
  cartState.items.push(product)
  cartState.total += product.price
  renderCart()
  saveCart()    // persist after every state change
}

function removeFromCart(productId) {
  let item = cartState.items.find(i => i.id === productId)
  cartState.items = cartState.items.filter(i => i.id !== productId)
  cartState.total -= item.price
  renderCart()
  saveCart()
}

function saveCart() {
  localStorage.setItem("cart", JSON.stringify(cartState))
}
```

**Load when the page starts:**

```js
document.addEventListener("DOMContentLoaded", function() {
  let stored = localStorage.getItem("cart")

  if (stored !== null) {
    cartState = JSON.parse(stored)
  }

  renderCart()    // always render on load, whether from storage or defaults
})
```

Now the cart survives page refreshes. The user adds items, closes the tab, comes back tomorrow, and the cart is exactly as they left it.

<!-- 
IMAGE: Two browser windows side by side. Left window shows an e-commerce cart with two items and a total of ₹3,200. The tab is being closed (an X animation on the tab). Right window shows the same browser reopened the next day, with the same two items and total still in the cart. A label between them: "localStorage persisted the state". Caption: "localStorage saves the state as a JSON string. On the next visit, the state is loaded and the cart is restored exactly as the user left it." Style: realistic browser screenshot side-by-side with a persistence arrow between them.
-->

#### What localStorage Is and Is Not For

`localStorage` is the right tool when:
- You want to persist user preferences (dark mode, language choice, font size)
- You want to restore UI state across sessions (a draft message, a half-filled form, a cart)
- You are building a small application that does not need a backend database

`localStorage` is not the right tool when:
- The data needs to be shared across devices (it is browser-specific and device-specific)
- The data is sensitive (it is accessible to any JavaScript on the page -- do not store passwords or tokens)
- The data is large (storage is limited to around 5MB per origin)

For our chatbot, localStorage is appropriate: it can remember the user's name, their preferred settings, and recent conversation history for a single browser session.

### Let's Define Things Now...

> **State:** A plain JavaScript object that holds the application's current data. The single source of truth that the UI is derived from.

> **Render function:** A function that reads the state and builds or updates the DOM to reflect it. Called after every state change.

> **State-first pattern:** Update state first, then call render. The DOM is always a consequence of the state, never a source of truth.

> **localStorage:** Browser-provided key-value storage that persists across page refreshes and tab closures. Stores strings only.

> **localStorage.setItem(key, value):** Stores a string. Use JSON.stringify to convert objects first.

> **localStorage.getItem(key):** Returns the stored string, or null if the key does not exist. Use JSON.parse to convert back to an object.

> **localStorage.removeItem(key):** Deletes a specific key from storage.

> **localStorage.clear():** Deletes all keys stored by this origin.

> **Save-on-update pattern:** Call a save function at the end of every function that modifies state, so localStorage always reflects the latest state.

> **DOMContentLoaded initialisation:** Load persisted state from localStorage when the page starts, fall back to defaults if nothing is stored, then render.

### Activity: State and Storage

1. Build a shopping cart with the state-first pattern. The state is `{ items: [], total: 0 }`. Write `addToCart(product)`, `removeFromCart(id)`, and `renderCart()`. Confirm that calling any action re-renders all three UI parts (badge, list, total) correctly.

2. Add `saveCart()` and update `addToCart` and `removeFromCart` to call it after every change. Then add a `DOMContentLoaded` listener that loads from localStorage and renders.

3. Open DevTools, go to Application > Local Storage, and inspect the saved JSON. Manually delete the key and reload the page to confirm the default empty state loads correctly.

4. Add `localStorage.removeItem("cart")` to a "Clear Cart" button and call `renderCart()` after.

5. Add the following to `chatbot-logic.js`:

```js
let appState = {
  messages: [],
  userName: "Guest",
  messageCount: 0
}

function saveState() {
  localStorage.setItem("chatbot-state", JSON.stringify(appState))
}

function loadState() {
  let stored = localStorage.getItem("chatbot-state")
  if (stored !== null) {
    appState = JSON.parse(stored)
  }
}

function addMessage(sender, text) {
  appState.messages.push({ sender, text, timestamp: new Date().toISOString() })
  appState.messageCount++
  saveState()
}

document.addEventListener("DOMContentLoaded", function() {
  loadState()
  console.log(`Welcome back, ${appState.userName}. You have ${appState.messageCount} messages in history.`)
})
```

## What's Coming Next...

You can now build interactive UI that stays in sync, responds to user input, and remembers its state between visits. But all the data in these examples lives in the browser. Real applications need to communicate beyond the browser: fetching a list of products from a database on a server, submitting a form to an API that processes and stores the data, receiving real-time updates from a backend service.

In the next module we look at how JavaScript sends requests to and receives responses from a server anywhere in the world -- the Fetch API, what Promises are and why asynchronous code needs them, and how async and await turn that complexity into readable, sequential-looking code. That is the bridge between a local JavaScript application and a real, live, data-driven product.
