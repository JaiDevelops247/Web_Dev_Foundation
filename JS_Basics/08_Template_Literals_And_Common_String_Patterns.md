## Introduction

Open Swiggy or Zomato after placing an order. You will see something like:

> **Your order will be delivered in 23 minutes**

Open Amazon after a purchase:

> **Hello, Jai. Your order #OD-948271 has been shipped.**

Open a streaming platform:

> **Welcome back! You have 3 episodes left in your watchlist.**

Look at what these messages have in common. Part of each message is fixed text that every user sees. The rest changes based on who the user is, what they ordered, or what is happening in their account right now.

```
"Your order will be delivered in"  +  23  +  "minutes"
"Hello,"  +  Jai  +  ". Your order #"  +  OD-948271  +  "has been shipped."
"Welcome back! You have"  +  3  +  "episodes left in your watchlist."
```

The greyed-out parts are static. The highlighted parts come from the user's data. Composing strings like this from a mix of fixed text and live data is something every web application does constantly.

The old way of doing this was string concatenation using `+`:

```js
let name = "Jai"
let deliveryTime = 23

let message = "Hello, " + name + ". Your order will be delivered in " + deliveryTime + " minutes."
console.log(message)
// Hello, Jai. Your order will be delivered in 23 minutes.
```

This works but becomes hard to read as the message grows. Counting the quotes and plus signs, tracking which spaces are inside the quotes and which are outside -- every addition to the string is a potential source of a small, maddening bug.

Template literals are the modern solution. They let you write the message as natural text and drop the live values directly into it at the exact position they belong.

### Template Literals: Syntax and Expression Embedding

A template literal is a string written with backticks (`` ` ``). Instead of using `+` to join pieces, you embed values directly using `${ }`:

```js
let name = "Jai"
let deliveryTime = 23

let message = `Hello, ${name}. Your order will be delivered in ${deliveryTime} minutes.`
console.log(message)
// Hello, Jai. Your order will be delivered in 23 minutes.
```

The string reads exactly as it will appear. No plus signs, no quote switching, no space counting. What you write is what you get.

#### Embedding Any Expression

The `${ }` slot accepts any valid JavaScript expression, not just a variable name. The expression is evaluated and its result is converted to a string and placed into the message.

**Arithmetic:**

```js
let price = 850
let taxRate = 0.18

let receipt = `Subtotal: ₹${price}  |  Tax: ₹${price * taxRate}  |  Total: ₹${price + price * taxRate}`
console.log(receipt)
// Subtotal: ₹850  |  Tax: ₹153  |  Total: ₹1003
```

**Function calls:**

```js
function getGreeting(hour) {
  if (hour < 12) return "Good morning"
  if (hour < 17) return "Good afternoon"
  return "Good evening"
}

let hour = 14
let greeting = `${getGreeting(hour)}, Jai!`
console.log(greeting)    // Good afternoon, Jai!
```

**Ternary operator for conditional text:**

```js
let itemCount = 1
let label = `You have ${itemCount} ${itemCount === 1 ? "item" : "items"} in your cart.`
console.log(label)    // You have 1 item in your cart.
```

```js
let isOnline = false
let status = `Bot status: ${isOnline ? "Online" : "Offline"}`
console.log(status)    // Bot status: Offline
```

**Nested template literals:**

```js
let score = 88
let feedback = `Your score: ${score}. Grade: ${score >= 90 ? "A" : score >= 75 ? "B" : "C"}`
console.log(feedback)    // Your score: 88. Grade: B
```

<!-- 
IMAGE: A visual breakdown of a template literal string. The string `Hello, ${name}. You have ${count} messages.` is shown with annotations. The static text portions ("Hello, ", ". You have ", " messages.") are highlighted in one color with the label "Fixed text -- always the same." The ${name} and ${count} portions are highlighted in a different color with the label "Dynamic expressions -- evaluated at runtime." Below the string, arrows show name resolving to "Jai" and count resolving to 5, and the final composed string "Hello, Jai. You have 5 messages." Caption: "Template literals mix fixed text with dynamic expressions. ${ } is evaluated and inserted at that position." Style: flat vector, annotated string with color-coded sections and arrows showing value resolution.
-->

### Multi-Line Strings

Before template literals, creating a string that spanned multiple lines required the newline escape character `\n`:

```js
let oldStyle = "Dear Jai,\n\nThank you for your order.\nYour package is on its way.\n\nRegards,\nSupport Team"
console.log(oldStyle)
```

Output:
```
Dear Jai,

Thank you for your order.
Your package is on its way.

Regards,
Support Team
```

With template literals, you simply press Enter. The line break is preserved exactly as you typed it:

```js
let email = `Dear ${userName},

Thank you for your order.
Your package is on its way.

Regards,
Support Team`

console.log(email)
```

Same output, far more readable code. This is especially useful for generating HTML strings, SQL queries, formatted notifications, or any content where layout matters.

```js
let userName = "Jai"
let orderId = "OD-948271"

let notification = `
Order Confirmed
Order ID: ${orderId}
Estimated delivery: 2-3 business days
Thank you for shopping with us, ${userName}!
`

console.log(notification)
```

### Tagged Template Literals

Template literals have an advanced form called **tagged templates**. This is worth knowing exists even if you do not need to write one today -- you will encounter them when working with libraries.

A tagged template puts a function name directly before the backtick, with no parentheses. JavaScript calls that function and passes it the pieces of the template separately: the fixed text parts as an array, and the evaluated expressions as individual arguments.

```js
function highlight(strings, ...values) {
  let result = ""
  strings.forEach((str, i) => {
    result += str
    if (values[i] !== undefined) {
      result += `[${values[i]}]`    // wrap each dynamic value in brackets
    }
  })
  return result
}

let name = "Jai"
let role = "Admin"

let tagged = highlight`User ${name} logged in with role ${role}.`
console.log(tagged)
// User [Jai] logged in with role [Admin].
```

The `highlight` function received `["User ", " logged in with role ", "."]` as the strings array and `"Jai"` and `"Admin"` as the values. It chose to wrap each value in brackets.

Libraries use this technique for CSS-in-JS (styled-components), internationalization (i18n translation), and SQL query sanitization. The practical takeaway: when you see `css\`...\`` or `gql\`...\`` in a codebase, that is a tagged template, and the function before the backtick is doing something meaningful with the pieces.

### Common String Patterns

Template literals handle the composing of strings. Now let us look at the patterns that come up repeatedly when transforming and analyzing string data.

#### Building Dynamic Messages from Data

The most common pattern: take a data object and compose a readable message from its properties.

```js
let user = {
  name: "Riya",
  messageCount: 14,
  plan: "Pro"
}

let summary = `${user.name} is on the ${user.plan} plan and has sent ${user.messageCount} messages today.`
console.log(summary)
// Riya is on the Pro plan and has sent 14 messages today.
```

```js
function formatOrderStatus(order) {
  return `Order #${order.id} | ${order.items} item(s) | Status: ${order.status} | Total: ₹${order.total}`
}

let order = { id: "OD-001", items: 3, status: "Shipped", total: 1299 }
console.log(formatOrderStatus(order))
// Order #OD-001 | 3 item(s) | Status: Shipped | Total: ₹1299
```

#### Padding Numbers for Formatted Output

Numbers presented in a table or list look better when aligned to a fixed width. `padStart` fills in leading characters to reach a target length.

```js
let invoices = [1, 42, 387, 5024]

for (let id of invoices) {
  let formatted = String(id).padStart(5, "0")
  console.log(`Invoice #${formatted}`)
}
// Invoice #00001
// Invoice #00042
// Invoice #00387
// Invoice #05024
```

Note that `padStart` works on strings, so the number is converted first using `String(id)`.

```js
// Countdown timer display: always show two digits
let seconds = 7
let display = `Time remaining: 00:${String(seconds).padStart(2, "0")}`
console.log(display)    // Time remaining: 00:07
```

#### Converting Between Strings and Arrays: split and join

`split` converts a string into an array. `join` converts an array back into a string. Together they form a powerful two-step pattern for transforming string content.

```js
let csv = "Mumbai,Delhi,Bangalore,Chennai"
let cities = csv.split(",")
console.log(cities)    // ["Mumbai", "Delhi", "Bangalore", "Chennai"]

let rejoined = cities.join(" | ")
console.log(rejoined)    // Mumbai | Delhi | Bangalore | Chennai
```

**Use cases:**
- Parsing a comma-separated value from a form field
- Splitting a sentence into words for word-level analysis
- Converting a tag list into an array, filtering it, then converting back to a string

```js
// Clean a tag input: "  js ,  css,html  " -> "js, css, html"
let rawTags = "  js ,  css,html  "
let cleanTags = rawTags
  .split(",")
  .map(tag => tag.trim())
  .join(", ")
console.log(cleanTags)    // "js, css, html"
```

#### Reversing a String

JavaScript has no built-in `reverse()` method for strings. The standard pattern chains three steps: split into characters, reverse the array, join back.

```js
function reverseString(str) {
  return str.split("").reverse().join("")
}

console.log(reverseString("hello"))        // "olleh"
console.log(reverseString("JavaScript"))   // "tpircSavaJ"
console.log(reverseString("racecar"))      // "racecar"  -- palindrome!
```

Step by step:
1. `"hello".split("")` produces `["h", "e", "l", "l", "o"]`
2. `.reverse()` produces `["o", "l", "l", "e", "h"]`
3. `.join("")` produces `"olleh"`

This is a pattern worth memorising: **split, transform, join** is the standard approach for string transformations that are easier to do at the array level.

#### Counting Character Frequency

A common interview question and a practical text analysis pattern: given a string, how often does each character appear?

```js
function charFrequency(str) {
  let frequency = {}

  for (let char of str) {
    if (frequency[char]) {
      frequency[char]++
    } else {
      frequency[char] = 1
    }
  }

  return frequency
}

console.log(charFrequency("hello"))
// { h: 1, e: 1, l: 2, o: 1 }

console.log(charFrequency("mississippi"))
// { m: 1, i: 4, s: 4, p: 2 }
```

The pattern uses an object as a counter. For each character in the string:
- If the character already exists as a key, increment its count
- If it does not exist yet, initialize it to 1

This object-as-frequency-counter approach extends to any counting task: word frequency in a sentence, emoji count in a message, vote tallying.

<!-- 
IMAGE: Four code pattern cards arranged in a 2x2 grid. Card 1: "Split + Join" -- shows split(",") converting "a,b,c" to ["a","b","c"] then join(" | ") converting back to "a | b | c". Card 2: "Reverse" -- shows split("") -> reverse() -> join("") converting "hello" to "olleh". Card 3: "padStart for alignment" -- shows String(7).padStart(3, "0") producing "007". Card 4: "Char Frequency" -- shows a for...of loop building {h:1, e:1, l:2, o:1} from "hello". Caption: "Four string transformation patterns you will use repeatedly in real projects." Style: flat vector, four equal-sized cards on a dark background, each with a mini code snippet and an arrow showing input to output.
-->

### Let's Define Things Now...

> **Template Literal:** A string using backticks that supports expression embedding with `${ }` and multi-line content without escape characters.

> **Expression Interpolation:** Embedding a JavaScript expression inside `${ }` within a template literal. The expression is evaluated and its result is inserted into the string.

> **Tagged Template:** An advanced use of template literals where a function name precedes the backtick. The function receives the string parts and interpolated values separately and can transform the result.

> **split(delimiter):** Converts a string into an array by cutting at each occurrence of the delimiter. The inverse of `join`.

> **join(separator):** Converts an array into a string, placing the separator between each element. The inverse of `split`.

> **Char Frequency Pattern:** Using an object as a counter where keys are characters and values are occurrence counts. Built by iterating over the string with `for...of`.

### Activity: Pattern Practice

1. Use a template literal to build a formatted bot response that includes the user name, message count, and a greeting based on the hour:

```js
let user = { name: "Priya", messageCount: 7 }
let currentHour = 10    // simulate 10 AM

// Expected output something like:
// "Good morning, Priya! This is your 7th message today."
// Use a ternary inside the template literal for the greeting
```

2. Write and test `reverseString` using split-reverse-join. Verify with these inputs:
   - `"hello"` should give `"olleh"`
   - `"racecar"` should give `"racecar"` (palindrome)
   - `"JavaScript"` should give `"tpircSavaJ"`

3. Write `charFrequency(str)` and test it with `"banana"`. Confirm: `{ b: 1, a: 3, n: 2 }`.

4. Add the following to `chatbot-logic.js` and run it:

```js
function buildBotMessage(userName, response) {
  let timestamp = new Date().toLocaleTimeString()
  return `[${timestamp}] ChatAI to ${userName}: ${response}`
}

function repeatChar(char, n) {
  return char.padStart(n, char)
}

// Rewrite any string concatenation already in the file to use template literals

console.log(buildBotMessage("Jai", "How can I help you today?"))
console.log(repeatChar("=", 30))
```

## What's Coming Next...

We are done with strings. Between the previous article and this one, you now have everything you need to work with text effectively in JavaScript: how to create strings, their methods, and the patterns for transforming them. These tools will appear in almost every program you write.

The next module moves to functions. We have mentioned functions throughout these articles and used them briefly, but it is time to understand them properly: how to define them, how they handle scope, and the patterns that make them one of the most powerful tools in the language.
