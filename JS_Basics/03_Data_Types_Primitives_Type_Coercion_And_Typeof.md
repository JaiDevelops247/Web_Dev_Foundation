## Introduction

Variables are containers that hold data. But data itself is not all the same kind of thing.

Your name is text. Your age is a number. Your answer to "are you above 18?" is a yes or a no. A list of your favorite movies is a collection. A student's profile is a group of related pieces of information bundled together.

JavaScript needs to know what kind of data it is dealing with because different kinds of data behave differently. You can divide two numbers. You cannot divide two names. You can check if a checkbox is checked with a yes or no. You cannot check if a price is "yes."

These categories that JavaScript uses to classify data are called **data types**.

JavaScript organises all data types into two broad categories: **primitive** and **non-primitive**.

**Primitive types** hold a single, simple value. A number, a piece of text, a true/false answer. They are the raw building blocks. When you copy a primitive value into a new variable, you get a completely independent copy.

**Non-primitive types** hold collections or structures of data. An array is a list of values. An object is a group of named values. When you copy a non-primitive value into a new variable, both variables point to the same underlying data in memory.

We will cover the primitive types first since they are the foundation, then the non-primitives briefly so you know they exist.

### The 7 Primitive Data Types

#### 1. String

A string holds text: any sequence of characters including letters, numbers, spaces, and symbols.

**Syntax:** wrap the text in single quotes, double quotes, or backticks. All three create strings.

```js
let firstName = 'Jai'
let lastName = "Gupta"
let greeting = `Hello there`
```

Backtick strings (called template literals) have a special power: you can embed expressions directly inside them using `${}`.

```js
let name = "Jai"
let message = `Welcome back, ${name}!`
console.log(message)    // Welcome back, Jai!
```

**Common uses in web development:**
- Usernames, email addresses, passwords
- Button labels and page headings
- URLs and file paths
- Messages displayed to the user
- HTML content being dynamically inserted into the page
- Search queries typed into an input field

#### 2. Number

JavaScript has a single number type for all numeric values: whole numbers (called integers), decimals (called floats), and even negative numbers. There is no separate "integer" type like in C or Java.

**Syntax:**

```js
let age = 24
let price = 999.99
let temperature = -5
let percentage = 0.85
```

JavaScript numbers follow the IEEE 754 standard, which means there is a maximum safe integer value. Beyond that, precision is not guaranteed. For most everyday use cases, this is not a concern.

**Common uses in web development:**
- Product prices in an e-commerce store
- User age or form input values
- Scores in a quiz or game
- API response counts (number of results returned)
- Progress percentages (85% completed)
- Pixel values for canvas drawing

#### 3. Boolean

A boolean holds exactly one of two values: `true` or `false`. No quotes, no other options.

**Syntax:**

```js
let isLoggedIn = true
let hasNotifications = false
let isSubmitting = false
```

**Common uses in web development:**
- Whether a user is logged in or not
- Whether a modal is open or closed
- Whether a form is being submitted (to disable the button while waiting)
- Whether a product is in stock
- Whether a checkbox is checked
- Whether dark mode is enabled

#### 4. null

`null` represents the intentional absence of a value. It is not an accident or an omission. It is a value a developer deliberately assigns to signal: there is nothing here right now.

**Syntax:**

```js
let selectedUser = null        // no user has been selected yet
let lastErrorMessage = null    // no error has occurred yet
```

The key word is intentional. When you see `null`, a developer put it there on purpose to say "empty by design."

**Common uses in web development:**
- The result of a database query that returned no record
- A selected item before the user has made a selection
- The current error state when no error has occurred
- An optional field in a form that the user left blank

#### 5. undefined

`undefined` also represents absence of a value, but with a different meaning. `undefined` is what JavaScript assigns automatically when a variable has been declared but nothing has been put into it yet.

**Syntax:**

```js
let userInput    // declared but not assigned
console.log(userInput)    // undefined
```

You can also assign `undefined` manually, but this is rare and generally discouraged since `null` is the conventional way to signal "intentionally empty."

**The practical distinction:**

```js
let selectedCity = null         // developer said: no city chosen yet
let connectionStatus            // forgot to assign -- undefined
```

`null` says "I thought about this and chose nothing." `undefined` often says "this was not set up yet."

**Common uses in web development:**
- A function that is expected to return a value but hits a code path with no return statement
- A property that is accessed on an object but does not exist
- A function parameter that was not passed when the function was called

#### 6. BigInt

`BigInt` stores integers larger than the maximum safe integer that the regular `Number` type can represent. You create one by appending an `n` to the end of a number.

```js
let largeId = 9007199254740993n
```

You will rarely need BigInt in typical web development. It exists for cases involving very large database IDs, cryptography, or high-precision financial calculations. We will not use it in this course, but it is part of the type system.

#### 7. Symbol

A `Symbol` creates a guaranteed unique value. Even if you create two symbols with the same description, they are not equal to each other.

```js
let id1 = Symbol("userId")
let id2 = Symbol("userId")
console.log(id1 === id2)    // false
```

Symbols are used primarily as unique property keys on objects in advanced JavaScript patterns. Like BigInt, we will not use them in this course. Knowing they exist is enough at this stage.

### Non-Primitive Data Types

Non-primitives hold collections or structures of data. JavaScript has three: Object, Array, and Function. We will cover all of them in depth in later articles. For now, understand what they are and how they differ from primitives.

#### Object

An object groups related data under named properties. Think of it as a profile or a record.

```js
let student = {
  name: "Riya",
  age: 20,
  isEnrolled: true
}

console.log(student.name)    // Riya
console.log(student.age)     // 20
```

A `name`, an `age`, and an `isEnrolled` flag are three separate primitive values, but they all belong to one student. An object bundles them together under a single variable.

#### Array

An array holds an ordered list of values. The values do not have to be the same type.

```js
let topCities = ["Mumbai", "Delhi", "Bangalore"]
let scores = [95, 88, 76, 100]

console.log(topCities[0])    // Mumbai
console.log(scores[2])       // 76
```

Items in an array are accessed by their position, starting from 0.

#### Function

A function is a reusable block of code that can be called by name. Functions are values in JavaScript, which means they can be stored in variables, passed to other functions, and returned from functions.

```js
function greet(name) {
  console.log(`Hello, ${name}!`)
}

greet("Jai")    // Hello, Jai!
```

We will dedicate full articles to objects, arrays, and functions. For now, the point is: non-primitives group and structure data, while primitives represent single values.

<!-- 
IMAGE: A two-column diagram. Left column labeled "Primitive Types" lists 7 rows: String (with a text/quotes icon), Number (with a 42 icon), Boolean (with a toggle switch icon), null (with an empty box icon), undefined (with a question mark icon), BigInt (with a large number icon), Symbol (with a unique key icon). Right column labeled "Non-Primitive Types" lists 3 rows: Object (with a grouped-properties icon), Array (with a list icon), Function (with a code block icon). A dividing line between the columns with the label "Single value vs Collection or structure." Caption: "Seven primitive types hold individual values. Non-primitives hold collections and structures." Style: flat vector, two-column layout, each type has a small icon and label.
-->

### The typeof Operator

Here is a situation you will encounter constantly: you receive a value from somewhere and you are not sure what type it is.

A form input always gives you a string, even if the user typed a number. So if a user fills in their age as `42`, the value you receive is not the number `42`. It is the string `"42"`. Now if you try to do math with it, something unexpected happens:

```js
let userAge = "42"    // came from a form input -- it is a string
console.log(userAge + 10)    // "4210" -- not 52
```

JavaScript concatenated the string with the number instead of adding them.

To check what type a value is before working with it, JavaScript gives you the `typeof` operator.

We will cover operators in detail in the next article. For now, think of `typeof` as a utility tool: you give it a value, it tells you the type as a string.

**Syntax:**

```js
typeof value
```

**Examples:**

```js
console.log(typeof "hello")        // "string"
console.log(typeof 42)             // "number"
console.log(typeof true)           // "boolean"
console.log(typeof undefined)      // "undefined"
console.log(typeof null)           // "object"   <-- we will explain this below
console.log(typeof [1, 2, 3])      // "object"
console.log(typeof { name: "Jai" }) // "object"
console.log(typeof function(){})   // "function"
```

Use `typeof` whenever you need to confirm the type of a value before performing an operation on it.

### Type Conversion

Now that we know types exist and can be checked, we need to handle situations where the type of a value is not what we need. JavaScript handles this in two ways: automatically (implicit) and on demand (explicit).

#### Implicit Coercion: When JavaScript Converts Automatically

JavaScript sometimes converts a value from one type to another automatically to make an operation work. This is called implicit coercion, and it is one of the most surprising parts of the language.

```js
console.log("5" + 3)     // "53" -- number 3 is converted to string, then concatenated
console.log("5" - 3)     // 2   -- string "5" is converted to number, then subtracted
console.log(true + 1)    // 2   -- true is converted to 1
console.log(false + 1)   // 1   -- false is converted to 0
console.log("" + 5)      // "5" -- number converted to string
```

The `+` operator with a string always chooses string concatenation. Other arithmetic operators (`-`, `*`, `/`) always try to convert both sides to numbers.

You do not choose when implicit coercion happens. JavaScript applies its own rules. This is why checking types with `typeof` before arithmetic operations is a good habit when values come from user input or external sources.

#### Explicit Conversion: Converting On Purpose

When you need to convert a value to a specific type intentionally, JavaScript gives you built-in conversion functions.

**Converting to a Number:**

```js
Number("42")        // 42
Number("3.14")      // 3.14
Number("")          // 0
Number("hello")     // NaN
Number(true)        // 1
Number(false)       // 0
Number(null)        // 0
Number(undefined)   // NaN
```

For strings that contain only digits (like form inputs), use `parseInt()` or `parseFloat()`:

```js
parseInt("42px")      // 42 -- stops at the first non-numeric character
parseFloat("3.14em")  // 3.14
parseInt("hello")     // NaN
```

`parseInt` is particularly useful for CSS-style strings like `"24px"` because it extracts the numeric part and ignores the unit.

**Converting to a String:**

```js
String(42)        // "42"
String(true)      // "true"
String(null)      // "null"
String(undefined) // "undefined"
```

Or using the `.toString()` method directly on a value:

```js
(42).toString()      // "42"
(true).toString()    // "true"
```

**Converting to a Boolean:**

```js
Boolean(1)          // true
Boolean(0)          // false
Boolean("hello")    // true
Boolean("")         // false
Boolean(null)       // false
Boolean(undefined)  // false
```

**When do you need explicit conversion?**

The most common real-world case: a user types their age into a form field. You receive the string `"24"`. Before you can do any calculation with it, convert it to a number:

```js
let rawInput = "24"               // from the input field
let age = Number(rawInput)        // convert to number
let nextBirthday = age + 1        // now this works correctly: 25
```

### Truthy and Falsy Values

In JavaScript, every value has an inherent boolean quality. When a value is used in a condition (an `if` statement, for example), JavaScript automatically treats it as either true or false, even if it is not a boolean.

Values that are treated as `false` in a boolean context are called **falsy**. There are exactly six of them:

```js
false
0
""          // empty string
null
undefined
NaN
```

Every other value is **truthy**: non-zero numbers, non-empty strings, objects, arrays, even `"false"` as a string.

```js
if ("") {
  console.log("this will not run")    // empty string is falsy
}

if ("hello") {
  console.log("this will run")        // non-empty string is truthy
}

if (0) {
  console.log("this will not run")    // 0 is falsy
}

if (42) {
  console.log("this will run")        // non-zero number is truthy
}
```

This matters in practice because you will often write conditions like:

```js
if (userInput) {
  // only runs if userInput is not empty, not null, not undefined
}
```

Instead of explicitly comparing:

```js
if (userInput !== "" && userInput !== null && userInput !== undefined) {
  // same result, much more verbose
}
```

> **Note for interviews:** The six falsy values are one of the most commonly tested JavaScript fundamentals in technical interviews. Know them by heart: `false`, `0`, `""`, `null`, `undefined`, `NaN`. Everything else is truthy.

### Exceptions and Unusual Behaviour

There are a few places where JavaScript's type system produces results that look wrong at first glance.

**typeof null returns "object"**

```js
typeof null    // "object"
```

`null` is not an object. This is a bug in the original JavaScript implementation from 1995 that has never been fixed because fixing it would break too many programs written against the old behaviour. `null` is a primitive, and `typeof null` returning `"object"` is simply a historical error that you memorise and move on from.

**Comparing values of different types**

```js
console.log(0 == false)       // true
console.log("" == false)      // true
console.log(null == undefined) // true
console.log("5" == 5)         // true
```

These produce `true` even though the types are completely different. If you compare a string to a number, JavaScript does not say "these are different types, they cannot be equal." Instead, it converts both sides to a common type and then compares.

This conversion-before-comparison is called loose equality, triggered by the `==` operator. The results are predictable once you know the rules, but the rules are complex enough that many developers find them unintuitive.

We will cover exactly how this works, why it happens, and how to avoid it in the next article on operators. For now, just know the behaviour exists. Seeing `"5" == 5` return `true` is not a glitch. It is a deliberate (if controversial) feature of the language, and there is a clean way to opt out of it entirely.

### Let's Define Things Now...

> **Data Type:** The category a value belongs to, which determines what operations can be performed on it. JavaScript has seven primitive types and three non-primitive types.

> **Primitive:** A data type that holds a single, simple value. Copying a primitive creates an independent copy. The seven primitives are: String, Number, Boolean, null, undefined, BigInt, and Symbol.

> **Non-Primitive:** A data type that holds a collection or structure. Copying a non-primitive copies the reference, not the value. The non-primitives are Object, Array, and Function.

> **typeof:** An operator that returns the data type of a value as a string. Example: `typeof 42` returns `"number"`.

> **Implicit Coercion:** When JavaScript automatically converts a value from one type to another to make an operation work. Example: `"5" + 3` produces `"53"` because the number is coerced to a string.

> **Explicit Conversion:** When the developer intentionally converts a value using `Number()`, `String()`, `Boolean()`, `parseInt()`, or `parseFloat()`.

> **NaN (Not a Number):** A special numeric value representing the result of an invalid arithmetic operation, like `Number("hello")`. Despite the name, `typeof NaN` returns `"number"`. Check for NaN using `isNaN()`, not `=== NaN`, because `NaN` is the only value in JavaScript that is not equal to itself.

> **Truthy / Falsy:** Every JavaScript value has an implicit boolean quality. The six falsy values are `false`, `0`, `""`, `null`, `undefined`, and `NaN`. Every other value is truthy.

### Activity: Coercion Predictor

1. Predict the output of each expression below before running it in the browser console. Write your prediction first, then run it and note whether you were right:

```js
typeof "42"
typeof 42
typeof true
typeof null
typeof undefined
typeof [1, 2, 3]
1 + "2"
"5" - 2
true + 1
false + 10
Number("99")
Number("hello")
Number("")
parseInt("48px")
Boolean(0)
Boolean("false")
```

2. Write a function called `isTruthy` that takes any value and logs whether it is truthy or falsy with a clear message:

```js
function isTruthy(value) {
  // if value is truthy, log: "Truthy: <value>"
  // if value is falsy, log: "Falsy: <value>"
}

isTruthy(0)
isTruthy("hello")
isTruthy(null)
isTruthy([])
isTruthy("")
isTruthy(42)
```

3. Add the following function to `chatbot-logic.js` and run it with Node.js:

```js
function validateMessage(input) {
  if (typeof input !== "string") {
    console.log("Invalid: input is not a string")
    return false
  }
  if (input.trim() === "") {
    console.log("Invalid: message cannot be empty")
    return false
  }
  console.log("Valid message:", input)
  return true
}

validateMessage("Hello bot")
validateMessage("")
validateMessage("   ")
validateMessage(null)
validateMessage(42)
```

Confirm the output matches your expectations for each case.

## What's Coming Next...

We now know what kinds of data JavaScript can store and how to check, convert, and evaluate them. The next article brings in the operators: the symbols and keywords that let us compare values, combine them, and build the conditions our programs make decisions on. That is also where we will fully explain what `==` versus `===` means and why one of them should almost never be used.
