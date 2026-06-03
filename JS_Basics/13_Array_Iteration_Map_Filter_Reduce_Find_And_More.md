## Introduction

When we introduced arrays, we said that they help us store and work with sets of similar data. We have seen the storage part: how to create arrays, add to them, remove from them, and access individual elements.

Now comes the working-with-them part.

Earlier, in the functions article, we introduced higher-order functions: functions that accept other functions as arguments. We said they are the foundation for the most important array tools in JavaScript. Now we will see exactly what that means in practice.

Consider the kinds of tasks real products perform on their data every day:

- Amazon needs to display only the products that are in stock. Out of 10,000 items in a category, show only the ones where `stock > 0`.
- Swiggy needs to convert a list of cart items into a total price. Each item has a price and a quantity, and the total is the sum of all of them combined.
- A job portal needs to extract just the company names from a list of full job listing objects to populate a dropdown filter.
- A streaming platform needs to check if any of the shows in a user's watchlist have a new episode today.
- A chatbot needs to find the first unanswered message in a support queue.

Every one of these tasks involves taking an existing array and doing something with all of it. Writing a `for` loop for each one works, but JavaScript provides a set of higher-order methods on arrays that describe exactly what you want to do -- without spelling out how to do it step by step.

These are the methods this article is about.

### map(): Transform Every Element

`map` creates a new array by running a function on every element of the original array and collecting the results.

**What it does:** Applies a transformation function to each element and returns a new array of the same length containing the transformed values.

**Parameters:** A callback function that receives `(currentElement, index, array)`. You typically only need the first.

**Returns:** A new array. The original is not modified.

**Syntax:**

```js
let result = array.map(function(element) {
  return transformedElement
})

// With arrow function (common shorthand)
let result = array.map(element => transformedElement)
```

**Examples:**

```js
// Extract just the names from an array of user objects
let users = [
  { name: "Jai", age: 24 },
  { name: "Riya", age: 22 },
  { name: "Arjun", age: 26 }
]

let names = users.map(user => user.name)
console.log(names)    // ["Jai", "Riya", "Arjun"]
```

```js
// Add 18% GST to every product price
let prices = [100, 250, 500, 1200]
let pricesWithTax = prices.map(price => price * 1.18)
console.log(pricesWithTax)    // [118, 295, 590, 1416]
```

```js
// Convert product slugs to display labels
let slugs = ["wireless-mouse", "mechanical-keyboard", "usb-hub"]
let labels = slugs.map(slug => slug.split("-").map(word => word[0].toUpperCase() + word.slice(1)).join(" "))
console.log(labels)    // ["Wireless Mouse", "Mechanical Keyboard", "Usb Hub"]
```

**Real-world use cases:**
- Extracting one field from each object to populate a dropdown list
- Applying tax, discount, or currency conversion to every price in a cart
- Converting raw API response data into a display-friendly format
- Building an array of JSX/HTML elements from an array of data

<!-- 
IMAGE: A diagram showing map() visually. Left side: an array of four circles labeled [1, 2, 3, 4]. A central box labeled "map(x => x * 2)" with an arrow passing each element through a transformation function. Right side: a new array of four circles labeled [2, 4, 6, 8]. Both arrays shown side by side with a label "Same length. New array. Original unchanged." Caption: "map transforms every element. The result is always a new array of the same length." Style: flat vector, two horizontal arrays connected by a function box with arrows flowing through it.
-->

### filter(): Keep Only What Matches

`filter` creates a new array containing only the elements that pass a test you define.

**What it does:** Runs a function on each element. If the function returns true for an element, that element is included in the result. If it returns false, the element is excluded.

**Parameters:** A callback function that receives `(currentElement, index, array)` and must return `true` or `false`.

**Returns:** A new array containing only the elements that passed. May be shorter than the original. The original is not modified.

**Syntax:**

```js
let result = array.filter(element => condition)
```

**Examples:**

```js
// Keep only in-stock products
let products = [
  { name: "Laptop", inStock: true, price: 45000 },
  { name: "Mouse", inStock: false, price: 999 },
  { name: "Keyboard", inStock: true, price: 2500 },
  { name: "Monitor", inStock: false, price: 18000 }
]

let available = products.filter(product => product.inStock)
console.log(available.map(p => p.name))    // ["Laptop", "Keyboard"]
```

```js
// Keep only messages from the user
let history = [
  { sender: "user", text: "Hello" },
  { sender: "bot", text: "Hi there!" },
  { sender: "user", text: "What can you do?" },
  { sender: "bot", text: "I can have conversations." }
]

let userMessages = history.filter(msg => msg.sender === "user")
console.log(userMessages.length)    // 2
```

```js
// Keep only passing scores (above 60)
let scores = [85, 42, 91, 58, 73, 39, 66]
let passing = scores.filter(score => score > 60)
console.log(passing)    // [85, 91, 73, 66]
```

**Real-world use cases:**
- Showing only in-stock products on a shopping page
- Filtering a support ticket list to show only unresolved tickets
- Removing empty strings from a list of user inputs
- Applying a price range filter to a product catalog

<!-- 
IMAGE: A diagram showing filter() visually. Left side: an array [1, 2, 3, 4, 5, 6]. A central funnel shape labeled "filter(x => x > 3)." Small elements 1, 2, 3 going into the funnel with a red X (filtered out). Elements 4, 5, 6 passing through with a green checkmark. Right side: new array [4, 5, 6]. Caption: "filter keeps elements that pass the test and discards the rest. The result may be shorter than the original." Style: flat vector, funnel diagram with elements entering from the left and only matching ones exiting to the right.
-->

### reduce(): Collapse to a Single Value

`reduce` is the most versatile array method. It takes all the elements of an array and combines them into a single output value. That output can be a number, a string, an object, or even another array.

**What it does:** Starts with an initial value (the accumulator) and runs a function on each element, updating the accumulator each time. Returns the final accumulated value.

**Parameters:**
- A callback function receiving `(accumulator, currentElement, index, array)`
- An initial value for the accumulator

**Returns:** A single value of any type.

**Syntax:**

```js
let result = array.reduce((accumulator, current) => {
  return updatedAccumulator
}, initialValue)
```

**Examples:**

```js
// Sum all prices in a cart
let cartItems = [
  { name: "Laptop", price: 45000 },
  { name: "Mouse", price: 999 },
  { name: "Keyboard", price: 2500 }
]

let total = cartItems.reduce((sum, item) => sum + item.price, 0)
console.log(total)    // 48499
```

Step through this manually:
- Start: `sum = 0`
- Item 1 (Laptop, 45000): `sum = 0 + 45000 = 45000`
- Item 2 (Mouse, 999): `sum = 45000 + 999 = 45999`
- Item 3 (Keyboard, 2500): `sum = 45999 + 2500 = 48499`
- Return: `48499`

```js
// Count how many messages each sender has sent
let messages = ["user", "bot", "user", "user", "bot", "user"]

let counts = messages.reduce((tally, sender) => {
  tally[sender] = (tally[sender] || 0) + 1
  return tally
}, {})

console.log(counts)    // { user: 4, bot: 2 }
```

```js
// Find the most expensive product
let items = [
  { name: "Pen", price: 20 },
  { name: "Notebook", price: 150 },
  { name: "Bag", price: 2000 },
  { name: "Eraser", price: 10 }
]

let mostExpensive = items.reduce((max, item) => {
  return item.price > max.price ? item : max
}, items[0])

console.log(mostExpensive.name)    // "Bag"
```

**Real-world use cases:**
- Computing a cart total
- Tallying votes, clicks, or message counts
- Grouping an array of items by a category property
- Finding the maximum, minimum, or average value in a dataset

<!-- 
IMAGE: A step-by-step diagram of reduce() summing [10, 20, 30]. Step 1: accumulator=0, current=10, result=10. Step 2: accumulator=10, current=20, result=30. Step 3: accumulator=30, current=30, result=60. Final output: 60. Each step shown as a box with the accumulator and current value flowing in and the updated accumulator flowing out. Caption: "reduce processes elements one at a time, carrying the running result forward. The final accumulator is returned." Style: flat vector, three sequential processing boxes connected by arrows, showing accumulator value evolving.
-->

### find() and findIndex(): Locate a Specific Element

These two methods search an array and return information about the first element that matches a condition.

**find():**

What it does: returns the first element for which the callback returns true.
Returns: the element itself, or `undefined` if nothing matches.

```js
let users = [
  { id: 1, name: "Jai", active: false },
  { id: 2, name: "Riya", active: true },
  { id: 3, name: "Arjun", active: true }
]

let firstActiveUser = users.find(user => user.active)
console.log(firstActiveUser)    // { id: 2, name: "Riya", active: true }

let notExist = users.find(user => user.id === 99)
console.log(notExist)           // undefined
```

**findIndex():**

What it does: returns the index of the first element for which the callback returns true.
Returns: the index number, or `-1` if nothing matches.

```js
let orderIds = [1042, 1089, 1103, 1140, 1155]
let targetIndex = orderIds.findIndex(id => id === 1103)
console.log(targetIndex)    // 2

let missing = orderIds.findIndex(id => id === 9999)
console.log(missing)        // -1
```

**The difference from filter:** `filter` returns all matching elements. `find` returns only the first and stops searching. For locating a specific item (a user by ID, a product by name), `find` is more appropriate and more efficient.

**Real-world use cases:**
- Finding a user in a list by their ID
- Locating the first unread notification
- Finding the index of an item to update or delete it using `splice`

### some() and every(): Test the Whole Collection

These two return a boolean based on how elements in the array relate to a condition.

**some():**

What it does: checks if at least one element satisfies the condition.
Returns: `true` if any element passes, `false` if none do.

```js
let inventory = [
  { name: "Laptop", stock: 0 },
  { name: "Mouse", stock: 15 },
  { name: "Keyboard", stock: 0 }
]

let anyInStock = inventory.some(item => item.stock > 0)
console.log(anyInStock)    // true -- Mouse is available
```

```js
// Check if any score is a fail
let classScores = [88, 72, 45, 91, 63]
let anyFailed = classScores.some(score => score < 60)
console.log(anyFailed)    // true
```

**every():**

What it does: checks if all elements satisfy the condition.
Returns: `true` only if every element passes, `false` if any one fails.

```js
let paymentDetails = [
  { field: "cardNumber", filled: true },
  { field: "expiry", filled: true },
  { field: "cvv", filled: false }
]

let formComplete = paymentDetails.every(field => field.filled)
console.log(formComplete)    // false -- CVV is not filled
```

```js
// Check if all products are in stock before confirming a bulk order
let bulkOrder = [
  { name: "Chair", stock: 50 },
  { name: "Desk", stock: 30 },
  { name: "Monitor", stock: 12 }
]

let canFulfil = bulkOrder.every(item => item.stock > 0)
console.log(canFulfil)    // true
```

**Real-world use cases:**
- `some`: check if any item in a cart is out of stock before checkout
- `some`: check if a user has any unread notifications to show a badge
- `every`: validate that all required form fields are filled before enabling the submit button
- `every`: confirm all items in an order are available before processing

### forEach(): Run Something for Each Element

`forEach` runs a function on each element of the array, purely for its side effects. It does not return anything useful.

**What it does:** Calls the provided function once per element.
**Returns:** `undefined` -- always.
**Key distinction:** Unlike `map`, `filter`, and `reduce`, `forEach` cannot be chained because it returns nothing.

```js
let notifications = ["Order shipped", "Payment received", "Account updated"]

notifications.forEach(notification => {
  console.log("Showing:", notification)
})
// Showing: Order shipped
// Showing: Payment received
// Showing: Account updated
```

```js
// Log each message in a conversation history
messageHistory.forEach((msg, index) => {
  console.log(`[${index + 1}] ${msg.sender}: ${msg.text}`)
})
```

Use `forEach` when you need to do something with each element but do not need a new array as output -- logging, sending a network request per item, updating a counter, rendering to the screen. For transformations that produce a new array, use `map` instead.

### Chaining Methods

Because `map` and `filter` each return a new array, you can chain them directly. The output of one becomes the input of the next.

```js
let products = [
  { name: "Laptop", price: 45000, inStock: true },
  { name: "Phone", price: 12000, inStock: false },
  { name: "Tablet", price: 25000, inStock: true },
  { name: "Watch", price: 8000, inStock: true },
  { name: "Earbuds", price: 3000, inStock: false }
]

// Get the names of all in-stock products priced above 10,000
let result = products
  .filter(p => p.inStock)                // keep only in-stock: 3 items
  .filter(p => p.price > 10000)          // keep only expensive: 2 items
  .map(p => p.name)                      // extract just names: ["Laptop", "Tablet"]

console.log(result)    // ["Laptop", "Tablet"]
```

```js
// Get all passing student names, sorted alphabetically
let students = [
  { name: "Zara", grade: 72 },
  { name: "Aman", grade: 45 },
  { name: "Priya", grade: 88 },
  { name: "Rohan", grade: 55 },
  { name: "Sneha", grade: 91 }
]

let passingNames = students
  .filter(s => s.grade >= 60)         // Zara, Priya, Sneha
  .map(s => s.name)                   // ["Zara", "Priya", "Sneha"]
  .sort()                             // ["Priya", "Sneha", "Zara"]

console.log(passingNames)
```

Chaining reads from left to right as a description of the data transformation: "take the students, keep only those who passed, extract their names, sort them." Each step has one responsibility. The result follows directly from reading the chain.

**Why this matters over for loops:**

```js
// Imperative style: describes HOW to do it
let result = []
for (let i = 0; i < products.length; i++) {
  if (products[i].inStock && products[i].price > 10000) {
    result.push(products[i].name)
  }
}

// Declarative style: describes WHAT you want
let result = products
  .filter(p => p.inStock && p.price > 10000)
  .map(p => p.name)
```

Both produce the same result. The declarative version says what the transformation is. The imperative version says how to loop, where to push, what conditions to check. For data transformations, the declarative approach is shorter, easier to read, and less prone to off-by-one bugs.

### Quick Reference

| Method | Returns | Mutates? | Use when |
|---|---|---|---|
| `map` | New array (same length) | No | You want to transform every element |
| `filter` | New array (shorter or equal) | No | You want to keep only matching elements |
| `reduce` | Any single value | No | You want to combine all elements into one result |
| `find` | First matching element or `undefined` | No | You want one specific item |
| `findIndex` | Index of first match or `-1` | No | You need the position of a specific item |
| `some` | Boolean | No | You want to know if any element passes |
| `every` | Boolean | No | You want to know if all elements pass |
| `forEach` | `undefined` | No | You want to run a side effect on each element |

### Let's Define Things Now...

> **map():** Transforms every element using a callback and returns a new array of the same length.

> **filter():** Returns a new array containing only elements for which the callback returns true.

> **reduce():** Accumulates all elements into a single value using a callback and an initial accumulator value.

> **find():** Returns the first element that satisfies the callback, or undefined.

> **findIndex():** Returns the index of the first matching element, or -1.

> **some():** Returns true if at least one element satisfies the callback.

> **every():** Returns true only if all elements satisfy the callback.

> **forEach():** Runs a callback for each element for side effects. Returns undefined. Cannot be chained.

> **Chaining:** Calling multiple array methods in sequence because each returns a new array. Reads as a left-to-right description of the transformation.

> **Declarative style:** Describing what the outcome should be rather than spelling out each step of how to get there. map-filter-reduce chains are declarative.

### Activity: Data Pipeline

```js
let students = [
  { name: "Aarav", grade: 82, subject: "Math" },
  { name: "Priya", grade: 54, subject: "Science" },
  { name: "Rohan", grade: 91, subject: "Math" },
  { name: "Sneha", grade: 67, subject: "English" },
  { name: "Kiran", grade: 43, subject: "Science" },
  { name: "Zara", grade: 78, subject: "English" },
  { name: "Dev", grade: 95, subject: "Math" },
  { name: "Meera", grade: 61, subject: "Science" }
]
```

Using the methods from this article, solve the following. Try to write each as a single chained expression where possible:

1. Get the names of all students who passed (grade above 60), sorted alphabetically.
2. Compute the class average grade using `reduce`.
3. Find the first student who failed (grade below 60) using `find`.
4. Check if any student scored 95 or above using `some`.
5. Check if all students have a `name` property using `every`.
6. Group the students by subject using `reduce` -- the result should be an object: `{ Math: [...], Science: [...], English: [...] }`.

Then add the following to `chatbot-logic.js`:

```js
function getMessagesByUser(history, sender) {
  return history.filter(msg => msg.sender === sender)
}

function getAllTexts(history) {
  return history.map(msg => msg.text)
}

function getMessageCount(history) {
  return history.reduce((counts, msg) => {
    counts[msg.sender] = (counts[msg.sender] || 0) + 1
    return counts
  }, {})
}

function hasUnread(history) {
  return history.some(msg => msg.unread === true)
}

// Test
let history = [
  { sender: "user", text: "Hello!", unread: false },
  { sender: "bot", text: "Hi there!", unread: false },
  { sender: "user", text: "Help me please", unread: true },
  { sender: "bot", text: "Of course!", unread: false }
]

console.log(getMessagesByUser(history, "user"))
console.log(getAllTexts(history))
console.log(getMessageCount(history))
console.log(hasUnread(history))
```

## What's Coming Next...

The syntax for these methods -- especially `reduce` -- can feel like a lot when you first encounter it. That is normal. These methods become intuitive quickly once you start using them in real data scenarios. The understanding follows the practice, not the other way around.

We have one more article on arrays before we move on: the utility methods. `sort`, `spread`, `flat`, `Array.from`, and destructuring. After that, we move into objects -- the data structure that represents structured, named data, and the foundation for almost everything in modern JavaScript.
