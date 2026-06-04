## Introduction

Look at the chatbot we have been building throughout this course. The messages, the bot name, the responses -- all of it lives in `chatbot-logic.js`. When the user sends "Hello", the bot checks a hardcoded object and returns a hardcoded reply. When you want to add a new response, you open the file and type it in.

This is not how real applications work.

On Swiggy, the restaurant list is not hardcoded in the JavaScript file. It lives in a database on Swiggy's servers. When you open the app, JavaScript sends a request to those servers, the servers query the database, and the restaurant data travels back to your browser. On YouTube, your recommended videos are computed on Google's servers -- no JavaScript file in your browser could store or calculate all of that. On a weather app, the temperature you see was fetched from a meteorological service seconds before you looked at it.

The data in real applications lives on servers. The browser's JavaScript fetches it on demand. In this article, we look at how that works.

### The Client-Server Model

Two computers are involved in every web request:

**The client** is the browser -- your computer. It is where your HTML, CSS, and JavaScript run. It is what the user sees and interacts with.

**The server** is a computer (often in a data centre, often in another city or country) that stores data and runs logic the client cannot or should not do -- accessing a database, authenticating users, processing payments.

The client and server communicate through **HTTP requests and responses**:

1. The client sends a **request** to a URL on the server
2. The server processes the request
3. The server sends back a **response** with a status code and a body (usually JSON data)

<!-- 
IMAGE: A diagram showing two computers facing each other. Left side labeled "Client (Browser)" with a laptop icon. Right side labeled "Server (Data Centre)" with a server rack icon. An arrow from client to server labeled "HTTP Request: GET /products". An arrow back from server to client labeled "HTTP Response: 200 OK + JSON data". Caption: "Every data request from a web application follows this pattern: the client asks, the server responds." Style: flat vector diagram, two icons with bidirectional arrows and labels.
-->

**HTTP status codes** are numbers the server attaches to every response to indicate what happened:

| Code | Meaning | When you see it |
|---|---|---|
| 200 | OK | Request succeeded, data is in the body |
| 201 | Created | A new resource was successfully created |
| 400 | Bad Request | The client sent malformed or invalid data |
| 401 | Unauthorized | Authentication is required or failed |
| 404 | Not Found | The requested resource does not exist |
| 500 | Server Error | Something went wrong on the server's side |

These codes are how your JavaScript knows whether to display the data or show an error message.

### What Is an API?

The server does not just accept random text. It exposes specific URLs called **endpoints** that accept defined types of requests and return structured data.

An **API** (Application Programming Interface) is the set of endpoints a server makes available for clients to interact with. It is the agreed contract between the client and the server: "send a request to this URL in this format, and I will send back data in this format."

When you open a food delivery app and search for "pizza", the app sends a request to an endpoint like:

```
GET https://api.swiggy.com/restaurants?query=pizza&city=mumbai
```

The server receives this, queries its database, and returns a JSON response containing a list of restaurants. The JavaScript in your browser receives that JSON, parses it, and renders the restaurant cards you see.

You have been using `JSON.stringify` and `JSON.parse` since the objects and localStorage articles. JSON is the format APIs use to send data back and forth. An API response is a JSON string; JavaScript parses it into a usable object.

### fetch(): Making Requests from JavaScript

The browser provides a built-in function called `fetch` for sending HTTP requests from JavaScript. Like any function we have studied, it takes a parameter and returns something.

**The parameter:** A URL string -- the address of the API endpoint you want to reach.

**What it returns:** We will get to this in a moment. First, a simple call:

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
```

`jsonplaceholder.typicode.com` is a free public API that returns fake data. This call asks for post number 1. You can make this exact call in your browser console right now.

**The syntax for the most common case:**

```js
fetch(url)
fetch(url, options)    // optional second argument for method, headers, body
```

The options object is used for POST requests, setting headers, or sending a body. For now, with no second argument, `fetch` sends a GET request -- a read-only request asking for data.

### What fetch Returns: A Promise

Here is where it gets interesting. When you call `fetch`, you are asking another computer on the internet to do something and send data back. That takes time -- anywhere from a few milliseconds to several seconds. JavaScript cannot stop and wait for it, because JavaScript runs on the same thread as the browser's UI. If the code froze and waited for a network response, the entire page would freeze with it -- no scrolling, no clicking, nothing.

So `fetch` does not return the data. It returns a **Promise**.

### What Is a Promise?

Think about ordering food at a restaurant. You tell the waiter what you want. The waiter does not make you stand at the counter watching the kitchen. They hand you a token and say "when it is ready, we will call your number." You go sit down. You can have a conversation, check your phone, drink water -- life continues normally. When the food is ready, your number is called and you go collect it.

A **Promise** is exactly this token. It is a JavaScript object that represents the eventual result of an operation that has not completed yet. You get it back immediately, before the result exists, and you use it to describe what should happen once the result arrives -- without blocking anything in the meantime.

```js
let promise = fetch("https://jsonplaceholder.typicode.com/posts/1")
console.log(promise)    // Promise {<pending>}
```

The Promise is returned immediately. The network request is still in flight. The rest of your code keeps running.

A Promise is always in one of three states:

| State | What it means |
|---|---|
| **Pending** | The operation is still in progress |
| **Fulfilled** | The operation completed successfully. The result is available |
| **Rejected** | The operation failed (network error, server unreachable) |

<!-- 
IMAGE: A three-state diagram showing a circle labeled "Pending" in the centre with two arrows branching out. The left arrow points to a green circle labeled "Fulfilled (data arrived)" and the right arrow points to a red circle labeled "Rejected (network error)". Caption: "A Promise starts in the pending state. It settles to either fulfilled or rejected -- never back to pending." Style: flat vector state diagram, colour-coded circles and labelled arrows.
-->

**One important thing to note:** a Promise moves from pending to either fulfilled or rejected exactly once. It never goes back to pending. Once settled, the state is permanent.

### Creating vs Handling a Promise

There are two sides to a Promise: creating one and handling one.

**Creating a Promise** is what happens on the server side or inside library code like `fetch`. When you call `fetch`, the browser's networking code creates a Promise and manages it -- resolving it when the response arrives, rejecting it if the network fails.

**Handling a Promise** is what you do in your JavaScript. You describe what to do when the Promise fulfils and what to do if it rejects. This is the part you are responsible for, and it is entirely what the rest of this article is about.

You do not need to know how `fetch` creates the Promise internally. That is the browser's job. Your job is to handle it.

### Handling a Promise: .then() and .catch()

Every Promise object has a `.then()` method. You pass `.then()` a function, and that function runs when the Promise fulfils. The fulfilled value is passed as the argument to your function.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(function(response) {
    console.log(response)    // the Response object
  })
```

The `response` here is not the data yet. It is a `Response` object that describes the server's reply -- the status code, the headers, and the body stream.

#### Step 1: Check response.ok

Before reading the data, always check whether the request succeeded. `response.ok` is `true` for any 2xx status code and `false` for anything else (404, 500, etc.).

This check is critical because `fetch` only rejects its Promise when there is a network-level failure (no internet, server unreachable). A 404 or 500 response from the server still fulfils the Promise -- `fetch` successfully received a response. Your code has to check whether that response indicates success or failure.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(function(response) {
    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`)
    }
    return response.json()
  })
```

#### Step 2: Parse the JSON

`response.json()` reads the response body and parses it as JSON. It also returns a Promise -- because reading a streaming response body is itself an asynchronous operation.

Returning a Promise from inside `.then()` passes it to the next `.then()` in the chain:

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(function(response) {
    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`)
    }
    return response.json()    // returns a Promise
  })
  .then(function(data) {
    // this runs when response.json() resolves
    console.log(data)
    // { id: 1, title: "...", body: "...", userId: 1 }
  })
```

The two-step pattern -- check `response.ok`, then call `response.json()` -- is the standard for every `fetch` call you write.

#### Handling Errors: .catch()

`.catch()` receives a function that runs if the Promise rejects. This catches network failures and any errors thrown inside `.then()` (including the error you throw when `response.ok` is false):

```js
fetch("https://jsonplaceholder.typicode.com/posts/1")
  .then(function(response) {
    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`)
    }
    return response.json()
  })
  .then(function(data) {
    console.log("Data received:", data)
    // use the data to update the DOM
  })
  .catch(function(error) {
    console.log("Something went wrong:", error.message)
    // show an error message to the user
  })
```

This is the complete, correct pattern for a fetch call:

1. `fetch(url)` -- make the request
2. `.then(response => ...)` -- check `response.ok`, return `response.json()`
3. `.then(data => ...)` -- use the parsed data
4. `.catch(error => ...)` -- handle any failure

**Real-world use -- loading a product list:**

```js
fetch("https://api.example.com/products")
  .then(function(response) {
    if (!response.ok) {
      throw new Error(`Could not load products: ${response.status}`)
    }
    return response.json()
  })
  .then(function(products) {
    let list = document.querySelector(".product-list")
    list.innerHTML = ""

    products.forEach(function(product) {
      let li = document.createElement("li")
      li.textContent = `${product.name} - ₹${product.price}`
      list.append(li)
    })
  })
  .catch(function(error) {
    document.querySelector(".error-msg").textContent =
      "Could not load products. Please try again."
  })
```

<!-- 
IMAGE: A browser showing a product listing page. Three states shown left to right: (1) a loading spinner with text "Loading...", (2) a list of three product cards with names and prices, (3) an error message "Could not load products. Please try again." with a retry button. Caption: "Every fetch call needs to handle three outcomes: loading, success, and error. The UI should reflect each state clearly." Style: realistic browser screenshot strip showing the three states.
-->

### Chaining Multiple Promises: .then() on .then()

When you return a Promise from inside `.then()`, the next `.then()` waits for it. This is how you sequence async operations without nesting them inside each other:

```js
fetch("/api/user")
  .then(response => response.json())
  .then(user => fetch(`/api/orders/${user.id}`))    // second fetch, uses result of first
  .then(response => response.json())
  .then(orders => {
    console.log(orders)
  })
  .catch(error => console.log(error.message))
```

Each `.then()` waits for the previous one to complete before running.

### Running Multiple Requests in Parallel: Promise.all()

If you need data from two or more endpoints and they do not depend on each other, there is no reason to wait for the first to finish before starting the second. `Promise.all` takes an array of Promises and returns a single Promise that fulfils when all of them have fulfilled -- with an array of all the results.

```js
let postsPromise = fetch("https://jsonplaceholder.typicode.com/posts")
  .then(r => r.json())

let usersPromise = fetch("https://jsonplaceholder.typicode.com/users")
  .then(r => r.json())

Promise.all([postsPromise, usersPromise])
  .then(function(results) {
    let posts = results[0]
    let users = results[1]
    console.log(`${posts.length} posts from ${users.length} users`)
  })
  .catch(function(error) {
    console.log("One of the requests failed:", error.message)
  })
```

Both requests fire immediately and run in parallel. The `.then()` runs when both have completed. If either one fails, `.catch()` runs.

**Real-world use:** A dashboard page that needs to load user profile data, recent orders, and unread notifications simultaneously. Three separate API calls, all fired at once, `Promise.all` waits for all three, then the dashboard renders with everything available.

### Let's Define Things Now...

> **Client:** The browser -- the computer the user is on. Runs HTML, CSS, and JavaScript. Sends requests and displays responses.

> **Server:** A remote computer that stores data, runs business logic, and responds to requests from clients.

> **HTTP Request:** A message the client sends to a server URL, containing a method (GET, POST, etc.) and optionally a body.

> **HTTP Response:** The server's reply, containing a status code and a body (usually JSON).

> **API (Application Programming Interface):** The set of URL endpoints a server exposes for clients to interact with. Defines what requests are accepted and what responses look like.

> **fetch(url):** A built-in browser function that sends an HTTP request and returns a Promise.

> **Promise:** A JavaScript object representing the eventual result of an asynchronous operation. In one of three states: pending, fulfilled, or rejected.

> **Pending:** The operation is still in progress.

> **Fulfilled:** The operation completed successfully. The resolved value is available.

> **Rejected:** The operation failed. An error is available.

> **.then(fn):** Registers a function to run when the Promise fulfils. Returns a new Promise, enabling chaining.

> **response.ok:** A boolean on the Response object. True for 2xx status codes. Always check this before parsing.

> **response.json():** Parses the response body as JSON. Returns a Promise.

> **.catch(fn):** Registers a function to run when the Promise rejects or when any error is thrown in the chain.

> **Promise.all(array):** Returns a Promise that fulfils when all Promises in the array have fulfilled. Rejects immediately if any one of them rejects.

### Activity: Fetch Basics

1. Open the browser console and call `fetch("https://jsonplaceholder.typicode.com/posts/1")`. Log the returned value before `.then()`. What state is the Promise in?

2. Chain `.then(response => response.json()).then(data => console.log(data))` and observe the result.

3. Deliberately request a non-existent resource (`/posts/9999`). Log `response.status`. Notice that the Promise still fulfilled -- add a `response.ok` check and throw an error if it is false.

4. Fetch from `https://jsonplaceholder.typicode.com/posts` (a list of 100 posts) and render the first 5 titles as `<li>` elements in a `<ul>` on the page.

5. Use `Promise.all` to fetch `/posts` and `/users` simultaneously. Log the count of each in a single `.then()` that runs after both complete.

6. Write a reusable `fetchJSON(url)` function that wraps the full two-step pattern (check `response.ok`, parse JSON, throw on error) and returns a Promise resolving to the parsed data.

7. Add the following to `chatbot-logic.js`:

```js
function fetchBotResponse(userMessage) {
  return fetch(`https://api.example.com/chat?message=${encodeURIComponent(userMessage)}`)
    .then(function(response) {
      if (!response.ok) {
        throw new Error(`Bot API error: ${response.status}`)
      }
      return response.json()
    })
    .then(function(data) {
      return data.reply
    })
    .catch(function(error) {
      console.log("fetchBotResponse failed:", error.message)
      return "Sorry, I could not reach the server. Please try again."
    })
}
```

## What's Coming Next...

`fetch` with `.then().catch()` is the standard, fully supported way to handle API requests in JavaScript. It works correctly and you will see it in codebases everywhere.

But the chained `.then()` syntax has a readability cost -- especially when requests depend on each other and the chain grows. JavaScript introduced `async` and `await` as a cleaner way to write the exact same logic: instead of chaining callbacks, you write asynchronous code that reads like normal sequential code. In the next article, we look at how `async/await` works, how it compares to `.then()`, and how the same `fetch` request you just wrote can be expressed in a way that is easier to read, reason about, and debug.
