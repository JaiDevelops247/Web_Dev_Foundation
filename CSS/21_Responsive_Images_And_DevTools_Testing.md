## Introduction

A photograph for a hero section is exported from Figma at 1400 pixels wide. The designer made it that wide so it looks sharp on a large desktop monitor.

Now a user opens the page on a phone. The browser downloads that same 1400px image. On a 375px screen. Where the image will be displayed at 375px wide.

The browser downloads 1400 pixels of image data to display 375 pixels of it. The extra 1025 pixels are discarded. But the download time, the battery drain, and the data cost of those 1025 pixels are very real. On a slow mobile connection, that single oversized image can add several seconds to the page load.

Images are the single largest contributor to slow page loads on mobile. And unlike layout or typography issues, image problems are not just visual. They directly affect how fast the page reaches the user and how much it costs them on a limited data plan.

Responsive images solve this in two layers:

1. CSS controls how images display within their containers (sizing, cropping, positioning).
2. HTML controls which image file the browser downloads in the first place.

Both layers matter. We will cover both.

### max-width: 100% — The Baseline Rule

Before anything else, every image on a responsive page should have this rule:

```css
img {
  max-width: 100%;
  height: auto;
}
```

`max-width: 100%` prevents an image from overflowing its container. An image that is 1400px wide in a 375px container will be clamped to 375px. An image that is 200px wide in a 600px container is not stretched — it stays at 200px since `max-width` only restricts, never forces expansion.

`height: auto` keeps the aspect ratio intact. Without it, constraining the width while leaving the height at its original value distorts the image.

```css
/* Without height: auto */
img {
  max-width: 100%;
  /* height defaults to auto anyway, but explicit is clearer */
}
```

<!-- 
IMAGE: Two containers side by side, both 375px wide. Left: an image without max-width: 100%, the image (1400px wide) overflows the container and extends past the right edge, causing a horizontal scrollbar. Right: the same image with max-width: 100%, the image is constrained to the container width and displays correctly. Caption: "max-width: 100% is the one rule that prevents images from breaking responsive layouts." Style: flat vector, two dark card containers, overflow visible on the left and contained on the right.
-->

This one rule, placed in the CSS reset at the top of the stylesheet, handles the majority of image responsiveness for most projects.

### object-fit: Controlling How Images Fill Their Container

Sometimes an image needs to fill a specific container size: an avatar must be a 40px circle, a hero must be exactly 500px tall regardless of the image's original proportions. Setting explicit dimensions on an image without any other property causes distortion.

`object-fit` controls how an image (or video) fills its container when the container's dimensions differ from the image's natural proportions.

```css
img {
  width: 300px;
  height: 200px;
  object-fit: value;
}
```

#### object-fit: cover

The image is scaled to fill the container completely. The aspect ratio is preserved. Any part of the image that does not fit is cropped.

```css
.hero-image {
  width: 100%;
  height: 400px;
  object-fit: cover;
}
```

<!-- 
IMAGE: A 300x200 container with a wide landscape photograph. The image fills the container completely — the full width and height are covered. The left and right edges of the photograph are cropped to fit the taller proportions of the container. The image is not distorted. Caption: "object-fit: cover — fills the container completely. Maintains aspect ratio. Crops what does not fit." Style: flat vector, dark container with a photo inside, cropped edges shown with semi-transparent overlay.
-->

`cover` is the most commonly used value. It is the correct choice for hero images, card thumbnails, and any situation where the container dimensions are fixed and the image should fill them without distortion or empty space.

#### object-fit: contain

The image is scaled to fit entirely within the container without cropping. The aspect ratio is preserved. If the image proportions do not match the container, empty space appears on two sides.

```css
.product-image {
  width: 300px;
  height: 200px;
  object-fit: contain;
  background-color: #1e293b;   /* fills the empty space */
}
```

<!-- 
IMAGE: A 300x200 container with a tall portrait photograph. The image fits entirely inside the container without any cropping. Empty space (shown in a dark background color) appears on the left and right sides since the portrait is narrower than the container. Caption: "object-fit: contain — the full image is visible. Maintains aspect ratio. Empty space appears where the proportions do not match." Style: flat vector, dark container with a centered image, visible empty space on sides with a contrasting background color.
-->

`contain` is the correct choice for product images, logos, and icons where showing the full image without cropping is more important than filling the container.

#### object-fit: fill

The image is stretched or squished to fill the container exactly, ignoring the aspect ratio. This almost always produces a distorted image.

```css
.distorted {
  width: 300px;
  height: 200px;
  object-fit: fill;
}
```

<!-- 
IMAGE: A 300x200 container with an image visibly stretched horizontally and compressed vertically to fill the container dimensions exactly. The image looks squished and distorted. Caption: "object-fit: fill — fills the container but distorts the image. Rarely the right choice." Style: flat vector, container with visibly distorted image.
-->

`fill` is the default value when `object-fit` is not set. It is almost never appropriate for photographs. Use `cover` or `contain` instead.

#### object-fit: none

The image is rendered at its natural size, with no scaling applied. It will overflow the container if it is larger than the container dimensions.

```css
.natural-size {
  width: 300px;
  height: 200px;
  object-fit: none;
  overflow: hidden;
}
```

<!-- 
IMAGE: A 300x200 container with a large image rendered at its intrinsic size. The image is clipped by the container boundaries. Only the top-left portion of the image is visible. Caption: "object-fit: none — image renders at its natural size. Overflows and is clipped by the container." Style: flat vector, container with image visible only in the top-left quadrant, rest clipped.
-->

#### The object-fit Summary

```
cover    → fills container, crops overflow, preserves ratio  (most used)
contain  → fits entirely inside, may leave empty space       (for logos, products)
fill     → stretches to fill exactly, distorts image         (almost never)
none     → renders at natural size, may overflow             (rare)
```

### object-position: Controlling the Crop

When `object-fit: cover` crops an image, the browser centers the visible area by default. If the important part of the image is not at the center (a face in the upper third, a product on the left side), the default crop may cut off the key content.

`object-position` controls which part of the image is centered in the container during cropping. It works exactly like `background-position`.

```css
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
  object-position: center top;   /* keep the face visible, not the chest */
}
```

```css
/* Values: x-position y-position */
object-position: center center;   /* default */
object-position: left top;
object-position: right bottom;
object-position: 30% 20%;         /* 30% from left, 20% from top */
```

<!-- 
IMAGE: Three versions of the same avatar circle (40x40, border-radius: 50%) showing a portrait photo. Left (center center): the crop shows the middle of the face and shoulders. Middle (center top): the crop shows the top of the head and face clearly. Right (right bottom): the crop shows a detail from the lower-right of the image. Caption: "object-position controls which part of the image remains visible when cover crops it." Style: three circular avatar crops from the same source image, each showing a different portion.
-->

For hero images where a specific subject should always remain visible:

```css
.hero {
  width: 100%;
  height: 500px;
  object-fit: cover;
  object-position: center 25%;   /* keep the subject in the upper area visible */
}
```

### srcset: Serving the Right File Size

`max-width: 100%` and `object-fit` handle how an image displays. But the browser still downloads the original full-size file regardless of the screen size.

The `srcset` attribute on the `<img>` tag tells the browser about multiple versions of the same image at different resolutions, letting it choose the most appropriate file for the current device.

```html
<img
  src="hero-800.jpg"
  srcset="
    hero-400.jpg  400w,
    hero-800.jpg  800w,
    hero-1400.jpg 1400w
  "
  alt="ChatAI hero image"
/>
```

Each entry in `srcset` pairs an image file with its actual width in pixels (the `w` descriptor). The browser reads the current viewport width and its pixel density, calculates which file is most appropriate, and downloads only that file.

On a 375px mobile screen, the browser downloads `hero-400.jpg`. On a 1440px desktop, it downloads `hero-1400.jpg`. The mobile user never downloads the 1400px file.

The `src` attribute remains as the fallback for browsers that do not support `srcset`, though all modern browsers do.

### The picture Element: Art Direction

`srcset` handles resolution switching: the same image at different sizes. Sometimes a different crop is needed at different screen sizes, not just a smaller version of the same crop. A wide landscape hero image that works on desktop needs a tighter portrait crop to work on a phone. This is called **art direction**.

The `<picture>` element handles art direction by letting you specify entirely different image files for different conditions:

```html
<picture>
  <!-- Mobile: tall portrait crop, applied below 768px -->
  <source
    srcset="hero-portrait.jpg"
    media="(max-width: 767px)"
  />

  <!-- Tablet: square crop, applied between 768px and 1279px -->
  <source
    srcset="hero-square.jpg"
    media="(max-width: 1279px)"
  />

  <!-- Desktop fallback: wide landscape crop -->
  <img src="hero-landscape.jpg" alt="ChatAI hero" />
</picture>
```

The browser evaluates the `<source>` elements in order. The first one whose `media` condition matches is used. If no source matches, the `<img>` fallback is used. The `<img>` is always required as the last child and provides the alt text for all sources.

<!-- 
IMAGE: Three device mockups (phone, tablet, desktop) showing the same hero section. The phone shows a tight vertical portrait crop of a person. The tablet shows a square crop showing the person from a wider angle. The desktop shows the full wide landscape with the person on the right and the chatbot interface on the left. Below each: the file name used. Caption: "picture element serves a different image crop for each viewport range. Same subject, three compositions." Style: three device mockups at accurate relative sizes, each showing a clearly different crop of the same image.
-->

Use `<picture>` when the image framing needs to change between mobile and desktop, not just the file size. Use `srcset` on a plain `<img>` when the same framing works at all sizes and only the resolution needs to change.

### DevTools: Testing Responsive Layouts

Everything we have built needs to be tested across viewport sizes. DevTools in Chrome (and other browsers) has a dedicated set of tools for this.

#### The Device Toolbar

Open DevTools with `Cmd + Option + I` (Mac) or `Ctrl + Shift + I` (Windows). Click the device icon at the top of the DevTools panel, or press `Cmd + Shift + M` (Mac) / `Ctrl + Shift + M` (Windows), to toggle the Device Toolbar.

<!-- 
IMAGE: A Chrome browser window with DevTools open on the right side. The Device Toolbar is active, showing a simulated mobile viewport (375px iPhone view) of a webpage. At the top of the simulated viewport is a dropdown showing "iPhone 14 Pro". Controls for viewport width, height, and zoom level are visible. Caption: "DevTools Device Toolbar: test any viewport size without owning any physical device." Style: realistic screenshot or high-fidelity mockup of Chrome DevTools in device toolbar mode.
-->

The Device Toolbar provides:

**Preset device sizes**: a dropdown list of common devices (iPhone 14 Pro, Samsung Galaxy S20, iPad Air) with accurate viewport dimensions and pixel ratios pre-configured.

**Custom viewport**: type any width and height manually. Test at 320px, 375px, 414px, 768px, 1024px, 1280px, and 1440px to cover the most common breakpoints.

**Orientation toggle**: switch between portrait and landscape to test how the layout handles both orientations.

**Zoom control**: zoom out to see a large desktop viewport in the DevTools window, or zoom in to check detail on a small mobile view.

The standard testing protocol for any responsive page:

```
320px  — the narrowest common phone (older iPhones, small Androids)
375px  — iPhone standard width (the most common mobile test size)
768px  — iPad portrait
1024px — iPad landscape / small laptop
1280px — standard laptop
1440px — large laptop / desktop
```

Test at each of these widths. At every size, check for horizontal overflow (content wider than the viewport), text that is too small to read, images that distort or overflow, and interactive elements that are too small to tap comfortably.

#### Network Throttling

The Device Toolbar simulates screen size but not connection speed. A page might render correctly at 375px but take 12 seconds to load because the images are not optimized.

In DevTools, open the Network tab. Find the throttling dropdown (labeled "No throttling" by default). Change it to "Slow 3G" or "Fast 3G" to simulate a mobile data connection.

Reload the page with throttling active and watch the network waterfall. Oversized images will be immediately visible as the largest requests, taking the longest to complete.

<!-- 
IMAGE: A Chrome DevTools Network tab screenshot showing a network waterfall for a page load. Several rows of requests are visible. One row (the hero image) is highlighted as clearly the longest bar in the waterfall, labeled "hero-1400.jpg — 2.1MB — 8.3s on Slow 3G." Caption: "Network throttling reveals image performance problems. The hero image is the bottleneck." Style: realistic DevTools Network tab mockup, one image request visually dominant in the waterfall.
-->

Any request taking more than one second on a simulated Slow 3G connection is a candidate for optimization: smaller files, compressed formats, or `srcset` to serve the right size.

### Putting It Together: Chatbot Responsive Images

```css
/* Global reset: images never overflow their container */
img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* Bot avatar: always a circle, never distorts */
.bot-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
  object-position: center top;
  flex-shrink: 0;   /* never compresses in a flex row */
}

/* Hero image: fills its container, crops from center */
.hero-image {
  width: 100%;
  height: 300px;
  object-fit: cover;
  object-position: center center;
}

@media (min-width: 768px) {
  .hero-image {
    height: 500px;
  }
}
```

```html
<!-- Hero with art direction: portrait on mobile, landscape on desktop -->
<picture>
  <source srcset="hero-portrait.jpg" media="(max-width: 767px)" />
  <img src="hero-landscape.jpg" alt="ChatAI — your AI assistant" class="hero-image" />
</picture>
```

### Let's Define Things Now...

> **max-width: 100%:** A CSS rule that prevents an image from exceeding the width of its container. The baseline rule for responsive images.

> **object-fit:** Controls how an image fills its container when the container dimensions differ from the image's natural proportions. Values: `cover` (fill and crop), `contain` (fit inside, no crop), `fill` (stretch, distorts), `none` (natural size).

> **object-position:** Controls which part of an image is visible when `object-fit` crops it. Works like `background-position`.

> **srcset:** An HTML attribute on `<img>` that lists multiple image files at different widths. The browser selects the most appropriate file for the current device and viewport.

> **picture element:** An HTML element that enables art direction: serving entirely different image crops to different screen sizes using `<source>` children with `media` conditions.

> **Art Direction:** Serving a different image composition (not just size) for different viewport sizes. A portrait crop on mobile, a landscape crop on desktop.

> **Device Toolbar:** A Chrome DevTools tool that simulates any viewport size, device, and orientation for responsive testing.

> **Network Throttling:** A DevTools feature that simulates slow mobile connections to reveal performance problems like oversized images.

### Activity: Responsive Image Testing

1. Add the global image reset (`max-width: 100%`, `height: auto`) to the chatbot stylesheet. Confirm no images overflow their containers at any viewport size.

2. Style the bot avatar: `40px` circle with `border-radius: 50%`, `object-fit: cover`, and `object-position: center top`. Confirm the avatar stays circular at all sizes and never distorts.

3. Add a hero image to the page at `width: 100%` and `height: 300px`. Apply `object-fit: cover`. Resize the browser and confirm the image fills its container without distortion at all widths.

4. Change `object-fit` to `contain` and observe the empty space that appears. Then try `fill` and observe the distortion. Restore `cover`.

5. Use `object-position` to change which part of the hero image is visible. Try `top`, `bottom`, `left`, `right`, and a percentage value.

6. Open DevTools and activate the Device Toolbar. Test the chatbot at 320px, 375px, 768px, 1024px, and 1440px. Document any element that breaks or looks wrong at each size.

7. Open the Network tab. Set throttling to Slow 3G. Reload and identify the slowest-loading resource. If it is an image, note its file size and consider whether `srcset` would reduce the download.

## What's Coming Next...

This was all about responsive design. From the three pillars (fluid grids, flexible images, media queries) through typography that scales with the viewport, to images that serve the right file at the right size and DevTools as the testing tool throughout.

We now know how to build a webpage that is structurally correct, well-styled, correctly laid out, and responsive across every screen size. But the web is not static. Users click buttons and expect feedback. Elements transition between states. Things move in response to interaction.

In the next article, we move into the final area of CSS we will cover in this module: making interfaces feel alive. Transitions that animate property changes smoothly, transforms that move and scale elements, and the CSS properties behind hover effects, loading states, and the polished micro-interactions that make a modern interface feel designed rather than assembled.
