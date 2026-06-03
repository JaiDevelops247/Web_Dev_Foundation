## Introduction

In the previous article, we opened the browser console and typed things like this:

```js
console.log("Hello, JavaScript")
console.log(1 + 1)
```

The output appeared immediately. But the moment you typed the next line, the previous one was gone. Nothing was stored. The console ran the expression, printed the result, and moved on. If you needed that value again three lines later, you had to type it out again from scratch.

That works for one-off experiments in the console. It completely falls apart the moment you try to build anything real.

Think about a search bar on a webpage. When a user types a search query and hits Enter, the browser needs to:

1. Read what the user typed
2. Send that text to a server
3. Display the results when they come back

At step 1, the text exists. At step 3, the page needs that same text again to display something like "Showing results for: javascript tutorials". If nothing stored the text between step 1 and step 3, it is gone. The browser cannot do step 3.

Or think about the chatbot we are building. When the user types a message and clicks Send, the JavaScript needs to read that message, pass it to the bot logic, and then display both the user's message and the bot's reply on screen. That requires holding values in memory across multiple steps.

This is what variables are for. A variable is a named container that holds a value in the program's memory so you can read it and use it later.

### Declaration and Assignment

Using a variable in JavaScript happens in two steps. You can do them separately or together.

**Declaration** is the act of creating the variable and giving it a name. You are telling JavaScript: "reserve a spot in memory with this name."

**Assignment** is the act of putting a value into that container.

Separately, they look like this:

```js
let userName        // declaration: the container exists, currently empty
userName = "Jai"    // assignment: the container now holds the string "Jai"
```

Together on one line:

```js
let userName = "Jai"    // declaration and assignment at once
```

Both approaches are valid. In practice, the single-line form is standard unless you have a reason to declare first and assign later (for example, when the value depends on a condition that has not been evaluated yet).

Once a variable is declared and assigned, you can read its value simply by using its name:

```js
let userName = "Jai"
console.log(userName)    // Jai
```

You can also update the value after assignment:

```js
let score = 0
score = 10
score = 25
console.log(score)    // 25
```

### Three Ways to Declare Variables: let, const, and var

JavaScript has three keywords for declaring variables. Two of them are what you will use in all modern code. One is a relic of how JavaScript used to work, and you will mostly encounter it when reading older tutorials or legacy codebases.

#### var: The Old Way (Do Not Use in New Code)

`var` was the only way to declare variables in JavaScript from 1995 until 2015. It works, but it has two behaviours that cause confusing bugs: a scoping model that does not match how most developers think about code, and something called hoisting. Both are explained below.

```js
var botName = "ChatAI"
```

You will still see `var` in older JavaScript, Stack Overflow answers from before 2015, and legacy codebases. When you read it, understand it. When you write new code, never use it.

#### let: For Values That Change

`let` declares a variable whose value can be updated after the initial assignment. Use it whenever the value needs to change over the life of the program.

```js
let messageCount = 0
messageCount = messageCount + 1
messageCount = messageCount + 1
console.log(messageCount)    // 2
```

Good use cases for `let`:
- A counter that increments
- The current logged-in user's name (could change if the user signs out and a different one signs in)
- A loading state that starts as `false` and becomes `true` while a request is in flight

#### const: For Values That Do Not Change

`const` declares a variable whose binding cannot be reassigned after the initial assignment. If you try to reassign it, JavaScript throws an error.

```js
const maxMessageLength = 500
maxMessageLength = 1000    // TypeError: Assignment to constant variable.
```

Good use cases for `const`:
- Configuration values that are set once and never change
- A list of supported languages for the bot
- Any value that represents a fixed fact about the program

**The default rule:** use `const` for everything. Switch to `let` only when you specifically need to reassign the value. This makes it immediately clear to anyone reading the code which values are stable and which ones change.

```js
const botName = "ChatAI"           // will never change
const maxMessageLength = 500       // a fixed rule
let currentUserName = "Guest"      // will change when the user logs in
let messageCount = 0               // will change on every send
```

<!-- 
IMAGE: Two labeled boxes side by side. Left box labeled "const" with a padlock icon -- inside the box: botName = "ChatAI". An arrow tries to point into the box with a new value "BotX" and a red X blocks it. Caption: "const: the value is locked in. Reassignment throws an error." Right box labeled "let" with an open arrow icon -- inside the box: messageCount = 0, then an arrow changes it to 1, then 2. Caption: "let: the value can be updated freely." Style: flat vector, two distinct containers with clear visual metaphors for locked vs open.
-->

#### const with Objects and Arrays

One detail that surprises most developers when they first encounter it: `const` does not make the value itself immutable. It only prevents the variable from being reassigned to a different value entirely.

If the value is an object or an array, the contents of that object or array can still be modified:

```js
const supportedLanguages = ["English", "Spanish", "French"]
supportedLanguages.push("Hindi")    // this works fine
console.log(supportedLanguages)     // ["English", "Spanish", "French", "Hindi"]

supportedLanguages = ["Japanese"]   // TypeError: this does NOT work
```

`const` says: this variable will always point to this array. It does not say: the contents of the array will never change. The array is the same array throughout. Its contents are mutable.

The same applies to objects:

```js
const botConfig = { name: "ChatAI", version: 1 }
botConfig.version = 2    // works fine -- modifying a property
botConfig = {}           // TypeError -- reassigning the variable
```

This distinction matters when you are deciding whether to use `const` or `let` for an array or object. The answer is almost always `const`, because you rarely need to point the variable at a completely new object -- you just update properties or push to the array.

### Understanding Scope

Before we look at what makes `var` different from `let` and `const`, we need to understand scope.

**Scope** is the region of the program where a variable is accessible. A variable only exists within the scope it was declared in. Try to access it outside that scope and JavaScript throws a ReferenceError.

JavaScript has two kinds of scope that matter here: function scope and block scope.

**Block scope** means the variable lives inside the nearest pair of curly braces `{}` that contains its declaration. An `if` statement, a `for` loop, and a plain pair of curly braces are all blocks.

```js
if (true) {
  let message = "inside the block"
  console.log(message)    // "inside the block" -- works fine
}

console.log(message)    // ReferenceError: message is not defined
```

The variable `message` was declared inside the `if` block. It does not exist outside it. `let` and `const` both follow block scope.

**Function scope** means the variable lives inside the nearest function that contains its declaration. It is accessible anywhere within that function, regardless of which block inside the function it was declared in.

```js
function greetUser() {
  if (true) {
    var greeting = "Hello"    // declared with var inside a block
  }
  console.log(greeting)    // "Hello" -- var escapes the if block
}

greetUser()
console.log(greeting)    // ReferenceError -- var does not escape the function
```

This is where `var` differs from `let` and `const`. `var` ignores block boundaries. A `var` declared inside an `if` block leaks out into the containing function. A `let` or `const` declared in the same position stays inside the `if` block.

<!-- 
IMAGE: Two side-by-side diagrams showing a function containing an if block. Left diagram (var): the if block has "var greeting = 'Hello'" inside it. An arrow shows the variable leaking out of the if block into the function scope. The function boundary stops it from going further. Caption: "var ignores the if block. It is accessible throughout the entire function." Right diagram (let): the if block has "let greeting = 'Hello'" inside it. The variable is shown contained within the if block boundary with a wall stopping it from escaping. A red X appears outside the block. Caption: "let stays inside its block. Accessing it outside throws a ReferenceError." Style: flat vector, nested rectangles representing function and if block scopes, with arrows and walls showing variable visibility.
-->

Why does this matter? In practice, `var` leaking out of blocks causes bugs that are genuinely difficult to trace. When you have a `var` counter inside a `for` loop, the counter variable is accessible outside the loop. If another part of the function happens to use the same name, they collide silently. `let` prevents this entirely.

### Understanding Hoisting

Hoisting is a JavaScript behaviour where variable declarations are moved to the top of their scope before any code runs. This is done by the JavaScript engine during a preparation phase before execution.

With `var`, this means the declaration is moved to the top, but the assignment stays where it was written:

```js
console.log(score)    // undefined (not an error -- var was hoisted)
var score = 10
console.log(score)    // 10
```

The engine sees this as:

```js
var score             // declaration moved to top
console.log(score)    // undefined -- declared but not yet assigned
score = 10            // assignment stays here
console.log(score)    // 10
```

The variable exists from line 1 (because `var` is hoisted), but its value is `undefined` until the assignment line runs.

With `let` and `const`, hoisting also occurs, but the variables are not accessible before their declaration line. This window between the start of the scope and the declaration line is called the **temporal dead zone**:

```js
console.log(score)    // ReferenceError: Cannot access 'score' before initialization
let score = 10
```

This is actually the better behaviour. An error that tells you something is wrong is more useful than silently returning `undefined`.

**How much does hoisting matter right now?** Not much, practically speaking. You will almost never write code that accesses a variable before its declaration -- it is a natural code smell. But hoisting is one of those JavaScript concepts that comes up in job interviews and documentation constantly, so understanding what it is and why `var` behaves differently is important. Just know: use `let` and `const`, and hoisting edge cases become irrelevant.

### The Full Comparison

| | `var` | `let` | `const` |
|---|---|---|---|
| **Scope** | Function | Block | Block |
| **Can be reassigned** | Yes | Yes | No |
| **Can be redeclared in same scope** | Yes | No | No |
| **Hoisting behaviour** | Hoisted, initialised as `undefined` | Hoisted, temporal dead zone | Hoisted, temporal dead zone |
| **Use in modern code** | Never | When value changes | Default choice |

**When to use each:**

| Situation | Keyword |
|---|---|
| A value set once that never changes | `const` |
| A counter, flag, or any value that updates | `let` |
| An object or array whose contents may change | `const` |
| Reading old code or a legacy codebase | Understand `var`, but do not write it |

### Let's Define Things Now...

> **Variable:** A named container in memory that holds a value. Declared once, readable and (for `let`) updatable as many times as needed.

> **Declaration:** The act of creating a variable and reserving a name for it in memory. Done using `let`, `const`, or `var`.

> **Assignment:** The act of putting a value into a declared variable using the `=` operator.

> **Scope:** The region of the program where a variable is accessible. Variables declared with `let` and `const` are block-scoped. Variables declared with `var` are function-scoped.

> **Block:** Any pair of curly braces `{}` in JavaScript -- the body of an `if`, `for`, `while`, or a standalone block. `let` and `const` are confined to their nearest block.

> **Hoisting:** The JavaScript engine's behaviour of moving variable declarations to the top of their scope before code runs. `var` declarations are hoisted and initialised as `undefined`. `let` and `const` declarations are hoisted but not accessible until their line runs (temporal dead zone).

> **Temporal Dead Zone:** The period between the start of a block and the line where a `let` or `const` variable is declared. Accessing the variable in this zone throws a ReferenceError.

### Activity: Variable Audit

1. Predict the output of each of the following before running them. Note whether you expect a value, `undefined`, or an error:

```js
// Snippet 1
console.log(city)
var city = "Mumbai"
console.log(city)

// Snippet 2
console.log(country)
let country = "India"
console.log(country)

// Snippet 3
const maxRetries = 3
maxRetries = 5
console.log(maxRetries)

// Snippet 4
if (true) {
  var insideIf = "I am var"
}
console.log(insideIf)

// Snippet 5
if (true) {
  let insideLet = "I am let"
}
console.log(insideLet)
```

2. Rewrite every `var` declaration in the snippets above to the correct modern equivalent (`let` or `const`), based on whether the value changes.

3. Add the following lines to `chatbot-logic.js`. For each, choose `const` or `let` and write a one-line comment explaining why:

```js
// Bot configuration (fixed values)
___ botName = "ChatAI"
___ maxMessageLength = 500
___ supportedLanguages = ["English", "Spanish", "Hindi"]

// Session state (values that change during a conversation)
___ messageCount = 0
___ currentUserName = "Guest"
___ isLoading = false
```

Replace `___` with the correct keyword. Run the file with Node.js to confirm no errors.

## What's Coming Next...

We can now store values in variables using `const` and `let`. But what kinds of values can a variable actually hold? So far we have seen numbers and strings. JavaScript has a full set of data types, and each one behaves differently. The next article covers all of them: strings, numbers, booleans, null, undefined, and how JavaScript handles situations where the type of a value is not what you expect.
