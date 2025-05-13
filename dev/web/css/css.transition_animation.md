
Add `display` to the transitions list so that the `<dialog>` will remain as `display: block` (or another visible `display` value set on the dialog's open state) for the duration of the transition, ensuring the other transitions are visible.
### general stuff

#### common examples

```css
div {
	transition-property: transform;
	transition-duration: 1s;
	transform: scaleY(1);
}
```

#### transition vs animation

* *transitions* provide an easy way to make *animations between the current style and an end CSS state*, e.g., a resting button state and a hover state. Even if an element is in the middle of a transition, the new transition starts from the current style immediately instead of jumping to the end CSS state.
* *animations*, on the other hand, allow developers to make animations between a set of starting property values and a final set rather than between two states. CSS animations consist of two components: a style describing the CSS animation, and a set of key frames that indicate the start and end states of the animation's style, as well as possible intermediate points.

#### JS vs CSS

* Use CSS when you have smaller, self-contained states for UI elements. CSS transitions and animations are ideal for bringing a navigation menu in from the side, or showing a tooltip. You may end up using JavaScript to control the states, but the animations themselves will be in your CSS.
* Use JavaScript when you need significant control over your animations. The *Web Animations API* is the standards-based approach, available today in most modern browsers. This provides real objects, ideal for complex object-oriented applications. JavaScript is also useful when you need to stop, pause, slow down, or reverse your animations.
* Use `requestAnimationFrame` directly when you want to orchestrate an entire scene by hand. This is an advanced JavaScript approach, but can be useful if you're building a game or drawing to an HTML canvas.
* JS animations run in the browser’s main thread which is already busy with other JavaScript, style calculations, layout and painting. Often there is thread contention. This substantially increases the chance of missing animation frames

#### JS toggling transition classes

* manage state with JavaScript setting the appropriate classes on the target elements
* let CSS handle the animations


```css
.box {
  transform: translate(0, 0);
  transition: transform 500ms;
}
.box.move {
  transform: translate(100px, 100px);
}
```

then use JavaScript to toggle each animation on and off: 

```js
box.classList.add('move');
```

#### performance

Where you can, you should avoid animating properties that trigger layout or paint. For most modern browsers, this means limiting animations to `opacity` or `transform`, both of which the browser can highly optimize; it doesn’t matter if the animation is handled by JavaScript or CSS.

Layout Properties:
* width, height
* padding, margin
* display
* border, border-width
* top, left, right, bottom
* position
* font-size, font-family, font-weight
* float
* text-align
* overflow
* line-height
* vertical-align
* clear

### CSS transitions

Transitions enable you to define the *transition between two states of an element*.  
Different states may be defined using pseudo-classes like `:hover` or `:active` or dynamically set using JavaScript.

```css
[property name] [duration] [timing-function] [delay]
div {
	transition: transform 5s ease 1s;
	/* equals: */
	transition-property: transform; /* CSS properties to which a transition effect should be applied */
	transition-duration: 5s;
	transition-timing-function: ease;
	transition-delay: 5;
}
```

```css
div {
	width: 100px;
	height: 100px;
	background: red;
	transition: width 2s;
}
div:hover {
  	width: 300px;
}
```

#### properties

```css
/* transition-property */
/* If you specify a shorthand property (e.g., background), all of its longhand sub-properties that can be animated will be. */
div {
	transition-property: transform;
  transition-property: transform, filter;
}
/* transition-duration */
/* sets the length of time a transition should take to complete */
/* default: 0s, meaning that no animation will occur */
div {
	transition-duration: 5s, 400ms;
}
/* transition-timing-function */
/* sets how intermediate values are calculated for CSS properties being affected by a transition effect */
/* lets you *establish an acceleration curve* so that the speed of the transition can vary over its duration */
div {
	/* Keyword values */
	transition-timing-function: ease;
	transition-timing-function: ease-in;
	transition-timing-function: ease-out;
	transition-timing-function: ease-in-out;
	transition-timing-function: linear;
	transition-timing-function: step-start;
	transition-timing-function: step-end;
	
	/* Function values */
	transition-timing-function: steps(4, jump-end);
	transition-timing-function: cubic-bezier(0.1, 0.7, 1.0, 0.1);

	/* Steps Function keywords */
	transition-timing-function: steps(4, jump-start);
	transition-timing-function: steps(10, jump-end);
	transition-timing-function: steps(20, jump-none);
	transition-timing-function: steps(5, jump-both);
	transition-timing-function: steps(6, start);
	transition-timing-function: steps(8, end);

	/* Multiple timing functions */
	transition-timing-function: ease, step-start, cubic-bezier(0.1, 0.7, 1.0, 0.1);
}
/* transition-delay */
/* specifies the duration to wait before starting a property's transition effect when its value changes. */
div {
	transition-delay: 5s; /* Delay the start of the transition effect for 5s */
	transition-delay: -5s; 
	/* Begin the transition effect immediately, and partway through the effect */
	/* In other words, the effect will be animated as if it had already been running for the given length of time. */
}
```

#### multiple transitions

```css
/* multiple transitions */
div {
	transition-delay: 1s, 250ms;
	transition-property: margin-right, color;
}
```

You may specify *multiple durations*; each duration will be applied to the corresponding property as specified by the transition-property property, which acts as a master list. If there are fewer durations specified than in the master list, the user agent repeat the list of durations. If there are more durations, the list is truncated to the right size.

### tipps & tricks

You can't currently animate on `height` when one of the heights involved is `auto`, *you have to set two explicit heights*.


### CSS animations

* define the animation itself independently of the target element, and use `animation-name` to choose the required animation

```css
@keyframes myAnim {
  from { /* A starting offset of 0% */
    transform: translateX(0%);
  }
  75% { /* A percentage of the time through the animation sequence at which the specified keyframe should occur */

  }
  to { /* An ending offset of 100% */
    transform: translateX(100%);
  }
}

/* EXAMPLE 2 */

.box {
    animation-name: movingBox;      /* Choose the animation */
    animation-duration: 1300ms;     /* The animation’s duration */
    animation-iteration-count: infinite; /* The number of times we want the animation to run */
    animation-direction: alternate; /* Causes the animation to reverse on every odd iteration */
}
@keyframes movingBox {
    0% {
		transform: translate(0, 0);
		opacity: 0.3;
    }
    25% {
    	opacity: 0.9;
    }
    50% {
		transform: translate(100px, 100px);
		opacity: 0.2;
    }
    100% {
		transform: translate(30px, 30px);
		opacity: 0.8;
    }
}
```

#### properties

`animation`
It is a shorthand for 
* animation-name
* animation-duration
* animation-timing-function
* animation-delay
* animation-iteration-count
* animation-direction
* animation-fill-mode
* animation-play-state

```css
div {
    animation-name: example;
    animation-duration: 4s;
    animation-iteration-count: 3 | infinite; /* specifies the number of times an animation should run */
    animation-direction: normal | reverse | alternate | alternate-reverse;
    animation-timing-function: ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier(n,n,n,n);
    /* animation-fill-mode specifies a style for the target element when the animation is not playing (before it starts, after it ends, or both) */
    animation-fill-mode: none; /* Default value. Animation will not apply any styles to the element before or after it is executing */
    animation-fill-mode: forwards; /* The element will retain the style values that is set by the last keyframe (depends on animation-direction and animation-iteration-count) */
    animation-fill-mode: backwards; /* The element will get the style values that is set by the first keyframe (depends on animation-direction), and retain this during the animation-delay period */
    animation-fill-mode: both; /* The animation will follow the rules for both forwards and backwards, extending the animation properties in both directions */
}
```

Transform
---------------------------------------------

Transformation functions can *rotate*, *resize*, *distort*, or *move* an element in 2D or 3D space.   
* modifies the coordinate space of the CSS visual formatting model.  
* transformable elements: almost all elements whose layout is governed by the CSS box model
* Transforms are *very performant* in general because they do not affect any other element. Any manipulations you make apply to that element only, so your browser does not have to repaint the entire window. It only repaints the part of your screen that contains moving content.
* element using transform will not cause other elements to flow around it.

#### multiple transforms

```css
/* Multiple function values on one line */
/* are applied from right to left */
.multipleTransforms {
	transform: translateX(10px) rotate(10deg) translateY(5px);
}
```

```js
// first transform
el.style.transform = `translate(${finalX}px, ${finalY}px)`;
// second transform (use +=)
el.style.transform += "scaleY(0)";
```

#### transform-functions overview

CSS data type that *represents a transformation that affects an element's appearance*.  
It is used in the `transform` property.

* Rotation
  * `rotate()` 	# Rotates an element around a fixed point on the 2D plane.
  * `rotate3d()` 	# Rotates an element around a fixed axis in 3D space.
  * `rotateX()` 	# Rotates an element around the horizontal axis.
  * `rotateY()` 	# Rotates an element around the vertical axis.
  * `rotateZ()` 	# Rotates an element around the z-axis. 
* Scaling (resizing)
  * *1* 	# original
  * *n > 1* # larger
  * *n < 1* # smaller
  * `scale(n, n)` 	# Scales an element up or down on the 2D plane.
  * `scale3d(n, n, n)` # Scales an element up or down in 3D space.
  * `scaleX(n)` 	# Scales an element up or down horizontally.
  * `scaleY(n)` 	# Scales an element up or down vertically.
  * `scaleZ(n)` 	# Scales an element up or down along the z-axis. 
* Translation (moving)
  * `translate()` 	# Translates an element on the 2D plane.
  * `translate3d()` 	# Translates an element in 3D space.
  * `translateX()` 	# Translates an element horizontally.
  * `translateY()` 	# Translates an element vertically.
  * `translateZ()` 	# Translates an element along the z-axis. 

#### transform-box

defines the layout box to which the `transform` and `transform-origin` properties relate.  

```css
.transform-box {
	transform-box: content-box;	/* The content box is used as the reference box. */
	transform-box: border-box;	/* The border box is used as the reference box. */
	transform-box: fill-box;	/* The object bounding box is used as the reference box. */
	transform-box: stroke-box;	/* The stroke bounding box is used as the reference box. */
	transform-box: view-box;	/* The nearest SVG viewport is used as the reference box */
}
```

#### transform-origin

sets the origin for an element's transformations; the point around which a transformation is applied

This property is applied by first translating the element by the value of the property, then applying the element's transform, then translating by the negated property value.

```css
.transform-origin {
	/* This definition */
	transform-origin: -100% 50%; /* This is goingt to translate the element */
	transform: rotate(45deg);
	
	/* results in the same transformation as */
	transform-origin: 0 0;
	transform: translate(-100%, 50%) rotate(45deg) translate(100%, -50%);

	/* One-value syntax: */
	transform-origin: 5px|50%;
	transform-origin: left|center|right|top|bottom;

	/* x-offset | y-offset */
	transform-origin: 3cm 2px;
	/* x-offset-keyword | y-offset */
	transform-origin: left 2px;
	/* x-offset-keyword | y-offset-keyword */
	transform-origin: left top;
	transform-origin: left center;
	transform-origin: left bottom;
	transform-origin: right top;
	transform-origin: right center;
	transform-origin: right bottom;
}
```

### functions

#### translate()

*repositions an element* in the horizontal and/or vertical directions. Its result is a *transform-function* data type.  
* Origin = Start (Verschiebung von der Startposition ausgehend!)
* Translating an elements Y axis 50% will move it down 50% *of its own height, not 50% of the parents height*!

```css
div {
	transform: translate(x, y);
	transform: translate(2px); /* is equivalent to translate(2px, 0) */
	transform: translate(50%); /* refers to the width of the reference box defined by the transform-box property.*/
	transform: translate(100%); /* means: move the element to the right for 100% of its own width */
  	transform: translate(20%, 50%);
  	/* refers to the width/height of the reference box defined by the transform-box property.  */
  	transform: translateY(50vw, 50vh);
}
```

#### translate3d()

*repositions an element* in 3D space. Its result is a *transform-function* data type.

```css
div {
	transform: translate3d(x, y, z);
    transform: translate3d(20px, 20px, 0px);
}
```

#### Why use translate3d instead of top, left, bottom,...?

For animation purposes, *do not set* the `margin`, `padding`, `top`, `left`, `bottom`, or `right` CSS properties.  
Each time you modify the value set on the six CSS properties that I listed, your browser does extra work to calculate how this affects the layout of your entire document.  
So using these properties and modifying their values sixty times a second as part of an animation or transition is definitely not great.  

You could choose to set `position: fixed` or `position: absolute` of the elements you are moving. That avoids your browser from having to calculate the layout for your entire document. While this is a better optimization, your browser still does layout-related calculations on the element that is moving as part of calculating the box-model.

When you transform an element using `translate3d`, that element is in GPU country in Webkit-based browsers.