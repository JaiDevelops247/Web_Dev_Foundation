## Introduction

When we built the HTML module, one thing became clear very quickly: websites are fundamentally about text. Page headings, button labels, form inputs, error messages, product descriptions, chat messages -- the overwhelming majority of what a user sees and interacts with is textual content. Even things like prices and dates eventually get formatted into strings before they appear on the screen.

The data type that represents text in JavaScript is the string. We introduced it briefly in the Data Types article. Now it is time to get properly familiar with it, because working with strings effectively is one of the most frequently needed skills in web development.

We will cover how to create them, an important property they have that affects how they behave in memory, and the suite of built-in tools JavaScript gives you to work with them.

### Three Ways to Create a String

JavaScript lets you create strings using three different syntaxes. Each has its own advantages.

#### Single Quotes

```js
let firstName = 'Jai'
let city = 'Mumbai'
```

Clean and minimal. Use single quotes when the string contains no internal quotes or special logic. This is the most common style in many codebases.

**One limitation:** if your string contains an apostrophe, you need to escape it with a backslash, or switch to double quotes:

```js
let message = 'It\'s working'    // escaped apostrophe
let message = "It's working"     // easier -- use double quotes instead
```

#### Double Quotes

```js
let greeting = "Hello, welcome back!"
let errorMessage = "Something went wrong. Please try again."
```

Functionally identical to single quotes. The choice between single and double is a style convention -- most projects pick one and stick to it. The advantage of double quotes is that strings containing apostrophes do not need escaping:

```js
let tagline = "India's fastest chatbot"    // no escape needed
```

#### Template Literals (Backticks)

```js
let name = "Jai"
let welcomeMessage = `Hello, ${name}!`
console.log(welcomeMessage)    // Hello, Jai!
```

Template literals use backtick characters (\`) instead of quotes. They have two advantages that single and double quotes do not:

**1. Embedding expressions directly inside the string using `${ }`:**

```js
let price = 999
let tax = 0.18
let total = price + price * tax

let receipt = `Item: ₹${price}, Tax (18%): ₹${price * tax}, Total: ₹${total}`
console.log(receipt)
// Item: ₹999, Tax (18%): ₹179.82, Total: ₹1178.82
```

Anything inside `${ }` is evaluated as a JavaScript expression. You can embed variables, calculations, function calls, or even conditions.

**2. Multi-line strings without any special characters:**

```js
// With single/double quotes, multi-line requires \n
let oldStyle = "Line one\nLine two\nLine three"

// With template literals, just press Enter
let newStyle = `Line one
Line two
Line three`
```

The backtick string preserves the actual line breaks you type.

**Summary of when to use each:**

| Syntax | Use when |
|---|---|
| Single quotes `'...'` | Plain text with no apostrophes, consistent team style |
| Double quotes `"..."` | Plain text containing apostrophes |
| Template literals `` `...` `` | Embedding variables or expressions, multi-line strings |

### Strings Are Immutable

Once a string is created, it cannot be changed. Not a single character can be altered in place.

This is called **immutability**. In memory, a string is stored as a fixed sequence of characters. When you appear to "change" a string, what actually happens is that JavaScript creates a brand new string with the modified content and gives it back to you. The original is untouched.

```js
let username = "jai"
let uppercased = username.toUpperCase()

console.log(username)     // "jai"    -- original is unchanged
console.log(uppercased)   // "JAI"    -- a new string was created
```

You can reassign the variable to point to a new string:

```js
let username = "jai"
username = username.toUpperCase()    // variable now points to the new string "JAI"
console.log(username)    // "JAI"
```

But the original string `"jai"` was not modified. The variable was updated to point to a newly created string.

This distinction matters in practice because every string method returns a new string. If you call a method but do not store or use the result, nothing visible changes:

```js
let message = "  hello  "
message.trim()              // creates a new trimmed string, but it is discarded
console.log(message)        // "  hello  " -- still the original, with spaces
```

```js
let message = "  hello  "
message = message.trim()    // store the result back
console.log(message)        // "hello"
```

The pattern to remember: **string methods do not modify, they return.** Always capture or use the returned value.

### The length Property

Every string has a `length` property that tells you how many characters it contains.

```js
let word = "hello"
console.log(word.length)    // 5

let empty = ""
console.log(empty.length)   // 0
```

**Spaces count as characters:**

```js
let phrase = "hello world"
console.log(phrase.length)    // 11  -- "hello" (5) + space (1) + "world" (5)
```

**Practical uses:**

```js
// Enforce a character limit on a chat input
let userMessage = "This is my message"
let maxAllowed = 500
if (userMessage.length > maxAllowed) {
  console.log("Message too long")
}

// Show a live character counter
console.log(`${userMessage.length} / ${maxAllowed} characters used`)
```

### String Methods

Strings come with a built-in collection of operations that you can perform on them. Before going through the list, it is worth understanding what these operations actually are.

**A quick note on functions and methods:**

A function is a block of reusable code that you call by name to perform a task. We will dedicate a full article to functions soon. For now, just understand that certain operations are so commonly needed that JavaScript bundles them directly into the string type. These built-in, string-specific functions are called **methods**.

You call a method on a specific string using dot notation:

```
stringVariable.methodName(parameters)
```

Every method has three things worth knowing: what it does, what it takes as input (parameters), and what it gives back (return value). We will describe each method in these terms.

Let us go through the important ones.

### Character Access

#### charAt(index)

**What it does:** Returns the character at a specific position.
**Parameters:** `index` -- a number, the position to look at (starts from 0).
**Returns:** A single-character string. An empty string if the index is out of range.

```js
let name = "ChatAI"
console.log(name.charAt(0))    // "C"
console.log(name.charAt(3))    // "t"
console.log(name.charAt(10))   // "" -- index out of range
```

**Bracket notation** works the same way and is more concise:

```js
console.log(name[0])     // "C"
console.log(name[3])     // "t"
console.log(name[10])    // undefined -- slightly different from charAt
```

**Use case:** Reading the first character of a string to capitalize it, checking the last character to determine if a sentence ends with a question mark.

```js
let message = "How are you?"
let lastChar = message[message.length - 1]
console.log(lastChar)    // "?"
```

### Searching Within a String

#### indexOf(substring)

**What it does:** Searches for the first occurrence of a substring and returns the position where it starts.
**Parameters:** `substring` -- the text to search for. Optional second parameter: starting position for the search.
**Returns:** The index (number) of the first match. Returns `-1` if not found.

```js
let sentence = "The chatbot is a smart chatbot"
console.log(sentence.indexOf("chatbot"))    // 4
console.log(sentence.indexOf("chatbot", 5)) // 22 -- search starts from index 5
console.log(sentence.indexOf("robot"))      // -1 -- not found
```

The `-1` return value is important. It is the standard way to check whether something exists:

```js
if (message.indexOf("@") !== -1) {
  console.log("Looks like an email address")
}
```

#### lastIndexOf(substring)

**What it does:** Same as `indexOf` but finds the last occurrence instead of the first.
**Parameters:** Same as `indexOf`.
**Returns:** Index of the last match, or `-1`.

```js
let path = "/users/jai/documents/report.pdf"
let lastSlash = path.lastIndexOf("/")
console.log(lastSlash)                    // 18
console.log(path.slice(lastSlash + 1))    // "report.pdf"
```

**Use case:** Extracting the filename from a full file path.

#### includes(substring)

**What it does:** Checks whether a substring exists anywhere in the string.
**Parameters:** `substring` -- the text to look for. Optional second parameter: starting position.
**Returns:** `true` if found, `false` if not.

```js
let botReply = "I can help you with that!"
console.log(botReply.includes("help"))    // true
console.log(botReply.includes("sorry"))   // false
```

**Use case:** Checking if a user's message contains a keyword before deciding how to respond.

```js
if (userMessage.includes("?")) {
  return "question"
}
```

#### startsWith(substring) and endsWith(substring)

**What they do:** Check if the string begins or ends with a specific substring.
**Parameters:** `substring`. Optional second parameter for `startsWith`: the position to start checking from.
**Returns:** `true` or `false`.

```js
let url = "https://bytexl.app/dashboard"
console.log(url.startsWith("https"))       // true
console.log(url.startsWith("http://"))     // false
console.log(url.endsWith("dashboard"))     // true
console.log(url.endsWith(".pdf"))          // false
```

**Use cases:** Checking if a URL uses a secure protocol, checking if a file name ends with a specific extension, detecting if a message starts with a command keyword.

### Extracting Parts of a String

#### slice(start, end)

**What it does:** Extracts a portion of the string between two positions.
**Parameters:** `start` -- the index to begin at (included). `end` -- the index to stop at (not included). If `end` is omitted, extracts to the end of the string.
**Returns:** A new string containing the extracted portion.

```js
let text = "Hello, World!"
console.log(text.slice(7, 12))    // "World"
console.log(text.slice(7))        // "World!"
console.log(text.slice(0, 5))     // "Hello"
```

`slice` also supports **negative indices**, which count from the end of the string:

```js
let filename = "report_final.pdf"
console.log(filename.slice(-3))     // "pdf" -- last 3 characters
console.log(filename.slice(-7, -4)) // "fin" -- counting from the end
```

**Use case:** Extracting a file extension, cutting a long message for a preview.

```js
let preview = userMessage.slice(0, 100)    // first 100 characters only
```

#### substring(start, end)

**What it does:** Extracts a portion of the string, similar to `slice`.
**Parameters:** `start` and `end` indices.
**Returns:** A new string.

```js
let text = "JavaScript"
console.log(text.substring(4, 10))    // "Script"
```

**The key difference from `slice`:** `substring` does not support negative indices. If you pass a negative number, it treats it as `0`.

```js
console.log(text.slice(-6))        // "Script"    -- negative works
console.log(text.substring(-6))    // "JavaScript" -- negative becomes 0
```

**Which to use:** `slice` is more flexible and handles negative indices. Prefer `slice` in most cases. `substring` is useful when you know your indices will always be non-negative.

### Modifying String Content (Returning New Strings)

#### replace(search, replacement)

**What it does:** Replaces the first occurrence of a search value with a replacement.
**Parameters:** `search` -- the text to find (string). `replacement` -- the text to put in its place.
**Returns:** A new string with the first match replaced.

```js
let message = "Hello, user. Goodbye, user."
console.log(message.replace("user", "Jai"))
// "Hello, Jai. Goodbye, user."  -- only the first occurrence
```

#### replaceAll(search, replacement)

**What it does:** Replaces every occurrence of the search value.
**Parameters:** Same as `replace`.
**Returns:** A new string with all matches replaced.

```js
let message = "Hello, user. Goodbye, user."
console.log(message.replaceAll("user", "Jai"))
// "Hello, Jai. Goodbye, Jai."  -- all occurrences
```

**Use case:** Replacing a placeholder like `{{username}}` throughout a template string.

#### toUpperCase() and toLowerCase()

**What they do:** Convert all characters to uppercase or lowercase.
**Parameters:** None.
**Returns:** A new string in the converted case.

```js
let input = "JavaScript"
console.log(input.toUpperCase())    // "JAVASCRIPT"
console.log(input.toLowerCase())    // "javascript"
```

**Use cases:**

```js
// Case-insensitive comparison: both sides converted to the same case before comparing
if (userInput.toLowerCase() === "yes") {
  console.log("Confirmed")
}

// Display a username in all caps for a badge
let badge = username.toUpperCase()
```

### Cleaning Up String Content

#### trim(), trimStart(), trimEnd()

**What they do:** Remove whitespace (spaces, tabs, newlines) from the string.
- `trim()` removes from both ends
- `trimStart()` removes only from the beginning
- `trimEnd()` removes only from the end

**Parameters:** None.
**Returns:** A new string with whitespace removed.

```js
let rawInput = "   hello world   "
console.log(rawInput.trim())         // "hello world"
console.log(rawInput.trimStart())    // "hello world   "
console.log(rawInput.trimEnd())      // "   hello world"
```

**Use case:** Cleaning up form input before validating or storing it. Users accidentally type leading or trailing spaces constantly.

```js
let email = "  user@example.com   "
let cleanEmail = email.trim()
// validate cleanEmail, not the raw version with spaces
```

### Splitting and Padding

#### split(delimiter)

**What it does:** Splits a string into an array of substrings using a delimiter as the cut point.
**Parameters:** `delimiter` -- the character or string to split on. If empty string `""`, splits into individual characters.
**Returns:** An array of strings.

```js
let tags = "javascript,css,html"
let tagArray = tags.split(",")
console.log(tagArray)    // ["javascript", "css", "html"]

let sentence = "The quick brown fox"
let words = sentence.split(" ")
console.log(words)       // ["The", "quick", "brown", "fox"]

let chars = "hello".split("")
console.log(chars)       // ["h", "e", "l", "l", "o"]
```

**Use case:** Converting a comma-separated list from a form input into an array of values, splitting a message into individual words for analysis.

#### padStart(targetLength, padCharacter) and padEnd(targetLength, padCharacter)

**What they do:** Add padding characters to reach a target length.
- `padStart` adds padding at the beginning
- `padEnd` adds padding at the end

**Parameters:** `targetLength` -- the desired total length. `padCharacter` -- the character to pad with (default is a space).
**Returns:** A new padded string.

```js
let orderId = "42"
console.log(orderId.padStart(6, "0"))    // "000042"

let progress = "75%"
console.log(progress.padEnd(6, "."))     // "75%..."
```

**Use case:** Formatting order numbers, invoice IDs, or any identifier to a fixed width. Aligning columns in a formatted text output.

<!-- 
IMAGE: A reference card layout showing all the string methods covered, organized into four groups: Character Access (charAt, bracket notation), Searching (indexOf, lastIndexOf, includes, startsWith, endsWith), Extracting (slice, substring), Modifying (replace, replaceAll, toUpperCase, toLowerCase, trim, split, padStart, padEnd). Each method listed with its one-line description. Caption: "String methods reference. Remember: every method returns a new string. The original is never modified." Style: flat vector, four-column card layout on a dark background, each column a different accent color.
-->

### Chaining Methods

Because every string method returns a new string, you can call multiple methods one after the other on the same line. Each method operates on the result of the previous one.

```js
let rawInput = "   hello world   "
let result = rawInput.trim().toUpperCase().replace("WORLD", "JAVASCRIPT")
console.log(result)    // "HELLO JAVASCRIPT"
```

The chain reads left to right: first trim, then uppercase the trimmed result, then replace in the uppercased result.

```js
// Clean, validate, and format a message in one go
let userMessage = "   how are you?   "
let formatted = userMessage.trim().toLowerCase()
console.log(formatted)    // "how are you?"
```

### Let's Define Things Now...

> **String:** A sequence of characters representing text. Created with single quotes, double quotes, or template literals.

> **Template Literal:** A string created with backticks that supports embedded expressions using `${ }` and preserves line breaks.

> **Immutability:** A string cannot be changed after it is created. All string methods return a new string; they never modify the original.

> **length:** A property (not a method) that returns the number of characters in a string. Spaces count.

> **Method:** A built-in function attached to a data type that performs a common operation. Called using dot notation: `string.methodName(parameters)`.

> **indexOf:** Returns the position of the first match, or `-1` if not found. The `-1` result is the standard way to test for absence.

> **slice(start, end):** Extracts a portion of a string. Supports negative indices to count from the end.

> **split(delimiter):** Converts a string into an array by cutting at each occurrence of the delimiter.

### Activity: String Method Lab

Build the following utility functions using only the string methods covered in this article. No other tools needed.

```js
// 1. Count the number of words in a string
function wordCount(str) {
  // hint: split on spaces, check the resulting array's length
  // "hello world foo" -> 3
}

// 2. Convert a string to Title Case
function titleCase(str) {
  // "hello world" -> "Hello World"
  // hint: split into words, for each word slice the first character and uppercase it,
  // then join the words back
}

// 3. Truncate a string to a max length, adding "..." if it was cut
function truncate(str, max) {
  // truncate("Hello JavaScript World", 10) -> "Hello Java..."
}

// 4. Replace all occurrences of a search term
function findAndReplace(str, find, replacement) {
  // findAndReplace("one two one", "one", "1") -> "1 two 1"
}

// 5. Basic email format check using indexOf and includes
function basicEmailCheck(str) {
  // returns true if str contains "@" and has a "." after the "@"
}
```

Then add the following to `chatbot-logic.js`:

```js
function formatMessage(text) {
  if (typeof text !== "string") return ""
  let trimmed = text.trim()
  if (trimmed.length === 0) return ""
  let truncated = trimmed.length > 500 ? trimmed.slice(0, 500) + "..." : trimmed
  return truncated[0].toUpperCase() + truncated.slice(1)
}

function containsKeyword(message, keywords) {
  let lower = message.toLowerCase()
  for (let keyword of keywords) {
    if (lower.includes(keyword.toLowerCase())) {
      return true
    }
  }
  return false
}

console.log(formatMessage("  hello world  "))
console.log(formatMessage(""))
console.log(containsKeyword("I need help with my order", ["help", "support"]))
console.log(containsKeyword("Great chatbot!", ["help", "support"]))
```

## What's Coming Next...

You do not need to memorize all of these methods right now. You will naturally remember the ones you use regularly, and look up the rest when you need them. What matters is knowing they exist and understanding the pattern: every method tells you what it takes, what it does, and what it returns.

We touched on template literals briefly in this article, but there is more to explore there. The next article goes deeper into template literals: embedding expressions, multi-line strings, tagged templates, and the common patterns that make them one of the most useful additions to modern JavaScript.
