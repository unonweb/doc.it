## Grid

1. define a container element as a grid with `display: grid`
2. set the column and row sizes with `grid-template-columns` and `grid-template-rows`
3. then place its child elements into the grid with `grid-column` and `grid-row`

```css
.grid {
	grid-template-columns: 1fr 1fr 1fr; /* set the size of the columns */
  grid-template-columns: repeat(3, 100px); /* the same */
  
	grid-template-rows: 100px 1fr; /* set the size of the rows */
  grid-gap; /* sets the gap between rows and columns */
  align-content: end; /* alignment along the row-axis */
  justify-content: start; /* alignment along the column axis */
}
```

# examples

```css
#grid {
  display: grid;
  grid-template-columns: fit-content(200px) fit-content(250px) auto;
}

- The first column has a maximum allowable width of 200px
- The second column has a maximum allowable width of 250px. 
- The third column takes the remaining container space since it is assigned a value of auto.
```

```css
grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
```

- Let the browser handle column sizing and element wrapping 
  so that the elements will wrap into rows when the width is not large enough to fit them in without any overflow
- `1fr` ensures that in case the width allows for a fraction of a column to fit but not a full column, that space will instead be distributed over the column or columns that already fit, making sure we aren’t left with any empty space at the end of the row.
- `auto-fit`: behaves the same way as auto-fill, except that after grid item placement it will only create as many tracks as needed and any empty repeated track collapses.

```css
grid-template-columns: repeat(auto-fill, minmax(186px, 1fr));
```

* allows your columns to flex in size, ranging from 186 pixels to equal-width columns stretching across the full width of the container
* `auto-fill` will create as many columns as will fit in the width
  If, say, five columns fit, even though you have only four grid items, there will be a fifth empty column

# EXPLICIT GRID

## grid-template

 is a shorthand for the following CSS properties:

- `grid-template-areas`
- `grid-template-columns`
- `grid-template-rows`

```css
body {
 	/* grid-template-rows / grid-template-columns */
	grid-template: 100px 1fr / 50px 1fr;
	grid-template: auto 1fr / auto 1fr auto; 
}

body {
  display: grid;
  grid-template:
    "hd hd hd hd   hd   hd   hd   hd   hd" minmax(100px, auto)
    "sd sd sd main main main main main main" minmax(100px, auto)
    "ft ft ft ft   ft   ft   ft   ft   ft" minmax(100px, auto)
    / 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
}
```

* `grid-template-rows` defines the **height** of a row
* `grid-template-columns` defines the **width** of a column

## `<track-list>` values

* `fr` represents a fraction of the available space in the grid container

```css
.grid {
  grid-template-columns: 100px 1fr;
  grid-template-columns: [linename] 100px;
  grid-template-columns: [linename1] 100px [linename2 linename3];
  grid-template-columns: minmax(100px, 1fr);
  grid-template-columns: fit-content(40%);
  grid-template-columns: repeat(3, 200px);
}
```

## `<auto-track-list>` values

```css
.grid {
  grid-template-columns: 200px repeat(auto-fill, 100px) 300px;
  grid-template-columns: repeat(auto-fill, 100px); 
  /* creates as many tracks as fit into the grid container without causing the grid to overflow it. */
  grid-template-columns: repeat(auto-fit, 100px);
  /* behaves the same way as auto-fill, 
  except that after grid item placement it will only create as many tracks as needed 
  and any empty repeated track collapses. */
}
```

## grid-template-areas

* The ***given cells must form a rectangle***, if not the declaration is invalid.

can be referenced from the grid-placement properties 

- `grid-row-start`
- `grid-row-end`
- `grid-column-start`
- `grid-column-end`

and their shorthands

- `grid-row`
- `grid-column`
- `grid-area`

```css
.grid-container {
  grid-template-areas:
    'header header header header header header'
    'menu main main main aside aside'
    'menu footer footer footer footer footer';
}

/* placement */
.item1 { 
  grid-area: header; 
}
.item2 { 
  grid-area: menu; 
}
.item3 { 
  grid-area: main; 
}
.item4 { 
  grid-area: aside; 
}
.item5 { 
  grid-area: footer; 
}
```

```css
#page {
  display: grid;
  grid-template-areas:
    "head head"
    "nav  main"
    "nav  foot";
  grid-template-rows: 50px 1fr 30px;
  grid-template-columns: 150px 1fr;
}
```

#### repeat()

- https://developer.mozilla.org/en-US/docs/Web/CSS/repeat

##### auto-repeat

```css
.grid {
  /* a <length-percentage> value */
  grid-template-columns: repeat(auto-fill, 100px);
  grid-template-columns: repeat(auto-fill, 100%);
}
```

```css
.grid {
  /* minmax (1) */
  /* minmax(<length-percentage>, percentage) */
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, 200px)); 
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, 80%));
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, 1fr));
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, min-content));
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, max-content));
  grid-template-columns: repeat(auto-fill, minmax(<length-percentage>, auto));
}
```

```css
.grid {
  /* minmax (2) */
  grid-template-columns: repeat(auto-fill, minmax(200px, <length-percentage>));
  grid-template-columns: repeat(auto-fill, minmax(10%, <length-percentage>));
  grid-template-columns: repeat(auto-fill, minmax(min-content, <length-percentage>));
  grid-template-columns: repeat(auto-fill, minmax(max-content, <length-percentage>));
  grid-template-columns: repeat(auto-fill, minmax(auto, <length-percentage>));
}
```

##### track-repeat

arguments:

* an integer to set the repeat count
* `<track-size>` values to set track sizes

```css
.grid {
  grid-template-columns: repeat(3, 75%);
  grid-template-columns: repeat(3, 200px);
  grid-template-columns: repeat(3, 1fr);
  grid-template-columns: repeat(3, auto);
  grid-template-columns: repeat(3, min-content);
  grid-template-columns: repeat(3, max-content);
  /* minmax */
  grid-template-columns: repeat(3, minmax(min-content, max-content));
  grid-template-columns: repeat(3, minmax(auto, auto));
  grid-template-columns: repeat(3, minmax(min-content, 1fr));
  grid-template-columns: repeat(3, minmax(100px, 1fr));
  /* fit-content */
  grid-template-columns: repeat(3, fit-content(30%));
}
```

##### fixed-repeat

```css
.grid {
	grid-template-columns: repeat(3, 100px);
  grid-template-columns: repeat(3, 100%);
}
```

# IMPLICIT GRID

#### grid-auto-columns / grid-auto-rows

specifies the size of an implicitly-created grid column track or pattern of tracks

```css
/* Keyword values */
grid-auto-columns: min-content;
/* the largest minimal content contribution of the grid items occupying the grid track */

grid-auto-columns: max-content;
/* the largest maximal content contribution of the grid items occupying the grid track */

grid-auto-columns: auto;

/* <length> values */
grid-auto-columns: 100px;
grid-auto-columns: 20cm;
grid-auto-columns: 50vmax;

/* <percentage> values */
grid-auto-columns: 10%;
grid-auto-columns: 33.3%;

/* <flex> values */
grid-auto-columns: 0.5fr;
grid-auto-columns: 3fr;

/* minmax() values */
grid-auto-columns: minmax(100px, auto);
grid-auto-columns: minmax(max-content, 2fr);
grid-auto-columns: minmax(20%, 80vmax);

/* fit-content() values */
grid-auto-columns: fit-content(400px);
grid-auto-columns: fit-content(5cm);
grid-auto-columns: fit-content(20%);

/* multiple track-size values */
grid-auto-columns: min-content max-content auto;
grid-auto-columns: 100px 150px 390px;
grid-auto-columns: 10% 33.3%;
grid-auto-columns: 0.5fr 3fr 1fr;
grid-auto-columns: minmax(100px, auto) minmax(max-content, 2fr) minmax(20%, 80vmax);
grid-auto-columns: 100px minmax(100px, auto) 10% 0.5fr fit-content(400px);
```

#### grid-auto-flow

controls how the auto-placement algorithm works, specifying exactly how auto-placed items get flowed into the grid

```css
#grid {
  grid-auto-flow: row; 
  /* default; items are placed by filling each row in turn, 
  adding new rows as necessary */
	grid-auto-flow: column; 
  /* items are placed by filling each column in turn, 
  adding new columns as necessary */
	grid-auto-flow: dense; 
  /* "dense" packing algorithm attempts to fill in holes earlier in the grid, if smaller items come up later. 
  This may cause items to appear out-of-order, when doing so would fill in holes left by larger items.
  If it is omitted, a "sparse" algorithm is used, where the placement algorithm 
  only ever moves "forward" in the grid when placing items, never backtracking to fill holes. 
  This ensures that all of the auto-placed items appear "in order", 
  even if this leaves holes that could have been filled by later items. */
	grid-auto-flow: row dense;
	grid-auto-flow: column dense;
}
```

### sizing

```css
.grid-item {
  width: 100%; /* column-gap is ignored */
}
```

### placement

#### grid-column / grid-row

shorthand for:

- `grid-column-start`
- `grid-column-end`

```css
#grid-item {
  grid-column-end: 1; /* first column */
  /* the nth grid line to the grid item's placement. */
  grid-column-end: -1; /* last column */
  /* given a negative integer, it instead counts in reverse, 
  starting from the end edge of the explicit grid. */
}
grid-column: grid-column-start / grid-column-end;
grid-column: auto;

/* with line numbers */
grid-column: 1;
grid-column: 1 / 3;
grid-column: 1 / span 2;

/* with line names */
grid-column: main-start;
grid-column: main-start / main-end;

/* span */
use it to avoid specifying end lines when items should span more than one column or row.
```

#### grid-area

specifies a **grid item's size and location**

*    `grid-row-start`
*    `grid-column-start`
*    `grid-row-end`
*    `grid-column-end`

```css
.item {
  grid-area: grid-row-start / grid-column-start / grid-row-end / grid-column-end;
  
  grid-area: span 6 / span 4;
  /* if we want an item to be placed automatically, 
  but to span across a provided number of rows and columns */
  grid-area: 2 / 1 / span 2 / span 3;
  /* start on row 2 column 1, and span 2 rows and 3 columns */
}
```

# alignment

- `justify-content`: longer word: horizontal alignment
- `align-items`: shorter word: vertical alignment

#### justify-content | align-content

* **move the entire grid** 
  (not just the individual grid items)
* this movement will only occur if the size of the grid is smaller than the size of the parent container
* `justify-content`: 
  * inline axis (horizontally)

* `align-content`: 
  * grid: block axis (vertically)
  * flexbox: cross-axis

* distribute **space between and around** content items

```css
#grid {
  justify-content: center; 
}

#grid {
  align-content: start;
  align-content: end;
  align-content: center;
	align-content: space-between;
}
```

#### place-items

* shorthand property for: `align-items` and `justify-items` in Grid or Flexbox
* ff the second value is not set, the first value is also used for it

```css
.grid {
  place-items: 
}
```

#### justify-items | align-items

* `justify-items`
  * aligns the items **inside their grid areas** on the inline axis

* `align-items`
  * flexbox: alignment of items on the *Cross Axis*. 
  * grid:  alignment of items on the *Block Axis* within their grid area.

* sets `justify-self` or  `align-self`  on all direct children as a group

```css
#grid {
	justify-items: center;
  justify-items: left; /* Inhalte linksbündig ausrichten */
  justify-items: right; /* Inhalte rechtsbündig ausrichten */
}
```

```css
#grid {
	align-items: stretch;
  align-items: center;
  align-items: start;
  align-items: end;
}
```

# FUNCTIONS & KEYWORDS

## fit-content()

Boxes whose sizes are defined by `fit-content(value)`...

* never expand beyond the specified `value`
* while also never going beyond `max-content`
* can shrink to fit the content
* calculated similar to `auto` (`minmax(auto, max-content`) 
* except that the track **size is clamped at *argument*** if it is greater than the `auto` minimum.

Clamps a given size to an available size according to the formula:

`min(maximum size, max(minimum size, argument))`

* maximum: `max-content` 
* minimum: `auto`

```css
/* <length> values */
fit-content(200px)
fit-content(5cm)
fit-content(30vw)
fit-content(100ch)

/* <percentage> value */
fit-content(40%)
```

## minmax()

size range **greater than or equal to *min* and less than or equal to *max***

If *max* < *min*, then *max* is ignored and `minmax(min,max)` is treated as *min*. 

```css
minmax(auto, 200px)
/* here 'auto' represents the largest minimum size (as specified by min-width/min-height) of the grid items occupying the grid track */
minmax(auto, 200px)
/* here 'auto' is identical to max-content. However, unlike max-content, it allows expansion of the track by the align-content and justify-content property values like normal and stretch. */

minmax(auto, 1fr)
/* As a maximum, a flex value sets the flex factor of a grid track */
minmax(1fr, auto) /* ! */
/* a flex value is invalid as a minimum.

/* <inflexible>, <track> values */
minmax(200px, 1fr)
minmax(400px, 50%)
minmax(30%, 300px)
minmax(100px, max-content)
minmax(min-content, 400px)
minmax(max-content, auto)
minmax(auto, 300px)
minmax(min-content, auto)

/* <fixed>, <track> values */
minmax(200px, 1fr)
minmax(30%, 300px)
minmax(400px, 50%)
minmax(50%, min-content)
minmax(300px, max-content)
minmax(200px, auto)

/* <inflexible>, <fixed> values */
minmax(400px, 50%)
minmax(30%, 300px)
minmax(min-content, 200px)
minmax(max-content, 200px)
minmax(auto, 300px)
```

## `<flex>`

* When appearing outside a `minmax()` notation, it implies an automatic minimum:
  `minmax(auto, <flex>)`

```css
/* the same ? */
minmax(auto, 1fr)
1fr
```

## auto

```css
#grid {
  display: grid;
  grid-template-columns: minmax(100px, auto); 
}
/* here it represents the largest *max-content* size of the items in that track */

#grid {
  display: grid;
   grid-template-columns: minmax(auto, 300px); 
}
/* here it represents the largest minimum size of items in that track (specified by the `min-width`/`min-height` of the items). This is often, though not always, the min-content size. */ 

#grid {
  display: grid;
	grid-template-columns: auto;
}
/* represents the range between the minimum and maximum described above. This behaves similarly to 
minmax(min-content, max-content) in most cases. */
```

**Note:** `auto` track sizes (and only `auto` track sizes) can be stretched by the [`align-content`](https://developer.mozilla.org/en-US/docs/Web/CSS/align-content) and [`justify-content`](https://developer.mozilla.org/en-US/docs/Web/CSS/justify-content) properties.

### auto-fill

* Fills the row with as many columns as it can fit
* Creates implicit columns whenever a new column can fit, because it’s **trying to *FILL* the row with as many columns as possible**
* The newly added columns may be empty, but they will still occupy a designated space in the row

### auto-fit

* **Fits the *currently available* columns into the space by expanding them** so that they take up any available space. 
* The browser does that after FILLING that extra space with extra columns (as with `auto-fill` ) and then **collapsing the empty ones.**

```css
.grid {
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  /* wrap the columns into rows when there’s not enough space in our viewport 
  to fit the 240px minimum value without overflowing the content */
}
```