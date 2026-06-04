## Introduction

The previous article ended with a selected element. You ran `document.querySelector(".message")` and got back a DOM node -- a JavaScript object representing a real piece of your page. Now the question is: what can you actually do with it?

Think about the kinds of changes you see on real websites. On Twitter, when you like a tweet, the heart icon fills red and the like count increments -- the text inside a `<span>` changed from one number to another. On a food delivery app, when a dish is out of stock, the "Add to Cart" button becomes greyed out and disabled -- an attribute on the button changed. On any site with a dark mode toggle, clicking the switch changes the background, text, and border colours across the entire page -- a class was added to the body element, and the CSS responded.

All of these come down to the same thing: reading and writing properties on a DOM element. This article covers the four main ways to do that.

### Reading and Writing Element Content

Every DOM element has two properties for working with its content: `textContent` and `innerHTML`.

#### textContent

`textContent` reads or sets the raw text inside an element -- the actual characters, with no HTML parsing.

**Reading:**

```js
let heading = document.querySelector("h1")
console.log(heading.textContent)    // "Welcome back, Priya"
```

**Writing:**

```js
heading.textContent = "Welcome back, Aarav"
// The h1 on the page now shows "Welcome back, Aarav"
```

Setting `textContent` replaces everything inside the element with the new text.

<!-- 
IMAGE: A split screenshot of a web page header. Left side shows the original state: a greeting that reads "Welcome back, Priya" in large text. Right side shows the updated state: "Welcome back, Aarav" in the same position and style. A small code snippet floats above the right side showing: heading.textContent = "Welcome back, Aarav". Caption: "textContent replaces the text inside an element directly. The page updates instantly with no reload." Style: realistic browser screenshot, side-by-side before and after, subtle highlight on the changed text.
-->

**Real-world use -- live character counter:**

Twitter and LinkedIn both show a counter below the compose box that decrements as you type. Each keypress updates a `<span>` with the remaining character count:

```js
let counter = document.querySelector(".char-count")
counter.textContent = "240 characters remaining"

// Later, after the user types:
counter.textContent = "198 characters remaining"
```

**Real-world use -- updating a cart total:**

When a user adds an item to their cart on Amazon, the total in the navbar updates without a page reload:

```js
let total = document.querySelector(".cart-total")
total.textContent = "Total: ₹2,499"
```

**Safety note:** `textContent` treats everything you assign as plain text. If you set `element.textContent = "<b>Hello</b>"`, the browser displays the literal characters `<b>Hello</b>` on screen -- it does not render them as HTML. This makes it completely safe to use with content that came from a user.

#### innerHTML

`innerHTML` reads or sets the actual HTML markup inside an element. The browser parses whatever string you assign and renders it as real HTML.

```js
let card = document.querySelector(".product-card")
console.log(card.innerHTML)
// "<h2>Laptop</h2><p>₹45,000</p><button>Add to cart</button>"
```

**Writing:**

```js
card.innerHTML = "<h2>Keyboard</h2><p>₹2,500</p><button>Add to cart</button>"
```

This is useful when you need to insert structured HTML -- not just text, but elements with tags.

**Real-world use -- rendering a list of messages:**

In a chatbot or messaging interface, when new messages arrive, they are often injected as HTML into the message container:

```js
let chatWindow = document.querySelector(".chat-messages")
chatWindow.innerHTML += `
  <div class="message bot">
    <p>Hi! How can I help you today?</p>
  </div>
`
```

**The danger with user input:**

`innerHTML` is powerful but carries a serious risk. If you take text that a user typed and inject it directly into the page using `innerHTML`, you give the browser permission to execute anything in that string as HTML -- including `<script>` tags. This is called a **Cross-Site Scripting (XSS) attack** and is one of the most common web security vulnerabilities.

```js
// DANGEROUS -- never do this
let userInput = document.querySelector("#search").value
resultsDiv.innerHTML = userInput    // if userInput is "<script>stealCookies()</script>", it runs
```

**The rule:** Use `textContent` for any content that came from a user or an external source. Use `innerHTML` only for your own trusted static markup.

| | textContent | innerHTML |
|---|---|---|
| Parses HTML tags? | No | Yes |
| Safe with user input? | Yes | No |
| Use for | Text, numbers, labels | Injecting your own markup |

### Attributes

HTML attributes like `id`, `class`, `href`, `src`, `disabled`, and `placeholder` are part of the element's definition. JavaScript lets you read, set, remove, and check any attribute.

#### getAttribute() and setAttribute()

```js
element.getAttribute("attributeName")              // read
element.setAttribute("attributeName", "value")     // write
element.removeAttribute("attributeName")           // delete
element.hasAttribute("attributeName")              // check -- returns true or false
```

**Real-world use -- toggling a disabled button:**

On a form, the submit button should be disabled until all required fields are filled. When the user completes the form, the `disabled` attribute is removed:

```js
let submitBtn = document.querySelector("#submit-btn")

// Disable when the form is incomplete
submitBtn.setAttribute("disabled", "")
// The button is now greyed out and unclickable

// Enable when the form is complete
submitBtn.removeAttribute("disabled")
// The button is now active again
```

<!-- 
IMAGE: A side-by-side screenshot of a form's submit button. Left side: the button is greyed out with "Submit" text, showing it is disabled. Right side: the button is filled with the brand colour ("Submit"), showing it is enabled. A label below each: "setAttribute('disabled', '')" and "removeAttribute('disabled')". Caption: "Attributes control the state of HTML elements. Toggling disabled turns a button on and off without changing the page structure." Style: realistic form button screenshot, clean before/after.
-->

**Real-world use -- swapping an image source:**

On a product page where the user clicks a thumbnail to switch the main image:

```js
let mainImage = document.querySelector(".product-main-img")
mainImage.getAttribute("src")                  // "/images/laptop-front.jpg"
mainImage.setAttribute("src", "/images/laptop-side.jpg")
// The browser immediately loads and displays the new image
```

**Real-world use -- reading a data attribute:**

HTML elements can carry custom data attributes (prefixed with `data-`). These are commonly used to store the ID of a record so JavaScript knows which item to work with:

```js
// <li data-id="42" class="cart-item">Laptop</li>
let item = document.querySelector(".cart-item")
let productId = item.getAttribute("data-id")    // "42"
```

#### Direct Property Access

For the most common attributes, the DOM also exposes them as direct JavaScript properties on the element object. These are usually shorter and easier to work with:

```js
element.id          // the id attribute
element.href        // the href of a link
element.src         // the src of an image
element.alt         // the alt text of an image
element.value       // the current value of an input field
element.checked     // boolean -- whether a checkbox is checked
```

```js
let link = document.querySelector("a.profile-link")
console.log(link.href)    // "https://example.com/profile"

let input = document.querySelector("#username")
console.log(input.value)    // whatever the user typed

let checkbox = document.querySelector("#agree-terms")
console.log(checkbox.checked)    // true or false
```

**Note:** Direct property access and `getAttribute` are not always identical. `element.href` returns the full resolved URL (`"https://example.com/profile"`), while `element.getAttribute("href")` returns exactly what is written in the HTML (`"/profile"`). For most purposes either works, but be aware of this difference if you are comparing or storing URLs.

### Inline Styles

Every DOM element has a `style` property that maps to its inline CSS. You can read and write CSS properties through it.

```js
element.style.propertyName = "value"
```

CSS property names with hyphens are written in camelCase in JavaScript:

| CSS property | JavaScript property |
|---|---|
| `background-color` | `style.backgroundColor` |
| `font-size` | `style.fontSize` |
| `border-radius` | `style.borderRadius` |
| `z-index` | `style.zIndex` |

```js
let banner = document.querySelector(".alert-banner")
banner.style.backgroundColor = "#ff4444"
banner.style.color = "white"
banner.style.padding = "12px"
```

**Real-world use -- progress bar:**

A file upload progress bar fills based on upload percentage. As the upload progresses, JavaScript updates the width:

```js
let progressBar = document.querySelector(".progress-fill")
progressBar.style.width = "65%"    // sets the bar to 65% wide
```

<!-- 
IMAGE: A horizontal progress bar showing 65% completion. Below it, a label "Uploading... 65%". A small code snippet overlay: progressBar.style.width = "65%". Caption: "Inline styles via JavaScript are ideal for values that change dynamically, like a progress percentage." Style: clean UI screenshot of a file upload progress bar.
-->

**When not to use inline styles:**

Inline styles work for values that are dynamic and computed at runtime (like a width percentage from an upload progress). For everything else -- colours, sizes, shadows, transitions -- defining the state in CSS and switching classes with JavaScript is the better approach. Inline styles have the highest specificity in CSS, override everything else, and make your styling logic split between two files.

The right tool for dynamic visual states (dark mode, active tab, validation error, loading state) is `classList`, which is covered next.

### classList: The Right Way to Apply Visual Changes

Every element has a `classList` property that gives you methods to add, remove, and toggle CSS class names. Your CSS defines what each class looks like. JavaScript decides which classes are active.

```js
element.classList.add("className")                   // add a class
element.classList.remove("className")                // remove a class
element.classList.toggle("className")                // add if absent, remove if present
element.classList.contains("className")              // returns true or false
element.classList.replace("oldClass", "newClass")    // swap one class for another
```

#### classList.add() and classList.remove()

**Real-world use -- showing and hiding a dropdown:**

On any website with a navigation dropdown (like the account menu on Flipkart), the dropdown is hidden by default via CSS and made visible by adding a class:

```css
/* in your stylesheet */
.dropdown { display: none; }
.dropdown.open { display: block; }
```

```js
let dropdown = document.querySelector(".account-dropdown")

dropdown.classList.add("open")     // dropdown appears
dropdown.classList.remove("open")  // dropdown disappears
```

The CSS defines what `open` looks like. JavaScript just decides when to apply it.

#### classList.toggle()

Toggle adds a class if it is not there, and removes it if it is. Perfect for on/off states.

**Real-world use -- dark mode:**

Every website with a dark mode toggle does this exact thing:

```js
let toggleBtn = document.querySelector("#dark-mode-btn")

toggleBtn.addEventListener("click", function() {
  document.body.classList.toggle("dark-mode")
})
```

```css
/* in your stylesheet */
body.dark-mode {
  background-color: #1a1a1a;
  color: #ffffff;
}
```

One click adds `dark-mode` to the body and the page goes dark. Another click removes it and the page returns to light. The CSS handles all the visual work; JavaScript just flips the class on and off.

<!-- 
IMAGE: A side-by-side screenshot of the same webpage in light mode (left) and dark mode (right). The light mode shows a white background with dark text. The dark mode shows a dark background with light text. A toggle switch sits in the top-right corner of both screenshots. Caption: "Dark mode is a class on the body element. classList.toggle() switches it with one line." Style: realistic browser screenshot side-by-side.
-->

#### classList.contains()

Returns `true` if the class is currently applied, `false` if not. Use it to check state before deciding what to do next.

```js
let sidebar = document.querySelector(".sidebar")

if (sidebar.classList.contains("collapsed")) {
  sidebar.classList.remove("collapsed")    // expand it
} else {
  sidebar.classList.add("collapsed")       // collapse it
}
```

This is equivalent to `classList.toggle("collapsed")`, but sometimes you need the explicit check to do different things in each branch.

#### classList.replace()

Swaps one class name for another in a single step.

**Real-world use -- tab navigation:**

```js
let activeTab = document.querySelector(".tab.active")
let nextTab = activeTab.nextElementSibling

activeTab.classList.replace("active", "inactive")
nextTab.classList.replace("inactive", "active")
```

Instead of a remove + add pair, `replace` handles both in one call.

### The Pattern: CSS Defines States, JavaScript Switches Them

The consistent principle across `classList`, attributes, and styles is this: your CSS file should define what each visual state looks like. JavaScript's job is to decide when each state applies.

Never hardcode colours or sizes into JavaScript if you can define a class in CSS instead. The CSS stays in the stylesheet where designers can find it. The logic stays in JavaScript where developers can follow it. Each does its job.

```js
// Avoid this for state changes
element.style.border = "2px solid red"
element.style.color = "red"

// Do this instead
element.classList.add("error")    // CSS defines what .error looks like
```

### Let's Define Things Now...

> **textContent:** Reads or sets the plain text content of an element. Does not parse HTML. Safe with user input.

> **innerHTML:** Reads or sets the HTML markup inside an element. Parses the string as HTML. Never use with user-supplied content.

> **XSS (Cross-Site Scripting):** A vulnerability where user-supplied content is injected into the DOM as HTML, allowing malicious scripts to run. Prevented by using textContent instead of innerHTML for user data.

> **getAttribute(name):** Reads the value of any HTML attribute from an element.

> **setAttribute(name, value):** Sets an attribute to a new value, or creates it if it does not exist.

> **removeAttribute(name):** Removes an attribute entirely from the element.

> **element.value:** Direct property access for the current value of an input, textarea, or select element.

> **element.style.propertyName:** Reads or writes a single inline CSS style on an element. Property names use camelCase.

> **classList:** An object on every element that provides methods to add, remove, toggle, and check CSS class names.

> **classList.toggle(name):** Adds the class if absent, removes it if present. Used for on/off behavioral states.

### Activity: Modification Lab

1. Select the `<h1>` on a test HTML page and change its text to your name using `textContent`.

2. Create an input field and a `<p>` element. Write code that reads the input's current `value` and sets it as the `textContent` of the paragraph. Then explain why you would use `textContent` here and not `innerHTML`.

3. Select a button and use `setAttribute` to add a `disabled` attribute. Then use `removeAttribute` to enable it again.

4. Select an image on the page. Read its `src` attribute using both `getAttribute("src")` and the `src` property directly. Log both and note any difference in the output.

5. Use `classList.add` to add an `active` class to a nav link. Use `classList.contains` to check if it has the class. Use `classList.remove` to take it off.

6. Build a dark mode toggle: a button that calls `document.body.classList.toggle("dark-mode")` when clicked. Define the `.dark-mode` styles in a stylesheet.

7. Add the following to `chatbot-logic.js`:

```js
// Simulating DOM manipulation patterns used in the chatbot UI

function displayMessage(container, sender, text) {
  let messageEl = document.createElement("div")
  messageEl.classList.add("message", sender)
  messageEl.textContent = text    // textContent is safe -- never innerHTML for user text
  container.appendChild(messageEl)
}

function updateCharCount(countEl, input, maxLength) {
  let remaining = maxLength - input.value.length
  countEl.textContent = `${remaining} characters remaining`

  if (remaining < 20) {
    countEl.classList.add("warning")
  } else {
    countEl.classList.remove("warning")
  }
}
```

## What's Coming Next...

So far you can find elements and change what they contain or how they look. But none of this responds to the user yet. For that, you need to listen for events -- clicks, keypresses, form submissions, mouse movements. The next article covers how JavaScript waits for user actions and runs code in response.