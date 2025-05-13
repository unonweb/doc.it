SELECTORS
===================

## attributes vs classes

The predominant styling hooks in HTML/CSS are classes. 

* classes and attribute selectors have exactly the same specificity

### classes

* on/off: an element either *has it or it doesn’t*
* js methods via `classList`

### attributes

* on/off ability *plus* the ability to select based on the value it has at the same specificity level.

```css
/* Select any element with this data attribute and value */
[data-size="large"] {
  padding: 2rem;
  font-size: 125%;
}

/* You can scope it to an element or class or anything else */
button[data-type="download"] { }
.card[data-pad="extra"] { }
```



## attribute selectors

selects all elements that have the given attribute.

```css
[data-value] {
  /* Attribute exists */
}

[data-value="foo"] {
  /* Attribute has this exact value */
}

[data-value*="foo"] {
  /* Attribute value contains this value somewhere in it */
}

[data-value~="foo"] {
  /* Attribute has this value in a space-separated list somewhere */
}

[data-value^="foo"] {
  /* Attribute value starts with this */
}

[data-value|="foo"] {
  /* Attribute value starts with this in a dash-separated list */
}

[data-value$="foo"] {
  /* Attribute value ends with this */
}
```

```css
input[type="text"] { padding: 3px; }
input[type="radio"] { float: left; }

a[href^="http://afriendssite.com"] {
/* Style every single link to a friends site different than other links.  
No matter if you are linking to their homepage or any subpage, any links to them you want to style up. */
	color: red;
}

/* label file download anchor links with icons based on what type of file they are */
a[href$=".pdf"] { background: url(icon-pdf.png) left center no-repeat; padding-left: 30px; }
a[href$=".doc"] { background: url(icon-doc.png) left center no-repeat; padding-left: 30px; }
```

### ignore case

In case you’re needing to correct for possible capitalization inconsistencies in your data attributes, the attribute selector has a case-insensitive variant for that.

```css
/* Will match
<div data-state="open"></div>
<div data-state="Open"></div>
<div data-state="OPEN"></div>
<div data-state="oPeN"></div>
*/
[data-state="open" i] { }
```

### multiple attributes

```css
input[name="Sex"][value="M"] {} /* elements that have both attributes */
input[name="Sex"], input[value="M"] {} /* elements that have one or the other */
```

### attributes and class

```css
:host([attribute1].class1) {
  /* has both class class1 and [attribute1] */
}
:host([attribute1]) .class1 {
  /* anything with class1 inside the shadow DOM of the component with [attribute1] */
}
```

combinators
------------------

```css
/* combined */
.menu.item {
    /* all elements with both menu and item set within its class attribute */
}
.menu .item {
    /* all elements with an .item class which are children of a .menu class element */
}

/* descendant */
div p {
	/* all <p> elements inside <div> elements */
}

/* child */
div > p {
	/* all <p> elements where the parent is a <div> element */
}

/* subsequent-sibling  */
p ~ span {
	/* all <span> elements that follow a <p>, immediately or not */
	/* both share the same parent */
}

/* next-sibling */
div + p {
	/* all <p> elements that directly follow a <div> element */
	/* both share the same parent */
}
```

## :pseudo classes

The :keyword pseudo allow the selection of elements based on state information that is not contained in the document tree. 

> style an element based on its state

Pseudo-classes let you apply a style to an element not only in relation to the content of the document tree, but also in relation to external factors like the history of the navigator (`:visited`), the status of its content (`:checked`), or the position of the mouse (`:hover`)

| selector         | example               | explanation                                                  |
| ---------------- | --------------------- | ------------------------------------------------------------ |
| `:invalid`       | `input:invalid`       | all input elements with an invalid value                     |
| `:focus`         | `input:focus`         | the input element which has focus                            |
| `:checked`       | `input:checked`       | every checked `<input>` element                              |
| `:default`       | `input:default`       | the default `<input>` element                                |
| `:disabled`      | `input:disabled`      | every disabled `<input>` element                             |
| `:enabled`       | `input:enabled`       | every enabled `<input>` element                              |
| `:in-range`      | `input:in-range`      | input elements with a value within a specified range         |
| `:out-of-range`  | `input:out-of-range`  | input elements with a value outside a specified range        |
| `:optional`      | `input:optional`      | input elements with no "required" attribute                  |
| `:required`      | `input:required`      | input elements with the "required" attribute specified       |
| `:indeterminate` | `input:indeterminate` | input elements that are in an indeterminate state            |
| `:valid`         | `input:valid`         | all input elements with a valid value                        |
| `:read-only`     | `input:read-only`     | input elements with the "readonly" attribute specified       |
| `:read-write`    | `input:read-write`    | input elements with the "readonly" attribute NOT specified   |
| `:active`        | `a:active`            | the active link                                              |
| `:hover`         | `a:hover`             | links on mouse over                                          |
| `:visited`       | `a:visited`           | all visited links                                            |
| `:empty`         | `p:empty`             | every `<p>` element that has no children (including text nodes) |

### :defined

represents any element that has been defined (with `CustomElementRegistry.define()`)

```css
un-element:not(:defined) {
  display: none;
}
```

### :host

```css
/* style custom element from outside */
my-custom-el {
  width: calc(50% - 1em);
}
/* style custom element from inside */
:host {
  width: calc(50% - 1em);
}
```

### :focus-visible

applies when

1. an element matches `:focus` pseudo-class 
2. and the User Agent determines via heuristics that the focus should be made *evident* on the element

Because the `:focus-visible` pseudo-class matches the focused element when needed, using the `:focus-visible` (instead of the `:focus` pseudo-class) allows authors to change the appearance of the focus indicator without changing when the focus indicator appears.

### :scope

When used within a DOM API call — such as `querySelector()`, `querySelectorAll()`, `matches()`, or `Element.closest()` 
— `:scope` matches the element on which the method was called.

```css
/* Selects a scoped element */
:scope {
  background-color: lime;
}
```

### :last-child

represents the last element among a group of **sibling elements**

### :first-of-type

represents the first element of its type among a group of **sibling elements**

```css
dd:first-of-type {
  border: 2px solid orange;
}
```

### :last-of-type

represents the last element of its type among a group of **sibling elements**

```css
dd:last-of-type {
  border: 2px solid orange;
}
```

### order of :selectors

1. `a:link`
2. `a:visited`
3. `a:hover`
4. `a:active`

## :pseudo class functions

### :lang()

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

### :not()

You can negate several selectors at the same time.

```css
:not(.foo, .bar) {}
/* is equivalent to */
:not(.foo):not(.bar) {}
```



```js
document.querySelector('un-lang-switch slot:not(slot[name=drop-head])')
```

### shadow dom

#### :host()

* only descendants inside the shadow DOM can be accessed — nothing that’s been slotted into your web component (without using ::slotted).

```css
/* allows you to make changes should certain classes be added to the custom element */
:host(.high) {
  border: 2px solid blue;
}
:host(:hover) {
  border: 2px solid blue;
}

/* with attributes */
:host([theme='tertiary']) button {
    /* selects the button inside the :host-element if theme is set to 'tertiary' */
}
button[theme='tertiary'] {
    /* in this case the attribute has to be passed to the button element, too */
}
```

#### :host-context()

allows a custom element, or anything within that custom element's shadow DOM, to **apply different styles based on its position within the outer DOM** or classes/attributes applied to ancestor elements.

```css
/* Selects a shadow root host, only if it is
   a descendant of the selector argument given */
:host-context(h1) {
  font-weight: bold;
}

:host-context(main article) {
  font-weight: bold;
}

/* Changes paragraph text color from black to white when
   a .dark-theme class is applied to the document body */
p {
  color: #000;
}

:host-context(body.dark-theme) p {
  color: #fff;
}
```

### :nth-child()

`element:nth-child(value)` # matches elements based on their position in a ***group of siblings***

Note that the child count includes children of any element type; but it is considered a match only if the element *at that child position* is of the specified element type.

* keyword values:
  * `odd` 	# Represents elements whose numeric position in a series of siblings is odd: 1, 3, 5, etc.
  * `even` 	# Represents elements whose numeric position in a series of siblings is even: 2, 4, 6, etc.
* functional notation:
  * `<An+B>` or `(a*n+b)` # It can be read as the *An+Bth* element of a list.
  * `A` # an integer step size
  * `n` # all positive integers, starting from 0. (= *natürliche Zahlen*)
  * `+` is an operator and may be either “+” or “-”
  * `B` # an integer offset

```css
li:nth-child(even) {}
li:nth-child(odd) {}
li:nth-child(2) {} /* Selects the second <li> element in a list */
li:nth-child(4n) {} /* Selects every fourth element: 4 (4×1), 8 (4×2), 12 (4×3) */
li:nth-child(5n) /* 5 (5×1), 10 (5×2), 15 (5×3) */
li:nth-child(3n+4) /* 4 [=(3×0)+4], 7 [=(3×1)+4], 10 [=(3×2)+4], 13 [=(3×3)+4] */
li:nth-child(n+7) /* Represents the seventh and all following elements: 7 [=0+7], 8 [=1+7], 9 [=2+7] */

un-header-banner .overlay > :nth-child(1) {} /* selects the first direct child of .overlay */
un-header-banner .overlay > :nth-child(2) {} /* selects the second direct child of .overlay */
```

### :nth-of-type()

matches elements of a given type (tag name), based on their position among a ***group of siblings***

```css
p:nth-of-type(4n) {} /* selects every fourth p element among any group of siblings */
div:nth-of-type(2n) {} /* selects every second div element in the entire document */

p.fancy:nth-of-type(2n + 1) {
  text-decoration: underline;
}
```

There is no way to select the nth-of-class using this selector. The  selector looks at the type only when creating the list of matches. You  can however apply CSS to an element based on `:nth-of-type` location **and** a class, as shown in the example above.

### selector lists

```css
span {
  border: red 2px solid;
}
div {
  border: red 2px solid;
}
/* equals */
span,
div {
  border: red 2px solid;
}
/* equals */
:is(span, div) {
  border: red 2px solid;
}
```

#### simplify selector lists

:is() or :where() are particularly useful when dealing with HTML sections and headings. 
Since `<section>`, `<article>`, `<aside>`, and `<nav>` are commonly nested together, without :is(), styling them to match one another can be tricky.

**style all `<h1>` at different depths **
**- without is()**

```css
/* Level 0 */
h1 {
  font-size: 30px;
}

/* Level 1 */
section h1,
article h1,
aside h1,
nav h1 {
  font-size: 25px;
}

/* Level 2 */
section section h1,
section article h1,
section aside h1,
section nav h1,
article section h1,
article article h1,
article aside h1,
article nav h1,
aside section h1,
aside article h1,
aside aside h1,
aside nav h1,
nav section h1,
nav article h1,
nav aside h1,
nav nav h1 {
  font-size: 20px;
}

/* Level 3 */
/* don't even think about it! */
```

**style all `<h1>` at different depths **
**- with is()**

```css
/* with is() */

/* Level 0 */
h1 {
  font-size: 30px;
}
/* Level 1 */
:is(section, article, aside, nav) h1 {
  font-size: 25px;
}
/* Level 2 */
:is(section, article, aside, nav) :is(section, article, aside, nav) h1 {
  font-size: 20px;
}
/* Level 3 */
:is(section, article, aside, nav)
  :is(section, article, aside, nav)
  :is(section, article, aside, nav)
  h1 {
  font-size: 15px;
}
```

#### :where()

 takes a selector list as its argument, and selects any element that can be selected by one of the selectors in that list

##### difference to :is()

* `:where()` always has 0 specificity
* `:is()` takes on the specificity of the most specific selector in its arguments

**use `where` if you want to be able to override the css rule with a simple selector later**

```css
:is(section, aside, footer.my-link) a {
  color: red;
}
footer a {
  color: blue;
}
/* This won't work because the selectors inside :is() 
count towards the specificity of the overall selector, 
and class selectors have a higher specificity than element selectors. */

:where(section, aside, footer.my-link) a {
  color: orange;
}
footer a {
  color: blue;
}
/* However, selectors inside :where() have specificity 0, 
so the orange footer link will be overridden by our simple selector. */
```

#### :is()

takes a selector list as its argument, and selects any element that can be selected by one of the selectors in that list. 
This is useful for writing large selectors in a more compact form.

### :has()

* https://www.smashingmagazine.com/2023/01/level-up-css-skills-has-selector/

a way of selecting a parent element or a previous sibling element with respect to a reference element by taking a (forgiving relative) selector list as an argument.

```css
/* Selects an h1 heading with a p element 
that immediately follows the h1 and applies the style to h1 */
h1:has(+ p) { 
  margin-bottom: 0; 
}

/* adjust the spacing after H1 headings 
if they are immediately followed by an H2 heading */
h1, h2 {
  margin: 0 0 1.0rem 0;
}
h1:has(+ h2) {
  margin: 0 0 0.25rem 0;
}

/* combined with :is() */
h1, h2, h3 {
  margin: 0 0 1.0rem 0;
}
:is(h1, h2, h3):has(+ h2, + h3, + h4) {
  margin: 0 0 0.25rem 0;
}
/* :is() selects any of the heading elements in the list */
/* :has() selects any H1, H2, or H3 element that is immediately followed by (indicated by +) an H2, H3, or H4 element */
/* finally the CSS rule reduces the spacing after such H1, H2, or H3 elements */
```

```css
/* selects all siblings of h1 that have another h1 sibling (but excludes the h1 itself) */
/* furthermore selects the siblings of the last h1 */
rich-text h1.red ~ :has(~ h1):not(h1),
rich-text h1.red:last-of-type ~ * {
  color: red;
}
```

#### select siblings

```html
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="circle"></div>
<div class="box"></div>
```

```css
/* select previous sibling */
.box:has(+ .circle) {
  width: 40px;
  height: 40px;
}

/* select all previous siblings */
.box:has(~ .circle) {
  width: 40px;
  height: 40px;
}
```

#### select within a range

```html
<article>
  <h2>Lorem, ipsum.</h2>
  <!-- h2 range starts -->
  <p>Lorem ipsum, dolor sit amet consectetur adipisicing elit.</p>
  <p>Nobis iusto voluptates reiciendis molestias, illo inventore ipsum?</p>
  <!-- h2 range ends -->
  <h2>Lorem, ipsum dolor.</h2>
  <p>Lorem ipsum dolor sit amet.</p>
  <hr>
  <!-- hr range starts -->
  <p>Lorem ipsum dolor sit.</p>
  <p>Dolor animi nisi ut?</p>
  <p>Sunt consectetur esse quia.</p>
  <!-- hr range ends -->
  <hr>
  <p>Lorem ipsum dolor sit amet consectetur adipisicing elit.</p>
</article>
```

```css
/* select between <hr> and <hr>
/* select all sibling elements following an hr that themselves have a later sibling of an hr */
article hr ~ :has(~ hr)
/* matches all three paragraphs between the hr elements */
```

#### logical operations

The :has() relational selector can be used to check if one of the multiple features is true or if all the features are true.

By using comma-separated values inside the `:has()` relational selector, you are checking to see if any of the parameters exist. `x:has(a, b)` will style `x` if descendant `a` OR `b` exists.

By chaining together multiple `:has()` relational selectors together, you are checking to see if all of the parameters exist. `x:has(a):has(b)` will style `x` if descendant `a` AND `b` exist.

```css
body:has(video, audio) {
  /* styles to apply if the content contains audio OR video */
}
body:has(video):has(audio) {
  /* styles to apply if the content contains both audio AND video */
}
```

### has not

* `.card:has(:not(img))` means “select a `.card` that has any element that is not an image”. 
  This means that the selector only wouldn't apply if the card only contained images.
* `.card:not(:has(img))` means “select a `.card` doesn't have (not has) an image”,

## ::pseudo-elements

> style a specific part of a selected element

### ::before | ::after

* allow you to insert content onto a page without it needing to be in the HTML
* while the end result is not actually in the DOM, it appears on the page as if it is
* Will behave just like a child element *mostly*
  One tricky thing is that *no selector selects it* other than the one you used to create it
* they should pretty much *only* be decorative
* inline by default

```css
element::before {
  /* creates a pseudo-element that is the first child of the selected element. */
  content: "some text";
  content: '❭';
}
```

### ::first-line

### ::first-letter

### ::selection



### ::backdrop

* is a box the size of the viewport, which is rendered immediately beneath any element being presented in the top layer.
* makes it possible to obscure, style, or completely hide everything located below a top layer element

This includes:

* Elements which have been placed in fullscreen mode using the Fullscreen API `Element.requestFullscreen()` method.
* `<dialog>` elements that have been shown in the top layer via a `HTMLDialogElement.showModal()` call.
* Popover elements that have been shown in the top layer via a `HTMLElement.showPopover()` call.

<dialog> elements that have been shown in the top layer via a HTMLDialogElement.showModal() call.






### ::slotted()

represents any element that has been placed into a slot

* only works when used inside CSS placed within a shadow DOM. 
* won't select a text node placed into a slot; it only targets actual elements.
* **insertion point**: In the shadow DOM, `<slot name="X">` defines an “insertion point”, a place where elements with `slot="X"` are rendered.
* **default slot**: The first `<slot>` in shadow DOM that doesn’t have a name is a “default” slot. It gets all nodes from the light DOM that aren’t slotted elsewhere.
* slotted elements can be style from outside

Where ::part pierces through the shadow DOM to make a web component’s elements accessible to outside styles, ::slotted remains encapsulated in the `<style>` element in the component’s `<template>` and accesses the element that’s technically outside the shadow DOM.

* won't select a text node placed into a slot; **only targets actual elements**
* children of the element in the slot are inaccessible
* The browser monitors slots and updates the rendering if slotted elements are added/removed

```css
/* Selects any element placed inside a slot */
::slotted(*) {
  font-weight: bold;
}

/* Selects any <span> placed inside a slot */
::slotted(span) {
  font-weight: bold;
}
/* select children of a specific slot */
::slotted(div[slot="left"] ) { 
  color: green 
}
```

#### only top-level children may serve as slots

The `slot="..."` attribute is only valid for direct children of the shadow host. For nested elements it’s ignored.
For example, the second `<span>` here is ignored (as it’s not a top-level child of `<user-card>`):

```html
<user-card>
  <span slot="username">John Smith</span>
  <div>
    <!-- invalid slot, must be direct child of user-card -->
    <span slot="birthday">01.01.2001</span>
  </div>
</user-card>
```

### ::part()

allows you to pierce the shadow tree - it lets you style elements inside the shadow DOM from outside the shadow DOM.

* especially usefull if we need to style an element in the shadow DOM based on information that exists on the page
* **it's not possible to select an element inside of `::part()`** 
  But you can add a part attribute on that element and style it via its own part name.

```css
/* not possible: */
.some-class::part(wrapper) span {
  color: red;
}
```



```css
custom-element::part(foo) {
  /* Styles to apply to the `foo` part */
}
```

#### exportparts

The global attribute `part` makes the element visible on just a single level of depth. When the shadow tree is nested, parts will be visible only to the parent of the shadow tree but not to its ancestor. Exporting parts further down is exactly what exportparts attribute is for.

# SOLUTIONS

## default

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

button,
label,
input,
select,
progress,
meter {
  display: block;
  font-family: inherit;
  font-size: 100%;
  margin: 0;
  box-sizing: border-box;
  width: 100%;
  padding: 5px;
  height: 30px;
}

input[type="text"],
input[type="datetime-local"],
input[type="color"],
select {
  box-shadow: inset 1px 1px 3px #ccc;
  border-radius: 5px;
}
```

## layout

```css
/* mobile layout */
body {
	display: grid;
	grid-template-areas: 
		'headerbefore'
		'header'
		'headerafter'
		'asideleft'
		'main'
		'asideright'
		'footer';
	/*! overflow-x: hidden; */	/* prevent overflow-x on mobile devices */
	/*! height: 137%; */	/* ensures that position: sticky remains functional */
	margin: 0;
	grid-template-columns: 1fr;
	grid-template-rows: 1fr;
	justify-items: center; /* horizontal alignment in mobile layout */
}

@media (min-width: 1024px) {
	body {
	  --margin: clamp(12px, 5vw, 300px);
		grid-template-areas: 
		  'headerbefore headerbefore  headerbefore  headerbefore  headerbefore' 
		  'header       header        header        header        header' 
		  'headerafter  headerafter   headerafter   headerafter   headerafter' 
		  'marginleft   asideleft     main          asideright    marginright' 
		  'footerbefore footerbefore  footerbefore  footerbefore  footerbefore' 
			'footer       footer        footer        footer        footer' 
			'footerafter  footerafter   footerafter   footerafter   footerafter';
		column-gap: 0px;
		row-gap: 0px;
		align-content: start;
		justify-items: center;
		align-items: start;
		justify-content: normal;
		grid-template-columns: var(--margin) auto 1fr auto var(--margin);
		grid-template-rows: auto;
	}
}
```