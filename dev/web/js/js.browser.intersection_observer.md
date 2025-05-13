
# IntersectionObserver

* execute a callback function whenever the visibility of the **target** element changes so that it crosses desired amounts of intersection with the **root**.

* A **target** element intersects either the device's viewport or a specified element. 

* One observer has one set of thresholds and one root, but can watch multiple target elements for visibility changes in keeping with those. 

* That specified element is called the **root element** or **root** for the purposes of the Intersection Observer API.

  To watch for intersection relative to the device's viewport, specify `null` for `root` option.

* The degree of intersection between the target element and its root is the **intersection ratio**. 
  This is a representation of the percentage of the target element which is visible as a value between 0.0 and 1.0.

## solutions

### oberserve position: sticky

```js
// get the sticky element
const stickyElm = document.querySelector('header')
const observer = new IntersectionObserver(([e]) => {
  	e.target.classList.toggle('isSticky', e.intersectionRatio < 1)
	},
  { 
    threshold: [1],
    rootMargin: '-1px 0px 0px 0px'
  }
);

observer.observe(stickyElm)
```

## constructor

```js
const observer = new IntersectionObserver(callback, { 
  threshold: 1.0, // call callback when 100% of the target is visible within the element specified by the root option
  threshold: 0, // default; call callback as soon as even one pixel is visible
  threshold: [0, 0.25, 0.5, 0.75, 1] // call callback every time visibility passes another 25%
  root: null // default; watch for intersection relative to the top-level of the document's viewport
})

observer.thresholds // A list of thresholds, sorted in increasing numeric order, where each threshold is a ratio of intersection area to bounding box area of an observed target. Notifications for a target are generated when any of the thresholds are crossed for that target. If no value was passed to the constructor, 0 is used.
```

### callback

A function which is called when the percentage of the target element is visible crosses a threshold.

* An array of `IntersectionObserverEntry` objects, each representing one threshold which was crossed, either becoming more or less visible than the percentage specified by that threshold.
* The list of entries received by the callback includes one entry for each target which reported a change in its intersection status. 
* Check the value of the `isIntersecting` property to see if the entry represents an element that currently intersects with the root.
* Be aware that your callback is executed on the main thread. It should operate as quickly as possible; if anything time-consuming needs to be done, use `Window.requestIdleCallback()`

```js
function oberserverCallback(entries, observer) {
  entries.forEach((entry) => {
    console.log(entry) 
    // entry.boundingClientRect
    // entry.isIntersecting
    // entry.intersectionRatio
    // entry.intersectionRect
    // entry.rootBounds
    // entry.time
  })
}

// entry.isIntersecting
// A Boolean value which is true if the target element intersects with the intersection observer's root. If this is true, then, the IntersectionObserverEntry describes a transition into a state of intersection; if it's false, then you know the transition is from intersecting to not-intersecting.

// entry.target // The Element whose intersection with the root changed.
```

Note: the observer callback will always fire the first render cycle after `observe()` is called, even if the observed element has not yet moved with respect to the viewport. This means that, for example, an element that is outside the viewport when observe() is called on it will result in the callback being immediately called with at least one entry with intersecting set to false. An element inside the viewport will result in the callback being immediately called with at least one entry with intersecting set to true. 

### example

```js
const callback = (entries, observer) => {
	entries.forEach(entry => {
		if (entry.isIntersecting) {

			this.sectionContent.classList.toggle('open')
			let clone = this.template.content.cloneNode(true);
			this.template.replaceWith(clone)
			this.dataset.state = 'active'

		}
	})
}
const observer = new IntersectionObserver(callback, { threshold: [0.5] }) 
// If 50% of the element is in the screen, we count it! 
// Change the thresholds based on your needs. The default is 0 - it'll run only when the element first comes into view

observer.observe(metaHead)
```

## instance methods

### observe()

* adds an element to the set of target elements being watched by the IntersectionObserver.
* one observer has one set of thresholds and one root, but can watch multiple target elements for visibility changes in keeping with those. 

```js
observer.observe(targetElement)
// targetElement: An element whose visibility within the root is to be monitored. 
// This element must be a descendant of the root element 
// or contained within the current document, if the root is the document's viewport)
```

### unobserve()

```js
observer.unobserve(target) // stop observing target
```

### disconnect()

```js
observer.disconnect() // stops watching ALL of its target elements for visibility changes
```


