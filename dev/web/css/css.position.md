A "positioned" element is one whose position is anything except static.

`position:` specifies the type of positioning method used for an element. Elements are then positioned using the `top`, `bottom`, `left`, and `right` properties. However, these properties will not work unless the `position` property is set first. They also work differently depending on the position value.

You need to think of it as if there's an invisible force that pushes the specified side of the positioned box, moving it in the ***opposite direction***.

# z-index

* a child element is limited to the stacking context of its parent

# relative parent - absolute child

If we put `position: relative;` on the parent element, anything inside of it with `position: absolute;` will be placed absolutely, relative to that containing unit!

```css
.parent {
  position: relative;
}

.child {
  /* ... */
  position: absolute;
  top: 0;
  left: 0;
}
```

* `width` and `height` of the child are based on the **parent** (or nearest block-level ancestor)
* `top`, `right`, `bottom`, and `left` are based on the **nearest *positioned* ancestor**
* Those aren’t necessarily the same element!
* The absolutely positioned element is removed from the normal flow. It's then “contained” by the positioned ancestor

# position: inset

```css
div {
  inset: 0, 0, 0, 0; 
}
```

* shorthand that corresponds to the top, right, bottom, and/or left properties
* has the same multi-value syntax of the *margin* shorthand

# position: relative

```css
div {
  position: relative;
}
```

* el is positioned according to the *normal flow of the document*
* then *offset relative to itself*

# position: absolute

```css
div {
  position: absolute; 
}
```

* el is positioned relative to its closest positioned ancestor
* el is *removed from the normal document flow*
* *no space is created* for the element in the page layout

# position: static

```css
div {
    position: static; 
}
```

* default
* element is positioned *according to the normal flow* of the document
* top, right, bottom, left, and z-index properties have no effect

# position: fixed

```css
div {
  position: fixed; 
}
```

* element is *removed from the normal document flow*
* no space is created for the element in the page layout
* element is positioned relative to its initial *containing block*
* the containing block is established by the viewport (in the case of continuous media) or the page area (in the case of paged media).

# position: sticky

```css
div {
  position: sticky;
  top: 5%;
}
```

* el is positioned according to the normal flow of the document
* elements are only sticky *relative to their parent container*
* requires `top` to bet set
* `overflow-x: hidden;` on any of the parent containers will break `position: sticky` functionality
* you can't have `overflow: hidden;` on any other parent element
* If the parent element has *no* `height` set then the sticky element *won't* have any area to stick to when scrolling. 
  This happens because **the sticky element is meant to stick/scroll within the *height* of a containing element**
* https://www.designcise.com/web/tutorial/how-to-fix-issues-with-css-position-sticky-not-working#snippet-to-check-for-parents-with-overflow-property-set

```html
<style>
  un-menu-bar {
    position: sticky;
    top: 0;
  }
  nav {
    display: contents; /* if this is not set, then un-menu-bar only sticks *within* nav */
  }
</style>

<nav>
	<un-menu-bar></un-menu-bar>
</nav>
```

```css
html, body {
  /* prevent overflow-x on mobile devices */
	overflow-x: hidden;
  height: 100vh; /* prevent breaking position: sticky */
}
```

```js
// find parent element with overflow !== 'visible'
let parent = document.querySelector('.sticky').parentElement;

while (parent) {
  const hasOverflow = getComputedStyle(parent).overflow;
  if (hasOverflow !== 'visible') {
    console.log(hasOverflow, parent);
  }
  parent = parent.parentElement;
}
```

# How To
## how to center an absolutely positioned element

```css
.centered-axis-x {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}

/* The clue is:
left: 50% is relative to the parent while the 
transform: translateX(-50%) is relative to the elements width/height */

/* This way you have a perfectly centered element, with a flexible width on both child and parent. Bonus: this works even if the child is bigger than the parent. */

.centered-axis-xy {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
}
```