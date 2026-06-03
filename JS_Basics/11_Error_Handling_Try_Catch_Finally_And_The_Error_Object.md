## Introduction

You have seen this before, as a regular user of the internet:

<!-- 
IMAGE: A browser screenshot showing a clean 404 page. The page displays a large "404" heading with a message like "Page Not Found" or "We couldn't find what you were looking for." Below it, a "Go back home" button. This could be from any well-designed product -- Vercel, Notion, GitHub, or a simple e-commerce platform. Caption: "A 404 page is what a user sees. Behind it is a deliberate error-handling decision made by the developer." Style: realistic browser screenshot of a polished 404 error page.
-->

On an e-commerce platform, you try to open a product link and see "This item is no longer available." On a food delivery app, you search for a restaurant that has closed and the app shows "No results found." On a streaming platform, you try to access content that is not available in your region and get a friendly message instead of a blank screen.

As an end user, these feel like simple messages. What is actually happening on the server side is a deliberate decision by a developer: something went wrong during a function's execution, the developer anticipated that it could go wrong, wrote code to catch that failure, and chose to show a clean message rather than let the program crash entirely.

This is error handling.

We are covering it here, right after functions and scope, because it fits naturally into the same mental model. A function receives input, performs an operation, and returns something. But what happens when the input is not what the function expected? What happens when the data it needs does not exist? What happens when the operation fails halfway through?

Without any error handling, the program crashes. With it, the developer gets to decide what happens instead.

### Why Error Handling Is Necessary

JavaScript stops executing when it encounters an unhandled error. Not just the function that failed. The entire program.

```js
function getUserName(user) {
  return user.name.toUpperCase()
}

getUserName(null)    // TypeError: Cannot read properties of null (reading 'name')
// Program stops here. Everything after this line is never reached.
```

If this were a web server handling a user's request, that crash would mean the user gets no response. If this were a checkout function in an e-commerce app, the user's payment would not go through and they would see a blank screen instead of a confirmation.

The problem is not that errors happen. Errors are inevitable: users send unexpected input, network requests fail, data is missing, servers go down. The problem is letting errors reach the user as crashes or blank screens instead of handling them with intention.

Error handling gives you the ability to:
- Detect when something goes wrong
- Respond to it in a controlled way
- Show the user a meaningful message
- Keep the rest of the program running

### The try-catch Block

The fundamental tool for error handling in JavaScript is the `try-catch` block. It says: try running this code, and if anything goes wrong, catch the problem and handle it here.

**Formal definition:** `try-catch` is a construct that wraps a block of code you want to monitor. If any error is thrown inside the `try` block, execution immediately jumps to the `catch` block. Code outside the `try-catch` continues running normally.

**Syntax:**

```js
try {
  // code that might fail
} catch (error) {
  // code that runs if anything in the try block fails
  // error is the Error object describing what went wrong
}
```

```js
function getUserName(user) {
  return user.name.toUpperCase()
}

try {
  let name = getUserName(null)
  console.log(name)
} catch (error) {
  console.log("Something went wrong:", error.message)
}

console.log("Program continues running")
// Something went wrong: Cannot read properties of null (reading 'name')
// Program continues running
```

Without the `try-catch`, the program would have stopped at the error. With it, the error is caught, a message is logged, and everything after the `try-catch` continues normally.

**Real website example:** When a social media platform loads your profile, it makes a request to fetch your data. If the server returns nothing (account deleted, session expired, network blip), the code that tries to read `userData.name` or `userData.posts` would fail. A `try-catch` around that operation lets the app show "Could not load your profile. Please try again." instead of a broken screen.

```js
try {
  let userName = userData.profile.name
  let posts = userData.posts.length
  console.log(`${userName} has ${posts} posts`)
} catch (error) {
  console.log("Profile could not be loaded. Please refresh.")
}
```

**What the error object contains:**

The `error` variable in the `catch` block is an object with information about what went wrong. The two most useful properties are:

- `error.message`: a human-readable description of the error
- `error.name`: the type of error (TypeError, RangeError, etc.)

```js
try {
  null.toString()
} catch (error) {
  console.log(error.name)       // "TypeError"
  console.log(error.message)    // "Cannot read properties of null (reading 'toString')"
}
```

### The finally Block

`finally` is an optional third part of the `try-catch` structure. Code inside `finally` runs every time, regardless of whether the `try` block succeeded or failed.

**Syntax:**

```js
try {
  // code that might fail
} catch (error) {
  // runs if something fails
} finally {
  // runs always, success or failure
}
```

```js
function fetchUserData() {
  console.log("Loading...")    // before the operation

  try {
    // simulate fetching data
    let user = null
    console.log(user.name)    // fails
  } catch (error) {
    console.log("Failed to load user data.")
  } finally {
    console.log("Loading complete.")    // always runs
  }
}

fetchUserData()
// Loading...
// Failed to load user data.
// Loading complete.
```

**Why finally exists:**

Some actions must happen whether the operation succeeded or not. If you open a database connection before a `try` block, you need to close it after -- whether the query succeeded or failed. If you show a loading spinner while a request is in flight, you need to hide it when the request finishes -- whether it worked or not.

`finally` is the right place for these cleanup actions because it runs unconditionally.

**Real website example:** On Swiggy or Zomato, when you load a restaurant's menu:

```js
function loadMenu(restaurantId) {
  showLoadingSpinner()    // show spinner before trying

  try {
    let menu = fetchMenuFromServer(restaurantId)
    displayMenu(menu)
  } catch (error) {
    displayError("Menu could not be loaded. Please try again.")
  } finally {
    hideLoadingSpinner()    // always hide the spinner when done
  }
}
```

The spinner disappears whether the menu loaded successfully or failed. Without `finally`, a developer would need to call `hideLoadingSpinner()` twice -- once inside `try` and once inside `catch`. `finally` eliminates that duplication.

<!-- 
IMAGE: A flowchart for try-catch-finally. Three paths branch from a "Start" node. Path 1 (top): arrow goes into "try block runs" -> "No error" -> skips catch -> goes directly to "finally block" -> "continue." Path 2 (middle): "try block runs" -> "Error thrown" -> immediately jumps to "catch block" -> "finally block" -> "continue." A label on the finally box: "Runs in both cases." Caption: "try runs the risky code. catch handles failures. finally always runs last." Style: flat vector, three-path flowchart with clear color coding for success path, error path, and the shared finally box.
-->

### The throw Statement

So far we have only seen errors that JavaScript throws automatically (TypeError when accessing a property on null, for example). But you can also throw errors deliberately using the `throw` statement.

`throw` manually creates an error and sends it to the nearest `catch` block.

**Syntax:**

```js
throw value
```

Technically, you can throw any value -- a string, a number, an object. The convention in JavaScript is to throw an instance of an `Error` object, because the Error object gives you a structured `message` and `name` that are easy to work with in the catch block.

A brief note on objects: in JavaScript, almost everything can be treated as an object -- more on that when we cover objects in depth. For now, just know that each error JavaScript creates (and each one you create with `new Error(...)`) is a structured object with properties you can read.

```js
function divide(a, b) {
  if (b === 0) {
    throw new Error("Division by zero is not allowed")
  }
  return a / b
}

try {
  let result = divide(10, 0)
} catch (error) {
  console.log("Caught:", error.message)
}
// Caught: Division by zero is not allowed
```

The function detects an invalid situation and throws instead of returning a wrong result (dividing by zero in JavaScript produces `Infinity`, not an error, which is often worse than crashing because the program keeps running with corrupted data).

**Real website example:** A payment processing function that receives a zero or negative amount:

```js
function processPayment(amount) {
  if (amount <= 0) {
    throw new Error("Payment amount must be greater than zero")
  }
  // process the payment
}

try {
  processPayment(-500)
} catch (error) {
  console.log("Payment failed:", error.message)
  // show error to user, do not proceed with transaction
}
```

### Built-in Error Types

JavaScript comes with several specific Error types for different situations. Using the right one makes your error messages more informative and allows `catch` blocks to distinguish between different kinds of failures.

| Error Type | When to use |
|---|---|
| `Error` | Generic fallback when no specific type fits |
| `TypeError` | The wrong type of value was provided (string where number expected, calling something that is not a function) |
| `RangeError` | A value is outside the allowed range (negative array length, out-of-bounds index) |
| `ReferenceError` | A variable that does not exist was referenced (usually thrown by JavaScript automatically) |

```js
function setVolume(level) {
  if (typeof level !== "number") {
    throw new TypeError("Volume must be a number")
  }
  if (level < 0 || level > 100) {
    throw new RangeError("Volume must be between 0 and 100")
  }
  console.log(`Volume set to ${level}`)
}

try {
  setVolume("loud")
} catch (error) {
  console.log(`${error.name}: ${error.message}`)
  // TypeError: Volume must be a number
}

try {
  setVolume(150)
} catch (error) {
  console.log(`${error.name}: ${error.message}`)
  // RangeError: Volume must be between 0 and 100
}
```

Using `TypeError` and `RangeError` instead of generic `Error` communicates what kind of mistake was made. A developer reading the error in a log can immediately tell whether the problem was a wrong type or an out-of-bounds value, without needing to read additional context.

### Errors vs Validation: An Important Distinction

Not every problem in a program should be handled with `throw` and `catch`. There is an important difference between two situations:

**An error** is an unexpected failure. Something that should not happen in normal usage but could happen due to bad data, a network problem, or a programming mistake. `throw` is appropriate here.

**Validation** is expected behaviour. A user types their age as text instead of a number. A form field is left empty. These are not program failures. They are normal user behaviour that the program should handle by returning a message, not by throwing.

```js
// This is validation -- expected user behaviour, return a message
function validateAge(input) {
  if (input === "" || input === null) {
    return { valid: false, message: "Age is required" }
  }
  let age = Number(input)
  if (isNaN(age)) {
    return { valid: false, message: "Age must be a number" }
  }
  if (age < 0 || age > 120) {
    return { valid: false, message: "Age must be between 0 and 120" }
  }
  return { valid: true, value: age }
}

// Not this -- user leaving a field blank is not a program crash
function validateAge(input) {
  if (input === "") {
    throw new Error("Age is required")    // wrong -- validation should not throw
  }
}
```

**The rule:** throw errors at the boundaries of your program where unexpected data or system failures arrive -- a network response in the wrong format, a database that did not respond, a function called with the entirely wrong type of data. Use return values for handling the expected range of user input.

A form that shows "Please enter a valid email" when the user types a wrong format is doing validation. A server endpoint that throws when it receives a request with a completely malformed body is doing error handling. Both are necessary. The difference is in what each tool is for.

### Let's Define Things Now...

> **try-catch:** A block that monitors code for errors. If any error is thrown inside `try`, execution immediately moves to `catch`. Code after the `try-catch` continues running normally.

> **finally:** An optional block that always runs after `try` and `catch`, regardless of whether an error occurred. Used for cleanup operations that must happen in either case.

> **throw:** A statement that manually creates and throws an error, stopping the current execution and jumping to the nearest `catch` block.

> **Error Object:** A built-in JavaScript object with `name` and `message` properties describing an error. Created with `new Error("message")`, `new TypeError("message")`, etc.

> **TypeError:** An error thrown when a value is of the wrong type (accessing a property on null, calling a non-function).

> **RangeError:** An error thrown when a value is outside the allowed range.

> **Validation:** Handling expected invalid input from users by returning a descriptive message. Should use return values, not throw.

> **Error Handling:** Managing unexpected failures at system boundaries (network, external data, wrong types) using try-catch.

### Activity: Error Handling Practice

1. Wrap the `validateMessage` function from Article 03 in a try-catch and update it to throw a `TypeError` when the input is not a string:

```js
function validateMessage(input) {
  if (typeof input !== "string") {
    throw new TypeError(`Expected a string but received ${typeof input}`)
  }
  if (input.trim() === "") {
    return { valid: false, message: "Message cannot be empty" }
  }
  return { valid: true, message: input.trim() }
}

// Test all cases:
try { console.log(validateMessage(42)) } catch (e) { console.log(`${e.name}: ${e.message}`) }
try { console.log(validateMessage("")) } catch (e) { console.log(`${e.name}: ${e.message}`) }
try { console.log(validateMessage("Hello")) } catch (e) { console.log(`${e.name}: ${e.message}`) }
```

2. Write a `safeDivide(a, b)` function that throws a `RangeError` when `b` is zero. Test it with `try-catch`.

3. Write a `parseUserAge(input)` function that:
   - Throws a `TypeError` if input is not a string or number
   - Throws a `RangeError` if the parsed age is below 0 or above 120
   - Returns the parsed age number if valid

4. Add the following to `chatbot-logic.js`:

```js
function processMessage(input) {
  try {
    let validation = validateMessage(input)
    if (!validation.valid) {
      return { success: false, reply: validation.message }
    }
    let category = classifyMessage(validation.message)
    let response = getBotResponse(category)
    return { success: true, reply: buildBotMessage("ChatAI", response) }
  } catch (error) {
    console.error(`Unexpected error: ${error.name} -- ${error.message}`)
    return { success: false, reply: "Something went wrong. Please try again." }
  } finally {
    console.log(`Message processing attempt logged.`)
  }
}

console.log(processMessage("Hello!"))
console.log(processMessage(null))
console.log(processMessage(""))
```

## What's Coming Next...

Error handling is a mark of professional development practice. Real users do not behave the way your code expects. Real networks fail at inconvenient times. Real data arrives in unexpected formats. Writing code that handles these situations gracefully, rather than crashing, is what separates a working prototype from a production application. That is why we covered it here, before we go any further.

Next we move into collections. So far, every variable we have worked with holds one value at a time. But most real data is a list: a list of messages, a list of products, a list of users. Arrays are the JavaScript structure for ordered collections, and they come with a powerful set of tools for transforming, filtering, and searching through that data. That is what the next module covers.
