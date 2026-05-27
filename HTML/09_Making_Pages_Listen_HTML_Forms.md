## Introduction

In the very first article of this course, we followed Arjun as he typed a URL into his browser and pressed Enter. That single action, a person providing information to a web application, set off an entire chain of events across the Internet. We spent that whole article looking at what happens after the input. We never paused to think about the input itself.

That URL in the address bar is one kind of user input. A search query is user input. A username and password on a login screen is user input. A message typed into a chat window is user input. Every piece of information a user ever hands to a web application, in one form or another, passes through the same mechanism.

That mechanism is the HTML Form.

A Form is a structured interface on a webpage that collects information from the user, bundles it up, and sends it somewhere for processing. Every search engine, every login screen, every checkout page, every feedback survey, every chat input is built on this one concept.

Forms do exactly three things: they collect input from the user, they provide a way to submit that input, and they define where that input goes and how it gets there. This article is organised around those three responsibilities. Understanding this structure makes every form you will ever build or read feel predictable and logical.

### The form Element: The Container for Everything

Before anything else, the `<form>` element needs to be introduced, because it is the parent container for everything that belongs to a form.

Just as a `<ul>` wraps all the `<li>` items in a list, a `<form>` wraps every input, every label, and every button involved in collecting and submitting information. Nothing in a form exists outside of it.

```html
<form>
  <!-- inputs, labels, buttons all live inside here -->
</form>
```

On its own, an empty `<form>` renders nothing visible. Its behaviour and destination are controlled by two attributes we will cover shortly: `action` and `method`. For now, understand that the `<form>` element is the boundary within which all form interactions happen.

<!-- 
IMAGE: A diagram showing the form element as a large container box. Inside it, three labelled sections: "Inputs" on the left, "Submit Button" on the right, "Where data goes (action + method)" as an arrow pointing out of the box. Caption: "The form element wraps everything. It is the container, the instruction set, and the delivery mechanism all at once." Style: flat vector, clean three-section layout, neutral tones with a border around the outer form container.
-->

### The Three Things Every Form Does

Here is the structure that every HTML Form follows, whether it is a two-field login box or a twenty-field checkout page:

1. **Collect input** from the user using input elements
2. **Provide a submit mechanism** using a button
3. **Define the destination and delivery method** using attributes on the form element itself

We will go through each one now.

### Part 1: Collecting Input from the User

#### The input Element

The `<input>` element is the most fundamental way to collect information from a user in HTML. It is a Void Element, which means it has no closing tag. Everything about it is configured through its attributes.

```html
<input type="text" name="username" placeholder="Enter your name">
```

The `type` attribute is what makes `<input>` so versatile. Setting `type="text"` gives you a single-line text field. But the same `<input>` element, with a different `type` value, becomes a password field, a date picker, a checkbox, a file uploader, and more. We will map all of these in the next article. For now, the text input is our starting point.

The `name` attribute is essential. When a form is submitted, each input's value is sent as a key-value pair. The key is whatever you put in `name`. Without a `name`, the Browser does not include that input's value in the submission at all.

The `placeholder` attribute puts light grey text inside the input field that disappears the moment the user starts typing. It is a hint, not a label.

#### The label Element

Every `<input>` needs a visible, descriptive label. That label is not created by the `placeholder`. The `placeholder` is a hint. The label is the permanent, visible name for the field.

HTML has a dedicated `<label>` element for this purpose.

```html
<label for="username">Your Name</label>
<input type="text" id="username" name="username" placeholder="e.g. Arjun Mehta">
```

The `for` attribute on the `<label>` matches the `id` attribute on the `<input>`. This links them together. When the link is in place, clicking the label text focuses the cursor inside the input field. Screen readers announce the label text when the input is focused, making the form fully accessible. Without this link, a visually impaired user navigating with a screen reader hears nothing meaningful when they land on an input field.

Always pair every `<input>` with a `<label>`. Never use `placeholder` as a substitute for a label.

#### Beyond the Single Line: A Glimpse at Other Input Scenarios

The single-line `<input type="text">` handles names, email addresses, and short responses well. But some situations need something different.

What if the user needs to write a full paragraph? Think about a chatbot message input box. A user might type three or four sentences, hit Enter mid-thought, and keep writing. A single-line input cannot handle that. For this, HTML provides the `<textarea>` element: a resizable, multi-line text area. You can think of it as the difference between a sticky note and a notepad.

What if the user needs to pick from a specific set of options? Say you want to know which AI model they prefer: GPT-3.5, GPT-4, or GPT-4o. You could let them type it in, but then you risk getting "gpt4", "GPT 4", "the new one." Restricting the options is better. HTML provides the `<select>` element with `<option>` children for this. Think of it as a dropdown menu from which the user picks exactly one item from a pre-defined list.

There are other input types too: checkboxes for yes-or-no choices, radio buttons for single selection from a visible group, file uploaders for attachments. We will cover the full syntax and usage of every one of these in the next article. For now, the key takeaway is that collecting input is not limited to a single text field. HTML has purpose-built elements for every kind of user input imaginable.

### Part 2: Submitting the Form

Collecting input means nothing if the user has no way to send it. Every form needs a submission trigger. In HTML, that is the `<button>` element.

#### The button Element

```html
<button type="submit">Send Message</button>
```

The `type` attribute on a `<button>` determines what it does:

`type="submit"` is the default when a button is inside a `<form>`. Clicking it bundles all the form's input values and sends them to the destination defined by the form's `action` attribute. This is the standard form submission button.

`type="button"` does nothing on its own when clicked. No form submission, no reset. It is a blank trigger waiting for JavaScript to give it behaviour. Most interactive buttons that do not submit a form, like "Toggle Dark Mode" or "Show More," are `type="button"` elements with a JavaScript event attached.

`type="reset"` clears every input inside the form back to its default value. Use this sparingly. Most users who accidentally click a reset button on a long form are not happy about it.

```html
<form>
  <label for="message">Your Message</label>
  <input type="text" id="message" name="message" placeholder="Ask Aiden AI anything...">

  <button type="submit">Send</button>
  <button type="reset">Clear</button>
</form>
```

A `<button>` can contain text, icons, or even images. It is a full block element, unlike `<input type="submit">` which also exists but is less flexible. Prefer `<button>` for everything.

<!-- 
IMAGE: A simple rendered form illustration showing a text input labeled "Your Message" with a placeholder, and two buttons side by side: a blue "Send" button (type submit) and a grey "Clear" button (type reset). Caption: "The submit button is what connects the filled form to its destination. Without it, the data goes nowhere." Style: clean browser-render illustration, flat vector, form styled in a card with a subtle border.
-->

### Part 3: Where Does the Data Go

The form element has two attributes that together answer one question: when the user hits Submit, where does the data go and how does it get there?

#### The action Attribute

The `action` attribute specifies the URL where the form data is sent when submitted.

```html
<form action="/send-message">
```

When the user submits this form, the Browser takes all the input values, packages them up, and sends a request to `/send-message`. That path points to a server-side script (built in Node.js, Python, or any other backend language) waiting to receive and process the data.

If `action` is omitted, the Browser submits the form to the current page's URL, which is often not what you want.

#### The method Attribute

The `method` attribute specifies the HTTP method the Browser uses to send the form data. The two values used in HTML forms are `GET` and `POST`, and they behave very differently.

`method="GET"` appends the form data directly to the URL as a query string.

```html
<form action="/search" method="GET">
  <input type="text" name="query" placeholder="Search...">
  <button type="submit">Search</button>
</form>
```

If the user types "language models" and submits, the Browser navigates to `/search?query=language+models`. The data is visible in the URL. This is exactly how search engines work. GET is appropriate when the form is retrieving data, not changing anything, and when the data being submitted is not sensitive.

`method="POST"` sends the form data in the body of the HTTP request, not in the URL.

```html
<form action="/login" method="POST">
  <label for="password">Password</label>
  <input type="password" id="password" name="password">
  <button type="submit">Log In</button>
</form>
```

The data does not appear in the URL. It travels inside the request body, hidden from the browser's address bar and from logs that record URLs. POST is the correct choice for login forms, registration forms, payment forms, and any time you are sending sensitive information or creating something on the server.

A simple way to think about it: GET is for asking a question. POST is for sending an answer.

```html
<form action="/send-message" method="POST">
  <label for="user-message">Your Message</label>
  <input type="text" id="user-message" name="message" placeholder="Ask Aiden AI anything...">
  <button type="submit">Send</button>
</form>
```

This is a complete, functional form. It has a labeled input, a submit button, a destination URL, and an HTTP method. It is the foundation every login page, search bar, and contact form on the Web is built on.

<!-- 
IMAGE: A two-panel illustration. Left panel labeled "GET" shows a browser address bar with the URL "/search?query=language+models" after a form is submitted, with an annotation "Data is visible in the URL." Right panel labeled "POST" shows the same address bar still showing "/login" with no appended data, and a separate "Request Body" box below it containing the form data. Caption: "GET appends data to the URL. POST hides it in the request body. Use GET to retrieve, POST to create or send." Style: flat vector, two-column layout, blue highlight on the GET URL and grey highlight on the POST body box.
-->

### Let's Define Things Now...

> **form:** The parent container element for all form-related elements. Wraps inputs, labels, and buttons. Carries the `action` and `method` attributes that define where and how form data is sent.

> **input:** A void element that creates an interactive field for user input. Its behaviour is determined by the `type` attribute. `type="text"` produces a single-line text field.

> **label:** An element paired with an `<input>` via matching `for` and `id` values. Provides a visible, accessible name for the input field. Clicking the label focuses the input. Required for accessibility.

> **name attribute (on input):** Defines the key for the input's value when the form is submitted. Without it, the Browser excludes that field from the submitted data.

> **placeholder attribute:** Hint text displayed inside an input field when it is empty. Disappears when the user begins typing. Not a substitute for a label.

> **textarea:** A multi-line text input element for longer content. Used when a single-line input is insufficient.

> **select:** A dropdown element that presents a fixed list of options for the user to choose from.

> **button:** An interactive element the user clicks to trigger an action. `type="submit"` submits the form. `type="button"` does nothing without JavaScript. `type="reset"` clears the form.

> **action attribute (on form):** The URL to which the form data is sent on submission.

> **method attribute (on form):** The HTTP method used to send the form data. `GET` appends data to the URL. `POST` sends data in the request body, appropriate for sensitive or server-modifying submissions.

### Activity: Contact Form for the Chatbot Page

Build a simple contact form for your AI chatbot webpage. The form should collect the following:

- The user's full name (single-line text input)
- Their email address (text input for now, we will use `type="email"` in the next article)
- A subject line for their message (text input)
- Their message (a short note for now using a text input, we will switch it to `<textarea>` in the next article)

**Requirements**:
- Every input must have a paired `<label>` using matching `for` and `id` values
- The form must have `action="/contact"` and `method="POST"`
- Include a Submit button with appropriate label text and a Reset button
- Verify in DevTools that all `name` attributes are present on every input

Open the form in the Browser. Click each label text and verify the cursor jumps to the corresponding input. Try filling in the form and hitting Reset. Observe what happens.

## What's Coming Next...

That is the anatomy of any form on the Web: a `<form>` container with `action` and `method`, inputs collected with `<input>` and `<label>`, submitted through a `<button>`.

But we only used `type="text"` for every input in this article. HTML Forms have a remarkably rich set of input types, each purpose-built for a specific kind of data: email addresses, passwords, numbers, dates, file uploads, toggle switches, dropdown menus, and more. Each type comes with built-in browser validation and purpose-appropriate interfaces, especially on mobile.

The next article maps all of them. By the end of it, you will have the complete vocabulary for building any input interface a web application could need.
