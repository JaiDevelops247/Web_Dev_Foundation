## Introduction

Making a webpage responsive comes down to two questions. Answer both, and you know exactly what to write.

**Question 1: At what point does the current design stop working?**

A three-column card grid looks fine at 1200px. At 600px it is cramped. At 375px the cards are unusable. There is a point somewhere between 600px and 375px where the three-column layout breaks. That point is where the design needs to change.

**Question 2: What exactly needs to change when that point is reached?**

The three-column grid might need to become a single column. The sidebar might need to hide. The font sizes might need to reduce. The navigation might need to collapse. These are the specific CSS changes that make the page work at the new size.

The first question tells you **when** to apply a change. The second question tells you **what** to change. Together, they form a responsive rule. And the CSS mechanism that implements responsive rules is the **media query**.

### What Is a Media Query?

A **media query** is a conditional block in CSS. Styles written inside a media query are applied by the browser only when a specified condition about the viewport or device is true. When the condition is false, the browser skips that block entirely.

```css
@media (min-width: 768px) {
  /* These styles only apply when the viewport is 768px wide or more */
  .features {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

Outside this block, `.features` has whatever styles you set before it. Inside the block, the browser applies `grid-template-columns: repeat(3, 1fr)` on top of those styles, but only when the viewport is at least 768px wide. Resize the browser below 768px and those grid styles disappear.

### The Syntax

A media query always starts with the `@media` keyword, followed by a condition in parentheses. Any valid CSS rules can go inside the curly braces.

```css
@media (condition) {
  /* CSS rules that apply only when the condition is true */
}
```

The most common condition for responsive design is `min-width`, which checks whether the viewport is at or above a certain width:

```css
@media (min-width: 600px) {
  /* Applies at 600px and above */
}

@media (min-width: 1024px) {
  /* Applies at 1024px and above */
}
```

You can also specify a media type before the condition. The two most common are `screen` (monitors, phones, tablets) and `print` (the page when sent to a printer):

```css
@media screen and (min-width: 768px) {
  /* Only for screens, 768px and above */
}

@media print {
  /* Only when the user prints the page */
  .navbar, .sidebar {
    display: none;   /* hide navigation when printing */
  }
}
```

When no media type is specified, the condition applies to all media types by default. Most responsive CSS omits the media type:

```css
@media (min-width: 768px) {
  /* All media types, 768px and above */
}
```

### The Cascade and Why Media Queries Go at the Bottom

Media queries work through the normal cascade. A rule inside a media query block has the same specificity as it would outside it. When the media query condition is true, the rule enters the cascade and follows the same source-order rules as everything else.

This has an important implication for where media queries should be placed in the stylesheet.

Consider this:

```css
/* Placed first */
@media (min-width: 768px) {
  .navbar {
    flex-direction: row;
  }
}

/* Placed second */
.navbar {
  flex-direction: column;
}
```

The second `.navbar` rule comes later in the source and has the same specificity, so it always wins, even when the media query condition is true. The media query style is immediately overridden and never takes effect.

The correct order:

```css
/* Base style first */
.navbar {
  flex-direction: column;
}

/* Media query after — its rules are later in the source */
@media (min-width: 768px) {
  .navbar {
    flex-direction: row;   /* this wins at 768px and above */
  }
}
```

This is why media queries are always placed after the base styles they override. In a mobile-first stylesheet, the base styles are at the top, and media queries follow in ascending order of their breakpoint values.

<!-- 
IMAGE: A visual diagram of a CSS file structure. Three sections labeled from top to bottom: "Base styles (mobile, no condition)" at the top in one color, "Tablet overrides @media (min-width: 768px)" in the middle in another color, "Desktop overrides @media (min-width: 1280px)" at the bottom in a third color. A downward arrow on the side labeled "Source order — later rules win at equal specificity." Caption: "Mobile-first stylesheet structure: base at top, media queries in ascending order below." Style: flat vector, three stacked code-block sections with color-coded backgrounds and labels.
-->

### Mobile-First with min-width

In a mobile-first stylesheet, the base CSS applies to all screen sizes with no condition. Media queries then add layout changes at progressively larger widths using `min-width`.

```css
/* Base: applies to all screens, starting from the smallest */
.features {
  display: grid;
  grid-template-columns: 1fr;   /* single column on mobile */
  gap: 16px;
}

/* Tablet and above */
@media (min-width: 768px) {
  .features {
    grid-template-columns: repeat(2, 1fr);   /* two columns */
    gap: 20px;
  }
}

/* Desktop and above */
@media (min-width: 1280px) {
  .features {
    grid-template-columns: repeat(3, 1fr);   /* three columns */
    gap: 24px;
  }
}
```

The browser reads these in order. At 375px, only the base rules apply: one column. At 900px, the base runs first, then the 768px block runs on top: two columns. At 1400px, the base runs, then the 768px block, then the 1280px block: three columns. Each breakpoint adds to what came before.

<!-- 
IMAGE: A three-stage browser resize illustration. Stage 1 (375px mobile): the features section shows one card per row in a single column. Stage 2 (900px tablet): the same section shows two cards per row. Stage 3 (1440px desktop): the same section shows three cards per row. Below each stage, the active CSS is shown: base only, base plus first media query, base plus both media queries. Caption: "Each breakpoint adds a layer. At any given width, all matching media queries are active simultaneously." Style: three browser windows at different widths showing the card grid adapting.
-->

### Breakpoints: Based on Content, Not Devices

A breakpoint is the viewport width at which a media query activates and the design changes. Choosing the right breakpoints is one of the most important decisions in responsive implementation.

The wrong approach: choosing breakpoints based on specific device names. "I need a breakpoint for iPhone 14, one for iPad Pro, one for Galaxy S22." Device screen sizes change with every product release and vary across manufacturers. A breakpoint chosen for a specific device today is outdated next year.

The right approach: choose breakpoints where your content breaks.

Open the page in a browser. Drag the window narrower slowly. Watch the design. At some point something becomes uncomfortable: the columns get too cramped, the text too small to scan, the navigation too tight. That point, wherever it falls, is where a breakpoint belongs. The content tells you when to change. The device name is irrelevant.

That said, common reference breakpoints exist because content tends to break at similar points across most designs. These are not rules, they are starting references:

```css
/* Large phones in landscape */
@media (min-width: 480px) { }

/* Tablets */
@media (min-width: 768px) { }

/* Small desktops and laptops */
@media (min-width: 1024px) { }

/* Large desktops */
@media (min-width: 1280px) { }
```

Most interfaces only need two or three breakpoints. Adding more breakpoints does not make a design more responsive. Adding the right breakpoints, where the content actually breaks, does.

<!-- 
IMAGE: A horizontal viewport width scale from 0px to 1440px. Four labeled markers appear at 480px, 768px, 1024px, and 1280px. Above each marker, a short description: "480px: large phones", "768px: tablets", "1024px: small desktop", "1280px: large desktop." The space between 0 and 480px is labeled "Mobile base styles." Each segment between markers is labeled with the cumulative CSS that applies in that range. Caption: "Common reference breakpoints. Use them as starting points, not as rules." Style: flat vector, horizontal timeline with markers and labeled ranges.
-->

### Combining Conditions with and

A media query can test more than one condition at the same time using the `and` keyword. All conditions must be true for the styles to apply.

```css
/* Only for screens between 600px and 1024px */
@media (min-width: 600px) and (max-width: 1024px) {
  .sidebar {
    display: block;
  }
}
```

This range-based query is sometimes useful for targeting a specific viewport window. It is more common in desktop-first code than mobile-first, but understanding it helps when reading stylesheets built by others.

You can also combine a media type with a feature condition:

```css
@media screen and (min-width: 768px) {
  /* Only for screen devices at 768px and above */
}
```

### Media Queries Beyond Width: User Preferences

Media queries are not limited to screen width. They can also respond to system-level user preferences. Two of the most important are:

#### prefers-color-scheme

Detects whether the user has a light or dark mode preference set in their operating system.

```css
/* Base styles: light mode */
body {
  background-color: #ffffff;
  color: #0f172a;
}

/* Dark mode: applied when the OS is in dark mode */
@media (prefers-color-scheme: dark) {
  body {
    background-color: #0f172a;
    color: #f1f5f9;
  }
}
```

The chatbot interface we have been building uses a dark color scheme throughout. If the base styles were light, `prefers-color-scheme: dark` would be the hook for switching to the dark version automatically, without the user needing to find a toggle in the interface.

#### prefers-reduced-motion

Detects whether the user has enabled a "reduce motion" accessibility setting on their device. Users who experience motion sickness or have vestibular disorders often enable this setting to stop animations.

```css
/* Default: animation on */
.send-button {
  transition: transform 0.2s ease;
}

.send-button:hover {
  transform: scale(1.05);
}

/* Reduce motion: disable animations for users who need it */
@media (prefers-reduced-motion: reduce) {
  .send-button {
    transition: none;
  }

  .send-button:hover {
    transform: none;
  }
}
```

This is a small change with significant impact for users who depend on it. Whenever you add animations or transitions to an interface, pairing them with a `prefers-reduced-motion` override is considered good practice.

### Putting It Together: The Chatbot at Every Size

Here is how the chatbot page applies breakpoints across its main layout decisions:

```css
/* Base: mobile, single column, everything stacked */
.page {
  display: flex;
  flex-direction: column;
}

.sidebar {
  display: none;   /* hidden on mobile */
}

.chat-window {
  padding: 12px;
}

/* Tablet and above: show the sidebar, reduce to two sections */
@media (min-width: 768px) {
  .page {
    display: grid;
    grid-template-columns: 240px 1fr;
    grid-template-rows: 64px 1fr 48px;
    grid-template-areas:
      "header  header"
      "sidebar main  "
      "footer  footer";
  }

  .sidebar {
    display: block;
    grid-area: sidebar;
  }

  .chat-window {
    padding: 16px;
  }
}

/* Desktop: expand sidebar width, increase spacing */
@media (min-width: 1280px) {
  .page {
    grid-template-columns: 280px 1fr;
  }

  .chat-window {
    padding: 24px;
  }
}
```

Mobile gets a simple stacked layout with the sidebar hidden. The 768px breakpoint introduces the two-column Grid layout with the sidebar visible. The 1280px breakpoint widens the sidebar and increases spacing. Each media query adds to what came before.

### Let's Define Things Now...

> **Media Query:** A CSS conditional block (`@media (condition) { }`) that applies styles only when the specified condition about the viewport or device is true.

> **Breakpoint:** The viewport width at which a media query activates and the design changes. Breakpoints should be chosen where content breaks, not based on specific device names.

> **min-width:** A media feature that is true when the viewport is at or above the specified width. Used in mobile-first media queries to add complexity for larger screens.

> **max-width:** A media feature that is true when the viewport is at or below the specified width. Used in desktop-first media queries. Less predictable when combined with mobile-first styles.

> **prefers-color-scheme:** A media feature that detects the user's system dark or light mode preference.

> **prefers-reduced-motion:** A media feature that detects whether the user has enabled a reduce-motion accessibility setting. Used to disable or simplify animations for users who need it.

### Activity: Three-Breakpoint Layout

Build a card grid that changes at three breakpoints using only what has been covered:

1. Start with six cards in a single-column grid (base mobile styles, no media query).

2. Add a media query at `min-width: 600px` that changes the grid to two columns. Resize the browser through 600px and confirm the transition.

3. Add a media query at `min-width: 1024px` that changes the grid to three columns. Resize through 1024px and confirm.

4. Add a third media query at `min-width: 1280px` that increases the gap and the card padding. Confirm only the spacing changes at that point.

5. Test the layout at 320px, 375px, 600px, 768px, 1024px, 1280px, and 1440px using DevTools Device Toolbar.

6. Add `@media (prefers-color-scheme: dark)` to the page. Inside it, switch the card background and text color to a dark variant. Enable dark mode in your operating system settings and confirm the page adapts automatically.

## What's Coming Next...

This is how we define the when and the what of responsive design. The breakpoints decide when the layout changes. The CSS inside each media query decides what changes. Every layout property we covered in the Flexbox and Grid modules can go inside a media query, activated at exactly the right size.

But layout is only one part of what changes between screen sizes. Typography changes too. A 3rem heading that looks powerful on a desktop monitor can overpower a small mobile screen, where a 2rem heading feels more appropriate. Line lengths that are comfortable on a wide screen become too long or too short on a phone. In the next article, we will look at how font sizes, line heights, and text measurements adapt across screen sizes to keep reading comfortable and the visual hierarchy intact at every viewport.
