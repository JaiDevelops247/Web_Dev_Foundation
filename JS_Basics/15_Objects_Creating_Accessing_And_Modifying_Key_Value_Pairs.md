## Introduction

Arrays gave us ordered collections. You store a list of messages, a list of products, a list of cities, and you access each item by its position: index 0, index 1, index 2. That works when position is meaningful -- when you just want the first item, or when you want to loop through everything in order.

But consider what you actually need to store for a single user:

```
name: "Priya Sharma"
age: 22
email: "priya@example.com"
isVerified: true
```

This is not a list where position matters. There is no meaningful "first" or "second" here. Each piece of data has a name -- a label that tells you what it represents. Name is text. Age is a number. Email is text. Verified is a boolean. You do not access "Priya Sharma" by saying "give me index 0." You access it by saying "give me the name."

This is what objects are for. An object stores data as key-value pairs, where each value is identified by its key rather than its position.

Almost everything in JavaScript is eventually represented as an object: API responses, user profiles, configuration settings, form submissions, chatbot messages. Understanding objects is understanding how data actually lives in real programs.

### What Is an Object?

**Definition:** An object is a collection of key-value pairs, where each key is a string (or Symbol) that identifies the value. Values can be of any type -- strings, numbers, booleans, arrays, functions, or even other objects.

**Syntax:**

```js
let objectName = {
  key: value,
  key: value,
  key: value
}
```

```js
let user = {
  name: "Priya Sharma",
  age: 22,
  email: "priya@example.com",
  isVerified: true
}
```

The curly braces `{}` define the object. Each `key: value` pair is a **property**. Properties are separated by commas. This syntax -- writing an object directly with `{}` -- is called an **object literal**.

Object keys are strings. You do not need to quote them unless they contain spaces or special characters (more on this in a moment).

Values can be anything:

```js
let product = {
  name: "Laptop",
  price: 45000,
  tags: ["electronics", "computers"],    // array as a value
  inStock: true,
  discount: null
}
```

### Accessing Properties

There are two ways to read a property from an object.

#### Dot Notation

```js
object.key
```

```js
let user = { name: "Priya", age: 22 }

console.log(user.name)    // "Priya"
console.log(user.age)     // 22
```

This is the standard, most readable form. Use it whenever you know the key name in advance.

#### Bracket Notation

```js
object["key"]
```

```js
let user = { name: "Priya", age: 22 }

console.log(user["name"])    // "Priya"
console.log(user["age"])     // 22
```

The key goes inside quotes inside the brackets. This looks more verbose for simple cases, but it is essential in two situations.

**Situation 1: The key contains spaces or special characters.**

```js
let config = {
  "max length": 500,
  "allow-attachments": false
}

console.log(config["max length"])         // 500
console.log(config["allow-attachments"])  // false
// config.max length -- this is a syntax error
```

Dot notation cannot handle keys with spaces or hyphens. Bracket notation can.

**Situation 2: The key is stored in a variable.**

```js
let user = { name: "Priya", age: 22, city: "Mumbai" }

let field = "name"
console.log(user[field])    // "Priya" -- reads user["name"]

field = "city"
console.log(user[field])    // "Mumbai" -- reads user["city"]
```

When the key is dynamic -- determined at runtime rather than written literally in the code -- bracket notation is the only way to access it.

```js
// Real-world example: a form submission where you don't know in advance which field the user filled
function updateProfile(key, value) {
  profile[key] = value    // bracket notation for a dynamic key
}

updateProfile("name", "Aarav")
updateProfile("city", "Delhi")
```

If the key does not exist on the object, you get `undefined`:

```js
let user = { name: "Priya" }
console.log(user.email)    // undefined -- not an error, just not there
```

### Adding, Modifying, and Deleting Properties

Objects are not fixed structures. You can change them after creation.

**Adding a new property:**

```js
let user = { name: "Priya" }
user.email = "priya@example.com"

console.log(user)    // { name: "Priya", email: "priya@example.com" }
```

Assignment to a key that does not exist creates it.

**Modifying an existing property:**

```js
let user = { name: "Priya", age: 22 }
user.age = 23

console.log(user.age)    // 23 -- updated
```

Same syntax as adding -- if the key already exists, its value is replaced.

**Deleting a property:**

```js
let user = { name: "Priya", age: 22, temp: "remove this" }
delete user.temp

console.log(user)    // { name: "Priya", age: 22 }
```

`delete` removes the property entirely. After deletion, accessing that key returns `undefined`.

### Checking If a Property Exists

Accessing a missing key returns `undefined`, but `undefined` is also a valid value that a property might intentionally hold. To check whether a key actually exists on an object, use one of two tools.

#### The `in` Operator

```js
"key" in object    // returns true if the key exists (in the object or its prototype chain)
```

```js
let user = { name: "Priya", age: 22 }

console.log("name" in user)     // true
console.log("email" in user)    // false
```

#### hasOwnProperty()

```js
object.hasOwnProperty("key")    // returns true only if the key is directly on this object
```

```js
let user = { name: "Priya", age: 22 }

console.log(user.hasOwnProperty("name"))     // true
console.log(user.hasOwnProperty("email"))    // false
```

For most situations you will encounter in this course, both work. The distinction between them matters in the context of inheritance, which is covered in the OOP articles. For checking your own objects, either is fine.

**A common use pattern:**

```js
function getEmail(user) {
  if ("email" in user) {
    return user.email
  }
  return "No email on file"
}
```

This is safer than checking `if (user.email)` alone, because `if (user.email)` would also fail for an email that is an empty string or other falsy value.

### Shorthand Property Syntax

This comes up constantly in real JavaScript code, so it is worth knowing early.

When you have a variable and you want to use it as a property with the same name, you do not need to repeat yourself:

```js
let name = "Priya"
let age = 22

// Traditional way
let user = { name: name, age: age }

// Shorthand way
let user = { name, age }
```

Both produce the same result. When the variable name matches the key name, you can write just the name once. JavaScript infers the key.

```js
function createUser(name, email, city) {
  return { name, email, city }    // shorthand -- same as { name: name, email: email, city: city }
}

let user = createUser("Jai", "jai@example.com", "Pune")
console.log(user)    // { name: "Jai", email: "jai@example.com", city: "Pune" }
```

### Computed Property Names

Just as bracket notation lets you access a property using a dynamic key, you can also create properties with dynamic keys when building an object literal.

```js
let field = "name"

let user = {
  [field]: "Priya",    // the key is the value of the variable field
  age: 22
}

console.log(user)    // { name: "Priya", age: 22 }
```

The square brackets around `field` tell JavaScript to evaluate the expression inside and use the result as the key name. This is particularly useful when building objects programmatically:

```js
function setField(key, value) {
  return { [key]: value }
}

console.log(setField("city", "Mumbai"))    // { city: "Mumbai" }
console.log(setField("role", "admin"))     // { role: "admin" }
```

### Methods: Functions as Properties

A property value can be a function. When a function lives inside an object, it is called a **method**.

```js
let user = {
  name: "Priya",
  greet: function() {
    return `Hello, my name is ${user.name}`
  }
}

console.log(user.greet())    // "Hello, my name is Priya"
```

You call a method the same way you access a property, followed by parentheses.

**Shorthand method syntax** (the modern way to write methods):

```js
let user = {
  name: "Priya",
  greet() {
    return `Hello, my name is ${this.name}`
  }
}
```

Notice `this.name` instead of `user.name`. Inside a regular method, `this` refers to the object the method is called on. Using `this` is the correct way to refer to the object's own properties from within a method -- it means "the object this method belongs to" rather than hardcoding the variable name.

```js
let bot = {
  name: "ChatAI",
  version: "1.0",
  introduce() {
    return `I am ${this.name}, version ${this.version}`
  }
}

console.log(bot.introduce())    // "I am ChatAI, version 1.0"
```

`this` is covered in more depth in the OOP articles. For now, know that inside an object method, `this` refers to that object.

### Objects Are Reference Types

This is the same important distinction we saw with arrays, and it applies equally to objects.

When you assign an object to a new variable, you are not creating a copy. Both variables point to the same object in memory.

```js
let original = { name: "Priya", score: 90 }
let reference = original    // both point to the same object

reference.score = 100

console.log(original.score)     // 100 -- changed through reference, visible on original
console.log(reference.score)    // 100
```

Modifying the object through `reference` also modifies what `original` sees, because they are the same object.

<!-- 
IMAGE: A memory diagram showing two arrows (labeled "original" and "reference") both pointing to a single box labeled "{ name: 'Priya', score: 100 }". A caption reads: "Two variables, one object. Changing through either variable changes the same value." Style: flat vector, two arrows from variable labels pointing to a single object box in memory.
-->

This is in contrast to primitives, where assignment creates an independent copy:

```js
let a = 10
let b = a

b = 20

console.log(a)    // 10 -- unchanged
console.log(b)    // 20 -- only b changed
```

Numbers, strings, booleans -- assignment copies the value. Objects and arrays -- assignment copies the reference.

### Shallow Copying an Object

To create an independent copy of an object -- so that changes to the copy do not affect the original -- you have two options.

**Using the spread operator:**

```js
let original = { name: "Priya", score: 90 }
let copy = { ...original }

copy.score = 100

console.log(original.score)    // 90 -- unchanged
console.log(copy.score)        // 100 -- only the copy changed
```

The spread operator (`...`) expands all the key-value pairs of `original` into the new object literal. The result is a new object with the same properties.

**Using Object.assign():**

```js
let original = { name: "Priya", score: 90 }
let copy = Object.assign({}, original)

copy.score = 100

console.log(original.score)    // 90 -- unchanged
```

`Object.assign(target, source)` copies all properties from `source` into `target` and returns `target`. Passing `{}` as the target creates a fresh empty object to copy into.

Spread (`{ ...original }`) is the shorter and more commonly used form. `Object.assign` is useful when you need to merge multiple sources at once.

**The "shallow" limitation:**

Both of these create a **shallow copy**. This means that top-level primitive properties are copied by value, but nested objects are still copied by reference.

```js
let original = {
  name: "Priya",
  address: { city: "Mumbai" }    // nested object
}

let copy = { ...original }

copy.name = "Aarav"              // independent -- only affects copy
copy.address.city = "Delhi"      // this modifies the shared nested object

console.log(original.name)          // "Priya" -- unchanged
console.log(original.address.city)  // "Delhi" -- changed! shared reference
```

`name` is a primitive, so the copy got its own independent version. But `address` is an object, so both `original.address` and `copy.address` still point to the same nested object in memory.

For objects without nested objects (or when you only need to change top-level properties), shallow copying is fine and is what you will use most of the time.

### Let's Define Things Now...

> **Object:** A collection of key-value pairs where each key is a string (or Symbol) identifying its value. Created with object literal syntax `{}`.

> **Property:** A single key-value pair in an object. Can hold any type of value including functions.

> **Dot Notation:** Accessing a property using `object.key`. Requires the key to be a valid identifier known at write time.

> **Bracket Notation:** Accessing a property using `object["key"]`. Required when the key contains spaces, has special characters, or is determined at runtime.

> **Method:** A function stored as a property value. Called with `object.method()`.

> **`this`:** Inside a regular object method, refers to the object the method is called on.

> **`in` operator:** Returns `true` if a key exists in an object (or its prototype). `"key" in obj`.

> **`hasOwnProperty()`:** Returns `true` if a key exists directly on the object (not inherited). `obj.hasOwnProperty("key")`.

> **Shorthand property syntax:** When a variable name matches the intended key name, you can write `{ name }` instead of `{ name: name }`.

> **Computed property name:** Using `[expression]` as a key in an object literal to set the key dynamically.

> **Shallow copy:** A copy where top-level primitive properties are duplicated independently, but nested objects are still shared by reference. Created with `{ ...obj }` or `Object.assign({}, obj)`.

### Activity: Object Lab

1. Create a `chatUser` object with `name`, `age`, and `preferences` properties. The `preferences` value should itself be an object with `language` and `theme`.

2. Add a `greet()` method to the object that returns a personalized string using `this.name`.

3. Add a new property `isVerified` to the object after it is created. Then delete the `age` property.

4. Check whether `name` exists using `in`. Check whether a property called `score` exists using `hasOwnProperty`.

5. Create a shallow copy of the `chatUser` object using spread. Change `name` on the copy and confirm the original is unchanged. Then change `preferences.theme` on the copy and observe what happens to the original.

6. Write a function `createUserProfile(name, email, role)` that returns an object using shorthand property syntax. Call it with `"Jai"`, `"jai@example.com"`, `"student"` and log the result.

7. Add the following to `chatbot-logic.js`:

```js
let botConfig = {
  name: "ChatAI",
  version: "1.0",
  settings: {
    maxLength: 500,
    allowAttachments: false
  }
}

function updateSettings(config, newSettings) {
  return {
    ...config,
    settings: { ...config.settings, ...newSettings }
  }
}

function getBotInfo(config) {
  return `${config.name} v${config.version} | Max: ${config.settings.maxLength} chars`
}

let updated = updateSettings(botConfig, { maxLength: 1000 })
console.log(getBotInfo(botConfig))   // original unchanged
console.log(getBotInfo(updated))     // updated version
```

## What's Coming Next...

You now know how to create and work with individual objects. But JavaScript has a rich set of built-in tools for working with objects beyond just reading and writing properties: methods like `Object.keys()`, `Object.values()`, and `Object.entries()` for iterating over an object's contents, destructuring for pulling out specific properties cleanly, and JSON for converting objects into a format that can be stored or sent across a network. That is what the next article covers.
