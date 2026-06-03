## Introduction

Consider this idea: certain blocks of code that a user or a server needs to perform again and again can be wrapped into a separate named entity, stored once, and called on demand whenever that task is needed.

That might sound abstract for now. Let us look at a concrete example.

When you log in to your Google account, Google's server has to do the same set of tasks for every single user who logs in:

1. Read the email address that was submitted
2. Check if that email exists in the database
3. If it exists, compare the submitted password with the stored one
4. If both match, create a session and let the user in
5. If anything does not match, return an error

This exact sequence runs for every one of Google's billions of login attempts. The logic does not change. Only the data changes: a different email, a different password, each time. It would be absurd to copy and paste those five steps into the code once per user. Instead, Google writes that logic once, gives it a name, and calls it each time a new login attempt arrives.

More examples from products you use daily:

- Every time Netflix loads a new page, it checks whether your subscription is active. That check is one piece of logic written once and called on every page load.
- Every time you type in a search bar on Amazon, the input is trimmed, lowercased, and passed to the search engine. That preparation happens the same way every time.
- Every time a new user signs up on any platform, the submitted email is validated for format. The same email-checking logic runs every single time.

This idea of packaging a reusable piece of logic under a name, and calling it wherever that task is needed, is expressed in programming using **functions**. The next few articles are about how functions work in JavaScript, and why they are the single most important building block you will use.

### What Is a Function?

A function is a named, reusable block of code that performs a specific task. You define it once with a name. Wherever you need that task done, you call it by name. The code inside runs, and the result comes back to you.

Using functions in a program always involves two distinct steps:

1. **Declaration:** writing the function once -- defining what it does, what it needs, and what it gives back.
2. **Calling:** invoking the function by name whenever that task needs to happen.

Think of a function like a machine in a factory. The machine is built once (declaration). Then it can be operated as many times as needed (calling). The machine takes raw materials as input (parameters), processes them internally, and produces an output (return value).

Let us start with how to build the machine.

### Part 1: Declaring a Function

#### Function Declaration

The most straightforward way to define a function is with the `function` keyword.

**Syntax:**

```js
function functionName(parameter1, parameter2) {
  // body: the logic that runs when this function is called
  return result
}
```

**Breaking down each part:**

`function` is the keyword that tells JavaScript you are defining a function.

`functionName` is the name you give it. Choose a name that describes what the function does. The convention is camelCase: `checkUserExists`, `formatMessage`, `calculateTotal`.

`(parameter1, parameter2)` is the parameter list. Parameters are placeholders for the values the function will receive when called. A function can have zero, one, or many parameters.

The **body** between the curly braces is the code that runs each time the function is called.

The `return` statement sends a value back to wherever the function was called from. When execution reaches `return`, the function stops immediately and the value to its right is handed back.

```js
function greetUser(name) {
  return `Welcome back, ${name}!`
}
```

```js
function calculateTotal(price, taxRate) {
  let tax = price * taxRate
  return price + tax
}
```

**Using the returned value:**

```js
let message = greetUser("Jai")
console.log(message)    // Welcome back, Jai!

let total = calculateTotal(850, 0.18)
console.log(total)      // 1003
```

The function runs, produces a result, and that result is available for the rest of the program to use.

#### Hoisting: Function Declarations Can Be Called Before They Are Written

One specific behaviour of function declarations is worth knowing: they are **fully hoisted**. The JavaScript engine processes all function declarations before executing any code, which means you can call a function before it appears in the file.

```js
// calling before declaration -- works with function declarations
let result = add(3, 5)
console.log(result)    // 8

function add(a, b) {
  return a + b
}
```

This is unique to `function` declarations. We will see that other ways of defining functions do not share this behaviour.

#### Function Expression

A function expression defines a function and assigns it to a variable, rather than giving it a standalone name.

```js
const greetUser = function(name) {
  return `Welcome back, ${name}!`
}
```

The right side is an anonymous function (no name between `function` and the parentheses). It is assigned to the variable `greetUser` just like any other value would be.

**The key difference from a function declaration: function expressions are not hoisted.**

```js
// calling before declaration -- throws an error with function expressions
let result = multiply(3, 5)    // ReferenceError or TypeError

const multiply = function(a, b) {
  return a * b
}
```

The variable `multiply` exists (because `const` declarations are processed), but its value has not been assigned yet at the point the call happens. The function expression must appear before the call in the file.

**When to use a function expression over a declaration?**

Function expressions are used when you want to assign a function as a value -- storing it in an object, passing it to another function, or returning it from a function. Both approaches are valid for simple named utility functions. The choice often comes down to team convention.

#### Arrow Functions

Introduced in ES6, arrow functions are a more concise syntax for writing functions.

**Syntax:**

```js
const functionName = (parameter1, parameter2) => {
  // body
  return result
}
```

The `function` keyword and the function name are replaced by the `=>` arrow after the parameter list. The function is assigned to a variable.

```js
const greetUser = (name) => {
  return `Welcome back, ${name}!`
}
```

**Implicit return for single-expression bodies:**

When the function body is a single expression whose value you want to return, you can remove the curly braces and the `return` keyword entirely. The expression's value is returned automatically.

```js
// Full form
const double = (n) => {
  return n * 2
}

// Implicit return -- same result
const double = (n) => n * 2
```

```js
const calculateTotal = (price, taxRate) => price + price * taxRate

console.log(calculateTotal(850, 0.18))    // 1003
```

**Single parameter shorthand:**

When there is exactly one parameter, the parentheses around it are optional:

```js
const greet = name => `Hello, ${name}!`
```

Zero parameters still require parentheses:

```js
const getCurrentTime = () => new Date().toLocaleTimeString()
```

**The key behavioral difference: arrow functions do not have their own `this`**

This is the most important distinction between arrow functions and regular functions. Regular functions create their own `this` context based on how they are called. Arrow functions inherit `this` from the surrounding code where they were defined.

```js
const bot = {
  name: "ChatAI",
  greet: function() {
    console.log(`I am ${this.name}`)    // this refers to bot -- works
  }
}

const bot2 = {
  name: "ChatAI",
  greet: () => {
    console.log(`I am ${this.name}`)    // this does NOT refer to bot2 -- undefined
  }
}
```

For now: use regular functions or function expressions when you need `this` to refer to the object the function belongs to. Arrow functions are ideal for callbacks, short transformations, and any place where you want to inherit `this` from the outside context. We will return to this when covering objects in depth.

<!-- 
IMAGE: Three code blocks side by side showing the same function written three ways. Left block: function declaration (function greet(name) { return ... }), with a tag "Hoisted -- can call before definition." Center block: function expression (const greet = function(name) { return ... }), with a tag "Not hoisted -- define before use." Right block: arrow function (const greet = (name) => ...), with a tag "Not hoisted. No own 'this'. Concise implicit return." Below all three, a row showing "greet('Jai')" calling each and producing identical output. Caption: "Three ways to write a function. Behavior is the same for most purposes. The differences appear in hoisting and 'this' binding." Style: flat vector, three equal-width code cards on a dark background with colored tags.
-->

### Part 2: Calling a Function

Declaring a function does not run any code. The logic inside only executes when the function is **called** (also called invoked). You call a function by writing its name followed by parentheses:

```js
greetUser("Jai")
calculateTotal(850, 0.18)
```

The values inside the parentheses during the call are called **arguments**. They are the actual data passed into the function. The parameter names in the function definition are just placeholders -- arguments are the real values that fill those placeholders when the function runs.

```js
function add(a, b) {    // a and b are PARAMETERS -- placeholders
  return a + b
}

add(10, 5)    // 10 and 5 are ARGUMENTS -- the actual values
```

#### Too Few Arguments

If you call a function with fewer arguments than it has parameters, the missing parameters receive `undefined`:

```js
function introduce(name, city) {
  return `${name} is from ${city}`
}

console.log(introduce("Jai"))
// "Jai is from undefined"  -- city was not passed
```

#### Too Many Arguments

If you pass more arguments than the function has parameters, the extra ones are silently ignored:

```js
function add(a, b) {
  return a + b
}

console.log(add(3, 5, 100, 200))    // 8 -- extra arguments are ignored
```

#### Default Parameter Values

To handle missing arguments gracefully, you can define a default value for a parameter. If the argument is not passed (or is `undefined`), the default is used instead.

```js
function greetUser(name = "Guest") {
  return `Welcome back, ${name}!`
}

console.log(greetUser("Jai"))    // Welcome back, Jai!
console.log(greetUser())         // Welcome back, Guest!
```

```js
function createMessage(text, sender = "Bot", timestamp = "now") {
  return `[${timestamp}] ${sender}: ${text}`
}

console.log(createMessage("Hello!"))
// [now] Bot: Hello!

console.log(createMessage("Hello!", "User", "9:41 AM"))
// [9:41 AM] User: Hello!
```

Defaults make functions more resilient to being called with incomplete data without crashing.

#### The Rest Operator: Collecting Extra Arguments

Sometimes you want a function that can accept any number of arguments without knowing the count in advance. The rest operator (`...`) collects all remaining arguments into an array.

```js
function logAll(...items) {
  for (let item of items) {
    console.log(item)
  }
}

logAll("apple", "banana", "cherry")    // logs all three
logAll(1, 2, 3, 4, 5)                 // logs all five
```

The rest operator must be the last parameter in the list:

```js
function sendNotification(title, ...recipients) {
  console.log(`Sending "${title}" to:`)
  for (let recipient of recipients) {
    console.log(" -", recipient)
  }
}

sendNotification("New message", "jai@email.com", "riya@email.com", "arjun@email.com")
// Sending "New message" to:
//  - jai@email.com
//  - riya@email.com
//  - arjun@email.com
```

**Use case:** Any function that needs to handle a variable-length list -- sending to multiple recipients, logging multiple values, combining multiple strings.

#### When There Is No Return Statement

If a function has no `return` statement, or if execution reaches the end of the function without hitting one, it returns `undefined` automatically.

```js
function logMessage(message) {
  console.log(message)
  // no return statement
}

let result = logMessage("Hello")
console.log(result)    // undefined
```

This is expected for functions whose purpose is to produce a side effect (logging, updating the DOM, sending a request) rather than to produce a value. Knowing that `undefined` is the default return value helps when a function call produces unexpected results.

### Higher-Order Functions

JavaScript treats functions as values. A function can be stored in a variable (which we have already done with function expressions). It can also be passed as an argument to another function, and returned from a function.

A function that accepts another function as an argument, or returns a function, is called a **higher-order function**.

**Accepting a function as an argument:**

```js
function applyOperation(value, operation) {
  return operation(value)
}

const double = n => n * 2
const square = n => n * n

console.log(applyOperation(5, double))    // 10
console.log(applyOperation(5, square))    // 25
```

`applyOperation` does not know or care what `operation` does. It just calls it with the value. This makes it reusable for any single-argument transformation.

**Returning a function (factory functions):**

```js
function createMultiplier(factor) {
  return n => n * factor    // returns a new function
}

const triple = createMultiplier(3)
const quadruple = createMultiplier(4)

console.log(triple(10))      // 30
console.log(quadruple(10))   // 40
```

`createMultiplier(3)` returns a function. That returned function, when called with any number, multiplies it by 3. The `factor` value (3 or 4) is remembered by the returned function even after `createMultiplier` has finished running. This is called a **closure**, and it is the subject of the next article.

**Why higher-order functions matter:**

The most commonly used array methods -- `map`, `filter`, and `reduce` -- are higher-order functions. They take a function as an argument and apply it to each element of an array. When we cover arrays in depth, understanding higher-order functions is what makes those methods readable:

```js
// Preview of what's coming -- map takes a function and applies it to every element
let prices = [100, 200, 300]
let withTax = prices.map(price => price * 1.18)
console.log(withTax)    // [118, 236, 354]
```

The arrow function `price => price * 1.18` is passed to `map`, which calls it once for each price. You are telling `map` what to do with each element -- `map` handles the iteration.

### Let's Define Things Now...

> **Function:** A named, reusable block of code that performs a specific task, optionally accepts input (parameters), and optionally returns an output.

> **Declaration vs Call:** Declaring a function writes its logic once. Calling it runs that logic. A function can be called any number of times after a single declaration.

> **Parameter:** A named placeholder in the function definition. It receives the value passed during the call.

> **Argument:** The actual value passed to the function when it is called. Arguments fill the parameter placeholders.

> **Hoisting (Functions):** Function declarations are fully hoisted -- the engine processes them before code runs, so they can be called before their definition in the file. Function expressions and arrow functions are not hoisted in this way.

> **Arrow Function:** A concise function syntax using `=>`. Supports implicit return for single-expression bodies. Does not have its own `this` binding.

> **Default Parameter:** A fallback value for a parameter that is used when no argument is passed. Defined with `=` in the parameter list.

> **Rest Operator (`...`):** Collects all remaining arguments into an array. Must be the last parameter.

> **Higher-Order Function:** A function that accepts another function as an argument or returns a function. The basis for `map`, `filter`, and `reduce`.

### Activity: Function Practice

1. Write these three functions and call each with at least two different sets of arguments:

```js
// Calculates the area of a rectangle
function rectangleArea(width, height) { }

// Greets a user with a default name if none is provided
function greetUser(name = "Guest") { }

// Takes any number of scores and returns their average
function average(...scores) { }
```

2. Rewrite each as an arrow function:

```js
// Check if a number is even
function isEven(n) { return n % 2 === 0 }

// Capitalize the first letter of a string
function capitalize(str) { return str[0].toUpperCase() + str.slice(1) }
```

3. Implement the following higher-order functions:

```js
// Returns a function that multiplies its argument by n
function createMultiplier(n) { }

// Returns a version of fn that only runs on the first call, returning undefined after
function once(fn) { }

// Returns a function that passes its input through fn1, then fn2, then fn3 in sequence
function pipe(fn1, fn2, fn3) { }
```

Test `pipe` with:
```js
const processInput = pipe(
  str => str.trim(),
  str => str.toLowerCase(),
  str => str.replace(" ", "-")
)
console.log(processInput("  Hello World  "))    // "hello-world"
```

4. Add the following to `chatbot-logic.js`:

```js
function createResponder(botName) {
  return function(userMessage) {
    let category = classifyMessage(userMessage)
    let response = getBotResponse(category)
    return buildBotMessage(botName, response)
  }
}

const chatAIResponder = createResponder("ChatAI")

console.log(chatAIResponder("Hello there!"))
console.log(chatAIResponder("How do I reset my password?"))
console.log(chatAIResponder("help"))
```

## What's Coming Next...

This was the basics of functions, focused primarily on the different ways to declare them and how calling them works. We touched briefly on closures when a returned function remembered the `factor` value from its outer scope.

That behaviour is not a side effect. It is one of the most important features of JavaScript functions. In the next article we will dig into scope properly: what it means for a variable to be local to a function, how inner functions can see outer variables but not vice versa, and how closures let functions carry their environment with them.
