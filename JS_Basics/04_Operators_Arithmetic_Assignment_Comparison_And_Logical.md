## Introduction

We now know that data exists in our programs as standalone values: a name stored as a string, a price stored as a number, a login status stored as a boolean. But data sitting in variables by itself does not accomplish anything.

The vast majority of actual programming work involves performing operations on that data:

- Combining a first name and last name to display a full name on the screen
- Adding two prices together to show a cart total
- Checking whether a user's age is above 18 before allowing access
- Reducing a counter by 1 when an item is removed from a wishlist
- Showing a default username when the user has not set one yet
- Safely reading a nested setting from a user profile without crashing if part of the profile is missing

Every one of these tasks involves taking existing data and doing something with it. The tools JavaScript provides for this are called **operators**.

An **operator** is a symbol or keyword that performs a specific operation on one or more values. The values it operates on are called **operands**. The operation produces a result.

```
operand   operator   operand
  10          +         5       =    15
  price       *         1.18    =    price with tax
  age         >=        18      =    true or false
```

Every operator expression has two parts: the data you are working with (operands) and the action being performed (operator). JavaScript has several categories of operators, each designed for a different kind of task. We will go through them one by one.

### 1. Arithmetic Operators

Arithmetic operators perform mathematical calculations. They are used anywhere numbers need to be combined, compared, or transformed.

**Common uses in web development:**
- Calculating a cart total from individual item prices
- Computing a discount (original price minus percentage off)
- Tracking scores and incrementing counters
- Converting values (temperature from Celsius to Fahrenheit, currency conversion)
- Pagination (figuring out how many pages a list of results needs)

| Operator | Name | Example | Result |
|---|---|---|---|
| `+` | Addition | `10 + 3` | `13` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `10 * 3` | `30` |
| `/` | Division | `10 / 3` | `3.333...` |
| `%` | Modulo (remainder) | `10 % 3` | `1` |
| `**` | Exponentiation | `2 ** 8` | `256` |

**Modulo** is worth spending a moment on. It returns the remainder after division, not the result. `10 % 3` is `1` because 10 divided by 3 is 3 with a remainder of 1. It is used constantly for:

```js
// Check if a number is even (remainder when divided by 2 is 0)
let isEven = 8 % 2 === 0    // true

// Cycle through a fixed set of items (wrap around after the last)
let index = currentItem % totalItems

// Every Nth iteration (e.g. add a separator after every 5 items)
if (i % 5 === 0) { /* add separator */ }
```

#### The + Operator with Strings

The `+` operator has a second job beyond addition: when either operand is a string, it concatenates (joins) instead of adding.

```js
console.log("Hello" + " " + "World")    // "Hello World"
console.log("Page " + 5)                // "Page 5"  -- number becomes string
console.log(5 + 5)                       // 10        -- both numbers, addition
console.log(5 + "5")                     // "55"      -- string present, concatenation
```

This is the source of the `"5" + 3 = "53"` behaviour from the previous article. The `+` operator sees a string and switches to concatenation mode.

#### Increment and Decrement

`++` increases a value by 1. `--` decreases it by 1. Both come in two forms that differ in when the change is applied.

**Postfix** (`value++`): uses the current value first, then increments.

**Prefix** (`++value`): increments first, then uses the updated value.

```js
let count = 5

console.log(count++)    // 5  -- uses 5, then count becomes 6
console.log(count)      // 6

console.log(++count)    // 7  -- count becomes 7 first, then uses 7
console.log(count)      // 7
```

In most everyday use (incrementing a counter inside a loop), the distinction does not matter because you are not using the result of the expression directly. Where it matters is when you use `++` or `--` as part of a larger expression.

```js
let a = 5
let b = a++    // b gets 5, then a becomes 6
// a is 6, b is 5

let c = 5
let d = ++c    // c becomes 6 first, d gets 6
// c is 6, d is 6
```

### 2. Assignment Operators

Assignment operators store a value into a variable. You have already seen the basic one (`=`). The compound forms combine an arithmetic operation with assignment in one step.

**Common uses in web development:**
- Updating a running total in a shopping cart
- Tracking how many times a button has been clicked
- Building a string piece by piece
- Reducing a countdown timer

```js
let score = 0

score = score + 10    // long form: add 10 to score
score += 10           // short form: same result

score -= 5            // subtract 5
score *= 2            // multiply by 2
score /= 4            // divide by 4
score %= 3            // replace with the remainder when divided by 3
```

All compound assignment operators (`+=`, `-=`, `*=`, `/=`, `%=`) are shorthand for the full `variable = variable operator value` form. They do not do anything different, they just save typing.

### 3. Comparison Operators

Comparison operators evaluate a relationship between two values and return a boolean: `true` or `false`. They are the foundation of every condition in every program.

**Common uses in web development:**
- Checking if a user is old enough to access content
- Verifying that a price is within a budget
- Finding out if a form field has reached its character limit
- Sorting a list by comparing values

| Operator | Meaning | Example | Result |
|---|---|---|---|
| `>` | Greater than | `10 > 5` | `true` |
| `<` | Less than | `10 < 5` | `false` |
| `>=` | Greater than or equal | `5 >= 5` | `true` |
| `<=` | Less than or equal | `4 <= 5` | `true` |
| `===` | Strict equality | `5 === "5"` | `false` |
| `!==` | Strict inequality | `5 !== "5"` | `true` |
| `==` | Loose equality | `5 == "5"` | `true` |
| `!=` | Loose inequality | `5 != "5"` | `false` |

#### === vs == : The Difference That Matters Most

This is one of the most important distinctions in JavaScript. The results above already hint at it.

`===` (strict equality) checks both the value AND the type. If both do not match, the result is `false`. No conversion happens.

`==` (loose equality) checks the value, but first converts both sides to the same type if they differ. This is the coercion behaviour from the previous article, and it produces results that look wrong:

```js
5 === "5"          // false -- different types, no conversion
5 == "5"           // true  -- "5" is converted to 5 before comparison

0 === false        // false -- different types
0 == false         // true  -- false is converted to 0

null === undefined  // false -- different types
null == undefined   // true  -- special case: these two are loosely equal

"" === false        // false
"" == false         // true
```

**The rule:** always use `===`. Use `==` only when you have a specific reason to want coercion, which is rare. The accidental surprises that `==` produces are not worth its occasional convenience.

```js
// Write this
if (userInput === "") {
  console.log("Field is empty")
}

// Not this
if (userInput == "") {
  // also matches false, 0, null... probably not what you wanted
}
```

### 4. Logical Operators

Logical operators work with boolean values to combine or invert conditions. They are how you express "this AND that", "this OR that", and "NOT this" in code.

**Common uses in web development:**
- A user can proceed only if they are logged in AND have verified their email
- Show an error message if the field is empty OR too long
- Toggle a state by inverting a boolean

#### AND: &&

`&&` returns `true` only if both sides are truthy. If either side is falsy, it returns `false`.

```js
true && true     // true
true && false    // false
false && true    // false
false && false   // false
```

```js
let age = 20
let hasTicket = true

if (age >= 18 && hasTicket) {
  console.log("Entry allowed")
}
```

#### OR: ||

`||` returns `true` if at least one side is truthy. It only returns `false` when both sides are falsy.

```js
true || false    // true
false || true    // true
false || false   // false
```

```js
let isAdmin = false
let isModerator = true

if (isAdmin || isModerator) {
  console.log("Can access dashboard")
}
```

#### NOT: !

`!` inverts a boolean. `true` becomes `false`, `false` becomes `true`.

```js
!true     // false
!false    // true
```

```js
let isLoading = false

if (!isLoading) {
  console.log("Data is ready")
}
```

#### Short-Circuit Evaluation

JavaScript does not always evaluate both sides of a logical expression. It stops as soon as the result is determined.

Think of it like a relay race. With `&&`, if the first runner (the left side) falls (is falsy), there is no point sending the second runner out because the team has already lost. With `||`, if the first runner finishes the race (the left side is truthy), there is no need for the second runner at all.

`&&` stops at the first falsy value. If found, it returns that falsy value. If all values are truthy, it returns the last value.

`||` stops at the first truthy value. If found, it returns that truthy value. If all values are falsy, it returns the last value.

```js
// && short-circuits on the first falsy
false && console.log("never runs")    // false -- second side never evaluated

// || short-circuits on the first truthy
true || console.log("never runs")     // true -- second side never evaluated
```

This is used practically as a default value pattern:

```js
let userName = ""
let displayName = userName || "Guest"
console.log(displayName)    // "Guest" -- because "" is falsy, || returns the right side
```

And as a conditional execution pattern:

```js
let user = { name: "Jai" }
user && console.log("User exists:", user.name)    // runs because user is truthy
```

<!-- 
IMAGE: Two side-by-side diagrams illustrating short-circuit evaluation. Left diagram (&&): two boxes labeled "Left side" and "Right side" connected by &&. An arrow from Left side shows "Falsy? Stop here, return this value." and "Truthy? Keep going, check Right side." Caption: "&& stops at the first falsy -- no point checking further." Right diagram (||): same layout. An arrow from Left side shows "Truthy? Stop here, return this value." and "Falsy? Keep going, check Right side." Caption: "|| stops at the first truthy -- already have a winner." Style: flat vector, two parallel relay-race style diagrams with stop/continue arrows.
-->

### 5. The Nullish Coalescing Operator: ??

The `||` default-value pattern has a problem. Because `||` short-circuits on the first truthy value, it treats `0` and `""` (empty string) as falsy -- which means it replaces them with the default even when they are legitimate values.

```js
let score = 0
let displayScore = score || "No score yet"
console.log(displayScore)    // "No score yet" -- but 0 IS a valid score!

let searchQuery = ""
let query = searchQuery || "Enter a search term"
console.log(query)           // "Enter a search term" -- but "" might be intentional!
```

`??` (nullish coalescing) solves this. It only returns the right side when the left side is `null` or `undefined`. It does not treat `0` or `""` as triggering the default.

```js
let score = 0
let displayScore = score ?? "No score yet"
console.log(displayScore)    // 0 -- 0 is not null or undefined, so keep it

let playerName = null
let displayName = playerName ?? "Anonymous"
console.log(displayName)    // "Anonymous" -- null triggers the default
```

**The rule of thumb:**
- Use `||` when any falsy value (including `0`, `""`, `false`) should trigger the default
- Use `??` when only `null` and `undefined` should trigger the default and other falsy values are legitimate

### 6. The Optional Chaining Operator: ?.

Consider a user profile object:

```js
let user = {
  name: "Jai",
  address: {
    city: "Mumbai"
  }
}

console.log(user.address.city)    // "Mumbai"
```

Now what if the user has not filled in their address?

```js
let user = {
  name: "Jai"
    // no address property
}

console.log(user.address.city)    // TypeError: Cannot read properties of undefined
```

JavaScript tries to access `.city` on `user.address`, but `user.address` is `undefined`. Accessing a property on `undefined` throws an error and crashes the program.

The traditional fix was to check at every step:

```js
let city
if (user && user.address && user.address.city) {
  city = user.address.city
}
```

`?.` (optional chaining) is the cleaner solution. It says: if the value before `?.` is `null` or `undefined`, stop here and return `undefined` instead of crashing.

```js
let city = user?.address?.city
console.log(city)    // undefined -- no crash, no error
```

If `user` is `null` or `undefined`, the whole expression evaluates to `undefined`. If `user` exists but `user.address` does not, same result. You only get the value if every step of the chain exists.

**Breaking it down if the syntax feels unfamiliar:**

`?.` is essentially doing this check for you:

```js
user?.address?.city

// is equivalent to:
user == null ? undefined : (user.address == null ? undefined : user.address.city)
```

Optional chaining also works with function calls and array access:

```js
let config = {
  getTimeout: function() { return 3000 }
}

config.getTimeout?.()        // 3000 -- function exists, call it
config.getMissing?.()        // undefined -- function does not exist, no crash

let items = ["a", "b", "c"]
items?.[0]                   // "a" -- optional array access
```

**Combining ?? and ?. together:**

```js
let city = user?.address?.city ?? "City not provided"
```

This reads: try to get the city; if any step does not exist, or if the city is explicitly `null` or `undefined`, use the default.

<!-- 
IMAGE: A flowchart for optional chaining. Start node: "user". Arrow to diamond: "Does user exist?" -- Yes branch continues to "user.address". No branch goes to "Return undefined." Second diamond: "Does user.address exist?" -- Yes branch continues to "user.address.city". No branch goes to "Return undefined." Final box: "Return city value." Caption: "?. checks each step before proceeding. One missing link returns undefined instead of crashing." Style: flat vector, vertical flowchart with diamond decision nodes and rectangular result boxes.
-->

### Let's Define Things Now...

> **Operator:** A symbol or keyword that performs an operation on one or more values (operands) and produces a result.

> **Operand:** The value an operator acts on. In `10 + 3`, both `10` and `3` are operands.

> **Modulo (%):** Returns the remainder after division. `10 % 3` is `1`.

> **Strict Equality (===):** Compares both value and type. No type conversion happens. The correct operator to use for equality checks.

> **Loose Equality (==):** Compares values after converting both sides to the same type. Produces surprising results and should be avoided in favour of `===`.

> **Short-Circuit Evaluation:** The behaviour where `&&` stops at the first falsy value and `||` stops at the first truthy value without evaluating the remaining operands.

> **Nullish Coalescing (??):** Returns the right side only when the left side is `null` or `undefined`. Unlike `||`, it does not treat `0` or `""` as triggering the default.

> **Optional Chaining (?.):** Safely accesses properties and methods on a value that might be `null` or `undefined`. Returns `undefined` instead of throwing a TypeError if any step in the chain is missing.

### Activity: Operator Challenges

1. Solve the following using the operators from this article:

```js
// Tip calculator: given a bill amount and a tip percentage, compute the tip and total
let bill = 850
let tipPercent = 18
// compute: tipAmount and totalAmount

// Leap year: a year is a leap year if divisible by 4,
// but NOT by 100, unless also divisible by 400
let year = 2024
// compute: isLeapYear (should be true)

// FizzBuzz check for a single number (not a loop):
// divisible by both 3 and 5 -> "FizzBuzz", by 3 only -> "Fizz", by 5 only -> "Buzz"
let n = 15
// compute: result using % and &&

// Default value using ??: display 0 as a valid score, but replace null with "Not yet scored"
let playerScore = 0
let display = playerScore ?? "Not yet scored"
console.log(display)    // should print 0

// Safe property access: get the user's subscription plan name without crashing
let userProfile = { account: null }
let planName = userProfile?.account?.plan?.name ?? "Free tier"
console.log(planName)   // "Free tier"
```

2. Predict the output of each before running it. Focus on the `==` vs `===` difference:

```js
console.log(0 == false)
console.log(0 === false)
console.log("" == false)
console.log("" === false)
console.log(null == undefined)
console.log(null === undefined)
console.log(1 == "1")
console.log(1 === "1")
console.log(NaN == NaN)
console.log(NaN === NaN)
```

3. Add the following to `chatbot-logic.js` and run it with Node.js:

```js
function getDisplayName(user) {
  return user?.name ?? "Anonymous"
}

function isValidLength(message, max) {
  return typeof message === "string" && message.length <= max
}

console.log(getDisplayName({ name: "Jai" }))    // "Jai"
console.log(getDisplayName({ name: null }))      // "Anonymous"
console.log(getDisplayName(null))                // "Anonymous"

console.log(isValidLength("Hello", 500))         // true
console.log(isValidLength("", 500))              // true
console.log(isValidLength(null, 500))            // false
```

## What's Coming Next...

That was the complete set of operators you will use daily in JavaScript: arithmetic for calculation, assignment for updating values, comparison for evaluating relationships, logical for combining conditions, and the modern `??` and `?.` for handling the absent data that is inevitable in real applications.

Operators produce values. The next step is using those values to make decisions. In the next article we will cover conditionals: `if`, `else`, the ternary operator, and `switch` -- the tools that let your program take different paths based on what the data says.
