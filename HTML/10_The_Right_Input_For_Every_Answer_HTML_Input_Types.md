## Introduction

In the last article we built a complete form using `<input type="text">` for every field. It works. A user can technically type their email address, their age, and their date of birth into a plain text field. The information gets through.

But that experience is rough. On mobile, a plain text keyboard appears for an email field instead of the one with the @ symbol front and centre. A date field gives the user no calendar to pick from, so they have to type it and hope the format matches what the server expects. A password field shows every character as the user types it. A quantity field accepts the letter "k" when only numbers make sense.

HTML already has a solution for every one of these. The `<input>` element supports a wide range of `type` values, each one producing a purpose-built interface with the right keyboard on mobile, the right validation in the browser, and the right visual feedback for the user. This article maps the complete input vocabulary.

### Starting Point: type="text" and the label

Before moving on, here is the baseline again with the full pair that every input needs:

```html
<label for="username">Full Name</label>
<input type="text" id="username" name="username" placeholder="e.g. Arjun Mehta">
```

`type="text"` is the default. A single-line field that accepts any character. Use it when no more specific type applies. The `for`-`id` link between the `<label>` and the `<input>` is what makes the form accessible and is non-negotiable in any form you write.

Two useful attributes that work across most input types:

`value` sets a pre-filled default inside the field when the page loads. Unlike `placeholder`, it does not disappear when the user clicks the field. The user can edit or clear it.

```html
<input type="text" name="country" value="India">
```

`disabled` makes the field visible but completely non-interactive. The user cannot click it, type into it, or submit it. It is greyed out visually.

```html
<input type="text" name="plan" value="Free Tier" disabled>
```

### Text-Based Inputs

These four types look almost identical to a plain text input visually, but each one adds specific browser behaviour.

#### type="email"

```html
<label for="email">Email Address</label>
<input type="email" id="email" name="email" placeholder="you@example.com">
```

On mobile, the keyboard switches to one that shows the @ symbol and .com button prominently. Before submission, the browser automatically checks that the value contains an @ symbol and a domain. If it does not, submission is blocked and the browser shows an error. You get email format validation for free without writing a single line of JavaScript.

You see this on every login and signup screen, from Gmail to GitHub to any SaaS product.

#### type="password"

```html
<label for="password">Password</label>
<input type="password" id="password" name="password" placeholder="At least 8 characters">
```

Every character the user types appears as a filled circle or asterisk. The value is masked visually but still submitted with the form. The browser typically offers to save the value to its password manager.

One useful attribute here is `autocomplete`. Setting `autocomplete="new-password"` tells the browser this is a field for creating a new password, suppressing autocomplete suggestions that belong to a different account.

#### type="tel"

```html
<label for="phone">Phone Number</label>
<input type="tel" id="phone" name="phone" placeholder="+91 98765 43210">
```

On mobile, this triggers the numeric phone keypad instead of the full text keyboard. Unlike `type="email"`, there is no automatic format validation. Phone number formats vary so widely across countries that the browser cannot enforce a single pattern. If you need a specific format, the `pattern` attribute handles that, which we will cover in the Validation section.

#### type="url"

```html
<label for="website">Website</label>
<input type="url" id="website" name="website" placeholder="https://yoursite.com">
```

The browser validates that the entered value begins with a valid protocol like `https://` before allowing submission. Useful on profile pages and anywhere a user is expected to provide a link.

### Numeric and Range Inputs

#### type="number"

```html
<label for="quantity">Quantity</label>
<input type="number" id="quantity" name="quantity" min="1" max="10" step="1" value="1">
```

Produces a numeric field with up and down arrows. The browser blocks non-numeric input. Three key attributes work with it:

`min` and `max` define the acceptable range. The browser refuses to submit a value outside this range.

`step` defines the increment each up or down arrow click applies. For whole numbers use `step="1"`. For prices or measurements you might use `step="0.01"`.

Think of the quantity selector on an Amazon product page. That is a `type="number"` with `min="1"`.

#### type="range"

```html
<label for="temperature">Response Creativity</label>
<input type="range" id="temperature" name="temperature" min="0" max="1" step="0.1" value="0.7">
```

Renders a draggable slider instead of a text field. Uses the same `min`, `max`, and `step` attributes as `type="number"`. The difference is the visual interaction: the user drags rather than types. Use this when precision is less important than quick, intuitive adjustment.

The AI model temperature setting in many tools is a real example of this. The slider runs from 0 (deterministic) to 1 or 2 (creative and unpredictable), and a rough position is all that matters.

<!-- 
IMAGE: A side-by-side comparison of type="number" and type="range". Left shows a number input with up/down arrows and the value "3" from an Amazon-style product card. Right shows a horizontal slider with a handle positioned at 0.7, labeled "Response Creativity" from an AI tool style panel. Caption: "Same data, different interaction model. Pick the one that fits the experience." Style: flat vector, two-panel layout, clean and minimal.
-->

### Date and Time Inputs

#### type="date"

```html
<label for="checkin">Check-In Date</label>
<input type="date" id="checkin" name="checkin" min="2024-01-01">
```

Produces a native date picker. On desktop, clicking the field opens a calendar. On mobile, a scrollable date wheel appears. The submitted value is always in `YYYY-MM-DD` format regardless of how the calendar displays it locally, which makes server-side handling consistent.

This is exactly the check-in and check-out selector on Booking.com and Airbnb. Before `type="date"` existed, developers had to build these calendars themselves with JavaScript libraries.

HTML also provides `type="time"` for time-only fields and `type="datetime-local"` for combined date and time input. They work on the same principle.

### Selection Inputs

#### type="checkbox"

```html
<label>
  <input type="checkbox" name="terms" value="agreed"> I agree to the Terms of Service
</label>
```

A toggle box. When checked, the field is included in the form submission with the value you set. When unchecked, it is not included at all. Use checkboxes for independent yes-or-no choices.

Notice that the `<label>` wraps the entire input here instead of using `for` and `id`. This is a valid alternative syntax. The label area extends to cover the checkbox itself, making the whole line of text clickable.

The `checked` attribute (Boolean, no value needed) pre-checks the box when the page loads.

```html
<input type="checkbox" name="newsletter" checked>
```

Every "subscribe to newsletter" and "remember me" toggle is a checkbox. The Terms of Service agreement on any signup page is a required checkbox.

#### type="radio"

```html
<fieldset>
  <legend>Preferred AI Model</legend>
  <label><input type="radio" name="model" value="gpt-3.5"> GPT-3.5 Turbo</label>
  <label><input type="radio" name="model" value="gpt-4"> GPT-4</label>
  <label><input type="radio" name="model" value="gpt-4o"> GPT-4o</label>
</fieldset>
```

Radio buttons are for selecting exactly one option from a group. The key is the `name` attribute: all radio buttons in the same group must share the same `name` value. The browser automatically ensures that selecting one deselects the others within that group.

`<fieldset>` groups related inputs with a visible border, and `<legend>` provides a caption for the group. Together they are the semantic container for radio groups and related fields.

Think of the payment method selection at checkout: Credit Card, UPI, Net Banking. Only one can be active. That is a radio group.

<!-- 
IMAGE: A two-panel illustration. Left panel shows three checkboxes with labels: "Dark Mode", "Email Notifications", "Auto-Save". All three can be checked independently. Right panel shows a radio group with a fieldset border: "Preferred AI Model" as the legend, with three radio options where only one can be selected at a time. Caption: "Checkboxes for independent choices. Radio buttons for one-from-many selection." Style: flat vector, side-by-side, clean browser-render illustration.
-->

#### type="file"

```html
<label for="upload">Attach a File</label>
<input type="file" id="upload" name="attachment">
```

Renders a "Choose File" button that opens the operating system's file browser. The selected file is included in the form submission.

The `accept` attribute filters which file types the file browser shows. It does not prevent the user from uploading other types entirely, but it narrows the default view.

```html
<!-- Only show image files -->
<input type="file" name="avatar" accept="image/*">

<!-- Only show PDF files -->
<input type="file" name="resume" accept=".pdf">
```

Adding the `multiple` attribute allows the user to select more than one file at once.

```html
<input type="file" name="attachments" multiple>
```

### type="hidden"

```html
<input type="hidden" name="source" value="homepage-cta">
```

A hidden input is not visible on the page and the user cannot interact with it. It submits its value silently alongside the rest of the form. Developers use it to pass metadata to the server, like which version of a page the form was on, a user session token, or a tracking reference. The user never sees it but the server receives it.

### Multi-Line Text: textarea

```html
<label for="message">Your Message</label>
<textarea id="message" name="message" rows="5" placeholder="Describe your issue..."></textarea>
```

Unlike `<input>`, `<textarea>` has an opening and a closing tag. Any text placed between the tags becomes the default value. The user can resize the text area by dragging the corner handle.

`rows` controls the visible height of the field in lines. `cols` controls the width in characters, though width is usually better controlled through CSS.

Use `<textarea>` whenever you expect the user to write more than one line: a message body, a product review, a bio, a chatbot's multi-line message input.

<!-- 
IMAGE: A comparison showing a type="text" input on the left labeled "Subject" (single line, compact) and a textarea on the right labeled "Your Message" (taller, multi-line, with a resize handle in the bottom-right corner). Caption: "textarea is the text input's multi-line sibling. Use it when a sentence is not enough." Style: flat vector, browser-render illustration, clean two-column layout.
-->

### Fixed Option Selection: select and option

```html
<label for="model">Choose a Model</label>
<select id="model" name="model">
  <option value="">-- Select a model --</option>
  <option value="gpt-3.5">GPT-3.5 Turbo</option>
  <option value="gpt-4">GPT-4</option>
  <option value="gpt-4o" selected>GPT-4o</option>
</select>
```

`<select>` creates a dropdown menu. Each `<option>` is one item in the list. The `value` attribute on each `<option>` is what gets submitted. The text between the opening and closing `<option>` tags is what the user sees.

The `selected` attribute on an `<option>` pre-selects it when the page loads.

An empty first option with a prompt text like "-- Select a model --" is a common pattern that forces the user to make a conscious choice rather than accidentally submitting the pre-selected default.

For long lists, `<optgroup>` groups related options under a non-selectable heading:

```html
<select name="timezone">
  <optgroup label="Asia">
    <option value="IST">India Standard Time</option>
    <option value="JST">Japan Standard Time</option>
  </optgroup>
  <optgroup label="Europe">
    <option value="GMT">Greenwich Mean Time</option>
    <option value="CET">Central European Time</option>
  </optgroup>
</select>
```

Adding `multiple` to `<select>` allows the user to hold Ctrl or Cmd and select several options. Use this when the field genuinely allows more than one selection.

### Built-In Validation

One of the most useful features of HTML input types is that many of them validate user input before the form is ever submitted, without any JavaScript. The browser handles it automatically.

#### required

The `required` attribute (Boolean, no value) prevents the form from submitting if the field is empty.

```html
<input type="email" name="email" required>
```

If the user clicks Submit with an empty required field, the browser highlights the field and shows an error message. No JavaScript needed.

#### min, max, minlength, maxlength

`min` and `max` enforce a numeric or date range on `type="number"`, `type="range"`, and `type="date"` inputs.

`minlength` and `maxlength` enforce character count limits on text-based inputs.

```html
<!-- Must be between 18 and 99 -->
<input type="number" name="age" min="18" max="99" required>

<!-- Between 8 and 64 characters -->
<input type="password" name="password" minlength="8" maxlength="64" required>
```

#### pattern

The `pattern` attribute accepts a regular expression. The input value must match the pattern for the form to submit. This is useful for enforcing specific formats.

```html
<!-- Indian mobile number: starts with 6-9, followed by 9 digits -->
<input type="tel" name="phone" pattern="[6-9][0-9]{9}" placeholder="9876543210">
```

Do not go deep into regular expressions yet. Think of `pattern` as a rule you hand to the browser: the value must look like this. The browser checks it and blocks submission if it does not.

#### Type-Based Validation

Several input types validate automatically without any extra attributes. `type="email"` checks for @ and a domain. `type="url"` checks for a protocol. `type="number"` rejects non-numeric characters. This automatic validation is one of the strongest reasons to use the correct `type` rather than defaulting to `type="text"` for everything.

<!-- 
IMAGE: A form screenshot mockup showing three inputs. The first is a type="email" field with "arjun@" entered, showing a red browser tooltip error "Please include an '@' and a domain name." The second is a type="number" field highlighted red, with browser tooltip "Value must be between 18 and 99." The third is a required text field left empty with the tooltip "Please fill in this field." Caption: "HTML's built-in validation catches errors before they ever leave the browser." Style: browser-render illustration with red error highlights and tooltip bubbles, clean educational layout.
-->

### Let's Define Things Now...

> **type="email":** An input that validates email format automatically and shows an email-optimised keyboard on mobile.

> **type="password":** An input that masks characters as the user types and integrates with browser password managers.

> **type="number":** An input that accepts only numeric values. Supports `min`, `max`, and `step` for range control.

> **type="range":** A draggable slider input for numeric values where approximate selection is sufficient.

> **type="date":** An input that renders a native date picker. Always submits values in YYYY-MM-DD format.

> **type="checkbox":** A toggle input for independent yes-or-no choices. Omitted from submission when unchecked.

> **type="radio":** A single-selection input within a group. All radios sharing the same `name` form one group.

> **type="file":** An input that opens the file browser. The `accept` attribute filters visible file types.

> **type="hidden":** An invisible input that passes metadata values to the server silently.

> **textarea:** A multi-line text input element. Uses `rows` for height. Content between tags is the default value.

> **select / option:** A dropdown menu element. Each `<option>` has a visible label and a submitted `value`. `<optgroup>` groups related options under a heading.

> **required:** A Boolean attribute that prevents form submission if the field is empty.

> **pattern:** An attribute that provides a regular expression the input value must match before submission is allowed.

> **minlength / maxlength:** Enforce minimum and maximum character counts on text-based inputs.

> **min / max:** Enforce a numeric or date range on number, range, and date inputs.

### Activity: Chatbot Settings Form

Build a settings panel for your AI chatbot page. The form should include the following:

- A text input for the bot's display name (`required`, `maxlength="30"`)
- A `select` dropdown to choose the AI model (GPT-3.5 Turbo, GPT-4, GPT-4o)
- A `type="range"` slider for response creativity, from 0 to 1 in 0.1 steps
- A `type="number"` input for max response length in tokens (`min="100"`, `max="4000"`)
- A `type="email"` input for the account email (`required`)
- A `type="password"` input for API key (`minlength="20"`)
- A `type="checkbox"` for enabling dark mode (pre-checked using the `checked` attribute)
- A `type="radio"` group for selecting response language (English, Hindi, Both)
- A `<textarea>` for a custom system prompt, with `rows="4"`
- A Submit button

Try submitting the form with the email field blank and observe the browser validation. Then enter an invalid email and try again. Try entering a number outside the token range. None of these should submit.

## What's Coming Next...

With forms covered completely, our HTML pages can now collect every type of information a user could offer: text, numbers, dates, files, choices, and toggles. Forms are the last major piece of the interactive input side of HTML.

But so far, every page we have built contains only text, links, images, and form elements. The Web also carries video, audio, and embedded content from other sources. YouTube embeds, Spotify player widgets, Google Maps inside a contact page, all of that is HTML too.

The next article is about media elements: how to embed video and audio natively in HTML, and how the `<iframe>` element brings external content directly into your page. This is how you add multimedia and third-party content to any website.
