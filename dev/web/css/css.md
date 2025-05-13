# DATA TYPES

## `<basic-shape>`

Three or more pairs of values (a polygon must at least draw a  triangle). These values are co-ordinates drawn with reference to the reference box.

* Starting at the upper right corner, the coordinates would be `X0 Y0` or `0 0`

```css
div {
 clip-path: polygon(50% 2.4%, 34.5% 33.8%, 0% 38.8%, 25% 63.1%, 19.1% 97.6%); 
 clip-path:polygon(0 0, 100% 0, 100% 100%, 0 100%); /* square */
 /* X0 Y0, X100 Y0, X100 Y100, X0 Y100 */
}
```

# FUNCTIONS / KEYWORDS

## Misc

### attr()

```html
<blockquote cite="https://mozilla.org/en-US/about/">Mozilla makes browsers, apps, code and tools that put people before profit.</blockquote>

<blockquote cite="https://web.dev/about/">Google believes in an open, accessible, private, and secure web.</blockquote>
```

```css
blockquote {
    margin: 1em 0;
}

blockquote::after {
    display: block;
    content: ' (source: ' attr(cite) ') ';
    color: hotpink;
}
```

### url()

Argument may be an URL or an ID reference to an SVG shape

```css
<css_property>: url("https://example.com/image.png")
<css_property>: url('https://example.com/image.png')
<css_property>: url(https://example.com/image.png)
```

* Using an ID of an SVG shape: circle, ellipse, line, path, polygon, polyline, or rect — using the shape's geometry as the path

```css
background-image: url(#IDofSVGpath);
```



* Using a Data URL:

```css
/* using a data URL */
background: yellow;
background: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='90' height='45'%3E%3Cpath d='M10 10h60' stroke='%2300F' stroke-width='5'/%3E%3Cpath d='M10 20h60' stroke='%230F0' stroke-width='5'/%3E%3Cpath d='M10 30h60' stroke='red' stroke-width='5'/%3E%3C/svg%3E");
```



```css
.topbanner {
  background: url("topbanner.png") #00d no-repeat fixed;
}

ul {
  list-style: square url(http://www.example.com/redball.png);
}


```

### var()

The selector given to the ruleset defines the scope that the custom property can be used in. 
A common best practice is to define custom properties on the `:root` pseudo-class, so that it can be applied globally across your HTML document:

```css
html {
	color: var(--my-color, blue); /* add a fallback value */
  /* when the given variable is not yet defined; 
  this can be useful when working with Custom Elements and Shadow DOM. */
  background-color: var(--my-var, var(--my-background, pink));
  /* pink if --my-var and --my-background are not defined */
}
```

#### pseudo-private custom properties

You use a different property internally than the one you expose, which is set to the one you expose plus the fallback:

```css
.fancy-button {
	--_color: var(--color, black);
	border: .1em solid var(--_color);
	background: transparent;
	color: var(--_color);
}

.fancy-button:hover {
	background: var(--_color);
	color: white;
}
```

#### js

```js
// assignment through JS
ELEMENT.style.setProperty('--element-width', NEW_VALUE);
// get property value through JS
ELEMENT.style.getPropertyValue('--element-width');
```



## Size

### em / rem

* on `font-size`: will be relative to the font-size of the ***parent element***
* on other properties: will be relative to the font-size of the ***current element***
* rem units sizes will always be relative to the font-size of the root `html` element

```css
.parent {
  font-size: 18px;
}
.child {
  font-size: 1.5em;
}
/* the child would have a font-size of 27px (1.5 * 18px = 27px) */
```

If the parent element doesn’t specify a value for font-size, a value will be looked for higher up in the DOM tree. 
If no font-size is specified all the way up to the root element (`<html>`), then the browser default of **16px** is used.

```css
.parent {
  font-size: 18px;
}
.child {
  font-size: 1.5em;
  padding: 2em;
}
/* font-size on .child is 27px (1.5 * 18px); padding on .child will be 54px (2 * 27px) */
```

> I use `rem` exclusively. Set the base `font-size : 62.5%`, then it's super easy to convert `px` to `rem` by just moving 1 decimal point to the left. I find this works best for  stuff like if you have the ability to resize the text on your site, so  all the elements in the design scale in tandem so you don't end up with  broken menu containers or other weirdness. It's all the same scale, so  all you need to do is change the base `font-size` percentage.  

### vw / vh

One annoying thing I've discovered about `vh` is that ***on mobile when they scroll down and the address bar is hidden then the value of vh changes*** because the viewable area is larger, which can cause reflow. So for the past few projects I've stored `window.innerHeight`  as a CSS variable and used that instead.

One of the problems with `vh` being the "largest view size" is that anything that is `height: 100vh` is now larger or overflows the screen when you first load a page. It's  pretty difficult, using just CSS, to get content to fit the page *exactly*.

The new large / small / dynamic viewport units are additions to the already existing ones. With this, the CSSWG chose to keep these “old” units ambiguous: `vw`/`vh`/`vmin`/`vmax` have no explicit definition, and it’s totally up to the browser to define how they behave. 
Some browsers will have `vh` behave like `lvh` *(like the current Safari does)*, while other browsers can make `vh` behave like `dvh`.

### vmin / vmax

* `vmin` stands for **viewport minimum** - the *smaller dimension of the viewport*. 
* `vmax` stands for **viewport maximum** - the *larger dimension of the viewport*.

### large / small / dynamic viewport units



### min()

* picks the smallest (most negative) value from a list of comma-separated expressions
* think of `min()` as **providing the *maximum* value** a property can have

```css
.el {
  width: min(50vw, 800px);
}
/* the same as */
.el {
  width: 50vw;
  max-width: 800px;
}
Wenn 50vw = 600px, dann werden 600px verwendet.
Wenn 50vw = 940px, dann werden 800px verwendet.
```

### max()

* Think of `max()` as **providing the *minimum* value** a property can have.

```css
#el {
  width: max(20vw, 400px);
  /* stop shrinking with the viewport at 400px /*
  /* minimal width is 400px	but will grow with the viewport */
}
```

### clamp()

```css
clamp(min, pref, max)
```

### fit-content

* the element will be able to resize between the min and max

```css
.box {
  width: fit-content;
}
/* ... is the same as ... */
.box {
 	width: auto;
  min-width: min-content; /* the smallest an element can be based on the content it contains */
  /* the smallest size a box can take without overflowing its content. */
  max-width: max-content; /* the largest the content of an element can push it */ 
}
```

### min-content

* represents the ***intrinsic minimum*** size of the content
* For text content this means that the content will take *all soft-wrapping* opportunities, becoming as small as the longest word.
  Therefore it may make it all too narrow

### max-content

* doesn’t allow wrapping


# RESPONSIVE DESIGN



## IMG

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



# INHERITANCE

### automatic inheritance

- `color`
- `cursor`
- `direction`
- `font`
- `font-*` (`font-family`, `font-size` etc.)
- `letter-spacing`
- `line-height`
- `text-align`
- `text-indent`
- `text-transform`
- `visibility`
- `word-spacing`

```css
.child {
  background-color: inherit; /* take the computed value of the property from its parent element */
	all: initial; /* reset all inheritable styles for a component (Shadow and Light DOM) */
}
```


PROPERTIES
==================

Misc
---------------

#### links

`linkObject.disabled = true|false` # sets or returns whether the linked document is disabled, or not.

* currently only used with style sheet links
* if true, the linked style sheet will not have any effect.

#### all

* resets all of the selected element’s properties, except the `direction` and `unicode-bidi` properties that control text direction
* allows component-level resetting of styles
  sometimes it’s far easier to start from scratch with styling rather than fight against everything that is already there

```css
.module {
  all: unset;
}
```

#### content

`content` # replaces an element with a generated value.

* `<string>` # Specifies the "alt text" for the element.   
  Non-Latin characters must be encoded using their *Unicode escape sequences*: for example, `\000A9` represents the copyright symbol.

```css
 div::before {
  content: url(image.jpg);
}
```

* Note that you cannot change the dimensions of the image when inserted this way

#### quotes

* sets how the browser should render quotation marks that are added using the open-quotes or close-quotes values of the CSS content property.

```html
<div lang="en">
  <q>This English quote has a <q>nested</q> quote inside.</q>
</div>
<div lang="fr">
  <q>This French quote has a <q>nested</q> quote inside.</q>
</div>
<div lang="de">
  <q>This German quote has a <q>nested</q> quote inside.</q>
</div>
```

```css
:lang(en) > q {
  quotes: "\201C""\201D""\2018""\2019";
}
:lang(fr) > q {
  quotes: "« " " »";
}
:lang(de) > q {
  quotes: "»" "«" "\2039""\203A";
}
```



#### line-height
`line-height: normal|number|length|%`




Effects
--------------------------------------------------------

### transparency

* whole element including children:
  use an *RGB color value with an alpha channel* (RGBA)
  `background: rgba(76, 175, 80, 0.3)` # Green background with 30% opacity
  
* only background:  
  `opacity: value` # Sets the overall transparency of an element (including child elements); takes a value from 0.0 - 1.0. The lower value, the more transparent

## Filter

```css
/* <filter-function> values */
filter: blur(5px);
filter: brightness(0.4);
filter: contrast(200%);
filter: drop-shadow(16px 16px 20px blue);
filter: grayscale(50%);
filter: hue-rotate(90deg);
filter: invert(75%);
filter: opacity(25%);
filter: saturate(30%);
filter: sepia(60%);
```







Display
----------------------------------

the most important CSS property for controlling layout; specifies if/how an element is displayed.  
Every HTML element has a default display value depending on what type of element it is. The default display value for most elements is block or inline.  

```css
.block-level {
	display: block; 
	/* Displays an element as a block element; It starts on a new line, and takes up the whole width */
	display: flex; 
	/* Displays an element as a block-level flex container */
	display: grid;
	/* Displays an element as a block-level grid container */
}

.other {
	display: list-item;
	/* Let the element behave like a `<li>` element */
	display: run-in;
	/* Displays an element as either block or inline, depending on context */
	display: contents;
	/* Makes the container disappear, making the child elements children of the element the next level up in the DOM */
	display: none; 
	/* The element is completely removed */
}

.inline-level {
	display: inline; 
	/* Any height and width properties will have no effect */ 	
	display: inline-block; 
	/* inline-level block container. The element itself is formatted as an inline element (flows inline like text), but you can apply *height* and *width* values */
	display: inline-flex; 
	/* Displays an element as an inline-level flex container */ 	
	display: inline-grid; 
	/* Displays an element as an inline-level grid container */ 	
	display: inline-table; 
	/* The element is displayed as an inline-level table */
}

.table {
	display: table;
	/* Let the element behave like a <table> element */ 	
	display: table-caption;
	/* Let the element behave like a <caption> element */ 	
	display: table-column-group; 
	/* Let the element behave like a <colgroup> element */ 	
	display: table-header-group;
	/* Let the element behave like a <thead> element */ 	
	display: table-footer-group;
	/* Let the element behave like a <tfoot> element */ 	
	display: table-row-group;
	/* Let the element behave like a <tbody> element */ 	
	display: table-cell;
	/* Let the element behave like a <td> element */ 	
	display: table-column;
	/* Let the element behave like a <col> element */ 	
	display: table-row; 
	/* Let the element behave like a `<tr>` element */
}
```

#### block elements

* always start on a new line 
* take up the full *width* available   
  (stretches out to the left and right as far as it can).

Block elements by default:

```html
<div>		</div>
<h1>		</h1>
<h6>		</h6>
<p>			</p>
<form>		</form>
<header>	</header>
<footer>	<footer>
<section>	<section>
```

#### inline elements

* do not start on a new line
* only take up as much *width* as necessary

```html
<!-- inline elements by default: -->
<span>		<span>
<a>			<a>
<!-- inline-block by default: -->
<img> 
```

* inline-block: flow inline like text, but also have a *width* and *height* like block elements


Flow / Wrap
--------------------------------------------------------------

#### overflow-wrap

applies to *inline elements*, setting whether the browser should insert line breaks within an otherwise unbreakable string to prevent text from overflowing its line box.

```css
span {
	overflow-wrap: normal;
	overflow-wrap: break-word;
	overflow-wrap: anywhere;

/* break-word: To prevent overflow, an otherwise unbreakable string of characters — like a long word or URL — may be broken at any point if there are no otherwise-acceptable break points in the line. No hyphenation character is inserted at the break point. Soft wrap opportunities introduced by the word break are considered when calculating min-content intrinsic sizes. */

/* anywhere: The same as the anywhere value, with normally unbreakable words allowed to be broken at arbitrary points if there are no otherwise acceptable break points in the line, but soft wrap opportunities introduced by the word break are NOT considered when calculating min-content intrinsic sizes.*/
}
```

#### word-break

```css
div {
	word-break: normal; /* Use the default line break rule. */
	word-break: break-all; /* To prevent overflow, word breaks should be inserted between any two characters */
	word-break: keep-all;
}

```

#### overflow

* sets what to do when an element's content is too big to fit in its block formatting context.
* specifies whether to clip content or to add scrollbars when an element's content is too big to fit in a specified area.

shorthand for 

* `overflow-x`

* `overflow-y` 

  

if two keywords are specified, the first applies to overflow-x and the second to overflow-y. 

Otherwise, both are set to the same value. 

Specifying a value other than `visible` (the default) creates a new block formatting context.

In order for overflow to have an effect, **the block-level container must have either a set height** (`height` or `max-height`) 

or `white-space` set to `nowrap`



* `visible` # Content is not clipped and may be rendered outside the padding box. Default.
* *`hidden`*  # The overflow is clipped, and the rest of the content will be invisible 	
* `clip` # The overflow is clipped to the element's padding box. The difference between to hidden is that the clip keyword forbids all scrolling, including programmatic scrolling. The box is not a scroll container, and does not start a new formatting context.
* *`scroll`*  # The overflow is clipped, but a scroll-bar is added (whether or not any content is actually clipped).* `auto` # depends on the user agent. If content fits inside the padding box, it looks the same as visible, but still establishes a new block formatting context. Desktop browsers provide scrollbars if content overflows. 
* *`auto`*    # If overflow is clipped, a scroll-bar should be added to see the rest of the content


#### shape-outside

* Defines a shape—which may be non-rectangular—around which *adjacent inline content should wrap*.
* Doesn't change the shape of the div itself. If we add borders and a background-image we’ll find that the bounding box is in fact still rectangular.
* shape-outside *changes the relationship of other elements around an element*, not the geometry of the element itself. 
* In order to change geometry of the element itself, use shape-outside alongside the `clip-path()` property
* The element that shape-outside should be applied to has to be *floated*. It also has to have a *defined width and height*. 
* https://developer.mozilla.org/en-US/docs/Web/CSS/basic-shape

By default, inline content wraps around its *margin box*; shape-outside provides a way to customize this wrapping, making it possible to wrap text around complex objects rather than simple boxes.

* basic shapes:
  * `circle()` # for making circular shapes.
  * `ellipse()` # for making elliptical shapes.
  * `polygon()` # for creating .
* `url()` # identifies which image should be used to wrap text around.
* box:
  * `margin-box` (default)
  * `padding-box`
  * `border-box`

```css
/* --- BASIC SHAPES --- */

.ellipse {
	shape-outside: ellipse(150px 300px at 50% 50%);
}
.circle {
	shape-outside: circle(50%); /* creates a circle with a radius that is half the height and width of .circle */
}
.inset {
	shape-outside: inset(top right bottom left border-radius)` /* for making rectangular shapes. */
}
.polygon {
	/* any shape with 3 or more vertices */
	shape-outside: polygon(0 0, 100% 0, 100% 100%, 0 100%);
  	transition: shape-outside 1s;
}
/* 	if this property is going to be animated it requires the same number of vertices when you declare the animated state: */
.polygon:hover {  
	shape-outside: polygon(0 0, 100% 50%, 100% 50%, 0 100%);
}

/* --- OTHER --- */

.url {
	shape-outside: url('image.png'); 
  	shape-image-threshold: 0.5; /* informs the browser which pixels, depending on their transparency (0.0 - 1.0), should create the shape */
}
#with-clip-path {
  shape-outside: ellipse(100px 200px at 50% 50%);
  clip-path: ellipse(100px 200px at 50% 50%);
  width: 300px;
  height: 500px;
  float: left;
  opacity: .2;
  background-color: purple;
}
```


Alignment: Flexbox and Grid
------------------------------

### align-items / justify-items

#### common values

```css
.align-items,
.justify-items {
	/* Basic keywords */
	justify-items: auto | normal | stretch;

	/* Positional alignment */
	justify-items: center | start | end;    /* Pack items around the center | start | end */
	justify-items: left | right;       		/* Pack items from the left | right */
	justify-items: flex-start | flex-end; 	/* Equivalent to 'start'. Note that justify-items is ignored in Flexbox layouts. */
	justify-items: self-start | self-end;

	/* Baseline alignment */
	justify-items: baseline;
	justify-items: first baseline;
	justify-items: last baseline;
}
```

#### justify-items

*Horizontal alignment*. Defines the default `justify-self` for all items of the box, giving them all a default way of justifying each box along the appropriate axis.

* block-level # aligns the items inside their containing block on the inline axis.
* `position: absolute` # aligns the items inside their containing block on the inline axis, accounting for the offset values of top, left, bottom, and right.
* table # ignored
* flexbox # ignored
* grid # aligns the items inside their grid areas on the inline axis

#### align-items

*Vertical alignment*. Sets the `align-self` value on all direct children as a group. 

* flexbox # controls the alignment of items on the *Cross Axis*. 
* grid # controls the alignment of items on the *Block Axis* within their grid area.

### align-content / justify-content

* flex-box and grid

#### common values

```css
.justify-content {
	/* Positional alignment */
	justify-content: center | start | end;    /* Pack items around the center | start | end */
	justify-content: flex-start | flex-end; /* Pack flex items from the start | end */
	justify-content: left | right;       /* Pack items from the left */

	/* Distributed alignment */
	justify-content: space-between; /* Distribute items evenly. The first item is flush with the start, the last is flush with the end */
	justify-content: space-around;  /* Distribute items evenly. Items have a half-size space on either end */
	justify-content: space-evenly;  /* Distribute items evenly. Items have equal space around them */
	justify-content: stretch;       /* Distribute items evenly. Stretch 'auto'-sized items to fit the container */
}
```
#### justify-content

Defines how to *distribute space between and around content items* along
* flex container # main-axis
* grid container # inline axis

#### align-content

Sets the *distribution of space between and around content items* along 
* flex container: cross-axis 
* grid container: block axis
* has no effect on single line flex containers (i.e. ones with flex-wrap: nowrap).


Alignment: Inline elements
----------------------------------------------------

#### vertical-align
sets vertical alignment of an *inline*, *inline-block* or *table-cell* box.  
you can't use it to vertically align block-level elements.  

```css
.inline {
	vertical-align: baseline;
	vertical-align: sub; 
    /* Aligns the baseline of the element with the subscript-baseline of its parent. */
	vertical-align: super; 
    /* Aligns the baseline of the element with the superscript-baseline of its parent. */
	vertical-align: text-top;
	vertical-align: text-bottom;
	vertical-align: middle;
	vertical-align: top;
	vertical-align: bottom;
}
```

Alignment: Block elements
----------------------------------------------------------------

#### text-align

* affects all inline or inline-block elements in that container
* applies to: block containers

```css
div {
	text-align: start|end;
	text-align: left|right;
	/* The inline contents are aligned to the left|right edge of the line box */
	text-align: center;
	/* The inline contents are centered within the line box */
	text-align: justify; 
	/* The inline contents are justified. 
    Text should be spaced to line up its left and right edges to the left and right edges 
    of the line box, except for the last line. */
	text-align: justify-all;
	/* Same as justify, but also forces the last line to be justified. */
	text-align: match-parent;
	/* aligning the inner content of a block element. */
}
```


Alignment: Replaced elements
---------------------------------------------------------------

<img>`, `<video>`, `<embed>` or `<iframe>

```css
img {
	object-position: /* alignment of the replaced element's content object within the element's box. */
}
```

Alignment: Center ...
-------------------------------------

#### block element

```css
.center { /* center horizontally */
	margin: auto;
	width: 50%;
}

.center {
 	position: absolute;
	top: 50%;
  left: 50%; /* the position is calculated from the top left corner */
  transform: translate(-50%, -50%);  /* pull back the item with the half of its width and height */
}
```
#### text

or inline elements

```css
.text {
	text-align: center; /* center horizontally */
	vertical-align: middle; /* center vertically */
}
```

#### image

```css
/* center horizontally */
img {
	display: block;
	margin-left: auto;
	margin-right: auto;
	width: 50%; /* optional */
}
```

#### header image (wraped in anchor)

```html
<header style="text-align: center">
    <a href="/index.html">
		<img src="assets/img/unvergessen-blog-header.jpg" alt="unvergessen header image"/>
	</a>
</header>
```

#### flexbox

```css
/* set both the justify-content and align-items properties to center, and the flex item will be perfectly centered: */
.flex-container {
	display: flex;
	height: 300px;
	/* center horizontally */
	justify-content: center;
	/* center vertically */
	align-items: center;
}
```


Text
--------------------------------------------------

### text-decoration

```css
text-decoration: line color style; /* shorthand */
text-decoration-line: none|underline|overline|line-through;
/* you can also combine more than one value, like ... */
text-decoration-line: underline overline;

text-decoration-color: color;

text-decoration-style: solid|double|dotted|dashed|wavy;
```

### text-transform

```css

```


### text-shadow

```css
/* offset-x | offset-y | blur-radius | color */
text-shadow: 1px 1px 2px black; 

/* color | offset-x | offset-y | blur-radius */
text-shadow: #fc0 1px 0 10px; 

/* offset-x | offset-y | color */
text-shadow: 5px 5px #558abb;

/* color | offset-x | offset-y */
text-shadow: white 2px 5px;

/* offset-x | offset-y
/* Use defaults for color and blur-radius */
text-shadow: 5px 10px;

/* use it to create a stroked text */
.stroke-text{
  	text-shadow: 1px 0 0 black, 0 1px 0 black, -1px 0 0 black, 0 -1px 0 black;
}
```

### text-align | vertical-align

```css
.text {
	text-align: center; /* center horizontally */
	vertical-align: middle; /* center vertically */
}
```



Lists
-------------------------------------------------------------

* `list-style-type: circle|square|disc`
* `list-style-image: url('sqpurple.gif');`
* `list-style-position:` 
  * `outside` 	# the bullet points will be outside the list item. The start of each line of a list item will be aligned vertically. This is default.
  * `inside`	# the bullet points will be inside the list item. As it is part of the list item, it will be part of the text and push the text at the start.


Tables
---------------

### table-layout

`table-layout: auto|fixed` # defines the algorithm used to lay out table cells, rows, and columns
* `auto` # browsers use an automatic table layout algorithm. The column width is set by the widest unbreakable content in the cells. The content will dictate the layout
* `fixed` # sets a fixed table layout algorithm. The table and column widths are set by the widths of table and col or by the width of the first row of cells. Cells in other rows do not affect column widths. If no widths are present on the first row, the column widths are divided equally across the table, regardless of content inside the cells. The main benefit of `table-layout: fixed`; is that the table renders much faster. On large tables, users will not see any part of the table until the browser has rendered the whole table. So, if you use it, users will see the top of the table while the browser loads and renders rest of the table. This gives the impression that the page loads a lot quicker!

### column-count

`column-count: number|auto` # specifies the number of columns an element should be divided into  

```html
<!-- Divide the text in the <div> element into three columns: -->
div {
  column-count: 3;
}
```

### column-gap

`column-gap: length|normal` # specifies the gap between the columns

### column-width

The number of columns will be the minimum number of columns needed to show all the content across the element.  
It's a flexible property. Think of column-width as *a minimum width suggestion for the browser*. Once the browser cannot fit at least two columns at your specified width then the columns will stop and drop into a single column.

`column-width: auto|length` # specifies the column width.


### column-rule

```css
table {
	column-rule: 5px solid black;
	/* column-rule-width */
	/* column-rule-style */
	/* column-rule-color */
}


### border-collapse

Sets whether cells inside a `<table>` have shared or separate borders.  
When cells are separated, the distance between cells is defined by the `border-spacing` property.

```css
table {
	border-collapse: collapse;
	border-collapse: separate;
}
```

Replaced elements
-----------------------------------------------------

`<img>`, `<video>`, `<embed>`, `<iframe>`, `<textarea>`

#### object-fit

* Defines how an element responds to its `height` and `width`
  ... how it should be *resized* to match those values
   Therefore its required to set these properties
* Without object-fit the aspect ratio of the images will be destroyed when resizing (*fill*)
* In order to match the values of the elements container, use `width: 100%` and `height: 100%`

```css
img {
	object-fit: fill; 
    /* Default. The replaced content is sized to fill the element's content box. 
    If necessary, the object will be stretched or squished to fit */
	object-fit: contain;
	/* Preserve aspect ratio while the entire object is made to fill the box 
	(it will be "letterboxed" if its aspect ratio does not match the aspect ratio 
    of the box.) */
	object-fit: cover;
	/* Preserve the aspect ratio while filling the element's entire content box. 
    The object will be clipped to fit */
	object-fit: none;
  	/* The replaced content is not resized */
	object-fit: scale-down;
	/* The content is sized as if 'none' or 'contain' were specified 
	(would result in a smaller concrete object size) */

}
```

#### object-position

Specifies the alignment of the selected replaced element's contents within the element's box.  
Areas of the box which aren't covered by the replaced element's object will show the element's background.

* Initial value: 50% 50%

```css
2-values {
/* object-position: horizontal vertical */
	object-position: 50% 50%; /* center */
	object-position: right top; /* move to right top corner */
	object-position: 20px; /* move left edge of img 20px away from the left edge of box */
	object-position: 100% 10%; /* 1. move right edge of img to the right edge of the box 2. move the top edge 10% of img away from the top of the box */
	object-position: left bottom;

4-values {
/* object-position: keyword offset keyword offset */
	object-position: left 125px top 20%;
}
```

### examples

```css
img {
    width: 50vw;
    height: 50vh;
    object-fit: contain;
    object-position: left 50% top 10%;
    border: thick dotted orange;
    background-color: purple;
	border-radius: 50%;
	/* to create rounded images */
}
.grid-cell > img {
    height: 100%;
    width: 100%;
    object-fit: contain;
    object-position: 50% 50%;
}
```

### size

a percentage value, makes the image scale up and down when resizing the browser window;

#### width & max-width

If you set a fixed `width` and a `max-width`, this means the following:
*If the width goes above `max-width`, keep it at `max-width`. If the width is below `max-width`, keep it on `width`.*

```css
img {
	width: 100%; 
	/* however the image can be scaled up to be larger than its original size. */
	max-width: 100%;
	/* the image will scale down if it has to, 
	but never scale up to be larger than its original size */
	height: auto;
}
```

Nachteile: Entweder werden auf kleinen Displays zu große Dateien geladen, was der Performance schadet, oder das Bild wird über seine tatsächliche Breite hinaus skaliert. Letzteres führt zu schlechter Bildqualität auf großen Displays. Hochauflösende Displays werden gar nicht berücksichtigt.


#### image-set() for resolution switching

```css
div {
	background-image: image-set( "foo.png" 1x, "foo-2x.png" 2x);
}
```

#### center

```css
img {
	display: block;
	margin-left: auto;
	margin-right: auto;
	width: 50%;
}
```

## Scrollbars

```css
.styled-scrollbars {
  --scrollbar-foreground: #999
  --scrollbar-background: #333
  /* Foreground, Background */
  scrollbar-color: var(--scrollbar-foreground) var(--scrollbar-background);
}
.styled-scrollbars::-webkit-scrollbar {
  width: 10px; /* Mostly for vertical scrollbars */
  height: 10px; /* Mostly for horizontal scrollbars */
}
.styled-scrollbars::-webkit-scrollbar-thumb { /* Foreground */
  background: var(--scrollbar-foreground);
}
.styled-scrollbars::-webkit-scrollbar-track { /* Background */
  background: var(--scrollbar-background);
}
```

## Visibility

Kevin is correct in that `visibility: hidden` and `display: none` will be equally performant since they both retrigger layout, paint and composite. However, `opacity: 0` is functionally equivalent to `visibility: hidden` and does not retrigger the layout step, so I would advise using that if you don't mind the empty space still being allocated (otherwise use `display: none`).

```ini
										collapse events taborder
opacity: 0              No     Yes     Yes
visibility: hidden      No     No      No
visibility: collapse   Yes*    No      No
display: none          Yes     No      No
```

* `display: none` removes the element from the normal flow of the page, allowing other elements to fill in.
* `visibility: hidden` leaves the element in the normal flow of the page such that is still occupies space.

```css
div {
  opacity: 0; /* 3rd */
  /* just makes the element invisible */
  visibility: hidden; /* 2nd */
  /* makes the el invisible and removes for example cursor interactions */
  display: none; /* strongest */
  /* removes the element from the normal flow of the page, allowing other elements to fill in. */
  
}
```

## background-image

### multiple background-images

These are layered with the first background you provide on top and the last background listed in the back: **first is on top**

**Only the last background can include a background color.**

### use svg

There are TWO methods for displaying SVG images as a CSS background image:

1. Link directly to an SVG file

```css
.your-class {
   background-image: url( '/path/image.svg' );
}
```

1. Placing SVG code as the source.

```css
.your-class {
   background-image: url( "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 600 200'%3E%3Cpath d='M10 10h123v123H10z'/%3E%3C/svg%3E" );
}
```

#### layering background images

```css
background-image:
   url( '/path/image-1.svg' ),
   url( '/path/image-2.svg' ),
   url( '/path/image-3.svg' );
```

You can mix images, SVG data URIs, and CSS gradients. But you need to overlap images with transparency or take advantage of the  background-blend-mode discussed above. Otherwise you will only see one background. The first image is on top of the background stack.

LAYOUT
==================================================

## Notes

### grid vs flex

flexbox pros:

* items automatically wrap depending on their width
  - different sizes of columns are possible



## Solutions

### overflow-x on mobile devices

* attention: this rule breaks `position: sticky`

```css
html, body {
	/* prevent overflow-x on mobile devices */
	overflow-x: hidden;
  height: 100vh; /* prevent breaking position: sticky */
}
```

### stack elements

#### position

relative parent - absolute child: If we put `position: relative;` on the parent element, anything inside of it with `position: absolute;` will be placed absolutely, relative to that containing unit!

```css
.child {
  /* ... */
  position: absolute;
  top: 0;
  left: 0;
}

.parent {
  position: relative;
}
```

#### grid

And if one element should stack on the other, we can put them in the  exact same grid area. Let’s also offset them slightly by using a margin.

```css
.parent {
  display: grid;
  grid-template-columns: 250px 1fr;
  grid-template-rows: 150px 1fr;
}

.child {
  grid-area: 1 / 1 / 2 / 2;
}

.child-2 {
  margin-left: 200px;
}
```



## Block (float)

#### width & max-width

If you set a fixed `width` and a `max-width`, this means the following:
*If the width goes above `max-width`, keep it at `max-width`. If the width is below `max-width`, keep it on `width`.

### clearfix

If an element is taller than the element containing it, and it is floated, it will overflow outside of its container.  
Use the "clearfix" hack to fix this.

![css clearfix](/img/css_clearfix.png)

```css
.clearfix {
  overflow: auto;
}
```