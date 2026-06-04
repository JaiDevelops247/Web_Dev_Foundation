## Introduction

Look at this code:

```js
document.body.addEventListener("click", function() {
  document.body.style.backgroundColor = "lightblue"
})
```

Simple enough. Clicking anywhere on the page turns the background light blue. The listener is on `document.body`, and clicking the body triggers it.

Now put a button inside the body:

```html
<body>
  <div class="container">
    <button id="action-btn">Click Me</button>
  </div>
</body>
```

```js
document.body.addEventListener("click", function() {
  document.body.style.backgroundColor = "lightblue"
})
```

Click the button. The background turns light blue.

That might not surprise you -- the button is inside the body, so clicking it also clicks the body in some sense. But what is actually happening here is more specific than that, and it has consequences that affect how you write event handling code in real applications.

The button is three levels deep: `body > div > button`. You clicked the button. The body's listener fired. Why?

### Events Travel Upward: Bubbling

When an event fires on an element, it does not stay there. After the element's own listeners run, the event travels upward to the parent, then to the parent's parent, then further up, all the way to the `document`. At each level, any matching listeners run.

This upward travel is called **event bubbling**.

```
Click on <button>
  -> button's click listeners run
  -> event bubbles up to <div>
  -> div's click listeners run
  -> event bubbles up to <body>
  -> body's click listeners run  <-- this is why the background changed
  -> event bubbles up to document
  -> document's click listeners run
```

<!-- 
IMAGE: A vertical DOM tree showing body > div.container > button#action-btn. Arrows point upward from the button through the div to the body, each arrow labeled "event bubbles up". Small listener icons sit next to the body node. Caption: "A click on the button travels upward through every ancestor. Any listener attached to any ancestor will fire." Style: flat vector tree diagram with upward-pointing arrows showing the bubble path.
-->

This is not an accident or a bug. Bubbling is a deliberate design of the browser's event system. And once you understand it, it becomes one of the most useful tools in DOM programming.

### The Problem It Reveals

Bubbling also reveals a problem you would hit in practice. Say you have a to-do list with 50 items. Each item has a delete button. Your first instinct might be to attach a click listener to every delete button:

```js
let deleteButtons = document.querySelectorAll(".delete-btn")

deleteButtons.forEach(function(btn) {
  btn.addEventListener("click", function() {
    btn.parentElement.remove()
  })
})
```

This works -- for the items that existed when the page loaded. But what happens when the user adds a new task? The new `<li>` and its delete button were created after the `querySelectorAll` ran. They were not in the selection. They have no listener. Clicking their delete button does nothing.

You could re-run the selection and re-attach listeners every time a new item is added, but that is fragile and wasteful. There is a cleaner solution that bubbling makes possible.

### Event Delegation

**Definition:** Event delegation is the pattern of attaching a single event listener to a parent element and using the `event.target` property to determine which specific child triggered the event.

Instead of fifty listeners on fifty buttons, one listener on the list handles all of them:

```js
let taskList = document.querySelector(".task-list")

taskList.addEventListener("click", function(event) {
  if (event.target.classList.contains("delete-btn")) {
    event.target.parentElement.remove()
  }
})
```

When any delete button is clicked, the click bubbles up to the list. The list's listener fires. `event.target` is the exact element that was clicked. If it has the class `delete-btn`, the handler removes its parent item.

<!-- 
IMAGE: A to-do list with three items, each having a "Delete" button. A single listener icon sits on the parent <ul> element. Dotted upward arrows from each delete button converge at the ul. Caption: "One listener on the parent catches clicks from all children via bubbling. event.target identifies which child was actually clicked." Style: clean UI screenshot of a task list with the delegation pattern illustrated.
-->

**Why this also solves the dynamic element problem:**

When the user adds a new item, its delete button automatically works. The new button did not need a listener attached to it -- when clicked, it bubbles up to the list, and the list's listener was always there. Any new child that bubbles through the parent is handled automatically.

This is why event delegation is not just a performance optimisation. For dynamically created elements, it is the only approach that works without re-attaching listeners every time the DOM changes.

### event.target.closest(): Finding the Right Ancestor

`event.target` gives you the exact element that was clicked. But in a real component, that might be something very small -- an icon inside a button, or a `<span>` inside a list item. You do not want to handle the icon; you want to handle the card or list item it belongs to.

`closest(selector)` is a method on any DOM element that walks up the tree from that element and returns the first ancestor that matches the CSS selector. If no match is found, it returns `null`.

```js
element.closest("cssSelector")
```

```html
<li class="task-item" data-id="3">
  <span class="task-text">Buy groceries</span>
  <button class="delete-btn">
    <img src="trash-icon.svg" alt="Delete">
  </button>
</li>
```

If the user clicks the `<img>` inside the button, `event.target` is the `<img>`. You do not want that -- you want the `<li>`.

```js
taskList.addEventListener("click", function(event) {
  let item = event.target.closest(".task-item")

  if (item) {
    item.remove()
  }
})
```

`closest(".task-item")` starts at the `<img>`, walks up to `<button>`, walks up to `<li class="task-item">`, finds a match, and returns it. No matter how nested the actual click target is, `closest` finds the component root you care about.

**Real-world use -- product cards:**

On a shopping site, each product card has multiple clickable areas: the image, the title, the "Add to Cart" button, the wishlist icon. One listener on the product grid handles all of them:

```js
let productGrid = document.querySelector(".product-grid")

productGrid.addEventListener("click", function(event) {
  let card = event.target.closest(".product-card")
  if (!card) return    // click was outside any card

  let productId = card.getAttribute("data-id")

  if (event.target.closest(".add-to-cart-btn")) {
    addToCart(productId)
  } else if (event.target.closest(".wishlist-btn")) {
    addToWishlist(productId)
  }
})
```

One listener. Multiple actions. Any new cards added to the grid work immediately.

### event.target.matches(): Checking the Target Directly

While `closest` walks upward to find a matching ancestor, `matches` checks whether the target itself satisfies a selector. It returns `true` or `false`.

```js
event.target.matches("cssSelector")
```

```js
list.addEventListener("click", function(event) {
  if (event.target.matches(".delete-btn")) {
    event.target.parentElement.remove()
  }
})
```

Use `matches` when you expect the user to click the element itself and not a child inside it. Use `closest` when the user might click any child nested within a component and you need to find the component root.

In practice, `closest` is safer for complex components. `matches` works well for simple cases where the clickable element has no meaningful children.

### stopPropagation: Stopping the Bubble

You saw `event.stopPropagation()` mentioned in the previous article. Now that you understand bubbling, you can see what it actually does: it stops the event from travelling further up the tree after the current handler runs.

```js
button.addEventListener("click", function(event) {
  event.stopPropagation()    // the click stops here, does not reach parent listeners
  handleButtonClick()
})
```

There are legitimate uses: a modal's overlay listens for clicks to close the modal, but clicking inside the modal content should not trigger the overlay's listener. Stopping propagation on the inner content prevents this.

```js
let overlay = document.querySelector(".modal-overlay")
let modalContent = document.querySelector(".modal-content")

overlay.addEventListener("click", closeModal)

modalContent.addEventListener("click", function(event) {
  event.stopPropagation()    // click inside modal does not reach the overlay
})
```

**Why to use it sparingly:**

Every time you stop propagation, you silently prevent all parent listeners from receiving the event -- including ones you did not write and may not even know about. Third-party libraries, analytics scripts, and delegation patterns on ancestor elements may depend on receiving bubbled events. Stopping propagation in one place can break behaviour in a completely different part of the page in ways that are very hard to debug.

The rule: stop propagation only when you have a specific, narrow reason. Never use it as a shortcut to avoid thinking through your delegation structure.

### The Capturing Phase: Events Travel Down First

The full picture of event flow has three phases, not one:

1. **Capturing phase:** The event travels from the document down through the tree to the target element.
2. **Target phase:** The event is at the target element itself.
3. **Bubbling phase:** The event travels back up from the target to the document.

By default, `addEventListener` listens during the **bubbling phase** -- after the event has already reached the target. This is the correct default for nearly all use cases.

To listen during the **capturing phase** instead, pass `true` as the third argument:

```js
element.addEventListener("click", handler, true)    // captures on the way down
element.addEventListener("click", handler, false)   // bubbles on the way up (default)
```

A listener in the capturing phase runs before any listener in the bubbling phase, because capturing happens first. You will rarely need this -- almost all practical event handling uses bubbling. But understanding that capturing exists explains why events behave the way they do at the target level when multiple listeners are involved.

<!-- 
IMAGE: A DOM tree with document at the top, then body, then div, then button at the bottom. Three phases shown with arrows: downward arrows labeled "1. Capturing" traveling from document to button, a circle at button labeled "2. Target", and upward arrows labeled "3. Bubbling" traveling back up to document. Caption: "Every DOM event has three phases. Listeners fire during bubbling by default. Passing true as the third argument to addEventListener enables capturing." Style: flat vector tree diagram with three distinct arrow sets and phase labels.
-->

### Catching These Behaviours Is Part of Good Development

Bubbling and delegation are not advanced topics you revisit later -- they come up constantly as soon as you start building real interfaces. A delete button that does not work on newly added items, a modal that closes when you click inside it, a card where clicking any child element triggers the wrong action: all of these are bubbling-related bugs.

Understanding bubbling means you write event handling that works correctly the first time rather than discovering mysterious failures after the fact. Applying delegation means your code handles dynamic content naturally instead of requiring manual re-wiring every time the DOM changes.

These are the habits of a developer who thinks about how the browser works, not just what the code says.

### What's Coming Next...

You now have all the tools to build interfaces that respond intelligently to user interaction: selecting elements, modifying them, creating and removing them dynamically, and handling events with delegation. The next article puts all of this together in one of the most common and important real-world scenarios: form handling. Reading values, validating input, preventing default submission, showing error messages, and giving the user feedback -- all driven by the JavaScript patterns you have learned so far.
