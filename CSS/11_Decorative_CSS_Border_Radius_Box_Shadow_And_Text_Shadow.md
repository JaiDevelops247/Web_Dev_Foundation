## Introduction

Take two cards side by side. Same width, same background color, same padding, same content. The only difference: one has sharp corners and one has rounded corners.

<!-- 
IMAGE: Two identical dark cards side by side with the same content (a heading and a short line of text). Left card: sharp 90-degree corners. Right card: corners rounded with border-radius: 12px. No other difference between them. Caption: "Same element. Same color. Same padding. The rounded version feels designed. The sharp version feels accidental." Style: flat vector, dark background, both cards on a neutral dark page background.
-->

The rounded card feels intentional. It feels like a product. The sharp card feels like a raw HTML element that has not been styled yet.

This is not a subjective opinion, it is a pattern consistent across every major interface: Apple, Google, Airbnb, Stripe, every SaaS dashboard. Rounded corners signal that a UI element has been considered. They are one of the most immediate visual improvements you can make to any element on a page.

The property that creates this effect is `border-radius`. It is one of three properties in this article, and together they form the decorative foundation of modern web interfaces before we move into layout.

### border-radius: Rounding the Corners

`border-radius` controls the curvature of an element's corners. It rounds the corners of the border box, clipping the background and border along with it.

```css
.card {
  background-color: #1e293b;
  padding: 24px;
  border-radius: 12px;
}
```

A single value applies the same radius to all four corners.

#### Controlling Individual Corners

`border-radius` follows the same clockwise shorthand as `padding` and `margin`, starting from the top-left:

```css
/* top-left, top-right, bottom-right, bottom-left */
border-radius: 12px 12px 0 0;   /* rounded top, flat bottom */
```

Each corner can also be set individually:

```css
border-top-left-radius: 12px;
border-top-right-radius: 12px;
border-bottom-right-radius: 0;
border-bottom-left-radius: 0;
```

This is useful for elements that are visually connected to something below them, like a tab that sits above a content panel:

```css
.tab {
  border-radius: 8px 8px 0 0;   /* rounded top, flat bottom to connect to the panel */
}
```

For the chatbot message bubbles from the box model article, asymmetric radius is what creates the speech bubble appearance:

```css
/* Bot message: flat bottom-left corner, rounded everywhere else */
.bot-message {
  border-radius: 12px 12px 12px 0;
}

/* User message: flat bottom-right corner, rounded everywhere else */
.user-message {
  border-radius: 12px 12px 0 12px;
}
```

#### The Pill Shape

Setting `border-radius` to a value large enough to exceed half the element's height produces a pill shape: fully rounded ends with a straight center.

```css
.send-button {
  padding: 10px 24px;
  border-radius: 999px;   /* any large value works */
}
```

Using `999px` (or `100px` or any value well above the element's height) is the common pattern. The browser caps the radius at half the height, producing a perfect pill regardless of the element's actual dimensions.

<!-- 
IMAGE: Three versions of the same button element. Left: border-radius: 0 (sharp rectangle). Middle: border-radius: 8px (slightly rounded rectangle). Right: border-radius: 999px (full pill shape). Each labeled with its border-radius value. Caption: "border-radius: 999px always produces a pill. The browser caps the radius at the maximum that fits." Style: flat vector, three buttons in a row, dark blue fill, light text.
-->

#### The Perfect Circle

When applied to a square element, `border-radius: 50%` produces a perfect circle. This is the standard technique for avatar images:

```css
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;   /* clips the image to the circle */
}
```

The `overflow: hidden` is necessary when the element contains an image or child element that would otherwise extend beyond the circular boundary.

### box-shadow: Adding Depth

A flat card on a flat background has no visual separation. It is technically there, but it reads as part of the surface it sits on. This is fine for some designs, but the moment you need an element to feel elevated, like a dialog box that has appeared above the page, a card that is hovering above a dashboard, or a button that should feel pressable, you need depth.

`box-shadow` adds a shadow to the outside (or inside) of an element, creating the visual illusion of elevation or depth.

```css
box-shadow: offset-x  offset-y  blur-radius  spread-radius  color;
```

```css
.card {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}
```

Breaking down the values:

**offset-x**: how far the shadow shifts horizontally. Positive moves right, negative moves left. `0` keeps it centered horizontally.

**offset-y**: how far the shadow shifts vertically. Positive moves down, negative moves up. A positive value mimics a light source above the element.

**blur-radius**: how soft or sharp the shadow edge is. `0` produces a hard-edged shadow. Higher values produce a softer, more diffuse shadow.

**spread-radius**: how much the shadow expands or contracts. Positive values make the shadow larger than the element. Negative values shrink it. Often omitted (defaults to `0`).

**color**: the shadow color. `rgba()` with low alpha is standard, so the shadow blends naturally with the background.

```css
/* Subtle card lift */
.message-card {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

/* More prominent elevation for a modal or popup */
.modal-dialog {
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.5);
}

/* Glowing accent shadow (common in dark-theme UI) */
.send-button {
  box-shadow: 0 0 16px rgba(59, 130, 246, 0.4);
}
```

<!-- 
IMAGE: Three cards showing box-shadow variations. Left: box-shadow: 0 2px 8px rgba(0,0,0,0.2) with a subtle soft shadow below. Middle: box-shadow: 0 8px 32px rgba(0,0,0,0.5) with a deeper, more diffuse shadow. Right: box-shadow: 0 0 16px rgba(59,130,246,0.4) with a blue glow emanating evenly from all sides. Each card labeled with its box-shadow value. Caption: "Same property, different values. Shadows control perceived elevation and depth." Style: flat vector, three dark cards on a darker background.
-->

#### Inset Shadows

Adding the `inset` keyword before the other values draws the shadow on the inside of the element instead of the outside:

```css
.input-field {
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.3);
}
```

An inset shadow gives a sunken, pressed-in appearance. It is commonly used on text inputs and text areas to visually distinguish them from flat surfaces, and on buttons that should look depressed when clicked.

#### Multiple Shadows

`box-shadow` can accept multiple shadow definitions, separated by commas. Each shadow is drawn in order, with the first on top:

```css
.elevated-card {
  box-shadow:
    0 1px 3px rgba(0, 0, 0, 0.2),
    0 8px 24px rgba(0, 0, 0, 0.3);
}
```

Layering a tight, sharp shadow close to the element with a soft, diffuse shadow further away produces the most realistic depth effect. This is the technique design systems like Material Design use for their elevation model.

### text-shadow: Shadow on the Text Itself

`text-shadow` applies a shadow specifically to the rendered text of an element, not the box around it. The syntax is shorter than `box-shadow` because there is no spread-radius and no inset keyword:

```css
text-shadow: offset-x  offset-y  blur-radius  color;
```

```css
.hero-heading {
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.5);
}
```

Text shadow is most visible on large display text, especially when text sits over a photograph or gradient background where contrast is inconsistent. A dark shadow behind light text ensures legibility regardless of what is behind it.

```css
/* Large heading over a gradient hero background */
.chatbot-title {
  color: #f1f5f9;
  font-size: 3rem;
  text-shadow: 0 2px 16px rgba(0, 0, 0, 0.6);
}
```

<!-- 
IMAGE: A large heading on a gradient background, shown twice. Left: without text-shadow, the text has inconsistent contrast against the gradient and is hard to read in some areas. Right: with text-shadow: 0 2px 16px rgba(0,0,0,0.6), the text is consistently legible against every part of the gradient. Caption: "text-shadow solves the contrast problem on gradient and image backgrounds." Style: flat vector, dark atmospheric gradient background, large white heading text.
-->

Like `box-shadow`, multiple text shadows can be stacked with commas:

```css
.glow-text {
  color: #60a5fa;
  text-shadow:
    0 0 8px rgba(96, 165, 250, 0.8),
    0 0 24px rgba(96, 165, 250, 0.4);
}
```

Stacking two outward glows at different radii and opacities creates the soft neon glow effect common in dark-themed AI and gaming interfaces.

### Putting It Together: A Polished Chat Card

Here is what the message bubble from the box model article looks like with all three decorative properties applied:

```css
.bot-message {
  /* Box model */
  width: 70%;
  padding: 12px 16px;
  margin-bottom: 8px;

  /* Appearance */
  background-color: #1e293b;
  color: #f1f5f9;
  font-size: 0.9375rem;
  line-height: 1.6;

  /* Decorative */
  border-radius: 12px 12px 12px 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.chatbot-title {
  font-size: 2rem;
  font-weight: 700;
  color: #f1f5f9;
  text-shadow: 0 2px 12px rgba(0, 0, 0, 0.4);
}

.send-button {
  padding: 10px 24px;
  background-color: #3b82f6;
  color: #ffffff;
  border: none;
  border-radius: 999px;
  box-shadow: 0 0 12px rgba(59, 130, 246, 0.35);
  cursor: pointer;
}
```

Three properties. The card feels elevated. The button feels clickable. The heading reads over any background. This is the visual layer that sits between structural CSS and layout CSS.

### Let's Define Things Now...

> **border-radius:** Rounds the corners of an element's border box. A single value applies to all corners. Four values go clockwise from top-left. `50%` on a square produces a circle. Large values (999px) produce a pill shape.

> **box-shadow:** Adds a shadow outside (or inside, with `inset`) an element. Defined by horizontal offset, vertical offset, blur radius, spread radius, and color. Multiple shadows can be stacked with commas.

> **Inset Shadow:** A `box-shadow` with the `inset` keyword that draws the shadow on the inside of the element, creating a sunken or pressed appearance.

> **text-shadow:** Adds a shadow to the text content of an element. Defined by horizontal offset, vertical offset, blur radius, and color. No spread radius and no inset option.

> **Elevation:** The visual perception that an element is closer to the viewer than the surface it sits on. Created in CSS using `box-shadow`.

### Activity: Decorative System for the Chatbot

Apply the three properties across the chatbot interface:

1. Give every card, bubble, and panel on the page a `border-radius`. Use `12px` for large containers and message bubbles, `8px` for smaller inputs and badges, `999px` for buttons and tags, and `50%` for avatar images. Open the page and compare it to the sharp-corner version.

2. Add `box-shadow` to the chat window container (a deep shadow to lift it off the background), the send button (a blue glow matching the button color), and any modal or dialog (a heavy shadow to reinforce its elevation above the overlay).

3. Apply `box-shadow: inset` to the text input field at the bottom of the chat. Notice how it reads as a different surface type from the buttons.

4. Add `text-shadow` to the main chatbot title in the header. Try a tight dark shadow for depth, then a blue glow matching the brand color. Compare both versions and choose which suits the design.

5. Stack two `box-shadow` values on the elevated card: one tight and dark, one larger and more diffuse. Compare the layered version to the single-shadow version.

## What's Coming Next...

This was the last article in the box model and layout foundations section. We have covered how elements are sized, spaced, positioned, stacked, and now decorated.

Starting from the next section, we move into the layout systems that CSS provides for arranging groups of elements. The first of these is Flexbox: a one-dimensional layout system that makes it straightforward to align, distribute, and reorder elements in a row or a column. Navigation bars, message lists, input bars, button groups, the entire structural arrangement of a modern interface is built on Flexbox. It is one of the most important skills in frontend development, and we will build the entire chatbot layout with it.
