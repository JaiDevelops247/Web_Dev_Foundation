## Introduction

One of the fundamental things that separates a programming language from a markup language like HTML is the ability to make decisions.

HTML describes what a page looks like. It cannot ask questions. It cannot check a condition and choose between two different outcomes. Every HTML element you write will always be rendered exactly as written, regardless of any circumstances.

A programming language can say: perform this action only when a certain condition is met. Otherwise, do something else entirely.

This is what makes applications intelligent rather than static. Consider how often real web products make decisions:

- A user tries to submit a login form. Check if both the email and password fields are filled in. If they are, send the data to the server. If either is empty, show an error message instead.
- A product page loads. Check if the item is in stock. If it is, show an "Add to Cart" button. If it is not, show a "Notify Me" button.
- A user completes a quiz. Check their final score. If they scored above 90, show a "Distinction" badge. Between 60 and 90, show "Pass." Below 60, show "Try Again."
- A chatbot receives a message. Check whether the message is a question, a help request, or a general statement. Send a different type of response depending on which category it falls into.

None of these behaviours can be written in HTML. They require logic that can evaluate a condition and branch into different paths. In JavaScript, this is done using **conditionals**.

### The if Statement

The `if` statement is the most fundamental conditional. It says: if this condition is true, run this block of code.

**Syntax:**

```js
if (condition) {
  // code to run when condition is true
}
```

The condition goes inside the parentheses. It can be any expression that evaluates to a truthy or falsy value. The code inside the curly braces only runs when the condition is truthy. If the condition is falsy, the block is skipped entirely.

```js
let isLoggedIn = true

if (isLoggedIn) {
  console.log("Welcome back!")
}
// prints: Welcome back!
```

```js
let isLoggedIn = false

if (isLoggedIn) {
  console.log("Welcome back!")
}
// prints nothing -- the condition is false, the block is skipped
```

**Common uses in web development:**

```js
// Show a badge only when there are unread notifications
let unreadCount = 5
if (unreadCount > 0) {
  console.log("Show notification badge:", unreadCount)
}

// Prevent form submission if the input is empty
let userInput = ""
if (!userInput) {
  console.log("Please fill in the required field")
}

// Run setup code only after the page has loaded
let pageReady = true
if (pageReady) {
  console.log("Initialize the chatbot interface")
}
```

<!-- 
IMAGE: A flowchart for the if statement. A diamond labeled "Condition true?" has two arrows. The "Yes" arrow goes right into a box labeled "Run the code block." The "No" arrow goes down and bypasses the block entirely, continuing to "Rest of program." Caption: "The if statement runs its block only when the condition is truthy. False means skip." Style: flat vector, vertical flowchart with a diamond decision node and two clearly labeled paths.
-->

### The if-else Statement

The `if` statement handles one case: what to do when the condition is true. But most real decisions have two sides. What happens when the condition is false matters just as much.

`if-else` adds a second branch that runs when the condition is false.

**Syntax:**

```js
if (condition) {
  // runs when condition is true
} else {
  // runs when condition is false
}
```

Exactly one of the two blocks will run. Never both, never neither.

```js
let isInStock = true

if (isInStock) {
  console.log("Show: Add to Cart button")
} else {
  console.log("Show: Notify Me button")
}
```

```js
let userAge = 16

if (userAge >= 18) {
  console.log("Access granted")
} else {
  console.log("Access denied: must be 18 or older")
}
```

**Extending to multiple conditions with else if:**

When there are more than two possible outcomes, chain `else if` blocks between the `if` and the final `else`. JavaScript checks each condition from top to bottom and runs the first block whose condition is true. Once a match is found, the rest are skipped.

```js
let score = 74

if (score >= 90) {
  console.log("Distinction")
} else if (score >= 75) {
  console.log("Merit")
} else if (score >= 60) {
  console.log("Pass")
} else {
  console.log("Try Again")
}
// prints: Pass
```

The order matters here. If the conditions were reversed (checking `>= 60` first), a score of 95 would match the first condition and print "Pass" instead of "Distinction." Always write `else if` chains from the most specific condition to the most general.

<!-- 
IMAGE: A flowchart with four stacked decision diamonds, each labeled with a condition: "score >= 90?", "score >= 75?", "score >= 60?", and a final "else" box. Each diamond has a "Yes" arrow going right to a result box (Distinction, Merit, Pass, Try Again) and a "No" arrow going down to the next diamond. An annotation on the side reads "Only the first matching branch runs. All others are skipped." Caption: "else if chains evaluate top to bottom and stop at the first match." Style: flat vector, vertically stacked flowchart with clearly labeled branches.
-->

### The Ternary Operator

The `if-else` pattern for choosing between two values is so common that JavaScript has a shorthand for it. The ternary operator packs a condition and two outcomes into a single line.

**Syntax:**

```js
condition ? valueIfTrue : valueIfFalse
```

Read it as: "if condition, then valueIfTrue, otherwise valueIfFalse."

```js
// if-else version
let accessLevel
if (isAdmin) {
  accessLevel = "Admin"
} else {
  accessLevel = "User"
}

// ternary version -- same result, one line
let accessLevel = isAdmin ? "Admin" : "User"
```

```js
let cartCount = 3
let label = cartCount === 1 ? "item" : "items"
console.log(`${cartCount} ${label} in cart`)    // "3 items in cart"
```

```js
let isOnline = true
let statusColor = isOnline ? "green" : "gray"
```

**When to use the ternary vs if-else:**

Use the ternary when you are selecting between two simple values to assign to a variable or return from a function. It is a one-expression tool.

Do not use the ternary for complex logic, multiple statements, or side effects:

```js
// Too complex for ternary -- use if-else instead
isAdmin
  ? (sendAdminEmail(), logAccess(), redirectToDashboard())  // messy
  : showErrorPage()
```

If you find yourself putting multiple operations inside a ternary, that is a signal to go back to `if-else`.

### The switch Statement

Imagine you need to check a single variable against ten possible values. An `else if` chain handles this, but it becomes long and repetitive:

```js
if (day === "Monday") {
  console.log("Weekday")
} else if (day === "Tuesday") {
  console.log("Weekday")
} else if (day === "Wednesday") {
  console.log("Weekday")
} else if (day === "Thursday") {
  console.log("Weekday")
} else if (day === "Friday") {
  console.log("Weekday")
} else if (day === "Saturday") {
  console.log("Weekend")
} else if (day === "Saturday") {
  console.log("Weekend")
}
```

When you are matching one variable against many specific known values, `switch` is a cleaner alternative.

**Syntax:**

```js
switch (expression) {
  case value1:
    // code for value1
    break
  case value2:
    // code for value2
    break
  default:
    // code when no case matches
}
```

**Keywords explained:**

`switch` evaluates the expression once and compares it against each `case` value using strict equality (`===`).

`case` defines a value to match against. If the expression matches this value, execution starts here.

`break` exits the switch block after the matching case runs. Without `break`, execution continues into the next case regardless of whether it matches. This is called fall-through.

`default` runs when no case matches. It is the equivalent of `else` in an `if-else` chain.

```js
let day = "Saturday"

switch (day) {
  case "Monday":
  case "Tuesday":
  case "Wednesday":
  case "Thursday":
  case "Friday":
    console.log("Weekday")
    break
  case "Saturday":
  case "Sunday":
    console.log("Weekend")
    break
  default:
    console.log("Unknown day")
}
// prints: Weekend
```

Notice the stacked cases with no code between them. This is intentional fall-through being used on purpose: Monday through Friday all fall through to the same "Weekday" output. Saturday falls through to Sunday, and both reach "Weekend."

**Unintentional fall-through:**

If you forget a `break`, the consequences are immediate:

```js
let grade = "B"

switch (grade) {
  case "A":
    console.log("Excellent")
    // forgot break
  case "B":
    console.log("Good")
    // forgot break
  case "C":
    console.log("Average")
    break
  default:
    console.log("Below average")
}
// grade is "B", so execution starts at case "B"
// prints: Good
// prints: Average  <-- also runs because break was missing
```

Execution starts at the matching case and then continues into every subsequent case until a `break` is found or the switch block ends. Always include `break` unless you specifically intend the fall-through.

<!-- 
IMAGE: A diagram showing a switch block with four cases (A, B, C, default). Each case has a small arrow pointing down. Case A and B have no break, shown with a dashed continuation arrow flowing down into the next case. Case C has a solid stop-sign style "break" symbol that terminates the flow. A callout annotation: "Without break, execution falls through into the next case." An adjacent column shows the same diagram with break present in every case, each with a solid exit arrow. Caption: "break exits the switch. Missing it causes fall-through into the next case." Style: flat vector, two-column switch diagram showing with and without break.
-->

### Which One to Use and When

| Construct | Best used when |
|---|---|
| `if` | One condition, one outcome |
| `if-else` | Two outcomes (true path and false path) |
| `else if` chain | Multiple conditions checking different expressions or ranges |
| Ternary (`?:`) | Choosing between two values in a single expression or assignment |
| `switch` | Matching one variable against many specific known values |

**Quick decision guide:**

If you are checking a range (`>= 60`, `< 100`), use `else if`. Switch cannot handle ranges cleanly.

If you are matching a single value against a fixed list of known options (day of the week, status code, user role), use `switch`. It is cleaner than a long `else if` chain for this case.

If you just need to pick one of two values for a variable, use the ternary. One line is better than six.

If the logic involves multiple steps or side effects, use `if-else`. Ternary and switch are not built for that.

### Let's Define Things Now...

> **Conditional:** A construct that runs different code depending on whether a condition is true or false.

> **if statement:** Runs a block of code only when its condition is truthy. Skips the block when the condition is falsy.

> **else:** The block that runs when the if condition is false. Exactly one of if or else will run.

> **else if:** An additional condition checked only when all previous conditions were false.

> **Ternary Operator (?:):** A single-expression shorthand for if-else. Syntax: `condition ? valueIfTrue : valueIfFalse`. Best for simple value selection.

> **switch:** A construct that matches one expression against multiple specific values using strict equality. Uses `case`, `break`, and `default`.

> **Fall-through:** The behaviour in switch where execution continues into the next case after the matched one if `break` is missing. Can be intentional (grouping cases) or a bug (forgetting break).

> **break:** A keyword that exits the switch block immediately. Required after each case unless fall-through is intended.

### Activity: Conditional Practice

1. Write a `gradeClassifier(score)` function using `if-else if` that takes a score from 0 to 100 and returns a grade:

```
90 and above  ->  "A"
75 to 89      ->  "B"
60 to 74      ->  "C"
45 to 59      ->  "D"
below 45      ->  "F"
```

Test it with scores: 95, 80, 67, 50, 30.

2. Write a `getDayType(day)` function using `switch` that takes a day name as a string and returns `"Weekend"` or `"Weekday"`. Use intentional fall-through to group the cases.

3. Rewrite the following `if-else` assignments as ternary expressions:

```js
// Original 1
let buttonText
if (isSubmitting) {
  buttonText = "Sending..."
} else {
  buttonText = "Send"
}

// Original 2
let userLabel
if (isAdmin) {
  userLabel = "Admin"
} else {
  userLabel = "Member"
}

// Original 3
let discountedPrice
if (isMember) {
  discountedPrice = price * 0.9
} else {
  discountedPrice = price
}
```

4. Deliberately write a switch with a missing `break` on one case, predict which extra lines will print, then run it to confirm.

5. Add the following to `chatbot-logic.js` and run it with Node.js:

```js
function classifyMessage(message) {
  if (!message || message.trim() === "") {
    return "empty"
  } else if (message.includes("?")) {
    return "question"
  } else if (message.toLowerCase().startsWith("help")) {
    return "help-request"
  } else {
    return "statement"
  }
}

function getBotResponse(category) {
  switch (category) {
    case "question":
      return "That's a great question! Let me think about that."
    case "help-request":
      return "I'm here to help. What do you need?"
    case "empty":
      return "It looks like you didn't type anything. Try again?"
    default:
      return "Got it! Tell me more."
  }
}

let messages = ["How are you?", "help me with my order", "", "I like this chatbot"]

messages.forEach(function(msg) {
  let category = classifyMessage(msg)
  let response = getBotResponse(category)
  console.log(`User: "${msg}"`)
  console.log(`Bot [${category}]: ${response}`)
  console.log()
})
```

## What's Coming Next...

We are almost done with the pillars of logic building. Variables store data. Operators perform operations on that data. Conditionals make decisions based on the results. There is one pillar left: repetition.

Most real tasks involve doing something more than once. Checking every item in a list, sending a notification to every user, displaying every message in a conversation. Writing the same code ten times for ten items is not programming, it is copying. The next article covers loops: the tools that let you repeat an operation as many times as needed without repeating your code.
