# @container

* `@container`: change elements based on the ***size of the parent element***  
  especially when it comes to typography
* `@media`: change elements based on the ***size of the browser window***
* `container-type` # creates a containment context

## query units

- `cqw`: 1% of the width of a query container.
- `cqh`: 1% of the height of a query container.
- `cqi`: 1% of the inline size of a query container.
- `cqb`: 1% of the block size of a query container.
- `cqmin` = Der kleinere Wert von `cqi` und `cqb`
- `cqmax` = Der größere Wert von `cqi` und `cqb`

## named container

```css
.post {
  container-type: inline-size;
  container-name: summary;
}

@container summary (min-width: 400px) {
	/* Styles applied to elements within the 'summary' container when the container is over 400px wide  */
}
```

## anonymous container

```css
/* A container context based on inline size */
.post {
  container-type: inline-size;
}

/* Apply styles if the container is narrower than 650px */
@container (width < 650px) {
  .card {
    width: 50%;
    background-color: gray;
    font-size: 1em;
  }
}
```

## properties

```css
.post {
  container-type: inline-size;
}
/* definiert einen Container, welcher lediglich auf Veränderungen entlang der X- beziehungsweise nach logischem System der Inline-Achse achtet; informiert den Browser, dass der Container sich auf der Inline-Achse in der Größe verändern kann – also in unserem Beispiel in der Breite */

.post {
  container-type: size;
}
/* achtet auf Änderungen entlang beider Achsen */
```


## query conditions

- aspect-ratio: The aspect-ratio of the container calculated as the width to the height of the container expressed as a `<ratio>` value.
- block-size: The block-size of the container expressed as a `<length>` value.
- height: The height of the container expressed as a `<length>` value.
- inline-size: The inline-size of the container expressed as a `<length>` value.
- orientation: The orientation of the container, either landscape or portrait.
- width: The width of the container expressed as a `<length>` value.

```css
@container (width > 400px) and (height > 400px) {
  /* <stylesheet> */
}

@container (width > 400px) or (height > 400px) {
  /* <stylesheet> */
}

@container not (width < 400px) {
  /* <stylesheet> */
}
```

# @layer

* **The initial order in which layers are declared indicates which layer has precedence**. As with declarations, the last  layer to be listed will win if declarations are found in multiple layers.
* Having declared your layer names, thus setting their order, you can add CSS rules to the layer by re-declaring the name. The styles are then appended to the layer and the layer order will not be changed.

```css
/* establish a layer order up-front, from lowest to highest priority */
@layer reset, defaults, patterns, components, utilities, overrides;

/* add styles to layers */
@layer utilities {
  /* high layer priority, despite low specificity */
  [data-color='brand'] { 
    color: var(--brand, rebeccapurple);
  }
}

@layer defaults {
  /* higher specificity, but lower layer priority */
  a:any-link { color: maroon; }
}

/* un-layered styles have the highest priority */
a {
  color: mediumvioletred;
}
```

# @media

## media types

describe the general category of a device. Except when using the `not` or `only` logical operators, the media type is optional and the `all` type is implied.

- `all`: Suitable for all devices.  
- `print`: Intended for paged material and documents viewed on a screen in print preview mode. (Please see [paged media](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_paged_media) for information about formatting issues that are specific to these formats.)  
- `screen`:  primarily for screens.  

## media queries

```css
@media (min-width: 320px) and (max-width: 480px) {
    un-menu-bar::part(right) {
        flex-direction: row;
        border-left: none;   
    }
}

/* Introduced in Media Queries Level 4 is a new range syntax that allows for less verbose media queries when testing for any feature accepting a range: */

@media (320px <= width <= 480px) {
  body {
    line-height: 1.4;
  }
}
@media (height > 600px) {
  body {
    line-height: 1.4;
  }
}
```

## query media features

* `any-hover`: Does any available input mechanism allow the user to hover over elements? 
* `any-pointer`: Is any available input mechanism a pointing device, and if so, how accurate is it?
* `pointer`: Is the primary input mechanism a pointing device, and if so, how accurate is it?

```css
@media (hover: hover) {
  /* Does the primary input mechanism allow the user to hover over elements? */
  a:hover {
    color: white;
    background: black;
  }
}
```

## solutions

prevent that “stuck” hover state on touch devices like this:

```css
@media (hover: hover) {
  /* probably excludes devices like a convertible laptop with a touch screen that has a mouse connected */
  a:hover {
    background: yellow;
  }
}
```

```css
@media (any-hover: hover) {
  a:hover {
    background: yellow;
  }
}
```

# @supports

```css
@supports (display: table-cell) and (display: list-item) {
}

@supports (transform-origin: 5% 5%) {
}

```

