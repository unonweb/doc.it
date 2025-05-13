
## idioms

```js
var evt = event || window.event
```

This is a very common idiom to get the event object from event handlers. On standards compliant browsers, the event object is passed as the  first parameter to the event handler. But on IE the event object is a  global variable. And for historical reasons, all global variables are  members of the window object.

## propagation

```html
<html>
	<body>
		<div id="buttons">
			<button class="buttonClass">Click me</button>
		</div>
	</body>
</html>
```

On how many elements a click event gets triggered? 
* the button itself
* all button’s ancestors 
* even the document and window objects

A click event propagates in 3 phases:

* **Capture phase**: 
  Starting from window, document and the root element, the event dives down through ancestors of the target element
* **Target phase**:
  The event gets triggered on the element on which the user has clicked
* **Bubble phase**:
  Finally, the event bubbles up through ancestors of the target element until the root element, document, and window.

The third and fourth argument of the `addEventListener` method lets you catch events from different phases:

```js
element.addEventListener(eventType, handler, { options }, capture);
```

```js
/* listen to *target* and *bubble* phases: */
element.addEventListener(eventType, handler); // If the capture argument is missing ...
element.addEventListener(eventType, handler, false); // or 'false' ...
element.addEventListener(eventType, handler, { capture: false }); // or this option is set 
```

```js
/* listen to *capture* phase: */
element.addEventListener(eventType, handler, true);
element.addEventListener(eventType, handler, { capture: true });
```

## delegation

(parents listening)

*Instead of attaching the event listeners directly to the buttons, you delegate listening to the parent.*
When a button is clicked, the listener of *the parent element catches the bubbling event*. This listener reacts to buttons clicks because the button click event bubbles through ancestors (thanks to the event propagation).

### advantages

* Instead of attaching listeners to every button, just one event listener is necessary.
* Listening for an event on multiple elements is much easier
* The HTML structure is flexible, we can add/remove buttons at any time.

### limitations

* the event must be *bubbling*. Some events do not bubble.
* the delegation may add CPU load, because the container-level handler reacts on events in any place of the container, no matter whether they interest us or not. But usually the load is negligible, so we don’t take it into account.

### how to delega

This approach is called *event delegation*, and it works by taking advantage of something called *event bubbling*.

When an element in the DOM is clicked, the event bubbles all the way up to the parent element (the document and then the window). This allows you to listen for events on the parent item and still detect clicks that happen inside it. 

The `event.target` is always the element that was clicked (or for other event types, the element that triggered the event).


```js
// 1. Attach the event listener to a parent element
// 2. In the handler use event.target to select the target element. 
// 		(event.currentTarget) points to the element to which the event listener is attached directly.
// 3. If the event happened inside an element that interests us, then handle the event

document.addEventListener('click', (evt) => {
	if (evt.target.matches('selector')) {
		// style 1
	}
	if (evt.target.classList.contains('class')) {
    
	}
  if (myElement.contains(evt.target)) {
    // is the element clicked a child of myElement?
    // --> click was inside
  }
  if (!evt.target.closest('selector') {
		// is the element clicked not a parent of my element?
    // --> must be a child
    // --> click was inside
  }
	if (!event.target.matches('.click-me')) {
    return 
  }
}, false);
```

```js
// useful methods for propagation

myElement.contains(evt.target) // returns a Boolean value indicating whether a node is a descendant of a given node, i.e. the node itself, one of its direct children (childNodes), one of the children's direct children, and so on.

evt.target.closest('selector') // Looks for the nearest ancestor that matches the CSS-selector. The elem itself is also included in the search. The ancestors together form the chain of parents from the element to the top. In other words, it goes up from the element and *checks each of its parents*. If it matches the selector, then the search stops, and the ancestor is returned.
```

### performance

It feels like listening to every click in the document would be bad for performance, but *it’s actually more performant than having a bunch of event listeners on individual items*.

### bubbling and capturing

Event bubbling and event capturing are two ways of propagating events that occur in an element that is nested within another element, when both elements have registered a handle for that event. *The event propagation mode determines the order in which elements receive the event*.

### tricks

```css
/* make an element "transparent" to event triggering */
myElement {
    pointer-events: none;
}
```

evt
----------------------------

### methods

```js
function handleEvent(evt) {
 	evt.preventDefault() // Cancels the event (if it is cancelable).
	evt.stopPropagation() // prevents further propagation of the current event in the capturing and bubbling phases. It does not, however, prevent any default behaviors from occurring; for instance, clicks on links are still processed. If you want to stop those behaviors, see the preventDefault() method. It also does not prevent propagation to other event-handlers of the current element. If you want to stop those, see stopImmediatePropagation().  
}
```

### properties

```js
evt.target 
// reference to the target to which the evt was originally dispatched. Read only.
// different from `evt.currentTarget` when the evt handler is called during the bubbling or capturing phase of the evt.

evt.eventPhase 
// indicates which phase of the event flow is being processed. Read only. NONE, CAPTURING_PHASE, AT_TARGET, BUBBLING_PHASE.
evt.currentTarget 
// reference to the currently registered target for the evt. 
// This is the object to which the evt is currently slated to be sent. 
// It's possible this has been changed along the way through retargeting. Read only.

evt.bubbles // true; indicates whether or not the evt bubbles up through the DOM. Read only
evt.composed // true; 
// indicates whether or not the evt can bubble across the boundary between the shadow DOM and the regular DOM.
evt.cancelable // true; read only; indicates whether the evt is cancelable.
evt.defaultPrevented 
// read only; 
// indicates whether or not the call to evt.prevtDefault() canceled the evt.
evt.isTrusted 
// Read only. 
// Indicates whether or not the evt was initiated by the browser (after a user click, for instance) 
// or by a script (using an evt creation method, for example).
evt.timeStamp 
// Read only. 
// The time at which the event was created (in milliseconds). 
// By specification, this value is time since epoch—but in reality, browsers' definitions vary. 
// In addition, work is underway to change this to be a DOMHighResTimeStamp instead.
evt.type // Read only
```

evt.type
----------------------------

### overview

```js
element.addEventListener('click', handleClick)
element.addEventListener('change', handleChange)
```

### resource events

```js
el.addEventListener('error', (evt) => {
	// A resource failed to load. 
})
el.addEventListener('abort', (evt) => {
	// The loading of a resource has been aborted. 
})
el.addEventListener('load', (evt) => {
	// A resource and its dependent resources have finished loading.
})
el.addEventListener('beforeunload', (evt) => {
	// The window, the document and its resources are about to be unloaded.
})
el.addEventListener('unload', (evt) => {
	// The document or a dependent resource is being unloaded.
})
```

### form events

```js
el.addEventListener('reset', (evt) => {
	// The reset button is pressed
})
el.addEventListener('submit', (evt) => {
	// The submit button is pressed
})
```

### UIEvent

*  interface that represents simple user interface events.
  * **WheelEvent**
    	* `wheel` # A wheel button of a pointing device is rotated in any direction.
  * **KeyboardEvent**
    * `keydown` # provides a code indicating which key is pressed
    * `keypress` # indicates which character was entered
    * `keyup` # provides a code indicating which key is pressed

### Window

```js
window.addEventListener('DOMContentLoaded', (evt) => {
	// Fires when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading.
  // <script defer src=""> defer indicates to a browser that the script is meant to be executed after the document has been parsed, but before firing DOMContentLoaded.
	// Scripts with the defer attribute will prevent the DOMContentLoaded event from firing until the script has loaded and finished evaluating.
}) 
window.addEventListener('load', (evt) => {
	// The load event is fired when the whole page has loaded, including all dependent resources such as stylesheets, scripts, iframes, and images. This is in contrast to DOMContentLoaded, which is fired as soon as the page DOM has been loaded, without waiting for resources to finish loading.   
}) 
window.addEventListener('popstate', (evt) => {
  // is fired when the active history entry changes while the user navigates the session history. It changes the current history entry to that of the last page the user visited or, if history.pushState() has been used to add a history entry to the history stack, that history entry is used instead.
})
window.addEventListener('pageshow', (evt) => {
  // is sent to a Window when the browser displays the window's document due to navigation.
	// This includes:
	// * Initially loading the page
  // * Navigating to the page from another page in the same window or tab
  // * Restoring a frozen page on mobile OSes
  // * Returning to the page using the browser's forward or back buttons
});
```

### Document

```js
document.addEventListener('DOMContentLoaded', doSomething) 
// Fires when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading.
```

### FocusEvent

```js
el.addEventListener('focus', doSomething) // An element has received focus (does not bubble).
el.addEventListener('blur', doSomething) // An element has lost focus (does not bubble).
el.addEventListener('focusin', doSomething) // An element is about to receive focus (does bubble).
el.addEventListener('focusout', doSomething) // An element is about to lose focus (does bubble).
```

### MouseEvent

```js
el.addEventListener('click', handleClick)
// fires after both the mousedown and mouseup events have fired
// If the button is pressed on one element and the pointer is moved outside the element before the button is released, the event is fired on the most specific ancestor element that contained both elements.
// The MouseEvent object passed into the event handler for `click` has its `detail` property set to the number of times the target was clicked.
el.addEventListener('dbclick', handleClick)
el.addEventListener('mouseup', handleClick)
// will trigger if you click and hold the button elsewhere, then release it above the object
el.addEventListener('mousedown', handleClick)
// will trigger even when the mouse is clicked on the object then moved off of it
el.addEventListener('mouseenter', doSth) 
// is moved onto the element that has the listener attached.
el.addEventListener('mouseleave', doSth) 
// is moved off the element that has the listener attached.
el.addEventListener('mousemove', doSth) 
// is moved over an element (fired continuously as the mouse moves).
el.addEventListener('mouseover', doSth) 
// is moved onto the element that has the listener attached or onto one of its children.
el.addEventListener('mouseout', doSth) 
// is moved off the element that has the listener attached or off one of its children.
```

#### properties

```js
/* Coordinates of the mouse pointer... */

MouseEvent.offsetX
// relative to the position of the padding edge of the target node.
// Returns the X/Y coordinate of the mouse pointer *relative to the position of the edge of the target element*
MouseEvent.clientX
// Provides the horizontal coordinate *within the application's viewport* at which the event occurred 
// (as opposed to the coordinate within the page).  
// Clicking on the *left edge* of the viewport will always result in a mouse event with a clientX *value of 0*, regardless of whether the page is scrolled horizontally.
MouseEvent.movementX
MouseEvent.movementY
// The X/Y coordinate of the mouse pointer *relative to the position of the last mousemove event*.
// Provides the difference in the X/Y coordinate of the mouse pointer between the given event and the previous mousemove event. 
// In other words, the value of the property is computed like this: 
currentEvent.movementX = currentEvent.screenX - previousEvent.screenX

MouseEvent.screenX 
// The X/Y coordinate of the mouse pointer *in global (screen) coordinates*
```

### WheelEvent

Event < UIEvent < MouseEvent < WheelEvent

`Element.onwheel` # fires when the user rotates a wheel button on a pointing device

#### properties
* `deltaX` # Returns the horizontal scroll amount of a mouse wheel (x-axis)
* `deltaY` # Returns the vertical scroll amount of a mouse wheel (y-axis)
* `deltaZ` # Returns the scroll amount of a mouse wheel for the z-axis
* `deltaMode` # Returns a number that represents the unit of measurements for delta values (pixels, lines or pages)

#### examples
```js
function zoom(evt) {
	evt.preventDefault();
	// inscrease variable scrolling (slowed down)
	scale += evt.deltaY * -0.01;
	// Restrict scale
	scale = Math.min(Math.max(.125, scale), 4);
	// Apply scale transform
	el.style.transform = `scale(${scale})`;
}

let scale = 1;
const el = document.querySelector('div');
el.onwheel = zoom;
```

### ScrollEvent

`Element.onscroll` # fires when an element has been scrolled.

### DragEvent

* set `draggable="true"` on the HTML element!
* `dataTransfer` # Returns the data that is dragged/dropped

```js
el.addEventListener('drag', doSth) 
// an element or text selection is being dragged.
el.addEventListener('dragend', doSth) 
// a drag operation is being ended (by releasing a mouse button or hitting the escape key).
el.addEventListener('dragenter', doSth) 
// a dragged element or text selection enters a valid drop target.
el.addEventListener('dragexit', doSth) 
// an element is no longer the drag operation's immediate selection target.
el.addEventListener('dragleave', doSth) 
// a dragged element or text selection leaves a valid drop target.
el.addEventListener('dragover', doSth) 
// continuously when an element or text selection is being dragged and the mouse pointer is over a valid drop target (firing pattern is different than mouseover)
el.addEventListener('dragstart', doSth) 
// the user starts dragging an element or text selection.
el.addEventListener('drop', doSth) 
// an element or text selection is dropped on a valid drop target.
```

### keyboardEvent

```html
<label for="inputEnter">mirror on enter: </label>
<input type="text" value="some text" id="inputEnter"/>
<output id="outputEnter"></output>

<label for="inputDirect">mirror directly: </label>
<input type="text" value="enter" id="inputDirect"/>
<output id="outputDirect"></output>
```

```js
inputEnter.onkeydown = function(event) {
	if (event.key == 'Enter') {
		console.log(event.target.value);
		outputEnter.textContent = `You entered: ${event.target.value}`;
	}
}
inputDirect.oninput = function(event) {
	outputDirect.textContent = `You entered: ${event.target.value}`;
}
```

#### change focus

```js
window.onkeydown = function(event) {
	changeFocus(event.key)
};

function changeFocus(key) {
	console.log(`key pressed: ${key}`);
	console.log(`active element: ${document.activeElement}`);
	let next = document.activeElement.nextElementSibling;
	let prev = document.activeElement.previousElementSibling;

	switch (key) {
		case "ArrowRight":
			while (next.tabIndex !== 0) {
				next = next.nextElementSibling
			} 
			next.focus();
			break;
		case "ArrowLeft":
			while (prev.tabIndex !== 0) {
				prev = prev.previousElementSibling
			}
			prev.focus();
			break;
	}
},
```

#### multiple keys

```js
document.addEventListener('keydown', this._onKey)
document.addEventListener('keyup', this._onKey)

_keyMap = {}

_onKey = (evt) => {
    this._keyMap[evt.key] = evt.key == 't' // is t pressed?
    this._keyMap[evt.key] = evt.key == 'q' // is q pressed?
    if (this._keyMap['q'] && this._keyMap['t']) { // is q and t pressed?
        this.querySelector('input#search-title').focus()
    }

}
```



### KeyboardEvent

Event --> UIEvent --> KeyboardEvent

* When you need to handle text input, use the `input` event instead.
Event types:
* `keydown`   # provides a code indicating which key is pressed
* `keypress`  # indicates which character was entered
* `keyup`     # provides a code indicating which key is pressed

#### properties

`keyboardEvent.key`     # returns the value of the key pressed by the user
* considers the state of modifier keys such as Shift as well as the keyboard locale and layout
* [key values](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values)
`keyboardEvent.code`    # Returns the key value of the key represented by the event
* ignores the user's keyboard layout
* useful when you want to handle keys based on their physical positions on the input device rather than the characters associated with those keys

* keyboardEvent.key
  * `"ArrowLeft"`
  * `"ArrowRight"`
  * `" "` # spacebar
  * `"Tab"`	
  * `"Enter"`	

```js
// event.key
// If the pressed keyboard button is "a" or "A" (using caps lock or shift), alert some text.
var x = event.key;
if (x == "a" || x == "A") {
    alert ("You pressed the 'A' key!");
}
// event.code
window.addEventListener('keydown', function(event) {
	switch(event.key) {
		case "ArrowLeft":
			prevSlide()
			break;
		case "ArrowRight":
			nextSlide();
			break;
	}
});
```

### InputEvent

Event < UIEvent < InputEvent

```html
<input type="checkbox"> when checked or unchecked
<input type="radio"> checked (but not when unchecked)
<input type="text"> after editing the value
<select></select> by selecting a value
<textarea> after editing the value
```

The InputEvent interface represents an event notifying the user of editable content changes.

### ChangeEvent

```html
Fired when an alteration to the element's value is committed by the user
Unlike the input event, the change event is not necessarily fired for each alteration to an element's value.
<input>
<input type="checkbox"> when checked or unchecked
<input type="radio"> when checked (but not when unchecked)
<select> 

When the element loses focus after its value was changed:
<textarea> 
<input type="search">
<input type="url">
<input type="email">
<input type="password">
<input type="text">
```

## create event

The Event interface represents an event which takes place in the DOM.


```js
// Using a method:
window.addEventListener('load', (evt) => {
	console.log('The page has fully loaded')
})

// Using a property:
window.onload = (evt) => {
	console.log('The page has fully loaded')
}
```

### Event

```js
new Event(type)
new Event(type, options)

// create a look event that bubbles up and cannot be canceled
const evt = new Event("look", { 
  bubbles: true, 
  cancelable: false 
});

document.dispatchEvent(evt);

// event can be dispatched from any element, not only the document
myDiv.dispatchEvent(evt);
```

### CustomEvent (adding custom data)

Now generally when you are working with custom events you want to be  able to pass some form of custom data to your events. With the normal `new Event` constructor this is not possible which is why there is a second way to create custom events.

To add more data to the event object, the CustomEvent interface exists and the detail property can be used to pass custom data.

```js
const event = new CustomEvent("build", { detail: elem.dataset.time });

function eventHandler(e) {
  console.log(`The time is: ${e.detail}`);
}
```

```js
const event = new CustomEvent('dile-breadcrumbs-click', { 
	bubbles: true,
  composed: true,
  detail: {
  	name: this.name,
  }
})
```



```js
function _documentClose() {
		if (this.opened) {
			this.close();
			this.dispatchEvent(
				new CustomEvent(DILE_APP_DRAWER_EVENT_CLOSE_OUTSIDE, {
					bubbles: true,
					composed: true,
				})
			);
		}
	}
```

## addEventListener

```js
EventTarget.addEventListener('type', (evt) => {
	// Callback that's executed an event of the specified type occurs.
}))
```

```js
EventTarget.addEventListener('type', listener, options, true);
// useCapture:  
/* A Boolean indicating whether events of this type will be dispatched to the registered listener before being dispatched to any EventTarget beneath it in the DOM tree. Events that are bubbling upward through the tree will not trigger a listener designated to use capture. */
```

```js
EventTarget.addEventListener('type', callback, { 
  once: true, // listener will be automatically removed when invoked
  capture: true, // events of this type will be dispatched to the registered listener 
  // before being dispatched to any EventTarget beneath it in the DOM tree.
  passive: true // indicates that the function specified by listener will never call preventDefault(). 
  // If a passive listener does call preventDefault(), the user agent will do nothing other than generate a console warning.
  // See Improving scrolling performance with passive listeners to learn more.
});
```

EventTarget
-----------------

EventTarget is a DOM interface implemented by *objects that can receive events and may have listeners for them*.  
*Element*, *Document*, and *Window* are the most common event targets, but other objects can be event targets, too. For example *XMLHttpRequest*, *AudioNode*, *AudioContext*, and others.
Many event targets (including elements, documents, and windows) also support setting event handlers via *onevent* properties and attributes.

```js
EventTarget() // Creates a new EventTarget object instance
```

### GlobalEventHandlers

mixin; describes the event handlers common to several interfaces like `HTMLElement`, `Document`, or `Window`  

#### EventHandler

* `GlobalEventHandlers.onerror` # an *OnErrorEventHandler* representing the code to be called when the *error* event is raised.
* `GlobalEventHandlers.onfocus` # an *EventHandler* representing the code to be called when the *focus* event is raised.
* `GlobalEventHandlers.onchange` # an *EventHandler* representing the code to be called when the *change* event is raised.
* `GlobalEventHandlers.onclick` # an *EventHandler* representing the code to be called when the *click* event is raised.
* `GlobalEventHandlers.onselect` #  an *EventHandler* representing the code to be called when the *select* event is raised.
* `GlobalEventHandlers.onplay` # Is an *EventHandler* representing the code to be called when the *play* event is raised.
* `GlobalEventHandlers.oninput` # Is an *EventHandler* representing the code to be called when the *input* event is raised.
  * processes *input* events on the `<input>`, `<select>`, and `<textarea>` elements. 
  * Note: Unlike `oninput`, the `onchange` event handler is not necessarily called for each alteration to an element's value.
* `GlobalEventHandlers.onsubmit` # an *EventHandler* representing the code to be called when the *submit* event is raised.

#### DragEvent

* `GlobalEventHandlers.ondrag` # A global event handler for the drag event.
* `GlobalEventHandlers.ondragend` # A global event handler for the dragend event.
* `GlobalEventHandlers.ondragenter` # A global event handler for the dragenter event.
* `GlobalEventHandlers.ondragexit` # A global event handler for the dragexit event.
* `GlobalEventHandlers.ondragleave` # A global event handler for the dragleave event.
* `GlobalEventHandlers.ondragover` # A global event handler for the dragover event.
* `GlobalEventHandlers.ondragstart` # A global event handler for the dragstart event.
* `GlobalEventHandlers.ondrop` # A global event handler for the drop event. 

## handle events

### named callback

```js
// an event handler callback that can be used to handle both fullscreenchange and fullscreenerror:
function eventHandler(event) {
  if (event.type === 'fullscreenchange') {
    /* handle a full screen toggle */
  } else /* fullscreenerror */ {
    /* handle a full screen toggle error */
  }
}
```

### this.handleEvent

```js
/* handleEvent within a class */
/* Instead of having different functions to handle events, 
you can use a single handleEvent function to route all kinds of events */

class MyClass {
  
  constructor() {
    const btn = document.querySelector('button');
    const input = document.querySelector('input');
    // Handle clicks by this
    btn.addEventListener('click', this)
    // Handle input by this
    input.addEventListener('input', this)
  }
  
	handleEvent(event) {
  	if (event.type === 'click') {
    	// handle button clicks 
    }
    else if (event.type === 'input') {
      // handle text input
		}
	}
}
```

```js
handleEvent(evt) {
		this['on' + evt.type](evt) 
		// calls onclick(evt)
		// calls onmouseover(evt)
	}

	onclick(evt) {
		// open click
		
		console.log(evt.target)
		this.dest.classList.toggle(this.cls)
		this.toggleAttribute(this.cls)
	
		/* if (evt.target !== this.trigger) {
			// close click
			// if evt.target (where the user clicked) is not contained within the customElement
			this.dest.classList.remove(this.cls)
		} */
	}

	onmouseover(evt) {
		this.dest.classList.add(this.cls)
	}
```

```js
class TenClicks {

  constructor(currentTarget) {
    this.clicks = 10;
    currentTarget.addEventListener('click', this);
  }

  handleEvent(event) {
    this['on' + event.type](event);
  }

  onclick(event) {
    event.preventDefault();
    console.log(this.clicks)
    // once we reach 0
    if (--this.clicks < 1) {
      // remove the listener
      console.log('removing event listener...');
      event.currentTarget.removeEventListener(event.type, this);
      // show the result
      console.log('You made it!');
    }
  }

}

let ten = new TenClicks(document.body);
```

```js
/* within an object */

let obj  =  {
	init: function() {
  	document.getElementById("btn").addEventListener("click", this, false)
    document.getElementById("btn").addEventListener("touchstart", this, false)
	},
  handleEvent: function(e) {
  	switch(e.type) {
    	case "click":
      	this.button()
        break
			case "touchstart":
      	this.button()
				break
    	}
		},
    dude: "holla",
    button: function() {
    	alert(this.dude)
    }
}

obj.init() // binds all the events and passes in 'this' as the callback
// handleEvent then delegates off to whatever method it needs based on the event being triggered
```

```js
let listener = {
  greeting: 'Hello ',
  handleEvent: function(evt) {
    console.log(this.greeting + evt.type)
  },
}

dragElem.addEventListener( 'mousedown', listener )
// on mousedown...
// => 'Hello mousedown'
```

### this

**the value of this 'this' within the handler**

```js
// the value of this inside the handler is a reference to the element
element.addEventListener('click', function(evt) {
  console.log(this)           // className = element
})
```

```js
// arrow functions do not have their own 'this' context:
element.addEventListener('click', (e) => {
  console.log(this)           // `this` != element
})
```

#### this context: class vs. eventlistener

```js
class Counter () {
	#count
	constructor (counter) {
    this.buttonElement = counter.querySelector('button')
    this.buttonElement.addEventListener('click', this.increaseCount)
    // this.buttonElement --> points to the object
    // this.increaseCount --> points to the element that is clicked
	}
	increaseCount () {
    	this.#count = this.#count + 1
	}
}
```

## remove listeners

### removeEventListener()

Removing event listeners from DOM elements is pretty annoying because you must have the registered event handler function at hand.

Calling `removeEventListener()` with arguments that do not identify any currently registered event listener on the `EventTarget` has no  effect.

* use with named callback functions

```js
// add an event listener
el.addEventListener('click', handleClick)
el.removeEventListener('click', handleClick)
```

* don't use with anonymouse callback functions

```js
// add an event listener
document.querySelector('button').addEventListener('click', (evt) => {
  this.classList.toggle('active')
})

// to remove an event listener, the event type ('click') 
// and the function ('handleEvent') have to match the `addEventListener` call
document.querySelector('button').removeEventListener('click', (evt) => {
  this.classList.toggle('active')
})
```

* use with handleEvents() methods

```javascript
this.element.removeEventListener('click', this);
this.element.removeEventListener('mouseenter', this);
```

### remove with cloneNode

```js
const button = document.querySelector('button);

// replace the element with a copy of itself
// and nuke all the event listeners
button.replaceWith(button.cloneNode(true));
```

### remove with abort signal

```javascript
var parent = document.getElementById('parent');
// create an AbortSignal object
const abortSignal = new AbortController();

parent.addEventListener('click', event => {
  if(event.target.closest('#child')) {
    console.log('click');
  } else {
    abortSignal.abort(); // this will remove the listener
  }
}, {signal: abortSignal.signal});
```

## Solutions

### add

#### add an EventListener to every element of an array:

```js
// add an EventListener to every element of an array:
let elements = Array.from(document.getElementsByClassName('class'))
elements.forEach(item => {
    item.addEventListener("click", () => {
      item.style.color = "#92a8d3"
    })
})
```

### misc

#### handleFrontBack()

```js
function handleFrontBack(el, others = []) {
	// pull one element into the foreground
	// push other elements back
	
	return function frontBack(evt) {
		// eventHandler
		// pull it into the foreground:
		el.style.zIndex = 2;
		// push the others back
		for (let other of others) {
			if (other == el) continue; // jump to next if it's the element clicked
			other.style.zIndex = 1;
		}
	}
}
```



Troubles
-------------------------------------------------

### Problem: head referencing body

```js
// PROBLEM: We reference the button in the body from the JS in the head.
let button = document.getElementById('btn');
button.addEventListener('click', (e) => {
    // handle the click event
});

// SOLUTION 1) Wrap it in another Event Listener which is waiting for DOMContentLoaded.
document.addEventListener('DOMContentLoaded', () => {
    let button = document.getElementById('btn');
    button.addEventListener('click', () => {
        // handle the click event
    });
});

// SOLUTION 2) Use <script type="module">
```
### Problem: Event fires without click

```js
    let button = document.getElementById("button")
    // wrong (calling it directly):
    button.addEventListener("click", myFunction("go"));
    // right (put it in a callback):
    button.addEventListener("click", function (){
        myFunction("go");
    });
    // or using an arrow function:
    button.addEventListener("click", () => {
        myFunction("go");
    });
```

### Problem: passing arguments to referenced functions

It may seem that event listeners are like islands, and that it is extremely difficult to pass them any data, much less to get any data back from them after they execute. Event listeners only take one argument, the *Event* Object, which is automatically passed to the listener, and the return value is ignored.

#### the problem

```js
mydiv.onclick = myhandler(evt, myobject) // doesn't work
```

This immediately execute the handler as a normal function, passing it the variables. Then whatever the function returns, even if that is a string, or perhaps nothing at all, is assigned as a property of the element with the name of the event handler. Obviously this will not work correctly as an event handler.

```js
function myfunction() {
	var pElements = document.getElementsByTagName('p')
	var spanElements = document.getElementsByTagName('span')
	
    for(let i = 0; i < pElements.length; i++ ) {
		pElements[i].onclick = function(evt) {
            // assume that here, we want to know
            // - what the value of i was for this element
            // - the span that corresponds to spans[i]
		}
	}
}
```

There are two acceptable main ways to overcome the problem:  

* Assigning variables as properties of the element
* Using scope to remember the instances of the local variables

#### solution 1: assigning variables as properties of the element

The idea for this approach is to store all the variables that will be needed as properties of the element that detects the event.  
Then when the function is executed, it can look for these properties on the element, referenced using the `this` keyword.

This approach works well in most situations, but can have problems. Say for example that you want to use the same function multiple times, attaching it as a handler on the same element for multiple events, where each one expects different variables to be available. It is possible to work around this, but it can get messy. If using `this`, it is important to make certain that the chosen variable names will never be used by any browser as part of the DOM properties for that element. This also adds weight to the element objects, and is not the cleanest approach, but it does work:

```js
function myfunction() {
	var pElements = document.getElementsByTagName('p');
	var spanElements = document.getElementsByTagName('span');
    
	for( var i = 0; i < pElements.length; i++ ) {
		pElements[i].originalindex = i;
		pElements[i].relatedspan = spanElements[i];
		pElements[i].onclick = function(evt) {
			let a = this.originalindex, b = this.relatedspan;
			// ... etc.
		};
	}
}

/* Functions are just objects, and in javascript objects can be very flexible. 
The properties that you add on to the function can then be accessed from anywhere 
that the function itself can be accessed. */

function loadfunction () {
	alert("welcome and " + loadfunction.text);
}
loadfunction.text = 'hello';
window.addEventListener("load", loadfunction, true);
```
Since this executes the event handler in the local scope, it would also be possible to reference the span using:  
`b = spans[this.originalindex];`
Note that again, the objects returned by getElementsByTagName are dynamic, so if the number of spans changes between creating the event handler and running it, the spans object will be updated, and the index will not work.

#### solution 2: using a closure

Run a function, passing it the parameters, that returns another function that exists inside it, within its local scope. This becomes the event handler when it is assigned, and is executed inside the local scope of the outer function, with the local variables that were passed to it preserved within that scope.

```js
function handleAction(a,b) {
	return function(evt) {
		// do something with a and b
		// ... etc.
	}
}

function myfunction() {
	var paras = document.getElementsByTagName('p')
	var spans = document.getElementsByTagName('span')
	for( var i = 0; i < paras.length; i++ ) {
		paras[i].onclick = scopepreserver(i,spans[i])
	}
}
```
If you do not want to use an external function, it is also possible to define the scope preserving function inline, and execute it immediately. This has the added advantage that it preserves the scope of the main function (myfunction in the examples) as well as the inner scopes, but has the disadvantage that it becomes less easy for others to understand:

```js
function myfunction() {
	var paras = document.getElementsByTagName('p');
	var spans = document.getElementsByTagName('span');
	for( var i = 0; i < paras.length; i++ ) {
		paras[i].onclick = (function(a,b) {
			return function() {
				//do something with a and b
				// ... etc.
			};
		})(i,spans[i]);
	}
}
```

#### using bind()

```js
const someString = 'Data';

myButton.addEventListener('click', function() {
    console.log(this);  // Expected Value: 'Data'
}.bind(someString));

// in classes bind(this) references the instance object
myButton.addEventListener('click', this.myFunction.bind(this));
```

We used arrow functions on all the handle events. This allows us to call them from the view using the this context of the controller. If we did not use arrow functions, we would have to manually bind them.