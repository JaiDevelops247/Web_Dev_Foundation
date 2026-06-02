## Introduction

The chatbot stylesheet has grown. The brand's primary blue, `#3b82f6`, is referenced in dozens of places: the send button background, the bot avatar border, the input focus ring, the link color, the badge fill, the selection highlight, the box shadow on hover states.

Then the client calls. The brand color is changing from blue to violet.

```css
/* Every one of these needs to change */
.send-button { background-color: #3b82f6; }
.send-button:hover { box-shadow: 0 0 12px rgba(59, 130, 246, 0.4); }
.avatar-border { border-color: #3b82f6; }
.input:focus { border-color: #3b82f6; outline-color: #3b82f6; }
.badge { background-color: #3b82f6; }
.nav-link.active { color: #3b82f6; }
.progress-bar { background-color: #3b82f6; }
.send-button:focus-visible { outline-color: #3b82f6; }
```

Finding every instance of `#3b82f6` in the stylesheet and replacing it with the new color is error-prone and tedious. Miss one and the interface has a ghost of the old color appearing in a specific state no one notices until a user screenshots it.

The same problem exists for spacing values. If 24px is the standard padding throughout the interface and the design system shifts to 20px, every component that uses that spacing needs to be updated individually.

What we need is a system where a value is defined once and referenced everywhere. Change the definition and every reference updates automatically. This is exactly what CSS custom properties provide.

### What Are CSS Custom Properties?

**CSS custom properties** (also called CSS variables) are properties you define yourself, storing any CSS value under a name of your choosing. Once defined, the value can be referenced anywhere in the stylesheet using the `var()` function.

They are native CSS — no build tool, no preprocessor, no JavaScript required. They live in the browser alongside all other CSS and follow the same cascade and inheritance rules.

### Declaring a Custom Property

Custom property names always start with two dashes (`--`). They are defined just like any CSS property, on a selector.

```css
:root {
  --color-primary: #3b82f6;
}
```

`:root` is the standard selector for global custom properties. It refers to the top-level element of the document (the `<html>` element) and has higher specificity than `html`, making it the conventional home for variables that should be available everywhere on the page.

Any valid CSS value can be stored:

```css
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-surface: #1e293b;
  --color-text: #f1f5f9;
  --color-muted: #94a3b8;

  /* Spacing */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 16px;
  --space-lg: 24px;
  --space-xl: 48px;

  /* Typography */
  --font-base: 'Inter', sans-serif;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.25rem;

  /* Border radius */
  --radius-sm: 6px;
  --radius-md: 12px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-card: 0 2px 8px rgba(0, 0, 0, 0.3);
  --shadow-modal: 0 8px 32px rgba(0, 0, 0, 0.5);
}
```

The naming convention `--category-variant` is a common standard. It groups related variables together and makes their purpose immediately readable.

### Consuming a Custom Property: var()

Use the `var()` function to reference a custom property anywhere a CSS value is expected:

```css
.send-button {
  background-color: var(--color-primary);
  border-radius: var(--radius-full);
  padding: var(--space-sm) var(--space-lg);
  box-shadow: var(--shadow-card);
  font-size: var(--text-sm);
}
```

Now `#3b82f6` exists in exactly one place: the `--color-primary` declaration on `:root`. Every component that references `var(--color-primary)` reads from that single source.

Change the brand color:

```css
:root {
  --color-primary: #7c3aed;   /* one change */
}
```

Every button, badge, link, focus ring, and shadow that referenced `var(--color-primary)` updates immediately. No search and replace. No missed instances.

<!-- 
IMAGE: Two panels. Left: a stylesheet snippet showing --color-primary: #3b82f6 on :root and a list of component rules all containing var(--color-primary). An arrow labeled "One change here..." points from the :root block. Right: the same components now shown with var(--color-primary) resolving to #7c3aed (violet). All components have updated simultaneously. Caption: "Change the custom property once. Every component that references it updates automatically." Style: flat vector, code panels with colored highlight lines showing the single source and multiple consumers.
-->

#### The Fallback Value

`var()` accepts an optional second argument: a fallback value used when the custom property is not defined.

```css
.button {
  background-color: var(--color-primary, #3b82f6);
  /* Uses --color-primary if defined, falls back to #3b82f6 if not */
}
```

Fallback values are useful when a component might be used outside a context where the variables are defined, or during development before the full variable system is in place. In a complete design system, fallbacks are often omitted since the variables are always expected to be defined on `:root`.

### Scope and Inheritance

Custom properties follow the normal CSS cascade and are inherited by descendant elements, just like `color` or `font-family`.

A property defined on `:root` is available to every element on the page because every element is a descendant of `:root`.

A property defined on a more specific element overrides the `:root` value for that element and all its descendants.

```css
:root {
  --color-surface: #1e293b;   /* default dark surface */
}

.modal {
  --color-surface: #0f172a;   /* deeper surface for modals */
  background-color: var(--color-surface);   /* uses #0f172a */
}

.modal .card {
  background-color: var(--color-surface);   /* also uses #0f172a — inherited from .modal */
}

.page-content .card {
  background-color: var(--color-surface);   /* uses #1e293b — inherited from :root */
}
```

<!-- 
IMAGE: A DOM tree diagram showing :root at the top with --color-surface: #1e293b defined. Below it, two branches: .modal with --color-surface: #0f172a redefined (highlighted), and .page-content with no override. Arrows show .modal and its children (.modal .card) using #0f172a, while .page-content .card uses #1e293b from :root. Caption: "Custom properties are scoped to the element they are defined on. Descendants inherit the nearest ancestor's value." Style: flat vector, tree diagram with color-coded branches.
-->

This scoping behavior is powerful. A component can define its own local variable overrides without affecting the rest of the page.

### Building a Design System with Custom Properties

A well-organized set of custom properties on `:root` functions as a design system: a single source of truth for every visual decision in the interface.

Here is the complete custom property system for the chatbot:

```css
:root {
  /* Brand colors */
  --color-primary:      #3b82f6;
  --color-primary-dark: #2563eb;
  --color-primary-glow: rgba(59, 130, 246, 0.35);

  /* Surfaces */
  --color-bg:           #0a0f1e;
  --color-surface-1:    #0f172a;
  --color-surface-2:    #1e293b;
  --color-surface-3:    #334155;

  /* Text */
  --color-text:         #f1f5f9;
  --color-text-muted:   #94a3b8;
  --color-text-faint:   #475569;

  /* Borders */
  --color-border:       #1e293b;
  --color-border-focus: #3b82f6;

  /* Typography */
  --font-base: 'Inter', 'Helvetica Neue', sans-serif;
  --text-xs:   0.75rem;
  --text-sm:   0.875rem;
  --text-base: 1rem;
  --text-lg:   1.25rem;
  --text-xl:   1.5rem;
  --text-2xl:  2rem;

  /* Spacing */
  --space-1:  4px;
  --space-2:  8px;
  --space-3:  12px;
  --space-4:  16px;
  --space-6:  24px;
  --space-8:  32px;
  --space-12: 48px;

  /* Border radius */
  --radius-sm:   6px;
  --radius-md:   12px;
  --radius-lg:   16px;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm:    0 1px 4px rgba(0, 0, 0, 0.2);
  --shadow-md:    0 2px 8px rgba(0, 0, 0, 0.3);
  --shadow-lg:    0 8px 32px rgba(0, 0, 0, 0.5);
  --shadow-glow:  0 0 12px var(--color-primary-glow);

  /* Transitions */
  --transition-fast:   0.15s ease;
  --transition-base:   0.2s ease;
  --transition-slow:   0.4s ease-out;
}
```

With this system in place, component rules become self-documenting:

```css
.message-bubble {
  background-color: var(--color-surface-2);
  color: var(--color-text);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: var(--space-3) var(--space-4);
  font-size: var(--text-sm);
  box-shadow: var(--shadow-sm);
}

.send-button {
  background-color: var(--color-primary);
  color: var(--color-text);
  border-radius: var(--radius-full);
  padding: var(--space-2) var(--space-6);
  font-size: var(--text-sm);
  transition: background-color var(--transition-fast),
              transform var(--transition-fast),
              box-shadow var(--transition-fast);
}

.send-button:hover {
  background-color: var(--color-primary-dark);
  transform: translateY(-2px);
  box-shadow: var(--shadow-glow);
}
```

Reading these rules, any developer can immediately understand what each property represents without needing to decode a hex value.

### Dark Mode Theming with Custom Properties

The most elegant application of CSS custom properties is automatic dark mode. By defining all color values as custom properties, switching the entire color scheme requires only overriding those properties in a single media query block. No component rules need to change.

```css
/* Light mode: the default */
:root {
  --color-bg:       #ffffff;
  --color-surface:  #f8fafc;
  --color-text:     #0f172a;
  --color-text-muted: #64748b;
  --color-border:   #e2e8f0;
  --color-primary:  #2563eb;
}

/* Dark mode: overrides only the color tokens */
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg:       #0a0f1e;
    --color-surface:  #0f172a;
    --color-text:     #f1f5f9;
    --color-text-muted: #94a3b8;
    --color-border:   #1e293b;
    --color-primary:  #3b82f6;
  }
}
```

<!-- 
IMAGE: Two versions of the same chatbot interface side by side. Left: light mode with white backgrounds, dark text, and a blue primary color. Right: dark mode with near-black backgrounds, light text, and a slightly brighter blue primary color. A callout annotation shows that only the :root variable block changed between the two — all component rules are identical. Caption: "Light and dark mode from a single variable block. No component rules were touched." Style: two device mockups showing the chatbot in light and dark themes, side by side.
-->

Every component that uses `var(--color-bg)`, `var(--color-text)`, `var(--color-surface)` and the other tokens responds instantly to the system preference change. The component CSS has no concept of light or dark — only the token values do.

The chatbot we have been building uses a dark theme by default. Adding a light mode is now the reverse of this: define the dark variables as `:root` defaults and light overrides inside the media query.

### Custom Properties vs Preprocessor Variables

Before CSS custom properties existed natively, developers used preprocessor tools like Sass (`$color-primary: #3b82f6`) to achieve similar results. The difference is significant:

Preprocessor variables are compiled away at build time. The final CSS file has the literal values everywhere. There is no variable in the browser.

CSS custom properties exist at runtime in the browser. They can be read and changed by JavaScript, they respond to media queries, and they follow the cascade. They can be updated dynamically without a page reload.

```js
/* JavaScript can read and write custom properties at runtime */
document.documentElement.style.setProperty('--color-primary', '#7c3aed');
```

This makes CSS custom properties the foundation for runtime theming: letting users switch between color schemes, font sizes, or layout densities without any page reload.

### Let's Define Things Now...

> **CSS Custom Property:** A user-defined CSS property whose name starts with `--`. Stores any CSS value and is referenced using `var()`. Also called a CSS variable.

> **var():** The function used to consume a custom property. Accepts the property name and an optional fallback value: `var(--color-primary, #3b82f6)`.

> **Design Token:** A named design decision stored as a custom property. Color tokens, spacing tokens, and typography tokens form the vocabulary of a design system.

> **Design System (CSS):** A set of custom properties defined on `:root` that serves as the single source of truth for every visual value in the stylesheet. Changing a token updates every component that references it.

> **Runtime Variable:** Unlike preprocessor variables that are compiled away, CSS custom properties exist in the browser and can be changed by media queries, JavaScript, or element-level overrides at runtime.

### Activity: Design System and Theme Switcher

1. Create a fresh stylesheet. Define a complete set of custom properties on `:root`: at least 4 colors, 4 spacing values, 3 font sizes, 2 border radii, and 2 shadows.

2. Build five components (card, button, nav link, form input, heading) using only `var()` calls for all color, spacing, and size values. No hardcoded hex codes or pixel values in the component rules.

3. Change the value of `--color-primary` in the `:root` block only. Confirm every component that used that variable updates immediately.

4. Add `@media (prefers-color-scheme: dark)` and override the four color custom properties with dark theme values. Enable dark mode in the operating system settings and observe the entire page theme switch with no component rule changes.

5. Open DevTools, select the `<html>` element, and manually override a custom property in the Styles panel by adding `--color-primary: red` to the `:root` rule. Observe the live update across all components.

## What's Coming Next...

A design system built on custom properties is one of the most maintainable structures in CSS. Colors defined once. Spacing consistent everywhere. Themes changed with a single block override.

We are almost at the end of the CSS module. Before we close it out, there is one more article that every developer working in a team needs: how to write CSS that other developers can read, maintain, and extend without confusion. Naming conventions, file organization, the order rules should appear in, and the workflow habits that keep a stylesheet clean as a project grows. That is what the final CSS article covers.
