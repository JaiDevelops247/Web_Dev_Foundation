## Introduction

The previous article showed how to create an object, read and write its properties, and copy it safely. But there is a gap: how do you actually work with the contents of an object when you do not know the keys in advance?

With an array, looping over every item is straightforward -- `for...of` gives you each element. With an object, you cannot do that directly because objects are not iterable in the same way. You need tools that convert an object's contents into something you can work with.

JavaScript provides a set of static methods on the `Object` constructor that do exactly this. Alongside them, a destructuring syntax lets you pull specific properties out of an object cleanly in a single line, and JSON gives you a way to turn objects into storable, transferable text and back again.

### Object.keys(), Object.values(), Object.entries()

Consider this situation: you have a user preferences object and you want to display every setting the user has configured. You do not want to hardcode each property name -- you want to loop over all of them.

```js
let preferences = {
  theme: "dark",
  language: "English",
  notifications: true,
  fontSize: 16
}
```

Three methods on `Object` extract the contents of an object as an array, which you can then iterate with `for...of`, `map`, `filter`, or any other array tool.

#### Object.keys()

Returns an array of the object's own property names (the keys).

```js
console.log(Object.keys(preferences))
// ["theme", "language", "notifications", "fontSize"]
```

#### Object.values()

Returns an array of the object's own property values.

```js
console.log(Object.values(preferences))
// ["dark", "English", true, 16]
```

#### Object.entries()

Returns an array of `[key, value]` pairs. Each element is a two-item array.

```js
console.log(Object.entries(preferences))
// [["theme", "dark"], ["language", "English"], ["notifications", true], ["fontSize", 16]]
```

`Object.entries()` is the most useful of the three because it gives you both the key and the value together. Combine it with `for...of` and destructuring to iterate over an object cleanly:

```js
for (let [key, value] of Object.entries(preferences)) {
  console.log(`${key}: ${value}`)
}
// theme: dark
// language: English
// notifications: true
// fontSize: 16
```

This is the standard pattern for iterating over an object's properties in modern JavaScript.

**Real-world use case:** A settings page that renders each configuration option programmatically. Instead of hard-coding HTML for each setting, you loop over `Object.entries(settings)` and build each row dynamically.

### Object.assign()

Covered briefly in the previous article for copying, `Object.assign()` is more broadly used for merging objects together.

```js
Object.assign(target, source1, source2, ...)
```

It copies all properties from each source into `target`, overwriting if a key already exists, and returns the `target` object.

```js
let defaults = { theme: "light", fontSize: 14, notifications: true }
let userOverrides = { theme: "dark", fontSize: 16 }

let finalSettings = Object.assign({}, defaults, userOverrides)
console.log(finalSettings)
// { theme: "dark", fontSize: 16, notifications: true }
```

`userOverrides.theme` and `userOverrides.fontSize` replaced the defaults. `notifications` came through unchanged because `userOverrides` did not have it.

Passing `{}` as the first argument means the merge happens into a fresh object, leaving both `defaults` and `userOverrides` untouched.

The spread operator achieves the same result more concisely (covered below).

### Object.freeze()

Sometimes you want to lock an object so that no property can be added, changed, or deleted. `Object.freeze()` does this.

```js
let config = Object.freeze({
  apiVersion: "v2",
  baseUrl: "https://api.example.com"
})

config.apiVersion = "v3"    // silently ignored in non-strict mode
config.newProp = "test"     // silently ignored

console.log(config.apiVersion)    // still "v2"
```

Attempts to modify a frozen object either fail silently (normal mode) or throw a `TypeError` (strict mode). Either way, the object remains unchanged.

**When to use it:** Constants that must not change throughout the program -- API configuration, feature flags, application-wide settings. `Object.freeze()` makes your intent explicit and prevents accidental modifications.

**Exception:** `Object.freeze()` is shallow. Nested objects inside the frozen object are not frozen:

```js
let config = Object.freeze({
  settings: { theme: "dark" }    // this nested object is NOT frozen
})

config.settings.theme = "light"    // this works -- nested object is mutable
console.log(config.settings.theme)  // "light"
```

### Object Destructuring

You have already seen array destructuring -- pulling values out by position. Object destructuring pulls values out by key name.

**The problem without it:**

```js
let user = { name: "Priya", age: 22, city: "Mumbai", role: "admin" }

let name = user.name
let age = user.age
let city = user.city
```

Four lines to extract three values. With destructuring:

```js
let { name, age, city } = user

console.log(name)    // "Priya"
console.log(age)     // 22
console.log(city)    // "Mumbai"
```

One line. The variable names on the left must match the key names on the object. JavaScript looks up each key and assigns its value to the matching variable.

#### Renaming During Destructuring

If you need the variable to have a different name from the key, use the colon syntax:

```js
let { name: displayName, age: userAge } = user

console.log(displayName)    // "Priya"
console.log(userAge)        // 22
// "name" and "age" as variable names no longer exist
```

The pattern is `{ key: newVariableName }`. Read it as "pull out `key`, call it `newVariableName`." This is useful when the key name conflicts with an existing variable, or when a shorter or clearer name is more appropriate in context.

#### Default Values

If a key does not exist on the object, destructuring produces `undefined` by default. You can provide a fallback:

```js
let { name, role = "user" } = { name: "Priya" }

console.log(name)    // "Priya"
console.log(role)    // "user" -- key was missing, default used
```

Defaults and renaming can be combined:

```js
let { name: displayName = "Anonymous" } = {}

console.log(displayName)    // "Anonymous"
```

#### Nested Destructuring

When a property's value is itself an object, you can destructure into it directly:

```js
let user = {
  name: "Priya",
  address: {
    city: "Mumbai",
    pincode: "400001"
  }
}

let { name, address: { city, pincode } } = user

console.log(name)       // "Priya"
console.log(city)       // "Mumbai"
console.log(pincode)    // "400001"
// "address" itself is not a variable here -- only city and pincode are
```

#### The Rest Pattern

Collect all remaining properties that were not explicitly destructured into a new object:

```js
let user = { name: "Priya", age: 22, city: "Mumbai", role: "admin" }

let { name, role, ...rest } = user

console.log(name)    // "Priya"
console.log(role)    // "admin"
console.log(rest)    // { age: 22, city: "Mumbai" }
```

`...rest` captures everything that was not explicitly named. The rest variable must always come last.

**Real-world use case:** You receive a full user object from an API but need to separate the fields you will display from the fields you will store internally:

```js
let { password, token, ...publicProfile } = apiResponse
// password and token are extracted (and not passed to the UI)
// publicProfile contains everything safe to display
```

#### Destructuring in Function Parameters

One of the most common uses of object destructuring is directly in function signatures. Instead of accepting a full object and accessing its properties inside, you destructure in the parameter itself:

```js
// Without destructuring
function displayUser(user) {
  return `${user.name}, age ${user.age}`
}

// With destructuring in the parameter
function displayUser({ name, age }) {
  return `${name}, age ${age}`
}

displayUser({ name: "Priya", age: 22 })    // "Priya, age 22"
```

This pattern is very common in React (each component receives a `props` object and immediately destructures it) and in API handler functions.

### Spread with Objects

The spread operator works with objects the same way it works with arrays: it expands an object's properties into a new context.

**Copying an object:**

```js
let original = { name: "Priya", age: 22 }
let copy = { ...original }

copy.age = 30
console.log(original.age)    // 22 -- unchanged
```

**Merging objects:**

```js
let defaults = { theme: "light", fontSize: 14, notifications: true }
let overrides = { theme: "dark", fontSize: 16 }

let settings = { ...defaults, ...overrides }
console.log(settings)
// { theme: "dark", fontSize: 16, notifications: true }
```

When keys overlap, the later spread wins. `overrides` came after `defaults`, so its values replaced the matching ones.

**Merging while adding new properties:**

```js
let user = { name: "Priya", role: "user" }
let promoted = { ...user, role: "admin", promoted: true }

console.log(promoted)
// { name: "Priya", role: "admin", promoted: true }
```

Spread and direct property assignment can be combined in the same object literal. Properties written after the spread override what the spread brought in, and properties written before the spread can be overridden by it.

Spread (`{ ...source }`) and `Object.assign({}, source)` produce the same result for simple merges. Spread is shorter and more readable; prefer it in new code.

### The Rest Parameter with Objects in Functions

When a function receives an object and you want to handle some known properties and also accept arbitrary additional ones, you can combine destructuring with rest in the function signature:

```js
function createMessage({ sender, text, ...metadata }) {
  console.log(sender)      // "Jai"
  console.log(text)        // "Hello"
  console.log(metadata)    // { timestamp: "09:00", priority: "high" }
}

createMessage({
  sender: "Jai",
  text: "Hello",
  timestamp: "09:00",
  priority: "high"
})
```

`sender` and `text` are extracted explicitly. Everything else lands in `metadata`. The function does not need to know in advance what the additional fields are.

### JSON: Turning Objects into Text and Back

JavaScript objects live in memory. But sometimes you need to send an object to a server, or save it so it persists when the user closes the tab. Memory does not survive network requests or browser restarts. Text does.

**JSON** (JavaScript Object Notation) is a text format that represents structured data. It looks almost exactly like a JavaScript object literal, which makes it readable and easy to work with.

```json
{"name":"Priya","age":22,"isVerified":true}
```

JavaScript provides two functions for working with JSON.

#### JSON.stringify()

Converts a JavaScript object into a JSON string.

```js
let user = { name: "Priya", age: 22, isVerified: true }
let json = JSON.stringify(user)

console.log(json)           // '{"name":"Priya","age":22,"isVerified":true}'
console.log(typeof json)    // "string"
```

The result is a string, not an object. You can now store it in `localStorage`, send it in a network request body, or write it to a file.

**Formatted output (for readability during debugging):**

```js
let json = JSON.stringify(user, null, 2)
console.log(json)
// {
//   "name": "Priya",
//   "age": 22,
//   "isVerified": true
// }
```

The third argument is the indentation level. `2` means two spaces per level.

#### JSON.parse()

Converts a JSON string back into a JavaScript object.

```js
let json = '{"name":"Priya","age":22}'
let parsed = JSON.parse(json)

console.log(parsed.name)    // "Priya"
console.log(parsed.age)     // 22
```

The round-trip: object to string with `stringify`, string back to object with `parse`.

**The most common use case -- localStorage:**

```js
// Save to localStorage
let session = { user: "Priya", messageCount: 5 }
localStorage.setItem("session", JSON.stringify(session))

// Read back later
let stored = localStorage.getItem("session")
let restored = JSON.parse(stored)
console.log(restored.user)    // "Priya"
```

`localStorage` can only store strings. `JSON.stringify` converts the object to a string for storage. `JSON.parse` converts the string back to a usable object when you read it.

#### JSON Limitations

Not everything in a JavaScript object can be represented in JSON. The following are silently stripped during `JSON.stringify`:

| Value type | What happens |
|---|---|
| `function` | Removed entirely (functions are not data) |
| `undefined` | Removed entirely |
| `Symbol` | Removed entirely |
| `NaN` / `Infinity` | Converted to `null` |

```js
let obj = {
  name: "Priya",
  greet: function() { return "Hello" },
  score: undefined,
  tag: Symbol("id")
}

console.log(JSON.stringify(obj))
// '{"name":"Priya"}' -- only name survived
```

If your object has methods or undefined values that you need to preserve, JSON is not the right format. For everything else -- pure data objects with strings, numbers, booleans, arrays, and nested objects -- JSON works perfectly.

**Catching parse errors:**

If you receive a malformed JSON string (from a server, from storage, from user input) and try to `JSON.parse` it, you get a `SyntaxError`. Wrap it in `try-catch` when the source is not guaranteed to be valid:

```js
function safeParseJSON(str) {
  try {
    return JSON.parse(str)
  } catch (error) {
    console.log("Invalid JSON:", error.message)
    return null
  }
}
```

### Let's Define Things Now...

> **Object.keys(obj):** Returns an array of the object's own enumerable property names.

> **Object.values(obj):** Returns an array of the object's own enumerable property values.

> **Object.entries(obj):** Returns an array of `[key, value]` pairs. Used with `for...of` to iterate over an object.

> **Object.assign(target, ...sources):** Copies properties from all sources into target. Overwrites on key conflict. Returns target.

> **Object.freeze(obj):** Prevents any modifications to an object. Shallow -- nested objects remain mutable.

> **Object Destructuring:** Syntax for extracting properties from an object into named variables in a single expression. Variables are matched by key name, not position.

> **Renaming in Destructuring:** `{ key: newName }` extracts `key` but stores it in a variable called `newName`.

> **Default Values in Destructuring:** `{ key = defaultValue }` uses the default when the key is missing or undefined.

> **Rest in Destructuring:** `{ a, b, ...rest }` collects all remaining properties into a new object.

> **Spread with Objects:** `{ ...obj }` expands all properties of `obj` into a new object. Used for copying and merging.

> **JSON:** A text format for representing structured data. Compatible with strings, numbers, booleans, arrays, and nested objects. Functions, undefined, and Symbols are not supported.

> **JSON.stringify(obj):** Converts a JavaScript object to a JSON string for storage or transmission.

> **JSON.parse(str):** Converts a JSON string back into a JavaScript object.

### Activity: Object Methods Lab

1. Given the following object, use `Object.keys`, `Object.values`, and `Object.entries` to extract each part separately. Then iterate over the entries using `for...of` and log each setting in a formatted line:

```js
let settings = {
  theme: "dark",
  language: "Hindi",
  notifications: false,
  fontSize: 14
}
```

2. Use destructuring to extract `theme` and `language` from `settings`. Rename `theme` to `currentTheme`. Provide a default of `"medium"` for a `textSize` property that does not exist.

3. Merge `settings` with the following `userOverrides` object using spread, and confirm the original `settings` is unchanged:

```js
let userOverrides = { theme: "light", fontSize: 18 }
```

4. Freeze the merged settings and try to modify one property. Log the result to confirm it did not change.

5. Take the following nested object and use nested destructuring to extract `city` and `pincode` directly:

```js
let user = {
  name: "Dev",
  location: { city: "Bangalore", pincode: "560001" }
}
```

6. Convert the merged settings object to a JSON string using `JSON.stringify`. Log it. Then parse it back with `JSON.parse` and confirm you can access the properties normally.

7. Add the following to `chatbot-logic.js`:

```js
function saveSession(session) {
  let json = JSON.stringify(session)
  console.log("Session saved:", json)
  return json
}

function loadSession(jsonString) {
  try {
    let { name = "Anonymous", messageCount = 0, ...rest } = JSON.parse(jsonString)
    return { name, messageCount, ...rest }
  } catch (error) {
    console.log("Failed to load session:", error.message)
    return null
  }
}

let session = { name: "Jai", messageCount: 12, lastActive: "09:30" }
let saved = saveSession(session)
let restored = loadSession(saved)
console.log(restored.name)          // "Jai"
console.log(restored.messageCount)  // 12
console.log(loadSession("invalid")) // null -- graceful failure
```

## What's Coming Next...

That wraps up the core JavaScript basics -- variables, types, operators, conditionals, loops, strings, functions, scope, error handling, arrays, and objects. These are the foundational building blocks that everything else in JavaScript sits on top of.

One thing worth saying clearly: this is not a JavaScript course. These articles cover the concepts you need to understand before introducing JavaScript to a real web page. There is a lot more to the language -- user input handling, events, timers, more advanced patterns with Promises -- and you will encounter those as you build. What we have covered here is the minimum set of tools you need to think clearly in JavaScript and follow along when those topics come up.

Before we bring JavaScript into the browser, there is one more section to complete: object-oriented programming. OOP gives you a way to model real-world entities as self-contained units that combine data and behaviour -- the chatbot, the message store, the user session -- each as a structured object with its own properties and methods. That is what the next two articles cover. Once OOP is done, we circle back to the web development side and start connecting JavaScript to HTML.
