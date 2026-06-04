## Introduction

Consider this search bar on an e-commerce website. The user types "wireless headphones", selects a category from a dropdown, and clicks Search:

```html
<form id="search-form">
  <input type="text" id="search-query" placeholder="Search products..." />
  <select id="category">
    <option value="all">All Categories</option>
    <option value="electronics">Electronics</option>
    <option value="clothing">Clothing</option>
  </select>
  <button type="submit">Search</button>
</form>

<div id="results">
  <!-- search results should appear here -->
</div>
```

<!-- 
IMAGE: An e-commerce search bar with a text input showing "wireless headphones", a category dropdown showing "Electronics", and a blue "Search" button. Below it, a product results grid. Caption: "A search form on an e-commerce page. When submitted, results should update in the grid below without reloading the entire page." Style: realistic browser screenshot of a shopping website search area.
-->

The user clicks Search. The page reloads entirely. The search query disappears from the input. The results section is empty again. Everything the user was looking at is gone.

This happens because the browser's default behaviour when a form is submitted is to send the form data as a request and reload the page with the response. This made sense when websites were just server-rendered HTML documents. But in a modern application, you want JavaScript to intercept the submission, read the values, make a request to the server in the background, and update only the results section -- without touching anything else on the page.

The very first thing you do in any form submission handler is stop that default reload.

### Stopping the Default Behaviour

```js
let form = document.querySelector("#search-form")

form.addEventListener("submit", function(event) {
  event.preventDefault()    // stop the page from reloading

  // now handle the submission with JavaScript
  console.log("Form submitted -- page did not reload")
})
```

`event.preventDefault()` tells the browser: do not do what you would normally do for this event. For a `submit` event, that means do not reload. For a link click, it means do not navigate. For a right-click, it means do not show the context menu.

Once the reload is stopped, JavaScript takes over. You read the form values, validate them, and decide what to do -- typically sending a request to a server and displaying the response in the right part of the page. This is exactly how every modern web application works: Gmail, Amazon, Swiggy, Google Search. None of them reload the page when you interact with a form.

Now that the reload is stopped, the next step is reading what the user actually entered.

### Step 1: Reading Form Values

Every input element has a `value` property that holds whatever the user typed or selected. This is always a string.

```js
form.addEventListener("submit", function(event) {
  event.preventDefault()

  let query = document.querySelector("#search-query").value
  let category = document.querySelector("#category").value

  console.log(query)       // "wireless headphones"
  console.log(category)    // "electronics"
})
```

The types of inputs you will encounter and how to read each:

**Text inputs and textareas:**

```js
let searchInput = document.querySelector("#search-query")
console.log(searchInput.value)    // the text the user typed -- always a string
```

**Number inputs:**

```js
let ageInput = document.querySelector("#age")
let age = parseInt(ageInput.value, 10)    // convert string to integer
let price = parseFloat(document.querySelector("#price").value)    // for decimals
```

`input.value` for a number input is still a string. Always convert with `parseInt` or `parseFloat` before doing arithmetic. Passing the radix `10` to `parseInt` is a good habit -- it tells the function to interpret the string as a base-10 number.

**Checkboxes:**

```js
let agreeCheckbox = document.querySelector("#agree-terms")
console.log(agreeCheckbox.checked)    // true or false -- not .value
```

For checkboxes, `.value` gives you the HTML `value` attribute (which defaults to `"on"`). What you actually want is `.checked`, which reflects whether the box is ticked.

**Radio buttons:**

```js
// Find which radio button in a group is selected
let selected = document.querySelector("input[name='payment']:checked")
console.log(selected ? selected.value : null)    // "card", "upi", or null if none selected
```

**Select dropdowns:**

```js
let categorySelect = document.querySelector("#category")
console.log(categorySelect.value)    // the value of the selected option
```

Putting this together for the search form:

```js
form.addEventListener("submit", function(event) {
  event.preventDefault()

  let query    = document.querySelector("#search-query").value.trim()
  let category = document.querySelector("#category").value

  if (query === "") {
    console.log("Please enter a search term")
    return
  }

  console.log(`Searching for "${query}" in ${category}`)
  // next: send this to a server and display results
})
```

### The FormData API: Reading All Fields at Once

For forms with many fields, querying each input individually gets verbose. The `FormData` API reads all fields in a form at once, using the `name` attribute of each input as the key.

For `FormData` to work, every input needs a `name` attribute:

```html
<form id="search-form">
  <input type="text" name="query" placeholder="Search products..." />
  <select name="category">
    <option value="all">All Categories</option>
    <option value="electronics">Electronics</option>
  </select>
  <button type="submit">Search</button>
</form>
```

```js
form.addEventListener("submit", function(event) {
  event.preventDefault()

  let formData = new FormData(form)

  console.log(formData.get("query"))       // "wireless headphones"
  console.log(formData.get("category"))    // "electronics"
})
```

`new FormData(formElement)` reads all named fields. `formData.get("name")` retrieves a single value. For fields where multiple values can be selected (like a multi-select or multiple checkboxes with the same name), `formData.getAll("name")` returns an array of all selected values.

**Iterating over all fields:**

```js
for (let [name, value] of formData) {
  console.log(`${name}: ${value}`)
}
// query: wireless headphones
// category: electronics
```

`FormData` is iterable, so `for...of` works with destructuring just like `Object.entries`. This is particularly useful when you need to build a query string or send all form data to an API without listing every field by name.

**Converting to a plain object:**

```js
let data = Object.fromEntries(formData)
console.log(data)    // { query: "wireless headphones", category: "electronics" }
```

`Object.fromEntries` turns the `[name, value]` pairs into a regular JavaScript object, which is often more convenient to work with.

### Constraint Validation: The Browser's Built-In Checks

Now extend the search example into a full product listing form that a seller fills out:

```html
<form id="product-form">
  <input type="text"   name="title"    required minlength="3" maxlength="100" />
  <input type="number" name="price"    required min="1" max="999999" />
  <input type="email"  name="contact"  required />
  <input type="text"   name="sku"      pattern="[A-Z]{3}-[0-9]{4}" />
  <textarea            name="description" maxlength="500"></textarea>
  <button type="submit">List Product</button>
</form>
```

The HTML attributes here are **constraint validation attributes**. The browser enforces them automatically when the form is submitted without `event.preventDefault()`. Add `preventDefault` and that automatic enforcement stops -- validation becomes your responsibility.

But you do not have to start from scratch. The browser still does the checking internally and exposes the results through the `validity` object on each input.

#### The validity Object

Every input element has a `validity` property that is an object containing boolean flags describing its current state:

| Property | True when |
|---|---|
| `validity.valid` | All constraints are satisfied |
| `validity.valueMissing` | Field is `required` but empty |
| `validity.tooShort` | Value is shorter than `minlength` |
| `validity.tooLong` | Value is longer than `maxlength` |
| `validity.rangeUnderflow` | Number is below `min` |
| `validity.rangeOverflow` | Number is above `max` |
| `validity.typeMismatch` | Value does not match the input type (e.g. not a valid email) |
| `validity.patternMismatch` | Value does not match the `pattern` regex |

```js
let titleInput = document.querySelector("input[name='title']")

console.log(titleInput.validity.valid)          // false if anything fails
console.log(titleInput.validity.valueMissing)   // true if field is empty and required
console.log(titleInput.validity.tooShort)       // true if fewer than minlength chars
```

Use these to show your own error messages rather than the browser's default popups:

```js
form.addEventListener("submit", function(event) {
  event.preventDefault()

  let titleInput = document.querySelector("input[name='title']")
  let titleError = document.querySelector("#title-error")

  if (!titleInput.validity.valid) {
    if (titleInput.validity.valueMissing) {
      titleError.textContent = "Product title is required"
    } else if (titleInput.validity.tooShort) {
      titleError.textContent = "Title must be at least 3 characters"
    }
    return    // stop -- do not submit
  }

  // all valid -- proceed
})
```

<!-- 
IMAGE: A product listing form with three fields: Title, Price, and Contact Email. The Title field has a red border and a small error message below it reading "Title must be at least 3 characters". The Price and Email fields are normal. Caption: "Custom error messages driven by the validity object. The browser checks constraints; JavaScript displays the feedback." Style: realistic form UI with inline error message below the invalid field.
-->

#### setCustomValidity() and reportValidity()

For validation logic the browser cannot express through attributes -- like checking that a password confirmation matches the original -- use `setCustomValidity`:

```js
let password = document.querySelector("input[name='password']")
let confirm  = document.querySelector("input[name='confirm']")

confirm.addEventListener("input", function() {
  if (confirm.value !== password.value) {
    confirm.setCustomValidity("Passwords do not match")
  } else {
    confirm.setCustomValidity("")    // empty string means "valid"
  }
})
```

Setting a non-empty string marks the input as invalid and makes `validity.customError` true. Setting an empty string clears the custom error.

`reportValidity()` triggers the browser's built-in error display programmatically:

```js
form.addEventListener("submit", function(event) {
  event.preventDefault()

  if (!form.reportValidity()) {
    return    // browser shows the error tooltips; stop here
  }

  // all valid -- proceed
})
```

This is a middle ground: you let the browser display its native error UI while still handling the submission with JavaScript.

### Real-Time Validation: Feedback as the User Types

Submit-time validation (checking only when the user clicks Submit) means the user fills out an entire long form and discovers errors all at once at the end. Real-time validation catches problems as they happen.

Attach your validation logic to the `input` event instead of (or in addition to) `submit`:

```js
let titleInput   = document.querySelector("input[name='title']")
let titleError   = document.querySelector("#title-error")

titleInput.addEventListener("input", function() {
  if (titleInput.validity.valueMissing) {
    titleError.textContent = "Title is required"
  } else if (titleInput.validity.tooShort) {
    titleError.textContent = `At least 3 characters (${titleInput.value.length} so far)`
  } else {
    titleError.textContent = ""    // clear the error when valid
  }
})
```

The counter updates on every keystroke. Errors appear and disappear as the user types, giving immediate feedback rather than a final rejection.

**A common pattern: validate on blur, then keep validating on input:**

Showing an error the moment a user starts typing in a field is jarring. A better user experience is to wait until they leave the field (the `blur` event), then switch to real-time updates if there is an error:

```js
let hasInteracted = false

titleInput.addEventListener("blur", function() {
  hasInteracted = true
  validateTitle()
})

titleInput.addEventListener("input", function() {
  if (hasInteracted) {
    validateTitle()    // real-time only after the first blur
  }
})

function validateTitle() {
  if (titleInput.validity.valueMissing) {
    titleError.textContent = "Title is required"
  } else if (titleInput.validity.tooShort) {
    titleError.textContent = "Title must be at least 3 characters"
  } else {
    titleError.textContent = ""
  }
}
```

This is the pattern used by most professional forms: silent until the user leaves a field, then responsive in real time.

### A Note on Accessibility

Error messages shown via `textContent` are visible on screen, but screen readers need to know which input a message belongs to. Link them with the `aria-describedby` attribute:

```html
<input type="text" name="title" id="title-input" aria-describedby="title-error" />
<p id="title-error" class="error-message"></p>
```

```js
titleError.textContent = "Title must be at least 3 characters"
```

When the error text appears, a screen reader reading the input announces the error message along with the field. This does not require extra JavaScript -- the HTML attribute creates the relationship, and the screen reader follows it automatically.

### Let's Define Things Now...

> **event.preventDefault():** Cancels the browser's default behaviour for an event. For submit, prevents the page reload.

> **input.value:** The current string value of a text input, select, or textarea. Always a string -- convert with parseInt or parseFloat when numeric.

> **input.checked:** Boolean property for checkboxes and radio buttons reflecting whether they are selected.

> **FormData:** An API for reading all named form fields at once. `new FormData(form)`, then `.get(name)` for individual values.

> **Constraint validation attributes:** HTML attributes that define rules (required, minlength, pattern, type=email, min, max). The browser checks these and exposes results through the validity object.

> **validity object:** A set of boolean flags on every input (valid, valueMissing, tooShort, typeMismatch, etc.) describing which constraints pass or fail.

> **setCustomValidity(message):** Marks an input as invalid with a custom message. Pass an empty string to clear.

> **reportValidity():** Triggers the browser's native error display programmatically.

> **Real-time validation:** Listening on the input or blur event to show feedback while the user is typing rather than only on submit.

> **aria-describedby:** An accessibility attribute linking an input to its error message element so screen readers announce the error when the field is focused.

### Activity: Form Validation Lab

1. Build a search form with a text input and a category dropdown. Prevent the default reload on submit. Log the values using both direct `.value` access and the `FormData` API.

2. Build a registration form with name, email, password, and age fields. Add appropriate constraint attributes to each.

3. For each field, read its `validity` object on the submit event and display a specific error message below the field in a `<p>` tag. Do not submit if any field is invalid.

4. Add real-time validation using the blur-first pattern: validate on blur, then keep validating on input if the user has already left the field.

5. Use `setCustomValidity` to validate that the user is at least 13 years old in a way that integrates with `reportValidity`.

6. Add the following to `chatbot-logic.js`:

```js
function handleChatSubmit(event, inputEl, displayFn) {
  event.preventDefault()

  let text = inputEl.value.trim()

  if (text === "") {
    inputEl.setCustomValidity("Please enter a message")
    inputEl.reportValidity()
    return
  }

  inputEl.setCustomValidity("")
  displayFn("user", text)
  inputEl.value = ""
  inputEl.focus()
}
```

## What's Coming Next...

We are almost done with the browser interaction side of JavaScript. You can now select elements, modify them, create and remove them dynamically, respond to events with delegation, and handle form input with proper validation.

The one major piece that is missing is communication with a server. Right now, all the logic runs in the browser and nowhere else. But real applications need to fetch data from a backend -- product listings, search results, messages, user profiles -- and send data back. In the next module, we will look at how JavaScript communicates with a server using the Fetch API, what Promises are and why they exist, and how async and await make that asynchronous work readable. That is where the chatbot comes fully to life.
