## Introduction

In the previous article you learned how JavaScript communicates with a server. You know what an API is, how the client-server model works, and what HTTP status codes mean. You know that `fetch` returns a Promise -- a placeholder for a result that does not exist yet -- and that `.then()` and `.catch()` let you describe what to do when that result arrives.

Promises solved a real problem: asynchronous operations (network requests, file reads, timers) can no longer freeze the browser. The code keeps running while the request is in flight, and `.then()` runs when the result is ready.

But there is a problem with `.then()` that appears the moment your operations depend on each other.

### The Problem with Dependent Operations

Not every API request is independent. Often the result of one request is needed to make the next one. Consider a simple real-life scenario:

You want to make a meal. Here is the chain of dependent steps:

1. Order the ingredients online -- you cannot start cooking until they arrive
2. Once the ingredients arrive, wash and chop them -- you cannot cook raw, unprepped ingredients
3. Once they are prepped, cook the dish -- you cannot serve something that is not cooked
4. Once the dish is cooked, plate and serve it

Each step depends entirely on the one before it. Step 2 cannot start until step 1 is done. Step 3 cannot start until step 2 is done. And so on.

Now write this as JavaScript pseudocode using `.then()`:

```js
orderIngredients()
  .then(function(ingredients) {
    return washAndChop(ingredients)
      .then(function(preppedIngredients) {
        return cookDish(preppedIngredients)
          .then(function(cookedDish) {
            return plateAndServe(cookedDish)
              .then(function(meal) {
                console.log("Meal is ready:", meal)
              })
          })
      })
  })
  .catch(function(error) {
    console.log("Something went wrong:", error.message)
  })
```

Look at the shape of this code. Each dependent step pushes the next one further to the right. Four levels of nesting just to describe four sequential steps. This is sometimes called the **pyramid of doom**: the code structure grows sideways instead of downward, and by the fifth or sixth level it becomes genuinely difficult to read, debug, or modify.

And this was a simple, clean example. Four steps, four levels.

<!-- 
IMAGE: A code block showing the nested .then() structure from above, with an overlay highlighting the increasing indentation of each .then() level. Arrows on the right side point to each level labeled "Level 1", "Level 2", "Level 3", "Level 4". A caption below: "Each dependent operation adds another layer of nesting. This is readable at four levels. At eight it is not." Style: code block screenshot with colour-coded indentation levels and arrows.
-->

### What a Real Application Looks Like

In a real application, the chains go deeper. Consider what happens when a user opens their order history on an e-commerce website:

1. Fetch the user's authentication token from the session
2. Using that token, fetch the user's profile to get their user ID
3. Using that user ID, fetch their list of orders
4. Using the most recent order ID, fetch that order's full details
5. Using the product IDs in the order, fetch each product's current price and stock status

Five requests, each depending on the result of the one before it. In `.then()` syntax, this is five levels of nesting inside each other. By the time you add error handling, loading states, and logging, the code has drifted so far to the right that you spend more time managing indentation than understanding logic.

This is the problem `async/await` solves.

### Introducing async/await

`async/await` does not change how Promises work. Underneath, the same `.then()` and `.catch()` mechanics are happening. What it changes is how the code reads.

Instead of describing what to do when a Promise settles (callback style), `async/await` lets you write asynchronous code that looks like normal, sequential, top-to-bottom code.

**Two keywords, two rules:**

**`async`** -- placed before a function keyword. It marks the function as one that contains asynchronous operations. An `async` function always returns a Promise, automatically.

**`await`** -- placed before an expression that returns a Promise. It pauses execution inside the `async` function until that Promise settles, then evaluates to the resolved value. The rest of the program continues normally -- only the inside of the `async` function is paused.

### Side by Side: The Same Code Two Ways

Here is the meal preparation example rewritten with `async/await`:

**.then() version:**

```js
orderIngredients()
  .then(function(ingredients) {
    return washAndChop(ingredients)
      .then(function(preppedIngredients) {
        return cookDish(preppedIngredients)
          .then(function(cookedDish) {
            return plateAndServe(cookedDish)
              .then(function(meal) {
                console.log("Meal is ready:", meal)
              })
          })
      })
  })
  .catch(function(error) {
    console.log("Something went wrong:", error.message)
  })
```

**async/await version:**

```js
async function prepareMeal() {
  try {
    let ingredients        = await orderIngredients()
    let preppedIngredients = await washAndChop(ingredients)
    let cookedDish         = await cookDish(preppedIngredients)
    let meal               = await plateAndServe(cookedDish)

    console.log("Meal is ready:", meal)
  } catch (error) {
    console.log("Something went wrong:", error.message)
  }
}
```

The same four steps, the same order, the same result. But the `async/await` version reads exactly like the mental model: do this, then this, then this, then this. The pyramid is gone. Each step is a line.

Now apply this to the fetch call from the previous article:

**.then() version:**

```js
function loadPost(id) {
  return fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
    .then(function(response) {
      if (!response.ok) {
        throw new Error(`Request failed: ${response.status}`)
      }
      return response.json()
    })
    .then(function(post) {
      console.log(post.title)
    })
    .catch(function(error) {
      console.log("Error:", error.message)
    })
}
```

**async/await version:**

```js
async function loadPost(id) {
  try {
    let response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)

    if (!response.ok) {
      throw new Error(`Request failed: ${response.status}`)
    }

    let post = await response.json()
    console.log(post.title)
  } catch (error) {
    console.log("Error:", error.message)
  }
}
```

Both produce identical behaviour. The `async/await` version is what you will write in practice.

### The Rules of async/await

#### async functions always return a Promise

Even if you return a plain value from an `async` function, JavaScript wraps it in a fulfilled Promise automatically:

```js
async function greet() {
  return "Hello"
}

greet().then(msg => console.log(msg))    // "Hello"
```

This is why you can `await` a call to any `async` function.

#### await can only appear inside an async function

Using `await` outside of an `async` function throws a `SyntaxError`. This is the most common beginner mistake:

```js
// SyntaxError -- this does not work
function loadData() {
  let data = await fetch(url)    // await is not allowed here
}

// Correct -- mark the function async
async function loadData() {
  let data = await fetch(url)    // now await is valid
}
```

**Brief note -- top-level await:** In modern JavaScript modules (files using `import`/`export`), `await` can be used at the top level without an `async` wrapper. This is a newer feature and is less commonly seen in beginners' code, but worth knowing it exists.

#### async arrow functions

Arrow functions work with `async` the same way:

```js
const loadPost = async (id) => {
  let response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
  let post = await response.json()
  return post
}
```

#### The most common mistake: forgetting await

If you call an `async` function or a function that returns a Promise without `await`, you get the Promise object, not the resolved value:

```js
async function getPost() {
  let post = fetch("https://jsonplaceholder.typicode.com/posts/1")    // forgot await
  console.log(post)    // Promise {<pending>} -- not the data
}
```

```js
async function getPost() {
  let post = await fetch("https://jsonplaceholder.typicode.com/posts/1")    // correct
  console.log(post)    // Response object -- can now call .json() on it
}
```

If you ever see `[object Promise]` where you expected data, a missing `await` is almost always the cause.

### Error Handling in async Functions

In `.then()` chains, `.catch()` handles both network failures and errors thrown inside `.then()`. In `async/await`, the equivalent is `try/catch`:

```js
async function loadUserOrders(userId) {
  try {
    let userResponse = await fetch(`/api/users/${userId}`)
    if (!userResponse.ok) {
      throw new Error(`User not found: ${userResponse.status}`)
    }
    let user = await userResponse.json()

    let ordersResponse = await fetch(`/api/orders?userId=${user.id}`)
    if (!ordersResponse.ok) {
      throw new Error(`Could not load orders: ${ordersResponse.status}`)
    }
    let orders = await ordersResponse.json()

    return orders
  } catch (error) {
    console.log("Failed:", error.message)
    return []
  }
}
```

If any `await` rejects, or any `throw` is hit, execution immediately jumps to `catch`. The error is passed as the argument. The pattern is identical to synchronous error handling -- `try/catch` wraps the risky code, `catch` handles the failure.

**Note:** Inside an `async` function, throwing an error is equivalent to returning a rejected Promise. If you do not wrap with `try/catch` and the awaited Promise rejects, the error propagates as a rejected Promise from the `async` function itself. Always wrap async operations with `try/catch`.

### Sequential vs Parallel: A Critical Distinction

`await` inside a function pauses that function until the Promise settles. This is exactly what you want for dependent operations. But it means that if you `await` multiple independent operations one after another, they run sequentially -- each one waits for the previous to finish before starting.

**Sequential (slow -- each request waits for the previous):**

```js
async function loadDashboard() {
  let profile     = await fetch("/api/profile").then(r => r.json())
  let orders      = await fetch("/api/orders").then(r => r.json())
  let messages    = await fetch("/api/messages").then(r => r.json())
  // total time: time(profile) + time(orders) + time(messages)
}
```

Three independent requests, but they run one at a time. If each takes 300ms, the total is 900ms.

**Parallel (fast -- all requests fire simultaneously):**

```js
async function loadDashboard() {
  let [profile, orders, messages] = await Promise.all([
    fetch("/api/profile").then(r => r.json()),
    fetch("/api/orders").then(r => r.json()),
    fetch("/api/messages").then(r => r.json())
  ])
  // total time: max(time(profile), time(orders), time(messages))
}
```

All three fire at once. `Promise.all` waits until all three complete. If each takes 300ms, the total is still 300ms -- a 3x speedup.

Use sequential `await` when each operation depends on the result of the previous one. Use `Promise.all` when operations are independent and can run at the same time.

**The loop pitfall:**

Awaiting inside a `forEach` does not work as expected -- `forEach` does not understand Promises and will not wait for them. Use `for...of` instead:

```js
// Wrong -- forEach does not await properly
urls.forEach(async function(url) {
  let data = await fetch(url).then(r => r.json())    // not awaited by forEach
})

// Correct -- for...of respects await (sequential)
async function fetchAll(urls) {
  let results = []
  for (let url of urls) {
    let data = await fetch(url).then(r => r.json())
    results.push(data)
  }
  return results
}

// Best for independent requests -- parallel
async function fetchAllParallel(urls) {
  return await Promise.all(urls.map(url => fetch(url).then(r => r.json())))
}
```

<!-- 
IMAGE: Two timeline diagrams side by side. Left diagram labeled "Sequential": three horizontal bars (Profile, Orders, Messages) stacked top to bottom, each starting after the previous one ends. Total time bar on the right shows 900ms. Right diagram labeled "Parallel (Promise.all)": three horizontal bars all starting at the same time and ending at roughly the same point. Total time bar shows 300ms. Caption: "Sequential await runs each request after the previous completes. Promise.all runs them simultaneously." Style: flat timeline diagram, colour-coded bars, time labels.
-->

### async/await Is Syntactic Sugar

This phrase -- **syntactic sugar** -- means that `async/await` is a shorthand written on top of something that already existed. It does not introduce new capabilities. Under the hood, the JavaScript engine converts `async/await` code into the equivalent Promise chain before running it.

What changes is the developer experience. Code that would have been a deeply nested chain of `.then()` callbacks becomes flat, readable, sequential-looking code. You can use `try/catch` instead of `.catch()`. You can store intermediate results in variables with natural names. You can read an async operation the same way you read synchronous code.

This is why `async/await` is now the dominant style in professional JavaScript. Every major codebase, framework, and tutorial uses it. When you see `async` and `await` in a real project, you now know exactly what is happening underneath: a Promise is being created, settled, and handled -- just written in a way that any developer can read at a glance.

### Let's Define Things Now...

> **async:** A keyword placed before a function declaration. Makes the function always return a Promise. Enables the use of await inside it.

> **await:** A keyword placed before a Promise. Pauses execution of the async function until the Promise settles. Evaluates to the resolved value.

> **Syntactic sugar:** A syntax that makes code easier to write and read without changing the underlying behaviour. async/await is syntactic sugar over Promises.

> **Sequential execution:** Awaiting Promises one after another in order. Each starts only after the previous finishes. Use when operations depend on each other.

> **Parallel execution:** Starting multiple Promises simultaneously with Promise.all, then awaiting all of them together. Use when operations are independent.

> **Top-level await:** Using await outside any async function, available only in ES modules. A newer feature that removes the need for an outer async wrapper in module files.

### Activity: async/await Conversion

1. Take the `fetchBotResponse` function from the previous article written with `.then().catch()` and rewrite it using `async/await` and `try/catch`. Verify the output is identical.

2. Write an `async function fetchPost(id)` that fetches from `https://jsonplaceholder.typicode.com/posts/{id}`, checks `response.ok`, parses JSON, and returns the post object. Handle errors in `catch` by returning `null`.

3. Write `fetchMultipleSequential(ids)` that fetches each post ID one after another using `for...of` and returns an array of results. Time it with `console.time`.

4. Write `fetchMultipleParallel(ids)` that fetches all post IDs simultaneously using `Promise.all`. Time it with `console.time` on the same IDs and compare.

5. Deliberately forget `await` on a fetch call and log the result. Confirm you receive a Promise object. Add `await` back and confirm you receive the response.

6. Add the following to `chatbot-logic.js`:

```js
async function fetchBotResponse(userMessage) {
  try {
    let response = await fetch(
      `https://api.example.com/chat?message=${encodeURIComponent(userMessage)}`
    )

    if (!response.ok) {
      throw new Error(`Bot API error: ${response.status}`)
    }

    let data = await response.json()
    return data.reply
  } catch (error) {
    console.log("fetchBotResponse failed:", error.message)
    return "Sorry, I could not reach the server. Please try again."
  }
}
```

## What's Coming Next...

`async/await` is now your standard tool for any operation that communicates with a server. The code reads cleanly, the error handling is familiar, and the parallel vs sequential distinction is something you can now reason about clearly.

The one thing we have not fully addressed yet is how the UI responds to all the different things that can go wrong. A network failure, a 404, a server error, a slow connection that leaves the user staring at a blank screen -- each of these needs a different response in the interface. The next article covers exactly that: loading states, error states, empty states, and the finally block for cleanup. That wraps up our JavaScript module, and after that we circle back to the project -- wiring all of this together into the fully working chatbot interface.
