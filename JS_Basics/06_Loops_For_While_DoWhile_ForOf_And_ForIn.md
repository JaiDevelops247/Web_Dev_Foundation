## Introduction

At the end of the last article, we mentioned that most real tasks involve doing something more than once. Let us make that concrete.

Imagine a chatbot that stores a conversation history as a list of 50 messages. You want to display all 50 on the screen. You could write `console.log(messages[0])`, `console.log(messages[1])`, all the way to `console.log(messages[49])`. That is 50 lines of nearly identical code. If the conversation grows to 100 messages, you need to write 50 more lines. If it shrinks to 20, you have 30 lines doing nothing.

Or imagine an e-commerce site sending a promotional email to 10,000 registered users. You are not going to write 10,000 `sendEmail()` calls.

The solution to repeating an operation without repeating your code is a **loop**. A loop is a construct that runs a block of code repeatedly, either for a set number of times or until a specified condition is no longer met.

JavaScript gives us several types of loops. The right one to use depends on what you know before the loop starts:

- Do you know exactly how many times to repeat? Use a **for** loop.
- Do you not know how many times, but you have a condition to check before each run? Use a **while** loop.
- Same as while, but you need the code to run at least once before checking? Use a **do-while** loop.
- Do you have an existing collection (array, string) to step through value by value? Use a **for...of** loop.
- Do you need to step through all the keys of an object? Use a **for...in** loop.

The goal of this article is to understand each type well enough to choose the right one for any situation. Let us go through them.

### The for Loop

The `for` loop is the most commonly used loop in JavaScript because of its compact, familiar syntax. It is the right tool whenever you know in advance how many times the code needs to run.

**One-liner definition:** A `for` loop repeats a block of code a specific number of times, managing its own counter from start to finish.

**Syntax:**

```js
for (initialization; condition; increment) {
  // code to repeat
}
```

The parentheses hold three parts separated by semicolons:

**Initialization:** runs once before the loop starts. This is where you declare and set the starting value of the counter variable. By convention, this variable is named `i` (short for "index" or "iterator"), though you can name it anything.

**Condition:** checked before every iteration. If it is true, the block runs. If it is false, the loop ends.

**Increment:** runs after every iteration. This is where the counter is updated, typically by adding 1 with `i++` or subtracting 1 with `i--`.

```js
for (let i = 0; i < 5; i++) {
  console.log("Iteration number:", i)
}
// Iteration number: 0
// Iteration number: 1
// Iteration number: 2
// Iteration number: 3
// Iteration number: 4
```

Step by step on the first run: `i` is set to `0`. The condition `i < 5` is checked -- `0 < 5` is true, so the block runs. After the block, `i++` makes `i` equal to `1`. The condition is checked again -- `1 < 5` is true, so the block runs again. This continues until `i` is `5`, at which point `5 < 5` is false and the loop ends.

**The iterator variable does not have to be named `i`:**

```js
for (let count = 1; count <= 3; count++) {
  console.log("Sending notification:", count)
}
```

**`i` can also be declared outside the loop** if you need to use its final value after the loop ends:

```js
let i
for (i = 0; i < 5; i++) {
  console.log(i)
}
console.log("Loop ended at i =", i)    // 5 -- accessible because declared outside
```

**Looping backward:**

```js
for (let i = 5; i >= 1; i--) {
  console.log("Countdown:", i)
}
// Countdown: 5, 4, 3, 2, 1
```

**Common uses in web development:**

```js
// Render a fixed number of skeleton loading cards while data is fetching
for (let i = 0; i < 6; i++) {
  console.log("Render skeleton card", i + 1)
}

// Generate a list of page numbers for pagination
let totalPages = 8
for (let page = 1; page <= totalPages; page++) {
  console.log("Page button:", page)
}
```

#### Nested for Loops

A `for` loop can be placed inside another `for` loop. The inner loop runs its full cycle for every single iteration of the outer loop.

```js
for (let row = 1; row <= 3; row++) {
  for (let col = 1; col <= 3; col++) {
    console.log(`Row ${row}, Column ${col}`)
  }
}
```

This produces 9 lines -- every combination of row (1-3) and column (1-3). Nested loops are the natural structure for two-dimensional data: grids, tables, matrices, or any data organized in rows and columns.

```js
// Multiplication table
for (let i = 1; i <= 5; i++) {
  for (let j = 1; j <= 5; j++) {
    console.log(`${i} x ${j} = ${i * j}`)
  }
}
```

<!-- 
IMAGE: A diagram showing a nested loop execution trace. Outer loop labeled "i = 1, 2, 3" down the left side. For each value of i, three iterations of the inner loop (j = 1, 2, 3) are shown branching to the right. The 9 resulting combinations (1,1), (1,2), (1,3), (2,1)... are shown in a grid. Caption: "The inner loop runs its full cycle for each iteration of the outer loop. 3 outer iterations x 3 inner iterations = 9 total executions." Style: flat vector, grid-style visualization with outer loop as row labels and inner loop as column labels.
-->

### The while Loop

The `while` loop is the right choice when you do not know in advance how many times the code should run. It checks a condition before each iteration and keeps running as long as that condition is true.

**One-liner definition:** A `while` loop repeats a block of code as long as its condition remains truthy, checking the condition before every run.

**Syntax:**

```js
while (condition) {
  // code to repeat
  // something here must eventually make the condition false
}
```

```js
let attempts = 0
let maxAttempts = 3

while (attempts < maxAttempts) {
  console.log("Attempting connection... attempt", attempts + 1)
  attempts++
}
// Attempting connection... attempt 1
// Attempting connection... attempt 2
// Attempting connection... attempt 3
```

The counter (`attempts`) is declared and managed outside the loop, unlike the `for` loop where the counter lives inside the parentheses.

**A critical warning:** if nothing inside the loop ever makes the condition false, the loop runs forever. This is called an infinite loop and will freeze or crash your program.

```js
let count = 0

while (count < 5) {
  console.log(count)
  // forgot count++ -- this loop never ends
}
```

Always make sure something inside the `while` loop eventually changes the condition.

**Common uses in web development:**

```js
// Keep retrying a failed network request until it succeeds or hits the limit
let retries = 0
let connected = false

while (!connected && retries < 5) {
  console.log("Trying to connect...")
  retries++
  // connected would be set to true once the connection succeeds
}

// Process a queue of tasks until the queue is empty
let queue = ["task1", "task2", "task3"]

while (queue.length > 0) {
  let currentTask = queue.shift()    // removes and returns the first item
  console.log("Processing:", currentTask)
}
```

### The do-while Loop

The `do-while` loop is a variation of `while` with one key difference: the condition is checked **after** the block runs, not before. This means the block always runs at least once, regardless of the condition.

**One-liner definition:** A `do-while` loop runs its block first and then checks the condition. It guarantees at least one execution.

**Syntax:**

```js
do {
  // code to run
} while (condition)
```

```js
let userInput = ""

do {
  console.log("Please enter your name:")
  userInput = "Jai"    // simulating what the user types
} while (userInput === "")

console.log("Name received:", userInput)
```

The block runs once regardless. If the user immediately provides valid input, the condition is false and the loop ends after the first run. If the input was still empty, the condition would be true and the loop would run again.

**The key difference between while and do-while:**

```js
// while loop -- checks condition BEFORE running
let count = 10

while (count < 5) {
  console.log("This will never print")    // condition is false from the start
}

// do-while loop -- runs FIRST, checks condition AFTER
do {
  console.log("This prints once")         // runs once despite count being 10
} while (count < 5)
```

`while` skips entirely if the condition starts false. `do-while` always runs at least once.

**When is do-while the right choice?**

Use `do-while` when the first execution must happen unconditionally. The most natural example is input validation: you need to show the prompt at least once before you can check what the user entered. In real browser code, `do-while` is rare because the browser event model handles "run once, then wait for input" differently. But the concept remains important for understanding how loops reason about their entry point.

<!-- 
IMAGE: Two side-by-side flowcharts. Left (while): diamond "Condition true?" at the top. "Yes" goes down to "Run block" then back up to the diamond. "No" exits the loop. A note: "Condition checked first. Block may never run." Right (do-while): box "Run block" at the top. Arrow goes down to diamond "Condition true?" "Yes" goes back up to "Run block." "No" exits. A note: "Block runs first. Condition checked after. Always runs at least once." Caption: "The only difference is when the condition is checked -- before or after the first run." Style: flat vector, two vertical flowcharts side by side with clear entry and exit points labeled.
-->

### for...of: Iterating Over a Collection

The loops above all manage a counter variable to track position. But when you already have a collection of values (an array, a string) and just want to go through each one, that counter is unnecessary overhead.

`for...of` is designed for exactly this. It gives you each value in an iterable collection, one by one, without any index management.

**Syntax:**

```js
for (let item of collection) {
  // item is the current value
}
```

```js
let messages = ["Hello!", "How can I help?", "Goodbye!"]

for (let message of messages) {
  console.log(message)
}
// Hello!
// How can I help?
// Goodbye!
```

```js
let siteName = "ByteXL"

for (let character of siteName) {
  console.log(character)
}
// B, y, t, e, X, L  (one per line)
```

`for...of` works with arrays, strings, Sets, Maps, and any other iterable. We will use it extensively in the Arrays module. For now, understand that it exists as the clean, readable way to step through a collection when you do not need the index.

### for...in: Iterating Over Object Keys

`for...in` is specifically designed for objects. It gives you each key (property name) of an object, one by one.

**Syntax:**

```js
for (let key in object) {
  // key is the current property name as a string
}
```

```js
let botConfig = {
  name: "ChatAI",
  version: 2,
  language: "English"
}

for (let key in botConfig) {
  console.log(key, ":", botConfig[key])
}
// name : ChatAI
// version : 2
// language : English
```

**Important:** do not use `for...in` on arrays. It gives you the index positions as strings (`"0"`, `"1"`, `"2"`), not the values, and it can include inherited properties that are not part of your data. Use `for...of` for arrays instead.

We will use `for...in` extensively when we cover objects in depth. For now, the key point: `for...of` is for arrays and iterables, `for...in` is for object keys.

### break and continue

Both `break` and `continue` give you control over a loop's flow from inside the loop body.

#### break: Exit the Loop Early

`break` immediately ends the loop and moves execution to the code after it. Use it when you have found what you were looking for and further iterations are unnecessary.

```js
let messages = ["Hi", "Help me", "How are you?", "Goodbye"]

for (let message of messages) {
  if (message.includes("Help")) {
    console.log("Help request found:", message)
    break    // no need to check the rest
  }
  console.log("Scanned:", message)
}
// Scanned: Hi
// Help request found: Help me
// (loop stops here)
```

#### continue: Skip the Current Iteration

`continue` skips the rest of the current iteration and jumps to the next one. Use it when a particular item should be skipped but the loop should keep going.

```js
let scores = [85, 0, 92, 0, 67]

for (let score of scores) {
  if (score === 0) {
    continue    // skip missing scores
  }
  console.log("Processing score:", score)
}
// Processing score: 85
// Processing score: 92
// Processing score: 67
```

```js
// Skip even numbers, only process odd ones
for (let i = 1; i <= 10; i++) {
  if (i % 2 === 0) continue
  console.log(i)    // 1, 3, 5, 7, 9
}
```

### When to Use Which Loop

| Loop | Use when |
|---|---|
| `for` | You know exactly how many iterations are needed before the loop starts |
| `while` | You do not know the count; loop runs as long as a condition holds |
| `do-while` | Same as while, but the first run must happen unconditionally |
| `for...of` | You have an existing array or iterable and want each value |
| `for...in` | You have an object and want to visit each key |

**Quick gut check:**

If you are counting something (render N cards, generate N page buttons), reach for `for`.

If you are waiting for something to become true (keep retrying until connected, keep asking until valid input), reach for `while`.

If you are walking through a list you already have, reach for `for...of`.

If you are inspecting an object's properties one by one, reach for `for...in`.

### Let's Define Things Now...

> **Loop:** A construct that repeats a block of code either a set number of times or until a condition changes.

> **Iteration:** One single run of the loop's code block. A loop that runs 5 times has 5 iterations.

> **for loop:** A loop where the counter initialization, condition, and increment are all declared in one place. Best when the number of iterations is known in advance.

> **while loop:** A loop that checks its condition before each run. Runs as long as the condition is truthy. Best when the number of iterations is unknown.

> **do-while loop:** Like while, but checks the condition after each run. Always executes at least once.

> **for...of:** Iterates over values in an iterable (array, string, Set). No index variable needed.

> **for...in:** Iterates over the enumerable keys of an object. Not recommended for arrays.

> **break:** Exits the loop immediately, regardless of the condition.

> **continue:** Skips the rest of the current iteration and moves to the next one.

> **Infinite Loop:** A loop whose condition never becomes false. Freezes or crashes the program. Always ensure something inside a while loop eventually changes the condition.

### Activity: Loop Challenges

1. Use a `for` loop to print the multiplication table for 7 (7x1 through 7x10).

2. Use a nested `for` loop to print a 4x4 grid of asterisks (`*`). Each row should be on its own line.

3. Use a `while` loop to sum the digits of the number `9473` (add 9 + 4 + 7 + 3 using the modulo and division trick).

4. Use `for...of` with `break` to find the first message in an array that contains the word "error":

```js
let logs = [
  "User logged in",
  "Page loaded",
  "error: connection timed out",
  "Retry attempt 1",
  "error: server unreachable"
]
// Find and log only the first error message, then stop
```

5. Use `for...in` to iterate over the following settings object and print each key-value pair as a formatted line:

```js
let chatSettings = {
  theme: "dark",
  fontSize: 14,
  language: "English",
  soundEnabled: false
}
// Print: "theme: dark", "fontSize: 14", etc.
```

6. Use `continue` to skip all messages from the `"system"` sender and log only user and bot messages:

```js
let history = [
  { sender: "user", text: "Hello" },
  { sender: "system", text: "Session started" },
  { sender: "bot", text: "Hi there!" },
  { sender: "system", text: "Heartbeat ping" },
  { sender: "user", text: "How are you?" }
]
```

7. Add the following to `chatbot-logic.js` and run it:

```js
function processMessageHistory(messages) {
  let botCount = 0
  let userCount = 0

  for (let message of messages) {
    if (message.sender === "bot") {
      botCount++
    } else if (message.sender === "user") {
      userCount++
    }
  }

  return { botCount, userCount }
}

function searchMessages(messages, keyword) {
  for (let message of messages) {
    if (message.text.toLowerCase().includes(keyword.toLowerCase())) {
      return message
    }
  }
  return null
}

let history = [
  { sender: "user", text: "Hello there" },
  { sender: "bot", text: "Hi! How can I help?" },
  { sender: "user", text: "I need help with my order" },
  { sender: "bot", text: "Sure, what is your order number?" }
]

console.log(processMessageHistory(history))
console.log(searchMessages(history, "order"))
console.log(searchMessages(history, "payment"))
```

## What's Coming Next...

This wraps up the foundations of logic building. Variables hold data. Operators act on it. Conditionals make decisions. Loops repeat operations. These four ideas are the skeleton of every program you will ever write.

But there is much more we can do with data itself. We introduced strings in the Data Types article but barely scratched the surface. Strings have a rich set of built-in tools for searching, transforming, splitting, and formatting text. Given that so much of what happens on a web page involves text (user input, messages, labels, content), working with strings effectively is a core skill. The next module dives into strings and everything you can do with them.
