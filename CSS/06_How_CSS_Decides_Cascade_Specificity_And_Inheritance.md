## Introduction

The last five articles were about doing: picking a unit, applying a color, choosing a font. Every property we covered had a clear, direct effect on one element. Write the rule, see the result.

This article is different in tone. It is less about what CSS does to elements and more about how CSS itself works as a system. Before we move into layout, there are three concepts that explain the engine running underneath every stylesheet you will ever write. Understanding them will save you from hours of confusion as your CSS grows in size and complexity.

Consider what happens in a real project. A stylesheet might have two hundred rules. Several of them may target the same element. The browser's own default stylesheet is also targeting that element. An inline `style` attribute may be too. All of these rules are trying to tell the browser what the element should look like.

The browser does not panic. It has a defined, deterministic process for resolving every conflict. That process is what this article explains.

### How a Stylesheet Works as a Whole

Before CSS specificity or inheritance, understand the bigger picture: when a browser renders a page, it collects style rules from multiple sources simultaneously.

The first source is the **user-agent stylesheet**, the browser's built-in defaults we mentioned in the units article. It gives `<h1>` a large font size, `<a>` a blue underline, `<body>` a small margin. These rules are always present, always the starting point.

The second source is the **author stylesheet**: the CSS files you write and link to the page. Every rule in every `.css` file you load contributes to this pool.

The third source is **inline styles**: the `style` attribute written directly on individual HTML elements.

All of these rules land in the same pool. The browser then applies them all at once, resolving any conflicts using a consistent system. The result is the **computed style** for each element: the final value of every CSS property on that element after every rule has been considered.

Open DevTools on any element and look at the Computed tab. What you see is the end result of this entire process for that one element.

### The Cascade: CSS Has a Conflict Resolution System

When two or more rules target the same element and try to set the same property to different values, one of them has to win. The cascade is the mechanism that decides which one.

The cascade evaluates three factors in order of importance:

#### Factor 1: Origin

Where did the rule come from?

Rules from the author stylesheet (your CSS) have higher priority than rules from the user-agent stylesheet (browser defaults). This is why your `font-size: 1rem` on `<p>` overrides the browser's default paragraph size: your stylesheet has higher origin priority.

Inline styles have the highest origin priority of all. A `style` attribute on an HTML element overrides anything in any external or internal stylesheet targeting that same element and property.

```css
/* In style.css */
p {
  color: #94a3b8;  /* overrides browser default */
}
```

```html
<!-- Inline style overrides the stylesheet -->
<p style="color: red;">This text is red regardless of the stylesheet.</p>
```

#### Factor 2: Specificity

If two rules from the same origin both target the same element, the origin factor cannot separate them. The cascade then looks at specificity: how precisely does each selector target the element?

We will go into specificity in depth in the next section. For now, understand that it is the second tiebreaker.

#### Factor 3: Source Order

If two rules have the same origin and the same specificity, the one that appears later in the stylesheet wins. This is the last tiebreaker.

```css
h1 {
  color: blue;
}

h1 {
  color: red;  /* this wins: same origin, same specificity, appears later */
}
```

Source order is why the order of your rules in a stylesheet matters. It is also why placing a `<link>` to a reset stylesheet before your own stylesheet ensures your rules win over the reset's rules.

<!-- 
IMAGE: A flowchart diagram showing the cascade decision process. A question "Which rule wins?" at the top branches first to "Different origin?" with arrows to "Higher origin wins" (labeled "browser default vs author stylesheet = author wins"). If same origin, branches to "Different specificity?" with an arrow to "Higher specificity wins". If same specificity, branches to "Source order: later rule wins." Each step is clearly labeled. Style: flat vector flowchart, clean decision tree layout, neutral background.
-->

### Specificity: Not All Selectors Are Equal

The cascade's second factor requires a closer look. Specificity is a scoring system that measures how precisely a selector targets an element. The more targeted a selector is, the higher its specificity score, and the more likely it is to win a conflict.

Every selector earns points in three categories, written as three numbers separated by dashes: **(ID, Class, Type)**.

**Type selectors** (element selectors like `p`, `h1`, `nav`) score **0-0-1** each.

**Class selectors** (`.message`), **pseudo-classes** (`:hover`, `:focus`), and **attribute selectors** (`[type="text"]`) score **0-1-0** each.

**ID selectors** (`#chat-window`) score **1-0-0** each.

**Inline styles** sit above the scoring system entirely. They always win over any selector.

To compare two selectors, compare their scores column by column from left to right:

```css
/* Selector scores */
p                      /* 0-0-1 */
.message               /* 0-1-0 */
p.message              /* 0-1-1 (one type + one class) */
#chat-window           /* 1-0-0 */
#chat-window .message  /* 1-1-0 (one ID + one class) */
nav a:hover            /* 0-1-2 (one pseudo-class + two types) */
```

Consider a conflict:

```css
/* Rule A */
p {
  color: #94a3b8;  /* specificity: 0-0-1 */
}

/* Rule B */
.message-text {
  color: #f1f5f9;  /* specificity: 0-1-0 */
}
```

Both target a `<p class="message-text">`. Rule B wins because `0-1-0` beats `0-0-1`. The class selector is more specific than the type selector.

The columns never roll over into each other. Ten type selectors (`0-0-10`) do not beat one class selector (`0-1-0`). The comparison is column-first, left to right.

```css
/* Ten type selectors combined */
div div div div div div div div div p {
  color: blue;  /* 0-0-10: still loses to... */
}

/* One class selector */
.message {
  color: red;   /* 0-1-0: wins */
}
```

<!-- 
IMAGE: A specificity comparison diagram. Three columns labeled "ID", "Class", "Type". Three rows showing example selectors: "p" scoring 0-0-1, ".message" scoring 0-1-0, "#chat-window .message p" scoring 1-1-1. A versus arrow between two selectors showing how the scores are compared column by column from left to right. Caption: "Specificity is compared left column first. One ID beats any number of classes." Style: flat vector, three-column score grid, clean annotation.
-->

#### The !important Declaration

`!important` is a CSS keyword that can be appended to any property value. It overrides all other considerations: specificity, source order, everything.

```css
.message-text {
  color: #f1f5f9 !important;
}
```

Any rule targeting `color` on `.message-text`, regardless of how specific it is or where it appears, will be overridden by this declaration.

`!important` exists as a legitimate last resort. Its intended use cases are narrow: overriding third-party CSS you cannot modify, applying utility classes that must always win, or accessibility overrides that must not be suppressed.

In practice, `!important` is almost always a symptom of a specificity architecture problem. If you find yourself writing `!important` to make a rule work, the real fix is usually to write a more specific selector instead. Using `!important` does not solve the conflict: it amplifies it, because the only way to override an `!important` declaration is with another `!important` declaration of equal or higher specificity, which escalates into an arms race that becomes impossible to maintain.

**The rule: avoid `!important`. Write more specific selectors instead.**

### Inheritance: Styles That Pass Down

The cascade explains what happens when rules conflict. Inheritance explains something different: why setting a property on a parent element automatically affects its children without writing any rules for those children.

```css
body {
  color: #f1f5f9;
  font-family: 'Inter', sans-serif;
}
```

With only these two rules, every heading, paragraph, link, list item, span, and label on the entire page inherits white text and the Inter font. You did not write a single rule for any of those elements. The property values cascaded down through the document tree.

Think of it like passing down traits through a family. A parent has blue eyes. The children may inherit that trait. The grandchildren may inherit it from them. But the parent's specific job title does not pass to the children: that is a personal attribute, not a heritable one. CSS works the same way.

**Inherited properties** are primarily text-related: `color`, `font-family`, `font-size`, `font-weight`, `font-style`, `line-height`, `letter-spacing`, `text-align`, `text-transform`, `text-indent`, `visibility`, `cursor`.

These properties inherit because it makes practical sense. A paragraph inside a `<section>` should use the same font as the section unless explicitly overridden. Writing font rules on every single element would be exhausting.

**Non-inherited properties** are primarily layout and box-related: `margin`, `padding`, `border`, `background`, `width`, `height`, `display`, `position`, `flex`, `grid`, `overflow`.

These do not inherit because it would be counterproductive. If `padding: 20px` on a `<div>` was inherited by every nested element inside it, all nesting would compound padding infinitely. If `background-color: navy` on `<body>` was inherited by every `<div>` and `<p>` inside, setting any background would be nearly impossible to control.

The distinction between inherited and non-inherited properties is not arbitrary. It reflects the difference between properties that define the visual character of content (which should flow through a document) and properties that define the structural space of individual boxes (which should be specific to each element).

```css
/* Setting these on body saves writing them on every element */
body {
  color: #f1f5f9;            /* inherited by all text descendants */
  font-family: 'Inter', sans-serif;  /* inherited by all text descendants */
  line-height: 1.6;          /* inherited by all text descendants */
}

/* These do NOT inherit: each element controls its own */
.message-bubble {
  padding: 12px 16px;        /* only this element */
  background-color: #1e293b; /* only this element */
  border-radius: 12px;       /* only this element */
}
```

#### Controlling Inheritance Explicitly

CSS provides three keyword values that give explicit control over inheritance for any property:

`inherit` forces a property to inherit from its parent, even if it would not normally do so.

```css
.message-bubble a {
  color: inherit;  /* use the bubble's text color instead of browser's default link blue */
}
```

`initial` resets the property to its original value as defined by the CSS specification, not the browser's default, not the inherited value.

```css
.override {
  color: initial;  /* resets to the CSS spec's initial value, which is typically black */
}
```

`unset` is context-sensitive: if the property naturally inherits, it acts like `inherit`. If the property does not naturally inherit, it acts like `initial`. It is a convenient shorthand for "restore to what this property would be if this rule did not exist."

```css
.reset {
  color: unset;      /* naturally inherits: acts like inherit */
  padding: unset;    /* does not naturally inherit: acts like initial */
}
```

### Let's Define Things Now...

> **Cascade:** The algorithm CSS uses to resolve conflicts when multiple rules target the same element and property. Evaluates origin, specificity, and source order in that priority order.

> **User-Agent Stylesheet:** The browser's built-in default stylesheet that is always present, forming the baseline below the author's CSS.

> **Computed Style:** The final value of every CSS property on a given element after the cascade has resolved all applicable rules. Visible in the DevTools Computed tab.

> **Specificity:** A scoring system that determines which selector wins when multiple rules conflict. Scored as (ID, Class, Type). Columns are compared left to right.

> **!important:** A CSS keyword that overrides the cascade's normal specificity and source-order evaluation. Treat as a last resort; prefer writing more specific selectors.

> **Inheritance:** The mechanism by which certain CSS properties on a parent element are automatically applied to its descendants without being explicitly set on them.

> **Inherited Properties:** Properties that pass from parent to child by default. Primarily text-related: color, font-family, line-height, etc.

> **Non-Inherited Properties:** Properties that do not pass from parent to child by default. Primarily layout-related: margin, padding, border, background, etc.

> **inherit:** A keyword value that forces a property to inherit from its parent, regardless of whether it normally would.

> **initial:** A keyword value that resets a property to its CSS specification-defined initial value.

> **unset:** A keyword value that acts like inherit for naturally-inherited properties and like initial for non-inherited ones.

### Activity: Specificity Showdown

Open a blank HTML file with the following elements and a linked stylesheet:

```html
<div id="container" class="wrapper">
  <p class="message">First paragraph</p>
  <p class="message highlight">Second paragraph</p>
</div>
```

Write the following conflicting CSS rules and predict which color each paragraph will render before opening the browser:

```css
p { color: blue; }
.message { color: green; }
#container p { color: orange; }
div.wrapper .message { color: purple; }
.message.highlight { color: red; }
```

Calculate the specificity score for each rule targeting each paragraph, then verify your predictions in the browser.

Next, remove all color rules. Add `color: white` only to `#container`. Observe that both paragraphs inherit white text without any rule targeting them directly. Then add `color: inherit` to a link inside one of the paragraphs and observe that it picks up the white color instead of the browser's default link blue.

Finally, add one `!important` to any rule and watch it override specificity entirely. Then try to override it without using another `!important`, and observe that you cannot without restructuring the selector.

## What's Coming Next...

We have now covered the complete foundation of CSS: how to connect it to HTML, how to select elements, how to express sizes and colors, how to style text, and how the engine resolves every decision it makes.

Everything so far has treated elements as self-contained units. We have styled individual elements without thinking about how they relate to the space around them, the space inside them, or the space between them and their neighbours.

That changes in the next section. We move into the Box Model: CSS's fundamental model for how every element occupies space on the page, how padding and margin work, how borders are placed, and how the browser calculates the final rendered size of everything it draws. It is the foundation of every layout you will ever build.
