
CANVAS
===================================


```js
const canvas = document.querySelector('canvas');
const ctx = canvas.getContext('2d');
const width = canvas.width = window.innerWidth;
const height = canvas.height = window.innerHeight;
```

* get a reference to the `<canvas>` element
* call the `getContext()` method on it to give us a context on which we can start to draw.
* The resulting constant *ctx* is the object that directly represents the drawing area of the canvas and allows us to draw 2D shapes on it.
* The context is like the paper, and now we want to command our pen to draw something on it.



CanvasRenderingContext2D
---------------------------------


* provides the 2D rendering context for the drawing surface of a `<canvas>` element.
* used for drawing shapes, text, images, and other objects.

```js
const canvas = document.getElementById('canvas-el-id');
const ctx = canvas.getContext('2d');
```

### Paths

The following methods can be used to manipulate paths of objects.

* `ctx.beginPath()`  
  starts a new path
* `ctx.moveTo(x, y)`  
  begins a new sub-path at the point specified by the given coordinates.
* `ctx.lineTo(x, y)`  
  adds a straight line to the current sub-path by connecting the sub-path's last point to the specified coordinates.  
  Like other methods that modify the current path, this method does not directly render anything. To draw the path onto a canvas, you can use the fill() or stroke() methods.
* `ctx.closePath()`  
  Attempts to add a straight line from the current point to the start of the current sub-path.  
  If the shape has already been closed or has only one point, this function does nothing.  
  Basically states "finish drawing the path we started with beginPath(), and fill the area it takes up with the color we specified earlier in fillStyle."
* `ctx.arc(x, y, radius, startAngle, endAngle [, anticlockwise])`  
  Creates a circular arc centered at x, y with a radius of *radius*.  
  The path starts at *startAngle*, ends at *endAngle*, and travels in the direction given by *anticlockwise* (defaulting to clockwise).
  * `startAngle = 0, endAngle = 2 * PI` # gives us a complete circle
  * `startAngle = 0, endAngle = 1 * PI` # gives us half a circle


### Drawing rectangles

There are three methods that immediately draw rectangles to the canvas.

* `ctx.clearRect(x, y, width, height)`  
  Sets all pixels in the rectangle defined by starting point (x, y) and size (width, height) to transparent black, erasing any previously drawn content.
* `ctx.fillRect(x, y, width, height)`   
  Draws a filled rectangle at (x, y) position whose size is determined by width and height.
* `ctx.strokeRect(x, y, width, height)`  
  Paints a rectangle which has a starting point at (x, y) and has a w width and an h height onto the canvas, using the current stroke style. 

### Line styles

The following methods and properties control how lines are drawn.

* `ctx.lineWidth = <value>`  
  Width of lines. Default 1.0.
* `ctx.lineCap = "butt" || "round" || "square";`  
  determines the shape used to draw the end points of lines. 
* `ctx.lineJoin = "bevel" || "round" || "miter";`  
  determines the shape used to join two line segments where they meet.

### Fill and stroke styles

Fill styling is used for colors and styles inside shapes and stroke styling is used for the lines around shapes.

* `ctx.fillStyle = <color> | <gradient> | <pattern>`  
  Color or style to use inside shapes. Default #000 (black).


### examples

#### example: 2 paths

```js 
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

// First path
ctx.beginPath();
ctx.strokeStyle = 'blue';
ctx.moveTo(20, 20);
ctx.lineTo(200, 20);
ctx.stroke();

// Second path
ctx.beginPath();
ctx.strokeStyle = 'green';
ctx.moveTo(20, 20);
ctx.lineTo(120, 120);
ctx.stroke();
```

#### example: a house

```js
// Set line width
ctx.lineWidth = 10;

// Wall
ctx.strokeRect(75, 140, 150, 110);

// Door
ctx.fillRect(130, 190, 40, 60);

// Roof
ctx.beginPath();
ctx.moveTo(50, 140);
ctx.lineTo(150, 60);
ctx.lineTo(250, 140);
ctx.closePath();
ctx.stroke();
```
