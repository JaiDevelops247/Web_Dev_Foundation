## Introduction

In the previous article we covered how to declare functions in three different ways, how calling them works, and how hoisting lets function declarations be used before they appear in the file. We also briefly mentioned scope when talking about `let` and `const` back in the Variables article.

But we never actually looked at what scope means specifically in the context of functions. We know that `let` is block-scoped. We know that `var` is function-scoped. We said those words without fully unpacking what "function-scoped" means or why it matters.

Now that we understand functions properly, we can revisit scope and see the full picture: what determines which variables a piece of code can access, and what happens when a function carries that access with it after the original context is gone.

### What Is Scope?

**Scope** is the set of variables that a particular piece of code can see and use at any given point.

Not every variable in a program is available everywhere. A variable declared inside a function is not visible to code outside that function. A variable declared inside a loop is not visible after the loop ends. Scope is the rule system that decides this visibility.

Scope exists for good reason. Without it, every variable in a program would need a globally unique name. A loop counter called `i` inside one function would collide with a loop counter called `i` in another. Variables from different parts of a program would interfere with each other in ways that are difficult to trace.

JavaScript has three levels of scope.

### 1. Global Scope

A variable declared outside any function or block lives in global scope. It is accessible from anywhere in the file: inside functions, inside loops, inside conditions.

```js
let appName = "ChatAI"    // global scope

function showAppName() {
  console.log(appName)    // can access global variable -- "ChatAI"
}

showAppName()
console.log(appName)      // also accessible here -- "ChatAI"
```

Global variables are available everywhere, which sounds convenient but creates problems as programs grow. When any part of the code can read or modify a global variable, tracking down where an unexpected change happened becomes very difficult. The general principle is: keep variables as local as possible, and only put things in global scope when they genuinely need to be shared across the entire program.

### 2. Function Scope

A variable declared inside a function using `var` (and also with `let` or `const`) exists only within that function. It is created when the function is called and ceases to exist when the function finishes.

```js
function calculateDiscount() {
  let discount = 0.2    // exists only inside this function
  return discount
}

calculateDiscount()
console.log(discount)    // ReferenceError: discount is not defined
```

`discount` only exists while `calculateDiscount` is running. Once the function returns, the variable is gone. Code outside the function cannot reach it.

This is the core of **function scope**: the function creates its own private environment for its variables. Each call to the function creates a fresh copy of that environment. Two calls to the same function do not share variables.

```js
function makeId() {
  let count = 0
  count++
  return count
}

console.log(makeId())    // 1
console.log(makeId())    // 1 again -- each call starts fresh, count is always 0 at start
```

### 3. Block Scope

A variable declared with `let` or `const` inside any pair of curly braces `{}` is scoped to that block. The block could be an `if` statement, a `for` loop, a `while` loop, or even a standalone pair of braces.

```js
if (true) {
  let message = "inside the block"
  console.log(message)    // "inside the block"
}

console.log(message)      // ReferenceError: message is not defined
```

```js
for (let i = 0; i < 3; i++) {
  let squared = i * i
  console.log(squared)    // 0, 1, 4
}

console.log(i)        // ReferenceError: i is not defined
console.log(squared)  // ReferenceError: squared is not defined
```

Both `i` and `squared` are confined to the `for` block. They do not leak out. This is why `let` and `const` are preferred over `var` -- they respect block boundaries and stay exactly where you put them.

### The Scope Chain

When JavaScript encounters a variable name in the code, it does not look for it globally. It starts at the current scope and works outward:

1. Look in the current block or function
2. If not found, look in the enclosing block or function
3. Continue outward until the global scope is reached
4. If not found anywhere, throw a `ReferenceError`

This outward search path is called the **scope chain**.

```js
let siteName = "ByteXL"             // global

function outer() {
  let section = "Dashboard"         // outer function scope

  function inner() {
    let item = "Settings"           // inner function scope
    console.log(item)               // found immediately -- "Settings"
    console.log(section)            // not in inner, found in outer -- "Dashboard"
    console.log(siteName)           // not in inner or outer, found in global -- "ByteXL"
  }

  inner()
}

outer()
```

`inner` can see its own variables, its parent function's variables, and global variables. But the search goes **outward only**, never inward:

```js
function outer() {
  function inner() {
    let secret = "hidden"
  }
  inner()
  console.log(secret)    // ReferenceError -- outer cannot see inner's variables
}
```

<!-- 
IMAGE: A nested box diagram showing three concentric rectangles. Outermost box labeled "Global Scope" containing the variable siteName. Middle box labeled "outer() Function Scope" containing section. Innermost box labeled "inner() Function Scope" containing item. Arrows pointing outward from the innermost box, labeled "Scope chain: if variable not found here, look outward." A crossed-out arrow pointing inward from outer to inner labeled "Cannot look inward." Caption: "The scope chain searches outward. Each function can see its own variables and all enclosing ones, but not the variables of nested functions inside it." Style: flat vector, three nested colored rectangles with labeled variables and directional arrows.
-->

### Closures

Now we get to the behaviour that the scope chain makes possible but that surprises most developers when they first encounter it.

Consider what happens here:

```js
function createGreeter(name) {
  return function() {
    console.log(`Hello, ${name}!`)
  }
}

const greetJai = createGreeter("Jai")
greetJai()    // Hello, Jai!
```

`createGreeter("Jai")` runs and returns a function. After it returns, `createGreeter` is done. Its local variable `name` would normally be cleaned up. But `greetJai` is a reference to the returned function, and that function still works. It still knows `name` is `"Jai"`. How?

This is a **closure**. The inner function did not just receive a copy of `name` at the moment it was defined. It captured a reference to the scope it was created in. When `greetJai()` is called later, that scope is still alive because the returned function holds a reference to it.

**Formal definition:** A closure is a function that retains access to variables from its outer scope even after that outer function has finished executing.

#### The Lexical Environment

The key to understanding why closures work is knowing that scope is determined by **where a function is written**, not where it is called from.

This is called **lexical scoping** (lexical means "relating to the source code"). When the inner function in the example above was written inside `createGreeter`, it was surrounded by `name`. That surrounding context became part of the function's definition. No matter where you call the returned function from, it always looks for `name` in the scope it was written in.

```js
function makeAdder(x) {
  return function(y) {
    return x + y     // x is from the outer scope -- always the x this function was created with
  }
}

const add5 = makeAdder(5)
const add10 = makeAdder(10)

console.log(add5(3))     // 8   -- x is 5
console.log(add10(3))    // 13  -- x is 10
console.log(add5(7))     // 12  -- x is still 5
```

`add5` and `add10` are two separate closures, each with their own captured `x`. They do not share state. Every call to `makeAdder` creates a brand new scope with a fresh `x`.

#### The Classic Closure Bug: var in a Loop

This is one of the most well-known JavaScript interview questions, and understanding it shows that you genuinely understand closures.

```js
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i)
  }, 1000)
}
```

What do you expect to print after one second? `0`, `1`, `2`?

It prints `3`, `3`, `3`.

Here is why. The three `setTimeout` callbacks are closures. They all close over the variable `i`. But because `var` is function-scoped (not block-scoped), all three callbacks are closing over the **same `i`**. There is only one `i` in the enclosing scope.

By the time the callbacks run (after 1000ms), the loop has already finished. `i` is now `3`. All three callbacks read the same `i` and print the same value.

The fix is straightforward: replace `var` with `let`.

```js
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i)
  }, 1000)
}
// prints: 0, 1, 2
```

`let` is block-scoped, so each iteration of the loop creates its own separate `i`. Each callback closes over a different `i`: the one from its specific iteration. When the callbacks run, each one reads its own captured value.

This is the most practical reason `let` exists: it makes closures inside loops behave the way you expect.

#### Practical Use: Private Variables and Encapsulation

Closures are how JavaScript achieves something that other languages do with access modifiers like `private`. You can create a variable that is accessible only through specific functions, with no way to read or modify it directly from outside.

```js
function makeCounter() {
  let count = 0    // private -- cannot be accessed from outside

  return {
    increment: function() { count++ },
    decrement: function() { count-- },
    getCount:  function() { return count }
  }
}

const counter = makeCounter()

counter.increment()
counter.increment()
counter.increment()
counter.decrement()

console.log(counter.getCount())    // 2
console.log(counter.count)         // undefined -- no direct access
```

`count` is completely private. The only way to interact with it is through `increment`, `decrement`, and `getCount`. This is called **data encapsulation**: hiding the internal state of a component and exposing only a controlled interface.

In web applications, this pattern is used for:

- A shopping cart that exposes `addItem`, `removeItem`, `getTotal` but not the raw items array
- A session manager that exposes `incrementMessageCount`, `reset`, `getCount` but not the raw counter
- An API client that exposes `request` and `logout` but not the stored authentication token

```js
function createSession(userName) {
  let messageCount = 0
  let user = userName

  return {
    sendMessage: function() {
      messageCount++
    },
    reset: function() {
      messageCount = 0
    },
    getStats: function() {
      return `${user}: ${messageCount} messages sent`
    }
  }
}

const session = createSession("Jai")
session.sendMessage()
session.sendMessage()
session.sendMessage()
console.log(session.getStats())    // Jai: 3 messages sent
session.reset()
console.log(session.getStats())    // Jai: 0 messages sent
```

### Let's Define Things Now...

> **Scope:** The set of variables accessible to a particular piece of code at a given point. Determined by where variables and functions are written.

> **Global Scope:** Variables declared outside any function or block. Accessible everywhere in the program.

> **Function Scope:** Variables declared inside a function. Accessible only within that function. Created fresh on each call.

> **Block Scope:** Variables declared with `let` or `const` inside `{}`. Accessible only within that block.

> **Scope Chain:** The path JavaScript follows when looking up a variable: current scope, then enclosing scopes outward, until global scope. If not found anywhere, a ReferenceError is thrown.

> **Closure:** A function that retains access to its outer scope's variables even after the outer function has returned.

> **Lexical Scoping:** Scope is determined by where a function is written in the source code, not where it is called from.

> **Data Encapsulation:** Hiding internal state inside a closure and exposing only specific functions to read or modify it. Prevents direct access to the raw variable.

### Activity: Scope and Closure Lab

1. For each of the following, trace the scope chain and predict the output before running:

```js
// Snippet 1
let x = "global"
function outer() {
  let x = "outer"
  function inner() {
    console.log(x)
  }
  inner()
}
outer()

// Snippet 2
let value = 10
function addToValue(n) {
  return value + n
}
console.log(addToValue(5))

// Snippet 3
function wrapper() {
  let secret = "hidden"
}
wrapper()
console.log(secret)    // predict: value or error?
```

2. Predict the output of this classic loop bug, then run it to confirm:

```js
for (var i = 0; i < 4; i++) {
  setTimeout(function() { console.log(i) }, 500)
}
```

Then fix it by changing `var` to `let` and confirm the output is now `0, 1, 2, 3`.

3. Build a `makeCounter()` function that returns an object with `increment`, `decrement`, and `getCount`. Confirm that:
   - `counter.count` returns `undefined` (no direct access)
   - Two separate counters from two `makeCounter()` calls do not share state

4. Add the following to `chatbot-logic.js`:

```js
function createSession(userName) {
  let messageCount = 0
  let user = userName

  return {
    sendMessage: function() {
      messageCount++
    },
    reset: function() {
      messageCount = 0
    },
    getStats: function() {
      return `${user}: ${messageCount} messages sent this session`
    }
  }
}

const session = createSession("Jai")
session.sendMessage()
session.sendMessage()
console.log(session.getStats())
session.reset()
console.log(session.getStats())
```

## What's Coming Next...

Closures are one of JavaScript's most important underlying concepts. You will encounter them constantly -- in event handlers, in the array methods we are about to cover, in the way React components manage state, and in almost every real-world JavaScript pattern. You do not need to have every detail memorised right now. The goal was familiarity: understanding what a closure is when you see one, and knowing why it works.

The next article steps away from scope and looks at something more immediately practical: error handling. What happens when something in your program goes wrong -- a bad input, a failed operation, an unexpected value -- and how do you handle it gracefully instead of letting the program crash?
