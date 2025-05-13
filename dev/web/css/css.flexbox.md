# default values

## flex-container

```css
.flex-container {
    flex-direction: row; /* items will align horizontally */
	justify-content: flex-start; 
	/* items will stack at the start of the line on the main axis */
  align-items: stretch; 
  /* items will expand to cover the cross-size of the container */
	flex-wrap: nowrap; /* items are forced to stay in a single line */
}
```

## flex-items

* flex items are allowed to shrink by default (which prevents them from overflowing the container)

```css
.flex-items {
  flex: 0 1 auto;
  /* shorthand for: */
  flex-grow: 0; /* item is not allowed to expand */
  flex-shrink: 1; /* item is allowed to shrink */
  flex-basis: auto;
}
```

# container

## flex-flow

```css
.parent {
  flex-flow: row wrap;
  /* shorthand for: */
  flex-direction: row;
  flex-wrap: wrap;
  /* The flex items break into multiple lines. */
  flex-wrap: nowrap; 
  /* The flex items are laid out in a single line which may cause the flex container to overflow. */
}
```

## gap

shorthand for `row-gap` and `column-gap`

## alignment

### main-axis

#### justify-content

alignment along the **main-axis** of a flex container (*row* for `flex-direction: row`)

### cross-axis

* cross-axis = *column* for `flex-direction: row`

#### align-items

* 

#### align-content

* is for multi line flexible boxes
* has no effect when items are in a single line
* the **distribution of space between and around content items along a flexbox's cross-axis** or a grid's block axis

#### justify-self 

is *ignored*

#### align-self

aligns the item on the *cross axis*

```css
aligns the item on the cross axis
```

### aligning the last row

As seen above, the items in the last row do not align with the previous row. This is a common problem with flexbox.

We mentioned earlier that when flex items wrap, every line is a new flex line and items are justified based on the available space in that flex line. In this case, items in the last row are centered because we applied `justify-content: center;` to the flex container.

A quick fix is to use `::after` to create a pseudo-element in the flex container. Then, we can set the size of the `flex-basis` CSS property as equal to the size of the flex items.

```css
.image-gallery::after {
  content: "";
  flex-basis: 350px;
}
```

### split navigation pattern

(alignment and auto margins)

There is a specific use case in Flexbox where we might think that a `justify-self` property is what we need, and this is when we want to split a set of flex items, perhaps to create a split navigation pattern. For this use case, we can use an auto margin. A margin set to auto will absorb all available space in its dimension. This is how centering a block with auto margins works. By setting the left and right margin to auto, both sides of our block try to take up all of the available space and so push the box into the center.

By setting a margin of `auto` on one item in a set of flex items all aligned to start, we can create a split navigation. This works well with Flexbox and the alignment properties. As soon as there is no space available for the auto margin, the item behaves in the same way as all the other flex items and shrinks to try to fit into space.

```css
.box {
  display: flex;
}
.push {
    margin-left: auto; /* add this class to the item that should be pushed to the other side */
}
```

# item

## flex

```css
flex: [flex-grow] [flex-shrink] [flex-basis];
/* or... */
flex: [max] [min] [ideal size];

/* examples */
.auto {
  flex: auto;
  flex: 1 1 auto;
  /* The item is sized according to its width and height properties, 
  but grows to absorb any extra free space in the flex container, 
  and shrinks to its minimum size to fit the container. */
}

.initial {
  flex: initial;
  flex: 0 1 auto;
	/* The item is sized according to its width and height properties. 
    It shrinks to its minimum size to fit the container, 
    but does not grow to absorb any extra free space in the flex container. */
}

.none {
  flex: none;
  flex: 0 0 auto;
	/* The item is sized according to its width and height properties. 
  It is fully inflexible: it neither shrinks nor grows in relation to the flex container. */

}

.child-three {
  flex: 3 1 auto;
  /* that third element will try to grow up to three times the space as the others. */
}

.fixed {
  flex: 0 0 100px; /* don't grow, don't shrink, stay fixed at 100px */
  flex: 0 0 50%; /* don't grow, don't shrink, stay fixed at 100px */
}
```

```css
.half {
  flex: 1 1 100%;
}
```

## flex-grow

* specifies how much of the **remaining space** in the flex container should be assigned to the item
* The **remaining space** is the size of the flex container minus the size of all flex items' sizes together.
* If all items have `flex-grow: 1`, the remaining space in the container will be distributed equally to all children
* If one of the children has a value of `2`, that child would take up twice as much of the space either one of the others (or it will try, at least).

```css
.default {
  flex-grow: 0;
}
```

## flex-shrink

* If the size of all flex items is larger than the flex container, items shrink to fit according to `flex-shrink`

```css
.default {
  flex-shring: 1;
}
```

## flex-basis

* defines the **initial** size of an element before the remaining space is distributed

### difference to width and height

Without switching flex-direction, there's no difference between how `flex-basis` will function on your element and how `width` will function on your element. 

Width will even obey `flex-shrink` when using Flexbox.

The divergent factor between width and flex-basis is the **dynamic ability of flex-basis to change its effective direction based on the flex-direction**.

* flex-basis may be both `width` and `height`, depending on `flex-direction` (it works on the *main axis*)
* By itself, this does resolve to `width` or `height` *until* the other flex grow/shrink properties come into play.
* if `flex-direction: row`, then `flex-basis` controls width
* if `flex-direction: column`, then `flex-basis` controls height
* it will override any *width:* or *height:* properties if specifically declared anything other than `flex-basis: auto` (auto by default)
* it will obey any `min-width` or `max-width` or height settings

```css
.default {
  flex-basis: auto; /* look at my width or height property */
  /* if none, it uses the elements content sizing */
}

.child {
  flex-basis: 50%;
  flex-basis: content; /* size it based on the item’s content */
  flex-basis: min-content; /* size it based on the item’s min-width */
}

.child {
  flex-grow: 1;
 	flex-basis: 25%;
  /* will be 25% wide initially 
  but then it immediately expands as much as it can 
  until the other elements are factored in. If there are none.. it will be 100% wide/tall! */
}
```