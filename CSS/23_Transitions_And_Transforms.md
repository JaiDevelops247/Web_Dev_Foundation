## Introduction

Hover over a button on any modern website. The background color does not snap from one value to another. It flows. The button lifts slightly. The shadow deepens. These changes take a fraction of a second, but that fraction is what makes the interaction feel alive rather than mechanical.

Without transitions, every CSS state change is instant:

```css
.button {
  background-color: #3b82f6;
}

.button:hover {
  background-color: #2563eb;
}
```

<!-- 
IMAGE: A button in its default state (blue) and its hover state (darker blue) shown as two separate snapshots with an abrupt switch arrow between them. Caption: "Without transitions, state changes are instant. The jump feels abrupt and cheap." Style: flat vector, two button states with a sharp transition arrow.
-->

The color changes, but the user perceives it as a visual glitch: one color disappears and another appears. There is no movement, no time, no sense of the interface responding.

CSS transitions fix this with one property. CSS transforms add movement, scaling, and rotation. Together they are the tools behind every hover effect, card lift, icon spin, and smooth reveal you have encountered on a well-built product.

### CSS Transitions

`transition` tells the browser: when a CSS property changes on this element, animate the change over time instead of applying it instantly.

```css
.button {
  background-color: #3b82f6;
  transition: background-color 0.2s ease;
}

.button:hover {
  background-color: #2563eb;
}
```

Now when the cursor enters the button, the background color fades smoothly from `#3b82f6` to `#2563eb` over 200 milliseconds, then fades back when the cursor leaves.

`transition` is a shorthand for four individual properties:

```css
transition: property  duration  timing-function  delay;
```

#### transition-property

Specifies which CSS property to animate. Only properties with intermediate values (numbers, colors, lengths) can be animated.

```css
transition-property: background-color;
transition-property: transform;
transition-property: box-shadow;
transition-property: all;   /* animates every animatable property — use carefully */
```

`transition: all` is convenient but has a performance cost. It makes the browser watch every property on the element for changes, including properties you do not intend to animate. Name the specific properties you want animated instead.

#### transition-duration

How long the animation takes, in seconds (`s`) or milliseconds (`ms`).

```css
transition-duration: 0.2s;    /* 200ms — snappy, feels responsive */
transition-duration: 0.4s;    /* 400ms — deliberate, noticeable */
transition-duration: 1s;      /* 1 second — slow, use for emphasis only */
```

Most UI interactions feel best between 150ms and 400ms. Faster than 150ms and the animation is invisible. Slower than 500ms and it feels sluggish.

#### transition-timing-function

Controls the speed curve of the animation: does it accelerate, decelerate, or move at a constant rate?

```css
transition-timing-function: ease;          /* default: fast start, slows at end */
transition-timing-function: linear;        /* constant speed throughout */
transition-timing-function: ease-in;       /* slow start, fast end */
transition-timing-function: ease-out;      /* fast start, slow end */
transition-timing-function: ease-in-out;   /* slow at both ends */
transition-timing-function: cubic-bezier(0.34, 1.56, 0.64, 1);  /* custom curve */
```

<!-- 
IMAGE: Five graphs showing animation progress over time for the five named timing functions. Each graph has "Time" on the x-axis and "Progress" on the y-axis. ease: curves up steeply then flattens. linear: a straight diagonal line. ease-in: curves up slowly then steeply. ease-out: curves up steeply then flattens. ease-in-out: S-curve, slow start and slow end with fast middle. Each is labeled with its name and a one-line description. Caption: "Timing functions define the feeling of motion. ease-out feels most natural for UI elements entering the screen." Style: flat vector, five small graphs on a dark background, each with a clearly distinct curve shape.
-->

In practice: `ease` is the best default for most button and color transitions. `ease-out` feels most natural for elements entering the screen (they arrive fast and settle gently). `ease-in` suits elements leaving the screen. `cubic-bezier()` allows fully custom curves for expressive animations.

#### transition-delay

How long to wait before starting the animation.

```css
transition-delay: 0s;      /* start immediately (default) */
transition-delay: 0.1s;    /* start after 100ms */
```

Delay is useful for staggering multiple elements: start each one slightly after the previous.

#### Transitioning Multiple Properties

Separate multiple transition definitions with commas. Each property gets its own duration, timing, and delay:

```css
.card {
  transition:
    background-color 0.2s ease,
    box-shadow       0.2s ease,
    transform        0.2s ease-out;
}

.card:hover {
  background-color: #1e3a8a;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4);
  transform: translateY(-4px);
}
```

<!-- 
IMAGE: A card component shown in two states. Left (default): flat dark card, no shadow, at baseline position. Right (hover): card has moved up 4px, has a deeper shadow, and the background has shifted to a slightly lighter shade. An arrow shows the combined effect of the three simultaneous transitions. Caption: "Three properties transitioning simultaneously: color, shadow, and vertical position. The card feels like it lifts off the surface." Style: flat vector, dark themed card, side-by-side default vs hover states with labeled changes.
-->

**Real website suggestion:** Open Stripe's homepage. Hover over any pricing card. The card lifts, the shadow deepens, and the border subtly brightens — three simultaneous transitions on one hover. This exact pattern is achievable with the code above.

### CSS Transforms

`transform` changes the visual presentation of an element: its position, size, rotation, and perspective. Critically, transforms do not affect the document flow. The element still occupies its original space in the layout; only the visual rendering moves.

#### translate(x, y)

Moves the element from its current position. Positive `x` moves right. Positive `y` moves down. Negative values move left or up.

```css
.element {
  transform: translate(20px, -8px);   /* 20px right, 8px up */
}

/* Single axis shorthand */
transform: translateX(20px);
transform: translateY(-8px);
```

<!-- 
IMAGE: A dark element box shown in its original layout position (gray outline placeholder) and its transformed position (solid colored box) shifted 20px to the right and 8px upward. The original layout space (shown as a dashed outline) is still occupied. Caption: "translate moves the visual rendering without affecting the layout. The dashed box is where the browser still reserves space." Style: flat vector, dark background, dashed placeholder and solid moved box clearly distinguishable.
-->

Use case: hover lift effects (`translateY(-4px)` on card hover), revealing elements from off-screen (start at `translateX(-100%)`, animate to `translateX(0)`), and centering with `transform: translate(-50%, -50%)` from the positioning article.

**Real website suggestion:** Vercel's feature cards shift upward by a few pixels on hover using `translateY`. The landing pages for most AI products use `translateY` for their scroll-reveal animations.

#### scale(x, y)

Scales the element larger or smaller from its center point. Values above 1 enlarge. Values below 1 shrink. A single value scales both axes equally.

```css
transform: scale(1.05);        /* 5% larger on both axes */
transform: scale(1.1, 1);      /* 10% wider, same height */
transform: scale(0.95);        /* slightly smaller */
```

<!-- 
IMAGE: A button shown at three scale values: scale(0.95) (slightly smaller), scale(1) (original), and scale(1.05) (slightly larger). The scale differences are subtle but clearly visible. Caption: "scale() is ideal for press and hover feedback. A 5% scale change feels significant without looking exaggerated." Style: flat vector, three button states side by side at their respective scales.
-->

Use case: button press feedback (`scale(0.97)` on `:active` to simulate a press), icon hover growth (`scale(1.1)`), card hover emphasis.

**Real website suggestion:** GitHub's reaction emoji buttons scale up on hover. Notion's inline menu icons grow on hover. Linear's action buttons show a `scale(0.97)` press state on `:active`.

#### rotate(deg)

Rotates the element clockwise by the specified angle. Negative values rotate counterclockwise.

```css
transform: rotate(45deg);    /* 45 degrees clockwise */
transform: rotate(-90deg);   /* 90 degrees counterclockwise */
transform: rotate(180deg);   /* half turn */
```

<!-- 
IMAGE: Three versions of the same arrow icon: rotate(0deg) pointing right, rotate(90deg) pointing down, and rotate(180deg) pointing left. Caption: "rotate() is how dropdown chevrons and hamburger menu icons animate between their open and closed states." Style: flat vector, three arrow icons at three rotation angles.
-->

Use case: dropdown chevron that rotates 180 degrees when the menu opens, hamburger menu icon that transforms into an X, settings gear that spins on hover.

```css
/* Dropdown chevron: rotates when the parent has .open class */
.dropdown-chevron {
  transition: transform 0.2s ease;
}

.dropdown.open .dropdown-chevron {
  transform: rotate(180deg);
}
```

**Real website suggestion:** Every dropdown on Notion, Linear, and Vercel uses a rotating chevron icon. The `transform: rotate(180deg)` with a smooth `transition` is the exact implementation.

#### skew(x, y)

Tilts the element along the x and y axes, creating a slanted or perspective-distorted appearance.

```css
transform: skew(10deg, 0deg);    /* tilt along the x axis */
transform: skew(0deg, 5deg);     /* tilt along the y axis */
```

<!-- 
IMAGE: A card element shown at skew(10deg, 0deg), tilted to create a parallelogram shape. Caption: "skew() creates perspective distortion. Rarely used for interactive states but effective for decorative backgrounds and diagonal sections." Style: flat vector, dark card element skewed along the x axis.
-->

`skew` is rarely used for interactive states. It is most common for decorative background shapes: diagonal dividers between page sections, parallelogram-shaped decorative elements.

#### transform-origin

Every transform rotates, scales, or skews from a center point. By default that center point is the element's geometric center (`50% 50%`). `transform-origin` changes it.

```css
transform-origin: center center;   /* default */
transform-origin: top left;        /* rotate/scale from top-left corner */
transform-origin: bottom center;   /* rotate from bottom center */
transform-origin: 0 50%;           /* rotate from the left edge, vertically centered */
```

<!-- 
IMAGE: Three versions of a rectangle rotating 45 degrees, each with a different transform-origin. Left: rotates from the center (default). Middle: rotates from the top-left corner. Right: rotates from the bottom center. The anchor point is shown as a small dot on each. Caption: "transform-origin changes the anchor point. The same rotation produces a completely different visual effect." Style: flat vector, three rectangles mid-rotation with dot markers at their respective origin points.
-->

Use case: a page-turn animation rotating from the right edge (`transform-origin: right center`), an accordion panel that rotates open from its top edge (`transform-origin: top center`).

### Combining Transforms

Multiple transform functions can be applied in sequence, separated by spaces:

```css
.card:hover {
  transform: translateY(-4px) scale(1.02);
}
```

Note: transforms are applied right to left. `translateY(-4px) scale(1.02)` first scales, then translates. The order matters for the resulting visual output.

### The Performance Rule

Not all CSS properties are equal to animate. Some property changes require the browser to recalculate the entire layout (reflow), repaint elements, and then composite the result. Others skip directly to the compositing step.

**Expensive to animate (triggers layout recalculation):**
```css
width, height, padding, margin, top, left, font-size
```

**Cheap to animate (compositor-only, GPU-accelerated):**
```css
transform, opacity
```

When the browser animates `transform` or `opacity`, it hands the work to the GPU compositor, which handles it independently of the main rendering pipeline. The animation is smooth even when the main thread is busy.

When the browser animates `width` or `top`, it must recalculate the positions of every affected element on every frame of the animation. This can cause visible jitter on less powerful devices.

```css
/* AVOID: animates layout — expensive */
.card:hover {
  top: -4px;   /* triggers full reflow on every frame */
}

/* PREFER: animates transform — GPU-accelerated */
.card:hover {
  transform: translateY(-4px);   /* compositor handles this */
}
```

The practical rule: whenever you need to move, resize, or fade an element, reach for `transform` and `opacity`. Reserve layout property changes (width, height, position) for state changes that do not need animation.

<!-- 
IMAGE: Two side-by-side diagrams of the browser rendering pipeline. Left (expensive): a chain of boxes labeled Layout → Paint → Composite, with all three steps highlighted in red on every animation frame. Right (cheap): the same chain with only Composite highlighted in green, Layout and Paint grayed out and skipped. Caption: "transform and opacity skip layout and paint entirely. The GPU handles them alone, keeping animations smooth." Style: flat vector, pipeline diagram, two versions with different stages highlighted.
-->

### Putting It Together: Chatbot Interaction Polish

```css
/* Send button: smooth color and lift on hover */
.send-button {
  background-color: #3b82f6;
  transform: translateY(0);
  transition:
    background-color 0.15s ease,
    transform        0.15s ease-out,
    box-shadow       0.15s ease;
}

.send-button:hover {
  background-color: #2563eb;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(59, 130, 246, 0.4);
}

.send-button:active {
  transform: scale(0.97);
  box-shadow: none;
}

/* Message bubble: subtle lift on hover */
.message-bubble {
  transition: transform 0.2s ease-out;
}

.message-bubble:hover {
  transform: translateY(-2px);
}

/* Settings dropdown chevron: rotates when open */
.settings-chevron {
  transition: transform 0.2s ease;
}

.settings-panel.open .settings-chevron {
  transform: rotate(180deg);
}

/* Bot avatar: subtle scale on hover */
.bot-avatar {
  transition: transform 0.15s ease;
}

.bot-avatar:hover {
  transform: scale(1.08);
}
```

**Real website suggestion:** Open the Anthropic website or Vercel's dashboard. Hover over buttons, cards, and icons. Nearly every interactive element uses a combination of `translateY`, `scale`, and `opacity` transitions at 150-300ms. These are not dramatic animations: they are quiet confirmations that the interface noticed the interaction.

### Let's Define Things Now...

> **transition:** Animates CSS property changes over time instead of applying them instantly. Shorthand for `transition-property`, `transition-duration`, `transition-timing-function`, and `transition-delay`.

> **transition-timing-function:** Controls the speed curve of the animation. Common values: `ease` (default), `linear`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier()`.

> **transform:** Changes the visual rendering of an element (position, scale, rotation) without affecting the document flow. Composed of functions: `translate()`, `scale()`, `rotate()`, `skew()`.

> **translate(x, y):** Moves the element visually from its current position. Does not affect surrounding layout.

> **scale(x, y):** Scales the element larger or smaller from the transform origin. Values above 1 enlarge, below 1 shrink.

> **rotate(deg):** Rotates the element clockwise by the given angle around the transform origin.

> **transform-origin:** Changes the anchor point for transforms. Default is `center center`.

> **GPU Compositor:** The part of the browser rendering pipeline that handles `transform` and `opacity` changes independently, without triggering layout recalculation. This is why animating these properties is faster than animating width or position.

### Activity: Interactive Card and Button Polish

1. Build a card with `box-shadow` and add a `:hover` state that changes `box-shadow` to a deeper value and adds `transform: translateY(-4px)`. Add `transition` for both properties and observe the smooth lift effect.

2. Add a `:active` state to a button with `transform: scale(0.97)`. Hold the mouse button down and observe the press effect. Add a `transition` of 100ms and notice how a very short duration makes the press feel more physical.

3. Add a chevron icon next to a dropdown label. Apply `transition: transform 0.2s ease` and toggle a class on click that applies `rotate(180deg)`. Observe the rotation animation on open and close.

4. Try animating `width` from 100px to 200px using transition. Then achieve the same visual result using `transform: scaleX(2)`. Open the Performance tab in DevTools and record both animations. Compare the rendering cost.

5. Apply `transition-delay` to a series of cards so each one starts its entrance animation 100ms after the previous one.

## What's Coming Next...

Smooth hover states, lifting cards, rotating icons, press feedback on buttons: these are the interactions that make a modern interface feel built rather than generated. The difference between a website that feels professional and one that feels like a template is often entirely in these micro-interactions, and they are built entirely in CSS.

With transitions and transforms, we have covered every major visual CSS concept in this module. From the foundational (colors, typography, the box model) through layout (Flexbox, Grid) and responsiveness, through the decorative and interactive layer we just completed.

In the next article we will look at CSS custom properties, a system for defining your design values once and referencing them everywhere, making the entire stylesheet maintainable and themeable from a single block of variables.
