## Introduction

By the end of the last article we established something important: real applications deal with collections of data, not just single values. An e-commerce platform does not store one product. It stores thousands. A food delivery app does not serve one restaurant. It serves hundreds in your city alone. A chatbot does not keep track of one message. It maintains the entire conversation history.

Storing each of these individually in separate variables would be unmanageable:

```js
let product1 = "Laptop"
let product2 = "Phone"
let product3 = "Tablet"
// ... and so on for thousands of products
```

You cannot loop over these. You cannot pass them all to a function easily. You cannot sort or filter them without individually referencing each one. What we need is a single container that holds an ordered collection of values. That container is an **array**.

### What Is an Array?

An array is an ordered collection of values stored under a single variable name. Each value in the array is called an **element**, and every element sits at a specific numbered position called its **index**.

Arrays in JavaScript can store any type of value: strings, numbers, booleans, objects, other arrays, or even a mix of different types in the same array.

```js
let cities = ["Mumbai", "Delhi", "Bangalore"]           // strings
let scores = [95, 87, 76, 100]                          // numbers
let flags = [true, false, true, true]                   // booleans
let mixed = ["Jai", 24, true, null]                     // mixed types
```

Unlike some other languages, JavaScript does not enforce that all elements in an array be the same type. An array of mixed types is perfectly valid, though in practice you will mostly work with arrays of uniform data.

### Creating an Array

The standard way to create an array is with **literal syntax**: a pair of square brackets containing the elements, separated by commas.

```js
let fruits = ["apple", "banana", "mango"]

let emptyArray = []    // an empty array with no elements yet
```

You can also create an array and add elements to it later:

```js
let wishlist = []
// items will be added as the user interacts with the app
```

### How Arrays Are Stored in Memory

Primitives (numbers, strings, booleans) are stored directly at the variable's memory location. When you copy a primitive, you get an independent copy.

Arrays are different. An array is a **reference type**. The variable does not store the array directly -- it stores a reference (a memory address) pointing to where the array actually lives in memory. When you assign an array to another variable, both variables point to the same array in memory.

```js
let original = [1, 2, 3]
let copy = original        // both point to the same array

copy.push(4)

console.log(original)    // [1, 2, 3, 4]  -- changed!
console.log(copy)        // [1, 2, 3, 4]  -- same array
```

Modifying `copy` also modified `original` because they are both looking at the same place in memory. This is a common source of bugs. We will cover how to make a true independent copy of an array in the next article.

<!-- 
IMAGE: Two-part diagram. Left side (Primitives): two boxes labeled "a" and "b" each containing their own value "5" and "5". An arrow from "b = a" shows that b gets an independent copy. Modifying b to 10 leaves a as 5. Right side (Arrays): two boxes labeled "original" and "copy" both containing an arrow pointing to the same array [1,2,3] in a separate "Memory" area. Modifying through either variable changes the same underlying array. Caption: "Primitives store values directly. Arrays store a reference. Copying a reference does not copy the data." Style: flat vector, two-column diagram with colored boxes and arrows showing the reference distinction.
-->

#### const with Arrays

Because of the reference model, `const` and arrays work differently from what you might expect. `const` prevents the variable from being reassigned to a different array. It does not prevent the contents of the array from changing.

```js
const cart = ["Laptop", "Mouse"]

cart.push("Keyboard")    // works fine -- modifying the contents
console.log(cart)        // ["Laptop", "Mouse", "Keyboard"]

cart = ["Phone"]         // TypeError -- cannot reassign the variable
```

`const` is still the right keyword to use for arrays. You rarely need to point the variable at a completely new array. You just add to, remove from, or modify the existing one.

### Accessing Elements

Every element in an array sits at a numbered position called its **index**. JavaScript uses **zero-based indexing**, which means the first element is at index `0`, the second at index `1`, and so on.

```js
let cities = ["Mumbai", "Delhi", "Bangalore", "Chennai"]

console.log(cities[0])    // "Mumbai"   -- first element
console.log(cities[1])    // "Delhi"    -- second element
console.log(cities[3])    // "Chennai"  -- fourth element
console.log(cities[5])    // undefined  -- index out of range
```

To access the last element without knowing the array's length:

```js
console.log(cities[cities.length - 1])    // "Chennai"
```

You can also write to a specific index:

```js
cities[1] = "Hyderabad"
console.log(cities)    // ["Mumbai", "Hyderabad", "Bangalore", "Chennai"]
```

<!-- 
IMAGE: A horizontal row of four boxes labeled with their contents: "Mumbai", "Delhi", "Bangalore", "Chennai". Below each box is its index: 0, 1, 2, 3. An arrow points to the first box with the label "cities[0]" and an arrow points to the last with "cities[3]" or "cities[cities.length - 1]". Above the row, a label reads "Index starts at 0, not 1." Caption: "Arrays are zero-indexed. The first element is always at position 0." Style: flat vector, four equal-width boxes in a horizontal row with labeled index positions below each.
-->

### The length Property

`length` gives you the total number of elements in the array.

```js
let products = ["Laptop", "Phone", "Tablet"]
console.log(products.length)    // 3

let empty = []
console.log(empty.length)       // 0
```

Note that `length` is always one more than the last valid index. If the array has 3 elements, valid indices are `0`, `1`, and `2`. `length` is `3`.

```js
// Safe loop using length
let items = ["a", "b", "c", "d"]
for (let i = 0; i < items.length; i++) {
  console.log(items[i])
}
```

### Checking if Something Is an Array: Array.isArray()

A quick check you will need often: is this value actually an array?

`typeof` is not useful here because it returns `"object"` for arrays (arrays are objects in JavaScript):

```js
let scores = [1, 2, 3]
console.log(typeof scores)    // "object" -- not helpful
```

`Array.isArray()` gives you a reliable boolean answer:

```js
console.log(Array.isArray([1, 2, 3]))    // true
console.log(Array.isArray("hello"))      // false
console.log(Array.isArray(42))           // false
console.log(Array.isArray({}))           // false
```

Use `Array.isArray()` at the start of functions that expect an array to validate the input before working with it.

### Array Methods: Modifying the Collection

Arrays come with built-in methods for adding, removing, and rearranging elements. Before going through them, there is one distinction worth understanding clearly: some methods **mutate** the original array, while others **return a new array** and leave the original untouched.

This matters because if you call a mutating method and forget to account for the change, you have silently altered your data.

#### Mutating Methods: These Change the Original

**push(item) -- Add to the End**

What it does: adds one or more elements to the end of the array.
Parameters: the item(s) to add.
Returns: the new length of the array.

```js
let cart = ["Laptop", "Mouse"]
let newLength = cart.push("Keyboard")

console.log(cart)        // ["Laptop", "Mouse", "Keyboard"]
console.log(newLength)   // 3
```

Use case: adding a new message to the conversation history, adding a product to the cart, appending a log entry.

**pop() -- Remove from the End**

What it does: removes the last element from the array.
Parameters: none.
Returns: the removed element.

```js
let notifications = ["Order placed", "Payment confirmed", "Shipped"]
let removed = notifications.pop()

console.log(removed)         // "Shipped"
console.log(notifications)   // ["Order placed", "Payment confirmed"]
```

Use case: removing the most recent item from a stack, popping the last message off a queue.

**unshift(item) -- Add to the Beginning**

What it does: adds one or more elements to the start of the array, shifting all existing elements to higher indices.
Parameters: the item(s) to add.
Returns: the new length of the array.

```js
let messages = ["Hello!", "How can I help?"]
messages.unshift("System: Session started")

console.log(messages)
// ["System: Session started", "Hello!", "How can I help?"]
```

Use case: prepending a new announcement to the top of a feed, inserting a priority item at the front of a queue.

**shift() -- Remove from the Beginning**

What it does: removes the first element from the array, shifting all other elements down by one index.
Parameters: none.
Returns: the removed element.

```js
let queue = ["User A", "User B", "User C"]
let nextUser = queue.shift()

console.log(nextUser)    // "User A"
console.log(queue)       // ["User B", "User C"]
```

Use case: processing items in a first-in-first-out queue, removing the oldest notification once it has been shown.

**splice(start, deleteCount, ...items) -- Remove or Insert Anywhere**

What it does: removes elements from a specific position, optionally inserting new elements in their place.
Parameters:
- `start`: the index to begin at
- `deleteCount`: how many elements to remove (pass `0` to insert without removing)
- `...items`: optional elements to insert at the start position

Returns: an array of the removed elements.

```js
let playlist = ["Song A", "Song B", "Song C", "Song D"]

// Remove 1 element at index 1
let removed = playlist.splice(1, 1)
console.log(removed)    // ["Song B"]
console.log(playlist)   // ["Song A", "Song C", "Song D"]

// Insert without removing (deleteCount = 0)
playlist.splice(1, 0, "Song X", "Song Y")
console.log(playlist)   // ["Song A", "Song X", "Song Y", "Song C", "Song D"]

// Replace: remove 1 and insert 1
playlist.splice(2, 1, "Song Z")
console.log(playlist)   // ["Song A", "Song X", "Song Z", "Song C", "Song D"]
```

Use case: removing a specific item from a cart by its index, inserting a priority message at a specific position in a feed, replacing a placeholder with real content.

<!-- 
IMAGE: A visual reference card showing the four mutating methods with before/after array diagrams. Push: ["A","B"] -> push("C") -> ["A","B","C"] with arrow at the end. Pop: ["A","B","C"] -> pop() -> ["A","B"] with item "C" coming off the end. Unshift: ["A","B"] -> unshift("Z") -> ["Z","A","B"] with arrow at the start. Shift: ["A","B","C"] -> shift() -> ["B","C"] with item "A" coming off the front. Each method shown with the same consistent arrow style showing direction of change. Caption: "push and pop operate on the end. unshift and shift operate on the beginning." Style: flat vector, four mini before/after array diagrams in a 2x2 grid with directional arrows and method labels.
-->

#### Non-Mutating Methods: These Return a New Array

**slice(start, end) -- Extract a Portion**

What it does: extracts a portion of the array without modifying the original.
Parameters: `start` index (included), `end` index (not included). Both optional. Supports negative indices.
Returns: a new array containing the extracted elements.

```js
let messages = ["msg1", "msg2", "msg3", "msg4", "msg5"]

let recent = messages.slice(2)        // from index 2 to end
console.log(recent)                   // ["msg3", "msg4", "msg5"]
console.log(messages)                 // unchanged -- ["msg1", "msg2", "msg3", "msg4", "msg5"]

let middle = messages.slice(1, 4)     // index 1 up to (not including) 4
console.log(middle)                   // ["msg2", "msg3", "msg4"]

let lastThree = messages.slice(-3)    // last 3 elements
console.log(lastThree)               // ["msg3", "msg4", "msg5"]
```

Use case: getting the last N messages to display in the chatbot, extracting a page of results for pagination, creating a preview of the first few items without affecting the main list.

### Mutating vs Non-Mutating: A Quick Summary

| Method | Mutates original? | Returns |
|---|---|---|
| `push()` | Yes | New length |
| `pop()` | Yes | Removed element |
| `unshift()` | Yes | New length |
| `shift()` | Yes | Removed element |
| `splice()` | Yes | Array of removed elements |
| `slice()` | No | New array with extracted elements |

The rule to remember: if the method ends with the array looking different from how it started, it mutated. If the original is untouched and you have a new array to work with, it did not mutate.

### Two-Dimensional Arrays

An array can hold any type of value -- including other arrays. An array of arrays is called a **two-dimensional array** (2D array), and it is the natural way to represent grid or table data.

```js
let grid = [
  [1, 2, 3],    // row 0
  [4, 5, 6],    // row 1
  [7, 8, 9]     // row 2
]
```

To access an element, you use two indices: the first selects the row (the outer array), the second selects the column (the inner array):

```js
console.log(grid[0][0])    // 1   -- row 0, column 0
console.log(grid[1][2])    // 6   -- row 1, column 2
console.log(grid[2][1])    // 8   -- row 2, column 1
```

Nested loops are the natural way to process 2D arrays:

```js
for (let row of grid) {
  for (let cell of row) {
    process.stdout.write(cell + " ")    // print without newline
  }
  console.log()    // newline after each row
}
// 1 2 3
// 4 5 6
// 7 8 9
```

**Common uses of 2D arrays in web development:**

- A seating plan (rows of seats)
- A spreadsheet or data table (rows of records, each with multiple columns)
- A game board (chess, tic-tac-toe, sudoku)
- A weekly schedule (rows for time slots, columns for days)
- Pixel art or image data (rows of pixels, each with color values)

```js
// A simple schedule as a 2D array
let schedule = [
  ["Monday",    "Math",    "9:00 AM"],
  ["Tuesday",   "Science", "10:00 AM"],
  ["Wednesday", "English", "9:30 AM"]
]

for (let slot of schedule) {
  console.log(`${slot[0]}: ${slot[1]} at ${slot[2]}`)
}
// Monday: Math at 9:00 AM
// Tuesday: Science at 10:00 AM
// Wednesday: English at 9:30 AM
```

### Let's Define Things Now...

> **Array:** An ordered collection of values stored under a single variable name. Elements are accessed by their index position, starting from 0.

> **Index:** The numbered position of an element in an array. Zero-based: the first element is at index 0.

> **length:** A property that returns the total number of elements in an array. Always one more than the last valid index.

> **Array.isArray():** A method that returns `true` if a value is an array and `false` otherwise. More reliable than `typeof` for checking arrays.

> **Mutating Method:** An array method that modifies the original array directly (push, pop, unshift, shift, splice).

> **Non-Mutating Method:** An array method that returns a new array and leaves the original unchanged (slice).

> **splice():** A mutating method for removing, inserting, or replacing elements at a specific position. The most flexible basic array method.

> **slice():** A non-mutating method that extracts a portion of an array. Supports negative indices for counting from the end.

> **Two-Dimensional Array:** An array whose elements are themselves arrays. Used to represent grid, table, or matrix data. Accessed with two indices: `array[row][column]`.

> **Reference Type:** A value that stores a memory address rather than the value directly. Arrays are reference types. Assigning an array to another variable copies the reference, not the data.

### Activity: Array Basics Lab

1. Build a playlist manager using only the methods from this article. Start with an empty array and perform each of the following operations, logging the playlist after each:

```js
let playlist = []

// Add "Shape of You" to the end
// Add "Blinding Lights" to the end
// Add "Levitating" to the beginning
// Remove the last song
// Remove the first song
// Insert "Peaches" at index 1
// Remove the song at index 0 using splice
// Log the final playlist
```

2. Use `slice` to get the last 2 songs from a playlist without modifying it. Confirm the original is unchanged.

3. Write a function `insertAt(arr, index, item)` that inserts `item` at `index` without removing anything, using `splice`. Confirm it works for the beginning, middle, and end of an array.

4. Build a 3x3 tic-tac-toe board as a 2D array filled with `null` values. Write a function `makeMove(board, row, col, player)` that sets `board[row][col]` to `"X"` or `"O"`. Make three moves and log the board after each.

5. Add the following to `chatbot-logic.js`:

```js
function addMessage(history, sender, text) {
  let timestamp = new Date().toLocaleTimeString()
  history.push({ sender, text, timestamp })
}

function removeLastMessage(history) {
  return history.pop()
}

function getLastN(history, n) {
  return history.slice(-n)
}

let messageHistory = []

addMessage(messageHistory, "user", "Hello!")
addMessage(messageHistory, "bot", "Hi! How can I help?")
addMessage(messageHistory, "user", "What can you do?")
addMessage(messageHistory, "bot", "I can answer questions and have conversations.")

console.log("Full history:", messageHistory.length, "messages")
console.log("Last 2:", getLastN(messageHistory, 2))

let removed = removeLastMessage(messageHistory)
console.log("Removed:", removed.text)
console.log("Remaining:", messageHistory.length, "messages")
```

## What's Coming Next...

That was the foundation of arrays: how to create them, how to access their elements, and the core methods for adding and removing items. These six methods (push, pop, unshift, shift, splice, slice) are enough to build any basic data structure built on a list.

But arrays have a second, more powerful layer: the higher-order methods that let you transform, search, and summarise collections of data without writing loops manually. `map`, `filter`, `reduce`, `find`, `sort`, and more -- these are what separate junior-level array code from production-quality data handling. That is the next article.
