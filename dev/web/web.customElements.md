# CustomElements

## types

### autonomous custom elements

Standalone elements; they don't inherit from built-in HTML elements. 

```js
// autonomous custom element
class MyCustomElement extends HTMLElement {
    //...
}
customElements.define('my-custom-element', MyCustomElement)
```

```js
class MyCustomElement extends HTMLElement {
  static observedAttributes = ["color", "size"];

  constructor() {
    // Always call super first in constructor
    super();
  }

  connectedCallback() {
    console.log("Custom element added to page.");
  }

  disconnectedCallback() {
    console.log("Custom element removed from page.");
  }

  adoptedCallback() {
    console.log("Custom element moved to new page.");
  }

  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`Attribute ${name} has changed.`);
  }
}

customElements.define("my-custom-element", MyCustomElement);
```

### customized built-in elements

* don't use as Safari doesn't support the `is` attribute

```js
class ConfirmLink extends HTMLAnchorElement {
  connectedCallback() {
    this.addEventListener("click", e => {
      const result = confirm(`Are you sure you want to go to '${this.href}'?`);
      if (!result) e.preventDefault();
    });
  }
}

customElements.define("confirm-link", ConfirmLink, { extends: "a" });
```

```html
<a href="https://thewebplatformpodcast.com" is="confirm-link"></a>
```

## create | insert | declare | upgrade

### create

* triggers `constructor` - **if** the element has already been defined
* The `constructor` is only ever called once per element instance

```js
// can happen before definition
const myElement = document.createElement('my-element');
// can only happen if already defined
const myElement = new MyElement();
```

### insert

* triggers `connectedCallback` - **if** the element has already been defined

```js
document.body.append(myElement); // triggers connectedCallback()
```

The `connectedCallback` is only  when the element is added to the *document* and actually becomes a part of the page (not just appended to another element that we just created). So we can build detached DOM, create elements and prepare them for later use. 
They will only be actually rendered when they make it into the page.

### declare

* **create & insert**
* triggers `constructor` and `connectedCallback` - **if** the element has already been defined

```html
<my-element></my-element>
```

```js
document.body.innerHTML = '<my-element></my-element>';
```

### upgrade

In all the above cases the lifecycle methods are only called if the element has already been defined. An element is **upgraded** when it already exists before definition - at the point of definition the `constructor` is then called automatically. If the element was already attached to the DOM at this point `connectedCallback` will also be called.

## nested

Custom Elements are constructed and connected depending on the order of their `<script>` tag

```html
<script src='un-nav.js' type="module"></script>
<script src='un-btn.js' type="module"></script>
<!-- Now un-nav gets constructed and connected first -->
<body>
  <un-nav>
		<un-btn>Click</un-btn>
	</un-nav>
</body>
```

lifecycle
------------------------------------

```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    // element created
  }
  
  static observedAttributes = ['attribute']

  attributeChangedCallback(name, oldValue, newValue) {
    // called when one of attributes listed above is modified
  }
  
  connectedCallback() {
    // browser calls this method when the element is added to the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  disconnectedCallback() {
    // browser calls this method when the element is removed from the document
    // (can be called many times if an element is repeatedly added/removed)
  }

  adoptedCallback() {
    // called when the element is moved to a new document
    // (happens in document.adoptNode, very rarely used)
  }

  // there can be other element methods and properties
}
```

### constructor()

* Is only ever called once per element instance
  Initialisation that needs to happen each time the element is attached to the DOM should be deferred to `connectedCallback`
* Add **event listeners**. Why? Because `connectedCallback` can be called multiple times during the lifecycle of our element, for example when using drag and drop, or when an element is disconnected from one location and connected again in another. That would cause the event listener to be registered multiple times, a common source of bugs.
* Create your **shadow root** . Why? The constructor is when you have exclusive knowledge of your element. It's a great time to setup implementation details that you don't want other elements messing around with. Doing this work in a later callback, like the connectedCallback, means you will need to guard against situations where your element is detached and then reattached to the document.
* **Place any children the element creates into its shadow root.** Why? Children created by your element are part of its implementation and should be private. Without the protection of a shadow root, outside JavaScript may inadvertently interfere with these children.

- Set up **initial state** and events that don't need to be removed or cleaned up when the element is destroyed 
  (there is no `deconstructor` method)
- **attributes** and **child elements** should not be accessed or modified in the `constructor` since, depending on how the element is created, they may not exist e.g. 
  - `document.createElement('my-element')` will trigger the `constructor` but the element has had no chance yet to set attributes or children.


#### Do

* set state
* add event listeners
* set up a shadow root

#### Don't

* Try to access attributes or children 
  (with the exception of the shadow root)
* Inspect the element’s children or attributes
* Use document.write() or document.open()
* DOM manipulations: it is typically considered bad practice for element boot-up performance reasons

```js
class SimpleComponent extends HTMLElement {
 	constructor() {
  	
  	super();
		// setting some initial state
  	this.counter = 0;
  	this.active = false;
		this.attachShadow({ mode: "open" });
  	this.shadowRoot.innerHTML = ``

    // this won't work:
    this.id = "thisWontWork";
    this.setAttribute("dont-do-this", "it-will-cause-an-error");

     // append a clone of the template's content to the shadow root
     this.shadowRoot.appendChild(this.template.content.cloneNode(true));

	}
}
```

### connectedCallback()

* is called when the custom element is attached to the DOM
* most other initialisation tasks. Any clean up can then happen in `disconnectedCallback`.
* if you need to access / modify attributes or children on initialisation this must therefore happen in `connectedCallback`. In this case you will often need to guard against such initialisation happening multiple times as the element is removed and re-attached to the DOM.
* Word of warning: it’s very likely the connectedCallback() will fire multiple times over the life of the element; for example, when it’s moved from one part of the DOM to another.

#### Do

* inspect the DOM
* initial DOM manipulations
* read attributes
* add children to our element

```js
connectedCallback() {
	this.attachShadow({ mode: 'open' })
	this.template = document.querySelector('template')
	
	// style 1:
	const clone = document.importNode(this.template.content, true)
	this.shadowRoot.appendChild(clone)
	
	// style 2:
	this.shadowRoot.appendChild(this.template.content.cloneNode(true))
}
```

#### render

Rendering in `connectedCallback`, not in `constructor`: Why not in the `constructor`?

The reason is simple: when `constructor` is called, it’s  yet too early. The element is created, but the browser did not yet process/assign attributes at this stage: calls to `getAttribute` would return `null`. So we can’t really render there.

Besides, if you think about it, that’s better performance-wise – to delay the work until it’s really needed.

### disconnectedCallback

* is called after the custom element is removed from the DOM

### attributeChangedCallback()

* called when an *observed attribute* has been added, removed, updated, or replaced. 
* Also called for initial values when an element is created by the parser, or upgraded. 

Note: only attributes listed in the `observedAttributes` property will receive this callback.

```js
attributeChangedCallback(attrName, oldValue, newValue) {
	// code
}
```

### adoptedCallback()

* is called when the custom element is adopted from another documentFragment into the main document via adoptNode such as in HTMLTemplateElement

### customElements.define()

Defining an element **doesn't** trigger either the `constructor` or the `connectedCallback` methods since it does not create an instance of an element.

## CSS

### selectors

```css
:host { 
  display: block 
}
/* or */
my-element {
  display: block 
}
```

Custom elements are **`display: inline` by default**, so setting their width or height will have no effect. Unless you prefer an inline display, you should always set a default display value.

```css
:host([hidden]) { 
  display: none 
}
```

**Add a :host display style that respects the `hidden` attribute.** Why? A custom element with a default display style, e.g. :host { display: block }, will override the lower specificity built-in hidden attribute. This may surprise you if you expect setting the hidden attribute on your element to render it display: none. In addition to a default display style, add support for hidden.

### `<style>`

Problem: **For each instance of a component added to the page, a browser will parse the style sheet rules.**

```js
class FancyComponent extends HTMLElement {
  constructor() {
    super();
    const shadowRoot = this.attachShadow({ mode: 'open' });
    shadowRoot.innerHTML = `
      <!-- Styles are scoped -->
      <style>
        p { color: red; }
      </style>
      <div>
        <p>Hello World</p>
      </div>
    `;
  }
}
customElements.define('fancy-comp', FacyComponent);
```

### `<style>` in `<template>`

```html
<template id="card-template">
  <style>
    :host {
      background: white;
    }
    h2 {
      font: 900 1.5rem/1.1 -system-ui, sans-serif;
    }
  </style>

  <div id="card-hook">
    <h2 id="title-hook"></h2>
    <p id="desc-hook"></p>
  </div>
</template>
```

### CSSStyleSheet

```js
const myElementSheet = new CSSStyleSheet();
class MyElement extends HTMLElement {
    constructor() {
        super();
        const shadowRoot = this.attachShadow({ mode: "open" });
        shadowRoot.adoptedStyleSheets = [myElementSheet];
    }

    connectedCallback() {
        // Only actually parse the stylesheet when the first instance is connected.
        if (myElementSheet.cssRules.length == 0) {
            myElementSheet.replaceSync(styleText);
        }
    }
}
```

```js
    import { colorsSheet, headlinesSheet, listsSheet, } from '/style-system.js'
    import { styles, } from './styles.js'

    // ...

    connectedCallback() {
        // Only compose styles once
        if (this.shadowRoot.adoptedStyleSheets.length == 0) {
            this.shadowRoot.adoptedStyleSheet = [
                colorSheet,
                headlinesSheet,
                listsSheet,
                styles,
            ];
        }
    }
```

```js
    import styles from './styles.css';

    const sheet = new CSSStyleSheet();
    sheet.replace(styles);
```



## attributes & properties

* Keep attributes and properties in sync, reflecting from property to attribute, and vice versa
  Calling `setAttribute('foo', value)` should also set the corresponding property and vice versa
* You shouldn't reflect high frequency properties, e.g. currentTime in a video player.
* Do not reflect rich data properties to attributes.

### attribute observation

* attributes become available in the `connectedCallback`
* attributes are **not available** yet in the `constructor`
* the `attributeChangedCallback` runs **before** the `connectedCallback` for **observed attributes**

#### observedAttributes

```js
// As of May 2021 and the release of Safari 14.1, static class fields are available in all browsers, which means it's much easier to declare your observedAttributes:
class StaticElement extends HTMLElement {
	static observedAttributes = ['data-trig']
}
```

```js
// old style
class StaticElement extends HTMLElement {
  static get observedAttributes() {
      return ['data-trig'];
    }
}
```

#### attributeChangedCallback

* `attributeChangedCallback()` fires on every **single** attribute change, including init when the element is added to the DOM
* changing attribute in browser calls `attributeChangedCallback()` twice: 
  1. remove the old atttribute
  2. add the new attribute

* `$0.dataset.trig='click'` calls `attributeChangedCallback()` only once (for change)

```js
attributeChangedCallback(name, previous, current) {
  // attribute was added:
	console.log(previous, current) // null	"current"
  // attribute was changed:
  console.log(previous, current) // "previous"	"current"
  // attribute was removed:
	console.log(previous, current) // "previous"	null
}
```

```js
attributeChangedCallback(name, oldValue, newValue) {
  if (oldValue === null) {
    return // prevent to be called on initialization
  }
}
```

### property reflection

* not required if `data-*` attributes are used!

#### caution: infinite loop

attribute change >> set property
property change >> set attribute

#### reflect properties

```js
// When the property value changes the new value will be reflected to the attribute as well.
class MyCustomElement extends HTMLElement {

	get value() {
		return this.getAttribute('value');
	}

	set value(val) {
		if (val) {
			this.setAttribute('value', val);
		} else {
			this.removeAttribute('value');
		}
	}
  
}
```

#### reflect boolean properties

```js
class MyCustomElement extends HTMLElement {
  
  static observedAttributes = ['disabled']

  get disabled() {
    return this.hasAttribute('disabled'); // get property by attribute
  }
  
  set disabled(val) {
    if(val) {
      this.setAttribute('disabled', val); // set attribute by property
    } else {
      this.removeAttribute('disabled');
    }
  }

  attributeChangedCallback(name, oldValue, newValue) {
    this.disabled = newValue;  
  }

}
```

```js
class MyCustomElement extends HTMLElement {

	static observedAttributes = ["disabled"]

	get disabled() {
		return this.getAttribute("disabled") === "true";
	}
  
  set disabled(bool) {
		this.setAttribute("disabled", bool.toString());
	}

	attributeChangedCallback(attrName, oldVal, newVal) {
		if (attrName === "disabled") {
			this.shadowRoot.getElementById("button").disabled = newVal === "true";
		}
	}

}
```

## events

**Dispatch events in response to internal component activity.** Your component may have properties that change in response to activity that only your component knows about, for example, if a timer or animation completes, or a resource finishes loading. 

It's helpful to dispatch events in response to these changes to ***notify the host that the component's state is different***.

## HTML

### js template strings

```js
const template = (data) => (` 
<div> 
   <a id="test" href="${data.link}">${data.text}</a> 
</div> 
`); 
 
document.body.innerHTML = template({
  link: '#',  
  text: 'hello world', 
}); 
```

### innerHTML

* Is cleaner, especially when we start adding in extra properties like classes and javascript events:

  ```js
  div.innerHTML += '<p class="paragraph" onclick="doSomething()">New Element</p>'
  ```

* `innerHTML` overwrites everything inside

```js
this.innerHTML = '' // overwrites any innerHTML that is already present like children declared in the index.hml
```

```html
<un-menu-drop>
		<un-btn data-theme="x" data-dest="un-btn-1"></un-btn> <!-- gets overwriten by innerHTML -->
</un-menu-drop>
```

### createElement()

* `innerHTML` causes the web browsers to reparse and recreate all DOM nodes inside the element. 
* it is less efficient than creating a new element and appending to the DOM tree
* `createElement` is more secure

```js
const style = this.createElement('style')
style.innerHTML = `
	#target {
		color: blueviolet;
	}
	`;
this.append(style)
```

### createDocumentFragment()

Use `DocumentFragment` for composing DOM Nodes

Assuming that you have a list of elements and you need  in each iteration:

```js
const div = document.querySelector('.container')

for (let i = 0; i < 1000; i++) {
   let p = document.createElement('p');
   p.textContent = `Paragraph ${i}`;
   div.appendChild(p); 
  // This results in recalculation of styles, painting, and layout every iteration. 
  // This is not very efficient.
}
```

To overcome this, you typically use a `DocumentFragment` to compose DOM nodes and append it to the DOM tree:

```js
let div = document.querySelector('.container');

// compose DOM nodes
let fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
   let p = document.createElement('p');
   p.textContent = `Paragraph ${i}`;
   fragment.appendChild(p);
}

// append the fragment to the DOM tree
div.appendChild(fragment);
```

We composed the DOM nodes by using the `DocumentFragment` object and append the fragment to the active DOM tree once at the end.
A document fragment does not link to the active DOM tree, therefore, it doesn’t incur any performance.

```js
// alternatively use innerHTML + variable
let newElements = ''; 
for (i = 0; i < 100; i++) { 
 newElements += `<p>New Element Number: ${i}</p>` 
} 
div.innerHTML =+ newElements // here innerHTML can be the more efficient choice
```

## custom state

* https://www.dannymoerkerke.com/blog/the-hidden-power-of-custom-states-for-web-components/
* https://developer.mozilla.org/en-US/docs/Web/API/CustomStateSet#browser_compatibility