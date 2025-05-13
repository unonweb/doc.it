# CONCEPTS

## Mobile first

1. create a simple single-column layout for narrow-screen devices
2. then check for larger screens and implement a multiple-column layout when you know that you have enough screen width to handle it.

## window sizes

“A common setup is to use **12 columns** and **30px of gutter**.”

Device Dimensions (resolution) is based upon ***Pixel Per Inch***. In devices which have higher resolution in small display, their ONE pixel contains many more pixels, thats why Retina Display or Device Dimensions got bigger. However device actual dimensions depends on actual Pixels Per Inch which is called "**Viewport Size**" of device or  "**device-width**". Responsive Websites CSS styles are based upon Viewport sizes of devices.

### Hardware

1. Large desktop screens
2. Laptop screens
3. Tablets
4. Mobile phones

### Common Screen Resolutions

(2023)

**Mobile portrait:**

- 360×800 (11.01%)
  -  iPhone 6s: 375×667 (portrait)
- 390×844 (7.92%)
- 393×873 (5.26%)
- 414×896 (5.55%)
- 412×915 (5%)

**Mobile landscape:**

* 800×360 (11.01%)
  *  iPhone 6s: 667×375
* 844×390 (7.92%)
* 896×414 (5.55%)
* 873×393 (5.26%)

**Tablet portrait:**

* 820×1180
  iPad Air (2020)

**Tablet landscape:**

* 1180×820
  iPad Air (2020)

**Desktop:**

- 1920×1080 (22.18%)
  - Mein Bildschirm
- 1366×768 (14.04%)
- 1440×900 (6.41%)
  MacBook Pro 15”
- 1280×720 (5.45%)
- 1280×1024 (4.52%)

### concepts

**Tailwind:**

- sm	**640px**	@media (min-width: 640px) { ... }
- md	**768px**	@media (min-width: 768px) { ... }
- lg         **1024px**	@media (min-width: 1024px) { ... }
- xl	 **1280px**	@media (min-width: 1280px) { ... }
- 2xl       **1536px**	@media (min-width: 1536px) { ... }

**Microsoft:**

- Small: smaller than 640px (mobile portrait)
- Medium: 640px or wider (mobile landscape, tablet portrait)
- Large: 1008px or wider (tablet landscape, desktop)

**Google:**

* **compact**: width < 600 (phone in portrait)
* **medium**: 600 <= width < 840 (tablet in portrait)
* **expanded**: width >= 840 (phone in landscape, tablet in landscape, desktop)

**Zurb Foundation Framework**:

- Small: smaller than 640px
- Medium: 640 pixels or wider
- Large: 1024 pixels or wider

## max size main container

where you’ll have your columns

The most common max-width among our partners is **1140px**, which also covers most cases and makes a bit better use of larger screens to fill it with content.

## Material Design

### Basics

When writing CSS, use `px` wherever `dp` or `sp` is stated. Dp only needs to be used in developing for Android.
When designing for the web, replace `dp` with `px`.

### Layout

window size classes: compact, medium, expanded

* **compact**: width < 600 (phone in portrait)
* **medium**: 600 <= width < 840 (tablet in portrait)
* **expanded**: width >= 840 (phone in landscape, tablet in landscape, desktop)

| **Window class** | **Panes**            | **Navigation**                                       | **Communication**                | **Action**   |
| ---------------- | -------------------- | ---------------------------------------------------- | -------------------------------- | ------------ |
| Compact          | 1                    | Navigation bar, modal navigation drawer              | Simple dialog, fullscreen dialog | Bottom sheet |
| Medium           | 1 (recommended) or 2 | Navigation rail, modal navigation drawer             | Simple dialog                    | Menu         |
| Expanded         | 1 or 2 (recommended) | Navigation rail, modal or standard navigation drawer | Simple dialog                    | Menu         |

#### navigation

* expanded: drawers
* compact / medium: modal navigation drawers 

Each product view should have three layouts, one for each of the three window size classes.

Different components are recommended for performing the same function across the three layouts.

# TRICKS

```css
.state-indicator {
    position: absolute;
    top: -999em;
    left: -999em;
}
.state-indicator:before { content: 'desktop'; }

/* small desktop */
@media all and (max-width: 1200px) {
    .state-indicator:before { content: 'small-desktop'; }
}

/* tablet */
@media all and (max-width: 1024px) {
    .state-indicator:before { content: 'tablet'; }
}

/* mobile phone */
@media all and (max-width: 768px) {
    .state-indicator:before { content: 'mobile'; }
}
```



VIEWPORT
=========================================================

### \<meta\>

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
<!-- 
1. sets the width of the page to follow the screen-width of the device 
2. sets the initial zoom level when the page is first loaded by the browser. 
-->
```

IMAGE: HTML
=====================================================================

* *Resolution switching*: The problem whereby you want to serve smaller image files to narrow screen devices, 
  as they don't need huge images like desktop displays do — and also optionally that you want to serve different resolution images to high density/low density screens. You can solve this problem by using vector graphics (SVG images) and the `srcset` with `sizes` attributes.
* *Art direction*: The problem whereby you want to serve cropped images for different layouts 
  — for example a landscape image showing a full scene for a desktop layout, and a portrait image showing the main subject zoomed in for a mobile layout. You can solve this problem using the `<picture>` element.

## Resolution switching

### specifying image width

This has some advantages over the image-density-approach, because it gives the browser more information about the images, so it can make a better decision about which one to select. This is also good if your image versions aren’t in exact proportion to each other. 

* `sizes` legt den Raum fest, den das Bild innerhalb des Layouts einnimmt.
* `srcset` enthält eine Liste der Bilder und ihrer jeweiligen Größe.
* Ohne die Angabe von `sizes`, würde der Browser ein Bild entsprechend der Fensterbreite aussuchen und die Anzeigegröße im Layout außer Acht lassen.

Mit diesen Informationen wählt der Browser das kleinste Bild, das in das Layout  passt und es ausfüllt.
Mit *sizes* und *srcset* bestimmt also nicht mehr die Größe des Viewports allein über die Wahl des Bildes.

```html
<!-- srcset: 
An image filename (elva-fairy-480w.jpg) followed by the image's intrinsic width in pixels (480w) — note that this uses the w unit, not px as you might expect. This is the image's real size, which can be found by inspecting the image file on your computer. -->

<!-- sizes: 
A media condition (max-width:600px) followed by the width of the slot the image will fill when the media condition is true (480px)
The layout width can be defined with px, em, or vw -->

<!-- src: 
Browsers that do not support srcset and sizes will fallback to src. -->

<img
	 srcset="
             url size,
             img-480w.jpg 480w, 
             img-800w.jpg 800w"
	 sizes="
            (max-width: 600px) 480px, 
            800px" 
	 src="default.jpg" 
	 alt="some text"
>
```
Note: For the slot width, you may provide an absolute length (px, em) or a length relative to the viewport (vw), but not percentages. You may have noticed that the last slot width has no media condition (this is the default that is chosen when none of the media conditions are true). The browser ignores everything after the first matching condition, so be careful how you order the media conditions.

So, with these attributes in place, the browser will:
1. Look at its device width.
2. Work out which media condition in the sizes list is the first one to be true.
3. Look at the slot size given to that media query.
4. Load the image referenced in the srcset list that has the same size as the slot or, if there isn't one, the first image that is bigger than the chosen slot size.

#### pragmatic solution 1

* *1920px* (this covers FullHD screens and up)
* *1600px* (this will cover 1600px desktops and several tablets in portrait mode, for example iPads at 768px width, which will request a 2x image of 1536px and above)
* *1366px* (it is the most widespread desktop resolution)
* *1024px* (1024x768 screens are rarer, but I think you need some image size in between, not to leave too big a gap between pixel sizes, in case the market changes)
* *768px* (useful for 2x 375px mobile screens, as well as any device that actually requests something close to 768px)
* *640px* (for smartphones)

```html
<img
	 srcset="
             img1920.jpg 1920w,
             img1600.jpg 1600w,
             img1366.jpg 1366w,
             img1024.jpg 1024w,
             img768.jpg 768w,
             img640.jpg 640w"
	 sizes="
            (max-width: 640px) 640px, 
            (max-width: 768px) 768px, 
            (max-width: 1024px) 1024px,
            (max-width: 1366px) 1366px,
            (max-width: 1600px) 1600px,
            1920px," 
	 src="img.jpg" 
	 alt="some text"
>
```

This leaves you covered for most cases. If you think that 640px is too high a minimum, keep in mind that most smartphones on the market now have high pixel density, and even though they declare 320px of viewport width, they will support double that at minimum, therefore 640px.
The above sizes are meant as a reference for 100vw images. If an image covers less screen width in your design, you need to divide the above numbers by the actual (approximate) width of the image on screen.

Most used smartphone screen resolutions in Germany 2019 

1.	1080x1920	17.89%
2.	750x1334	17.09%
3.	1440x2960	12.40%
4.	720x1280	11.56%
5.	1440x2560	9.71%

```html
<img src="/assets/imgs/g-turn-lecture-series-summerterm24.webp" srcset="/assets/imgs/g-turn-lecture-series-summerterm24.webp 2339w, /assets/imgs/g-turn-lecture-series-summerterm24-1920x2715.webp 1920w, /assets/imgs/g-turn-lecture-series-summerterm24-1600x2263.webp 1600w, /assets/imgs/g-turn-lecture-series-summerterm24-1366x1932.webp 1366w, /assets/imgs/g-turn-lecture-series-summerterm24-1024x1448.webp 1024w, /assets/imgs/g-turn-lecture-series-summerterm24-768x1087.webp 768w, /assets/imgs/g-turn-lecture-series-summerterm24-640x906.webp 640w, " sizes="100vw" loading="eager" alt="G-TURN Lecture Series Summerterm24 poster" height="3308" width="2339">
```

### specifying image density

You need different images scaled in proportion to each other.

Start with the biggest image you plan to serve up. Then scale it down to each individual version.

- Largest or original — image-4x.jpg
  flamingo4x.jpg — 4025 × 2672 — 3.8 MB
- Scaled down to 75% — image-3x.jpg
  flamingo3x.jpg — 3019 × 2005 — 3.7 MB
- Scaled down to 50% — image-2x.jpg
  flamingo2x.jpg — 2013 × 1337 — 1.9 MB
- Scaled down to 25% — image-1x.jpg
  flamingo1x.jpg — 1006 × 668 — 338 KB
- flamingo-fallback.jpg — 1006 × 668 — 108 KB

srcset: Allow the browser to choose an appropriate resolution image by using `srcset` with `x`-descriptors and without `sizes`

```html
<img
 srcset="
  /img/flamingo4x.jpg 4x,
  /img/flamingo3x.jpg 3x,
  /img/flamingo2x.jpg 2x,
  /img/flamingo1x.jpg 1x
 "
 src="/wp-content/uploads/flamingo-fallback.jpg"
>
```

If the device accessing the page has a standard/low resolution display, with one device pixel representing each CSS pixel, the elva-fairy-320w.jpg image will be loaded (the 1x is implied, so you don't need to include it.) If the device has a high resolution of two device pixels per CSS pixel or more, the elva-fairy-640w.jpg image will be loaded.

### images in responsive columns

- Smartphone-Version: einspaltig 
- Tablet-Version: zweispaltig
- Desktop-Version: dreispaltig

Bei mehrspaltigen Layouts macht es keinen Sinn mehr die Bilder anhand der Viewport-Breite auszuwählen. 

```css
img {
  /* one column: mobile */  
  width:100%;
  height:auto;
}
@media screen and (min-width:28em) {
    /* two columns: tablet */
    img { width:50%; }
}
@media screen and (min-width:50em) {
  /* three columns: desktop */
    img { width:33%; }
}
```

```html
<img
    srcset="large.jpg 900w, medium.jpg 600w, small.jpg 320w"
    sizes="(min-width: 50em) 33vw, (min-width: 28em) 50vw, 100vw"
    src="small.jpg"
    alt="" 
>
(min-width: 50em) 33vw – dreispaltiges Layout. Ab 50em soll ein Bild ein Drittel des Viewports einnehmen.
(min-width: 28em) 50vw – Ab 28em Viewport-Breite soll das Bild die Hälfte (50%) des Viewports einnehmen.  100vw – Standard/Fallback
```

### examples

```html
<!-- ex1 -->

<img 
   alt="image alt text" 
   src="medium.jpg" 
   srcset="small.jpg 240w, medium.jpg 300w, large.jpg 720w" 
   sizes="(min-width: 960px) 720px, 100vw"
>

<!-- ex2 -->

<img sizes="(min-width: 960px) 540px, 100vw">

(min-width: 960px) 540px – if viewport width equals to 960px or greater than show the image with a width of 540px

100vw – fallback case (if the viewport width is smaller than 960px) then show the image as wide as the viewport.

<!-- ex3 -->

<img sizes="
            (max-width:720px) 100vw, 
            (max-width: 1260px) 70vw, 
            calc(50vw - 100px)" >

(max-width:720px) 100vw – bis zu einer Breite des Viewports von 720px wird das Bild in voller Breite angezeigt

(max-width:1260px) 70vw – bei einer Breite des Viewports bis zu 1260px tritt ein Spaltenlayout in Kraft und das Bild liegt in einer Spalte von 70% der Breite des Viewports.

calc(50vw - 100px) – bei noch größeren Viewports liegt das Bild in einer Spalte von 50% der Viewport-Breite (minus 100px Abstand zwischen den Spalten)

<!-- ex4 -->



<img srcset="/img/new-york-sm.jpg 120w,
             /img/new-york-md.jpg 200w,
             /img/new-york-lg.jpg 300w"
     sizes="((min-width: 10em) and (max-width: 20em)) 10em,
            ((min-width: 30em) and (max-width: 40em)) 30em,
             (min-width: 40em) 40em">
```




Art direction: `<picture>`
-------------------------------

Like `<video>` and `<audio>`, the `<picture>` element is a wrapper containing several `<source>` elements that provide different sources for the browser to choose from, followed by the all-important `<img>` element.

```html
<picture>
	<source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
	<source media="(min-width: 800px)" srcset="elva-800w.jpg">
	<img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
</picture>
```

* The `<source>` elements include a media attribute that contains a media condition — as with the first srcset example, these conditions are tests that decide which image is shown — the first one that returns true will be displayed. 
* The srcset attributes contain the path to the image to display. Just as we saw with `<img>` above, `<source>` can take a srcset attribute with multiple images referenced, as well as a sizes attribute. So, you could offer multiple images via a `<picture>` element, but then also offer multiple resolutions of each one.
* In all cases, you must provide an `<img>` element, with src and alt, right before `</picture>`, otherwise no images will appear. This provides a default case that will apply when none of the media conditions return true

> Note: You should use the `media` attribute only in art direction scenarios; when you do use media, don't also offer media conditions within the sizes attribute.

#### use cases

* For most responsive images, you shouldn’t use `<picture>`. You should use `srcset` and/or `sizes`.
* When you specify the media queries within `<picture>`, you are providing rules to the browser that it *must* follow. The browser has no discretion to make smart decisions about what to download based on user preference, network, etc.
* only use it when you’re solving for *art direction*, not for *resolution switching*.

# IMAGE: CSS

### width & max-width

a percentage value, makes the image scale up and down when resizing the browser window;

```css
img {
	width: 100%
}
/* the image will be responsive and scale up AND down HOWEVER the image can be scaled up to be larger than its original size.  A better solution, in many cases, will be to use the max-width property instead: */

img {
	max-width: 100%
}
/* the image will scale down if it has to (its containing column becomes narrower than the image's intrinsic size), BUT it will never grow larger and become pixelated if the column becomes wider than the image.*/
```

```html
<header>
    <a href="/index.html">
		<img src="/assets/img/unvergessen-blog-header.jpg">
	</a>
</header>
<style>
  	/* the image will be clipped and positioned always covering it's parent's area */
	img {
		width: 100%; 
		height: 100%; 
		object-fit: cover; 
		object-position: top left;
	}
</style>
```

# TYPOGRAPHY

The text size can be set with a *vw* unit (*viewport width*).  
That way the text size will follow the size of the browser window:   
Viewport is the browser window size.   
1vw = 1% of viewport width.   
If the viewport is 50cm wide, 1vw is 0.5cm.  

* *responsive*: changes (only) at set breakpoints
* *fluid*: resizes smoothly to match any device width;

Link: [fluid-typography](https://www.smashingmagazine.com/2016/05/fluid-typography/)

## responsive

* `vw`: if set with a "vw" unit, the text size will follow the size of the browser window.
* `rm`: As rems are relative to the html element, don’t forget to reset html font-size: `html { font-size:100%; }`

breakpoints: media quieres

```css
/* example 1 */
@media (min-width: 640px) {body {font-size:1rem;}} 
@media (min-width:960px) {body {font-size:1.2rem;}} 
@media (min-width:1100px) {body {font-size:1.5rem;}}

/* example 2 */

/* mobile minimum*/
@media (max-width: 599px) { body {font-size: 20px;}}
/* Responsive Zone */
@media (min-width: 600px) and (max-width: 1200px) {
  
}
/* Desktop maximum */
@media (min-width: 1201px) {body {font-size: 40px;}}
```

## fluid

Viewport units are what make fluid typography on the web possible.  
The easiest way to start using fluid typography is to set the `font-size` on the html element to be a fluid unit:

```css
html { font-size: 2vw; }
/* Now we’ve set the root element to 2vw. Therefore, we have changed the “root em.” Because all em and rem units are either directly or indirectly related to the root em, they will now be fluid */
h1 { font-size: 2em; }
/* A heading size of 2em is now equivalent to 4vw because this is twice the current font size of 2vw */
```

### drawbacks

* no precise control over the rate of scale; we don’t have min or max font sizes;
* might override the user’s font-size preferences

# VIDEO

make embedded videos responsive.

#### HTML

```html
<div class="video-container">
  <iframe src="http://player.vimeo.com/video/6284199?title=0&byline=0&portrait=0" width="800" height="450" frameborder="0"></iframe>
</div>
```

#### CSS

```css
.video-container {
	position: relative;
	padding-bottom: 56.25%;
	padding-top: 30px;
	height: 0;
	overflow: hidden;
}
.video-container iframe,  
.video-container object,  
.video-container embed {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}
```