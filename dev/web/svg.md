# XML

* `version=""1.0"`: Derzeit ist die Version 1.0 von XML maßgeblich. Es gibt zwar bereits  eine Version 1.1, doch die gegenwärtigen XML-Parser unterstützen  normalerweise nur die Version 1.0. Da das Konzept von XML syntaktisch  weitgehend ausgereift ist, ist auch nicht mit einer Versionenflut zu  rechnen. Benutzen Sie also außer in begründeten Ausnahmefällen die  Angabe version="1.0".
* `standalone`: Teilt dem XML-Parser vorab mit, ob die vorliegende Datei sich auf eine externe DTD (*Document Type Definition*) bezieht oder nicht. 
  * `standalone="yes"`: Die DTD befindet sich innerhalb der aktuellen Datei. 
  * `standalone="no"`: Die DTD befindet sich in einer separaten Datei.
    Die Quelle müssen Sie in diesem Fall mit Hilfe der Dokumenttyp-Deklaration angeben.

```html
<?xml version="1.0" standalone="no"?>
<!-- restliche XML-Datei folgt ab hier -->
```

# Accessibility

The `<title>` element provides an accessible, short-text description of any SVG container element or graphics element.

Text in a `<title>` element is not rendered as part of the graphic, but browsers usually display it as a tooltip. If an element can be described by visible text, it is recommended to reference that text with an aria-labelledby attribute rather than using the `<title>` element.

* For backward compatibility with SVG 1.1, `<title>` elements should be the first child element of their parent.

SVG
===================

* SVG is, essentially, to graphics what HTML is to text.

## embed svg into html

### img tag

* for images without interaction

```html
<img
  src="equilateral.svg"
  alt="triangle with all three sides equal"
  height="87"
  width="100" 
/>
```

#### pros

- Easy and quick implementation.
- Make the SVG image into a hyperlink by nesting <img> & <a> HTML element
- Caching of SVG file, hence reduced loading time.

#### cons

- You cannot manipulate the image with JavaScript.
- If you want to control the SVG content with CSS, you must include  inline CSS styles in your SVG code. (External stylesheets invoked from  the SVG file take no effect.)
- You cannot restyle the image with CSS pseudoclasses (like `:focus`).

When you add an SVG image using the `<img>` tag without specifying the size, it assumes the size of the original SVG file

### css background-image

```css
body {
  background-image: url(happy.svg);
}
```

### object tag

```html
<object data="happy.svg" width="300" height="300"></object>
```

### svg tag

* reduces load time
* lets you perform more customization as opposed to using either the <img> or background-image

### inline svg-sprite

Mit Hilfe von SVG-Definitionsbereichen und Symbolen können sog. SVG-Sprites erstellt werden: 

1. Innerhalb des Definitionsbereichs (`<defs>`) werden verschiedene Grafiken innerhalb von `<symbol>` angelegt und mit einer ID versehen. Wichtig ist, dass innerhalb des `<symbol>` die `viewBox` korrekt angegeben wird.
2. An anderer Stelle im Dokument werden die Objekte dann mit `<use>` über ihre ID angesprochen und geladen. 

```html
<!-- wird direkt nach dem öffnenden <body> geladen -->
<svg height="0" width="0">
  <defs>
    
    <symbol id="meine-id" viewBox="0 0 100 100">
      <title> … </title>
      <path d="…"> <!-- hier wird die Grafik gezeichnet --> </path>
    </symbol>

    <!-- weitere symbole -->
    
  </defs>
</svg>

<!-- später im Dokument wird die Grafik geladen -->
<svg class="icon icon-red">
  <use xlink:href="#meine-id"></use>
</svg>
```



## style

```xml
<svg viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg">
  <style>
    circle {
      fill: gold;
      stroke: maroon;
      stroke-width: 2px;
    }
  </style>

  <circle cx="5" cy="5" r="4" />
</svg>
```

## scale

### inline svg

If you use inline SVG then the `<svg>` element does double duty, *defining the image area within the web page as well as within the SVG*. 
Any height or width you set for the SVG with **CSS** will override the height and width attributes on the `<svg>`. 

```css
svg {
  width: 100%; 
  height: auto;
}
```

... will cancel out the dimensions and aspect ratio you set in the code, and give you the **default height for inline SVG**. Which will be either 150px or 100vh, depending on the browser.

So forget `height` and `width` (on `<svg>`). You don’t actually want to set the *exact* height and width anyway, you want the SVG to scale to match the width and/or height you set in the CSS. What you want is to set an *aspect ratio* for the image, and have the drawing scale to fit. You want a `viewBox`.

## composition

* use `<defs>` and `url()`

```html
<svg width='100%' height='100%' xmlns='http://www.w3.org/2000/svg'>
    <defs>
        <pattern id='japanese-3' patternUnits='userSpaceOnUse' width='69.141' height='40'
            patternTransform='scale(2) rotate(0)'>
            <rect x='0' y='0' width='100%' height='100%' fill='hsla(0, 0%, 100%, 0)' />
            <path d='M69.212 40H46.118L34.57 20 46.118 0h23.094l11.547 20zM57.665 60H34.57L23.023 40 34.57 20h23.095l11.547 20zm0-40H34.57L23.023 0 34.57-20h23.095L69.212 0zM34.57 60H11.476L-.07 40l11.547-20h23.095l11.547 20zm0-40H11.476L-.07 0l11.547-20h23.095L46.118 0zM23.023 40H-.07l-11.547-20L-.07 0h23.094L34.57 20z'
                stroke-width='1' stroke='hsla(258.5,59.4%,59.4%,1)' fill='none' />
        </pattern>
    </defs>
    <rect width='800%' height='800%' transform='translate(0,0)' fill='url(#japanese-3)'/>
  </svg>
```



ELEMENTS
==================

`<animate>`
-------------------------

* provides a way to animate an attribute of an element over time
* wrap it into the element that is going to be animated

### examples

```xml
<animate attributeType="XML" attributeName="y" from="0" to="50" dur="5s" repeatCount="indefinite"/>
<animate attributeName="rx" values="0;5;0" dur="10s" repeatCount="indefinite" />
<animate attributeName="cx" dur="4s" min="2s" repeatCount="indefinite" values="60 ; 110 ; 60 ; 10 ; 60" keyTimes="0 ; 0.25 ; 0.5 ; 0.75 ; 1"/>
```

## `<g>`

* a container used to group other SVG elements

## `<defs>`

* used to store graphical objects that will be used at a later time. 
* Objects created inside a `<defs>` element are not rendered directly. 
* To display them you have to reference them (with a `<use>` element for example).
* apply `style="display:none"`, damit die Grafik nicht am Ende der Seite wiederholt wird.

```xml
<svg viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    <!-- Some graphical objects to use -->
  <defs>
    <circle id="myCircle" cx="0" cy="0" r="5" />
    <linearGradient id="myGradient" gradientTransform="rotate(90)">
      <stop offset="20%" stop-color="gold" />
      <stop offset="90%" stop-color="red" />
    </linearGradient>
  </defs>

  <!-- using my graphical objects -->
  <use x="5" y="5" xlink:href="#myCircle" fill="url('#myGradient')" />
</svg>
```

### `<pattern>`

* defines a graphics object which can be redrawn at repeated x- and y-coordinate intervals ("tiled") to cover an area.
* `<pattern>` is referenced by the `fill` and/or `stroke` attributes on other graphics elements to fill or stroke those elements with the referenced pattern.
* like gradients, `<pattern>` should be put in the `<defs>` section of your SVG file.

#### attributes

By default the pattern’s coordinate system (`patternUnits='objectBoundingBox'`) is percentage-based which is awkward because patterns distort to fit the container.  Instead, I build everything using `patternUnits='userSpaceOnUse'` because the coordinate system behaves consistently whether the  container is fixed (pixel-based) or fluid (percentage-based). It’s no  different than working with the default `viewBox` coordinate system.

- `width` and `height` legen die Abmessung einer Kachel des Musters fest
- `x` und `y` geben an, wo die erste Kachel platziert ist. Default: `x="0"` und `y="0"`

The `width` and `height` within the pattern shapes determine their size, but the size cannot exceed the `width` and `height` set within the `<pattern>` element; the excess of any shape set larger than the bounds set by `<pattern>` will not render.

#### elements

You can use `fill` attribute with the following svg elements:

```html
<circle>
<ellipse>
<path>
<polygon>
<polyline>
<rect>
<text>
<textPath>
<tref>
<tspan>
```

#### examples

```html
<svg width="100%" height="100%" viewBox="0 0 400 240">
    <pattern id="simple-dots" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
        <circle fill="#aaa" cx="10" cy="10" r="4" />
    </pattern>
    <rect width="400" height="240" fill="url(#simple-dots)" />
</svg>
```

```html
<defs id="svg-japanese-squares-1">
	<pattern id='svg-pattern-japanese-squares-1'>
		<!-- .... -->
  </pattern>
</defs>

<rect width='800%' height='800%' transform='translate(0,0)' fill='url(#svg-pattern-japanese-squares-1)' />
```

## `<symbol>`

* used to define graphical template objects which can be instantiated by `<use>`

`<use>`
---------------

* takes nodes from within the SVG document, and duplicates them somewhere else
* Es ist allerdings nicht möglich innerhalb eines mittels `<use>` geladenen Symbols Unterpfade mit CSS umzugestalten, da `<use>` das Symbol im Shadow DOM lädt.

### attributes

* when written inline in HTML5, there is no need to specify the svg namespace or xlink namespace attributes

#### override

* Most attributes on `<use>` do not override those already on the element referenced by use. 
  (This differs from how CSS style attributes override those set 'earlier' in the cascade).
* Only the attributes `x`, `y`, `width`, `height` and `href` on the use element will override those set on the referenced element. 
* However, any other attributes not set on the referenced element will be applied to the use element.

#### width | height

`width`, `height` no effect on use elements, unless the element referenced has a `viewBox` - i.e. they only have an effect when use refers to a `<svg>` or `<symbol>` element.

### use cases

#### keep html clean

```html
<svg height="100%" viewBox="0 0 533 382.5" width="100%">
  <title>SVG inline ans Ende der Seite, mit use an die gewünschte Stelle</title>
  <use href="#mypic" />
</svg>

<!-- put this at the end of your document -->
<svg style="display: none" viewBox="0 0 533 382.5" width="100%">
   <g id="mypic"> 
      …
   </g>
</svg>
```

### `<use>` with external svg

Weil SVG *use* eine Referenz oder einen Link auf ein vorhandenes  Element setzt, braucht das SVG-Tag (in einer externen SVG-Datei) den  Namespace für xlink:

```html
<svg xmlns="http://www.w3.org/2000/svg" 
     xmlns:xlink="http://www.w3.org/1999/xlink"
     width="100%" height="100%" 
     viewBox="0 0 1045 627.1">
```



`<linearGradient>`
----------------------------

* define linear gradients that can be applied to `fill` or `stroke` of graphical elements

ATTRIBUTES
===========================

## viewBox

The SVG `viewBox` is a whole lot of magic rolled up in one little attribute. It’s the final piece that makes vector graphics *Scalable* Vector Graphics. The viewBox does many things:

- It defines the **aspect ratio** of the image.
- It defines how all the lengths and coordinates used inside the SVG should be scaled to fit the total space available.
- It defines the origin of the SVG coordinate system, the point where x=0 and y=0

`x`, `y`, `width`, `height`

* `width`, `height`: number of px/coordinates, *within the SVG code*, that should be scaled to fill the width/height of the area into which you’re drawing your SVG (the view*port*)

* width and height are used for zooming

  If those two have the same dimensions as the *viewport*, there’s no zooming in or out so nothing changes.

* `x`, `y`: specify the coordinate, in the scaled `viewBox` coordinate system, to use for the top left corner of the SVG viewport. 
  (Coordinates increase left-to-right and top-to-bottom). For simple scaling, you can set both values to 0. 
  However, the x and y values are useful for two purposes: to create a coordinate system with an origin centered in the drawing (this can make defining and transforming shapes easier), or to crop an image tighter than it was originally defined.

```html
<svg viewBox="0 0 200 100"></svg>
```

```html
<svg viewBox="0 0 100 100" ></svg>
```

Defines a coordinate system 100 units wide and 100 units high. 
In other words, if your SVG contains a circle centered in the graphic with radius of 50px, it would fill up the height or width of the SVG image, even if the image was displayed full screen.

### elements

You can use this attribute with the following SVG elements:

```html
<marker>
<pattern>
<svg>
<symbol>
<view>
```

Animation
----------------------

### attributes

```xml
<animate
    dur="1s|indefinite"         # simple duration of an animation
    repeatCount="5|indefinite"  # the number of times an animation will take place.
    repeatDur="5s|indefinite"   # the total duration for repeating an animation
    values="60 ; 110 ; 60 ; 10 ; 60" # a sequence of values used over the course of an animation
    keyTimes="0 ; 0.25 ; 0.5 ; 0.75 ; 1" # a list of time values used to control the pacing of the animation
    from=""         # the initial value of the attribute that will be modified during the animation.
/>
```

### timing

* begin
* dur="1s | indefinite"         # the simple duration of an animation
* end
* min # the minimum value of the active animation duration
* max
* restart
* repeatCount="5 | indefinite"  # the number of times an animation will take place.
* repeatDur="5s | indefinite"   #  the total duration for repeating an animation
* fill

### value

`calcMode`
`values`
* defines a sequence of values used over the course of an animation
* If this attribute is specified, any `from`, `to`, and `by` attribute values set on the element are ignored.
`keyTimes`
* a list of time values used to control the pacing of the animation.
* keySplines
`from`, `to`, `by`
* the initial value of the attribute that will be modified during the animation
* When used with `to`, the animation will change the modified attribute from the from value to the to value. 
* When used with `by`, the animation will change the attribute relatively from the from value by the value specified in by

### other

* `attributeName` # indicates the name of the CSS property or attribute of the target element that is going to be changed during an animation.
* `additive`
* `accumulate`

### event

`onbegin`
`onend`
`onrepeat`


Presentation
-----------------------

* All SVG presentation attributes can be used as CSS properties

### clip

clip-path, clip-rule

### color

color, color-interpolation, color-rendering

### fill

fill, fill-opacity, fill-rule

#### meaning

`fill` has two different meanings

* *shapes* and *text*: defines color
* *animation* it defines the final state of the animation

```xml
<svg viewBox="0 0 300 100" xmlns="http://www.w3.org/2000/svg">
    <!-- Simple color fill -->
    <circle cx="50" cy="50" r="40" fill="pink" />
</svg>
```

* `fill-opacity="0...1` 

#### elements

You can use `fill` attribute with the following svg elements:

```html
<circle>
<ellipse>
<path>
<polygon>
<polyline>
<rect>
<text>
<textPath>
<tref>
<tspan>
```

### stroke

stroke, stroke-dasharray, stroke-dashoffset, stroke-linecap, stroke-linejoin, stroke-miterlimit, stroke-opacity, stroke-width

* `stroke` # the color used to paint the outline of the shape
* `stroke-width` # the width of the stroke to be applied to the shape
* `stroke-opacity="0...1` # 

### other

cursor, display, transform, vector-effect, visibility, mask, opacity, pointer-events, shape-rendering

### filter

```xml
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
    <!-- blur -->
    <filter id="blur">
        <feGaussianBlur stdDeviation="2" />
    </filter>
    <rect x="10" y="10" width="80" height="80" filter="url(#blur)" />
</svg>
```

#### path

* `<textPath>` defines the path onto which the glyphs of a `<text>` element will be rendered.

Generic
-------------

The SVG core attributes are all the common attributes that can be specified on any SVG element.

id
lang
tabindex
xml:base
xml:lang
xml:space

#### id

```xml
<svg width="120" height="120" viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
    <style type="text/css">
        <![CDATA[
        #smallRect {
            stroke: #000066;
            fill: #00cc00;
        }
        ]]>
    </style>
    <rect id="smallRect" x="10" y="10" width="100" height="100" />
</svg>
```

#### href 

* defines a link to a resource as a reference URL. The exact meaning of that link depends on the context of each element using it.

#### style

* allows to style an element using CSS declarations.

```xml
<svg viewbox="0 0 100 60" xmlns="http://www.w3.org/2000/svg">
  <rect width="80"  height="40" x="10" y="10"
      style="fill: skyblue; stroke: cadetblue; stroke-width: 2;"/>
</svg>
```

Other
-------------------------------

### Shape

#### rx, ry

* radius on the x-axis, y-axis

Use it with:
* `<ellipse>`
* `<rect>`

#### cx, cy

* x-axis, y-axis coordinate of a center point

Use it with:
* `<circle>`
* `<ellipse>`
* `<radialGradient>`