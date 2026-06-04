## Introduction

Every article so far assumed the happy path: the server responds, the data arrives, everything works. But the moment your application talks to an external server, that assumption breaks.

The server might be slow -- sitting in another country, under heavy load, taking three seconds to respond. The server might be down entirely. The user's internet connection might drop mid-request. The endpoint might return a 404 because the resource was deleted. The response might come back with an empty list because the user has no data yet.

Any of these can happen in production, to real users, at any time. A UI that does not account for them leaves users staring at a blank page with no idea what is happening or what to do next. That is not an edge case -- it is a guaranteed failure mode of any application that fetches data.

The question is: what exactly can go wrong, and what should the UI show in each case?

### Breaking It Down: The Cases You Must Handle

Every time your JavaScript makes a request to a server, the interaction falls into one of these states:

**Loading** -- the request has been sent and the application is waiting for a response. The user is looking at the screen. They need to know something is happening.

**Success with data** -- the response came back successfully and contains the expected data. Render it.

**Success with empty data** -- the response came back successfully, but the data array is empty. The user searched for something that does not exist, or they have no orders yet, or their inbox is empty. This is technically a success, but the UI needs to say something useful rather than showing a blank area.

**Error** -- something went wrong. Either the network failed entirely (no response), or the server responded with a 4xx or 5xx status code. The user needs to know it failed and ideally have a way to try again.

<!-- 
IMAGE: Four panels showing the same product listing area of a website in each of the four states. Panel 1 (Loading): grey skeleton placeholder cards with a shimmer animation, label "Loading...". Panel 2 (Success with data): three real product cards with images and prices. Panel 3 (Empty state): an illustration of an empty box with text "No products found. Try a different search." Panel 4 (Error): a red alert area with text "Could not load products." and a "Retry" button. Caption: "Every data-driven UI section must handle all four states explicitly. Leaving any one unhandled means users see a blank screen." Style: four-panel flat UI mockup side by side.
-->

A UI that handles all four is a professional UI. Most beginners handle only the second -- they write the success case and assume everything else will not happen. It always does.

### Setting Up the State Model

Before writing any fetch logic, define your four states as a model in JavaScript. The cleanest approach is to track state in a JavaScript object (the same state-first pattern from the previous article):

```js
let state = {
  status: "idle",     // "idle" | "loading" | "success" | "error"
  data: [],
  error: null
}
```

A `renderUI()` function reads the state and decides what to show:

```js
function renderUI() {
  let container = document.querySelector(".results-container")

  if (state.status === "loading") {
    container.innerHTML = `<div class="spinner">Loading...</div>`
    return
  }

  if (state.status === "error") {
    container.innerHTML = `
      <div class="error-state">
        <p>${state.error}</p>
        <button id="retry-btn">Try Again</button>
      </div>
    `
    document.querySelector("#retry-btn").addEventListener("click", loadData)
    return
  }

  if (state.status === "success" && state.data.length === 0) {
    container.innerHTML = `<p class="empty-state">No results found.</p>`
    return
  }

  if (state.status === "success") {
    container.innerHTML = ""
    state.data.forEach(function(item) {
      let card = document.createElement("div")
      card.classList.add("card")
      card.textContent = item.title
      container.append(card)
    })
  }
}
```

Every state update calls `renderUI()`. The UI always reflects the current state exactly.

### Loading State: Tell the User Something Is Happening

The first thing to do when a request starts is set status to `"loading"` and render:

```js
async function loadData() {
  state.status = "loading"
  state.data = []
  state.error = null
  renderUI()

  // ... fetch happens here
}
```

The loading state should show something visible. At minimum, a text label. In production applications, a **skeleton screen** -- grey placeholder blocks in the shape of the content that is loading -- is the standard. It is less disorienting than a spinner because it tells the user roughly what the page will look like.

<!-- 
IMAGE: A product listing page showing skeleton loading cards. Each card is a grey rectangle representing the card shape, with smaller grey bars inside representing the image placeholder, title placeholder, and price placeholder. The cards have a subtle shimmer animation. Caption: "Skeleton screens show the shape of content before it loads. Users understand what is coming and the wait feels shorter." Style: realistic browser screenshot of skeleton loading cards with shimmer effect.
-->

**Real-world use:** Every major application uses this pattern. YouTube shows grey rectangles before thumbnails load. LinkedIn shows placeholder profile cards. Twitter shows shimmer rows before tweets appear. The skeleton is built in CSS using a shimmering animation on grey placeholder elements.

### try/catch: Handling What Goes Wrong

Wrap the entire fetch operation in a `try/catch`:

```js
async function loadData() {
  state.status = "loading"
  renderUI()

  try {
    let response = await fetch("https://api.example.com/products")

    if (!response.ok) {
      throw new Error(`Server returned ${response.status}`)
    }

    let data = await response.json()

    state.status = "success"
    state.data = data
  } catch (error) {
    state.status = "error"
    state.error = error.message
  }

  renderUI()
}
```

Two things are happening in the `catch`:

**Network failures** (no internet, server unreachable, request timed out) -- the `await fetch(...)` Promise rejects, and the error lands in `catch` automatically.

**Non-200 responses** -- `fetch` does not reject for 4xx or 5xx status codes. A 404 still fulfils the Promise, just with `response.ok` set to `false`. This is why the `if (!response.ok)` check followed by `throw new Error(...)` is essential. It converts the bad status into an error that your `catch` block handles exactly like a network failure. One `catch` block handles both.

Without this pattern, a 404 response would fall through to the success path, `response.json()` would try to parse an error page as JSON, and you would get a confusing parse error instead of a clear "not found" message.

### finally: Cleanup That Always Runs

Some UI actions must happen regardless of whether the request succeeded or failed. The most common:

- Hiding a loading spinner
- Re-enabling a submit button after a form submission
- Resetting a "loading" label on a button

These belong in `finally`. The `finally` block runs after `try` and after `catch`, always, with no exceptions:

```js
let submitBtn = document.querySelector("#submit-btn")

async function handleSubmit() {
  submitBtn.disabled = true
  submitBtn.textContent = "Submitting..."

  try {
    let response = await fetch("/api/submit", {
      method: "POST",
      body: JSON.stringify(formData)
    })
    if (!response.ok) throw new Error(`Submission failed: ${response.status}`)

    let result = await response.json()
    showSuccessMessage(result)
  } catch (error) {
    showErrorMessage(error.message)
  } finally {
    submitBtn.disabled = false        // always re-enable
    submitBtn.textContent = "Submit"  // always reset label
  }
}
```

Without `finally`, you would need to re-enable the button in both the success path and the error path -- two separate calls that are easy to miss or forget. `finally` eliminates that duplication.

**Real-world use:** On Razorpay or any payment form, the "Pay Now" button is disabled and shows "Processing..." the moment you click it. If the payment succeeds or fails, the button resets. `finally` is what guarantees it resets in either case.

### The Empty State: Success Is Not Always Data

A successful response with an empty array is a distinct case from an error. It is not a failure. It just means the user has no data yet -- no orders, no messages, no search results.

An empty state should say something useful and ideally suggest a next action:

```js
if (state.status === "success" && state.data.length === 0) {
  container.innerHTML = `
    <div class="empty-state">
      <p>No products match your search.</p>
      <button id="clear-search">Clear search and browse all</button>
    </div>
  `
  return
}
```

Never show a blank area. A blank area looks broken. An empty state message tells the user exactly what happened and gives them a path forward.

**Real-world use:** Gmail's "No messages" illustration. Amazon's "No results for this search." YouTube's "No videos" on an empty channel. Every well-designed product has thoughtfully designed empty states. A blank screen is not one of them.

### The Complete Four-State Pattern

Putting it all together:

```js
let state = {
  status: "idle",
  data: [],
  error: null
}

async function loadProducts(query) {
  state.status = "loading"
  state.data = []
  state.error = null
  renderUI()

  try {
    let response = await fetch(`https://api.example.com/products?q=${query}`)

    if (!response.ok) {
      throw new Error(`Could not load products (${response.status})`)
    }

    let products = await response.json()

    state.status = "success"
    state.data = products
  } catch (error) {
    state.status = "error"
    state.error = error.message
  } finally {
    renderUI()    // render once at the end -- after success or error
  }
}

function renderUI() {
  let container = document.querySelector(".products-container")

  if (state.status === "loading") {
    container.innerHTML = `<div class="skeleton-grid">
      <div class="skeleton-card"></div>
      <div class="skeleton-card"></div>
      <div class="skeleton-card"></div>
    </div>`
    return
  }

  if (state.status === "error") {
    container.innerHTML = `
      <div class="error-state">
        <p>${state.error}</p>
        <button id="retry-btn">Try Again</button>
      </div>
    `
    document.querySelector("#retry-btn")
      .addEventListener("click", () => loadProducts(currentQuery))
    return
  }

  if (state.data.length === 0) {
    container.innerHTML = `
      <div class="empty-state">
        <p>No products found for your search.</p>
      </div>
    `
    return
  }

  container.innerHTML = ""
  let fragment = document.createDocumentFragment()
  state.data.forEach(function(product) {
    let card = document.createElement("div")
    card.classList.add("product-card")
    card.textContent = product.name
    fragment.append(card)
  })
  container.append(fragment)
}
```

This is the complete, production-ready pattern. State drives the UI. Fetch populates the state. `renderUI` reads the state and decides what to show.

### AbortController: Cancelling a Request in Flight

There is one more scenario: the user makes a request, and before it finishes, they do something that makes the result irrelevant. On a search page, they type a new query before the results from the old query arrive. If you do not cancel the old request, two things can go wrong:

- The old response arrives after the new one and overwrites the newer results (race condition)
- An unmounted or replaced component still processes a response and tries to update a DOM element that no longer exists

`AbortController` solves this. You create a controller, pass its signal to `fetch`, and call `abort()` when the result is no longer needed:

```js
let currentController = null

async function searchProducts(query) {
  // Cancel any in-flight request from a previous search
  if (currentController) {
    currentController.abort()
  }

  currentController = new AbortController()

  state.status = "loading"
  renderUI()

  try {
    let response = await fetch(
      `https://api.example.com/products?q=${query}`,
      { signal: currentController.signal }    // pass the signal to fetch
    )

    if (!response.ok) throw new Error(`Request failed: ${response.status}`)

    let products = await response.json()
    state.status = "success"
    state.data = products
  } catch (error) {
    if (error.name === "AbortError") {
      return    // request was cancelled -- do not update UI
    }
    state.status = "error"
    state.error = error.message
  } finally {
    renderUI()
  }
}
```

When `abort()` is called, `fetch` immediately rejects with an `AbortError`. The `catch` block checks for this specific error name and exits without updating the UI -- the new request is already in flight and will update the UI when it completes.

**Real-world use:** Google Search cancels the previous autocomplete request every time you type a new character. Without this, earlier slow responses could overwrite results from more recent (and faster) queries, causing suggestions to jump around unpredictably.

### Let's Define Things Now...

> **Loading state:** The UI state shown while a request is in flight. Should display a spinner, skeleton screen, or progress indicator so the user knows something is happening.

> **Success state:** The request completed and returned data. Render the data.

> **Empty state:** The request succeeded but returned no data. Show a helpful message and a suggested action rather than a blank area.

> **Error state:** The request failed due to a network error or a non-2xx status code. Show a clear error message and a retry option.

> **response.ok:** Boolean on the Response object. True for 2xx status codes. Must be checked before parsing -- fetch does not reject on 4xx/5xx.

> **finally:** Runs after try and catch regardless of outcome. Use for cleanup that must always happen: hiding spinners, re-enabling buttons.

> **Four-state model:** Loading, success with data, success with empty data, error. Every data-fetching UI component should handle all four.

> **AbortController:** A browser API for cancelling a fetch request in flight. Create one per request, pass its `.signal` to fetch, call `.abort()` to cancel. Catching `AbortError` prevents the cancelled request from updating the UI.

> **Race condition:** A bug where two asynchronous operations complete in an unpredictable order, causing the later-started one to overwrite the results of the more recent one. AbortController prevents this.

### Activity: Four-State UI

1. Build a product search component. On form submit, call an async function that sets `state.status = "loading"` and renders a skeleton, then fetches from a public API, checks `response.ok`, and updates state to `"success"` or `"error"`. Render all four states correctly.

2. Add a retry button to the error state that re-runs the fetch function with the same query.

3. Test the empty state by searching for a term guaranteed to return no results. Confirm the empty state message appears and is distinct from the error state.

4. Disable the search button when status is `"loading"` using `finally` to re-enable it after the request completes -- whether it succeeded or failed.

5. Implement `AbortController` on the search form. Type a query, immediately type another, and confirm only the second result renders (check the Network tab in DevTools to see the first request cancelled).

6. Add the following to `chatbot-logic.js`:

```js
async function sendAndReceive(container, inputEl) {
  let text = inputEl.value.trim()
  if (!text) return

  inputEl.disabled = true
  inputEl.value = ""

  renderMessage(container, "user", text)

  let typingIndicator = document.createElement("div")
  typingIndicator.classList.add("typing-indicator")
  typingIndicator.textContent = "Bot is typing..."
  container.append(typingIndicator)

  try {
    let reply = await fetchBotResponse(text)
    renderMessage(container, "bot", reply)
  } catch (error) {
    renderMessage(container, "bot", "Sorry, something went wrong. Please try again.")
  } finally {
    typingIndicator.remove()
    inputEl.disabled = false
    inputEl.focus()
  }
}
```

## What's Coming Next...

That wraps up the JavaScript module. You have covered the language fundamentals in JS Basics, and then in this module you brought JavaScript into the browser: selecting and modifying the DOM, creating and removing elements dynamically, handling events with delegation, managing forms, building state-driven components with localStorage persistence, and communicating with servers using fetch, Promises, and async/await -- including handling all the ways that communication can go wrong.

These are not theoretical concepts. They are the exact tools used to build the real web applications you use every day.

Everything covered in this course -- the HTML structure, the CSS layout and styling, and the JavaScript logic -- can be built and run entirely on your own machine. The last module takes care of what comes after that: how to track and manage your work with version control, how to collaborate with other developers, and how to take what you have built locally and put it on the internet so anyone in the world can access it. That is where we go next.
