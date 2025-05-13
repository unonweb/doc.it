General
==========================================

* Lit is published as ES2019.
* Lit uses "bare module specifiers" to import modules.
* Lit uses modern web APIs such as `<template>`, custom elements, shadow DOM, and ParentNode.

# Directives

## until

```js
render() {
  console.log('render()')
  return html`
    ${until(this.asyncReady.then(() => {
      return this.renderCtrls()
    }), this.renderLoading())}
    ${until(this.asyncReady.then(() => {
      return this.renderPosts()
    }), this.renderLoading())}
  `
}
```




Get started
======================================

### install

```shell
npm i lit
```

The only transformation required to use Lit on modern browsers is to *convert bare module specifiers to browser-compatible URLs*.

### import

```js
// import from a CDN:
import {html, css, LitElement} from 'https://cdn.skypack.dev/lit-element';
import { LitElement, html } from 'https://unpkg.com/lit-element?module'; // deprecated
import { LitElement, html } from 'https://unpkg.com/lit?module' // new

// LitElement uses bare module specifiers to import modules from the lit-html library, like this:
import {html} from 'lit-html'
import { LitElement, html, render, css } from 'lit'

// import global module
import { LitElement, html, render, css } from '/home/frida/.nvm/versions/node/v16.17.1/lib/node_modules/lit/index.js'
```

Browsers currently only support loading modules from URLs or relative paths, not bare names referring to e.g. an npm package, so the build system needs to handle them: either by transforming the specifier to one that works for ES modules in the browser, or by producing a different type of module as output.

#### import directives

```js
import {classMap} from 'lit/directives/class-map.js';
```

# ShadowDOM

## no shadowDom

```js
/* createRenderRoot allows you to override the operation that creates a shadow root */
createRenderRoot() {
  return this
}
```

## get elements

Lit renders components to its `renderRoot`, which is a shadow root by default. To find internal elements, you can use DOM query APIs, such as `this.renderRoot.querySelector()`.

```js
/* You can query internal DOM after component initial render or use a getter pattern: */

firstUpdated() {
    this.staticNode = this.renderRoot.querySelector('#static-node')
} 

get _closeButton() {
    return this.renderRoot.querySelector('#close-button')
}

get _slottedElements() {  
    const slot = this.shadowRoot.querySelector('slot');  
    return slot.assignedElements({flatten: true});
}
```

## slotted elements

```js
get _slottedChildren() {  
    const slot = this.shadowRoot.querySelector('slot');  
    return slot.assignedElements({flatten: true});
}
```

You can also use the `slotchange` event to take action  when the assigned nodes change. 

```js
/* This example extracts the text content of all of the slotted children. */

handleSlotchange(e) {  
    const childNodes = e.target.assignedNodes({flatten: true});  
    // ... do something with childNodes ...  
    this.allText = childNodes.map((node) => {
        return node.textContent ? node.textContent : ''  
    }).join('')
} 

render() {
    return html`<slot @slotchange=${this.handleSlotchange}></slot>`
}
```

Lifecycle
===================================================

Lit introduces a set of render lifecycle callback methods *on top of the native Web Component callbacks*.  
These callbacks are triggered when declared Lit properties are changed.  
To use this feature, you must statically declare which properties will trigger the *render* lifecycle.

```js
static properties = {
    rating: { type: Number },
    vote: { type: String, reflect: true }
};
```

Custom element lifecycle
---------------------------------------

If you need to customize any of the standard custom element lifecycle methods, make sure to call the super implementation 
(such as `super.connectedCallback()`) so the standard Lit functionality is maintained.

### constructor()

```js
constructor() {
	super();
    // do one time initialization tasks that must be done before the first update
    // set default values for properties
  	this.foo = 'foo';
  	this.bar = 'bar';
}
```

### connectedCallback()

```js
connectedCallback() {
    super.connectedCallback() 
    // always call this because the LitElement class also does some work in the connectedCallback method
    addEventListener('keydown', this._handleKeydown)
    // setup tasks that should only occur when the element is connected to the document.  
		/* The most common of these is *adding event listeners to nodes 
    external to the element*, like a keydown event handler added to the window. */
}

```

Reactive update lifecycle
-----------------------------------------

The reactive update cycle is triggered when a reactive property changes or when the `requestUpdate()` method is explicitly called. 

### requestUpdate()

Call `requestUpdate()` to schedule an explicit update. This can be useful if you need the  element to update and render when something not related to a property  changes.

### shouldUpdate()

Called to determine whether an update cycle is required.

You can implement `shouldUpdate()` to specify which property changes should cause updates. Use the map of `changedProperties` to compare current and previous values.

```js
shouldUpdate(changedProperties) {  
  // Only update element if prop1 changed.  
  return changedProperties.has('prop1')
}
```

### willUpdate()

* called before *update()* and *render()* to compute values needed during the update.
* use it to compute property values that depend on other properties and are used in the rest of the update process.
* allows you to set values during the update cycle without triggering an additional update.
* LitElement batches property changes and makes rendering asynchronous. 
  Changes to reactive properties (like this.rating) in willUpdate() will not trigger unnecessary render lifecycle calls.

```js
willUpdate(changedProperties) {
// changedProperties: Map with keys that are the names of changed properties and values that are the corresponding previous values.
	if (changedProperties.has('firstName') || changedProperties.has('lastName')) {
		this.sha = computeSHA(`${this.firstName} ${this.lastName}`);
	}
}

render() {
	return html`SHA: ${this.sha}`;
}
```

### render()

* requires us to return a Lit template

```js
render() {
	const header = `<header>${this.header}</header>`;
	const content = `<section>${this.content}</section>`;
	return html`${header}${content}`;
}
```

If the changed property is not used in `render`, the DOM *will not be changed*. When Lit re-runs a `render`, although it may look like Lit is re-concatenating the strings and  setting the HTML, this is not how Lit actually works. On a re-render,  only the *values* (i.e. the result of the expressions in each `${...}` are compared to the previous values, and only when they differ is the DOM changed.

Lit is designed to use a very simple mechanism for triggering the update lifecycle: It creates accessors for each `@property`, and when a property is set to a different value, it triggers an update. The update lifecycle is responsible for running several callbacks,  including `update()` (which implements `reflect: true` attribute reflection) and passing the results from `render()` to `lit-html`, which will update any changed values (or no-op if they are all the same as previous).

We currently don't have a way to separate the part of the  update lifecycle that does attribute reflection from the part of the  update lifecycle that does render based on individual property options,  since that would introduce extra complexity for a level of optimization  is rarely necessary.

### firstUpdated()

Called after the component's DOM has been updated the first time, immediately before [`updated()`](https://lit.dev/docs/components/lifecycle/#updated) is called.

- perform one-time work after the component's DOM has been created

```js
firstUpdated() {
	this.renderRoot.getElementById('my-text-area').focus() // focus a particular rendered element 
}
```

#### updated()

Called whenever the component’s update finishes and the element's DOM has been updated and rendered.

- perform tasks that use element DOM after an update. 
- For example, code that performs animation may need to measure the element DOM.

# Templating

* *iterating arrays*: When you pass an array to lit-html, it will just iterate and render what's inside it
* *nested templates*

#### atomic updates

```js
const author = 'open-wc';
const homepage = 'https://open-wc.org/';
const footerTemplate = html`
  <footer>Made with love by <a href="${homepage}">${author}</a></footer>
`;
```

When embedding variables like this, lit-html remembers which parts of your template are static and which parts are dynamic. When re-rendering the same template you can change the value of these variables and lit-html will know to only update the parts that changed. This makes it very efficient!

## Expressions

### child expressions

An expression that occurs between the start and end tags of an element can add child nodes to the element.

```js
html`<p>Hello, ${name}</p>`
html`<main>${bodyText}</main>`

/* nest and compose templates */
const nav = html`<nav>...</nav>`;
const page = html`
	${nav}
  	<main>...</main>
`;

/* conditional templates */
html`
	${this.user.isloggedIn
    	? html`Welcome ${this.user.name}`
    	: html`Please log in`
  	}
`;

/* DOM nodes */
const div = document.createElement('div');
const page = html`
	${div}
  	<p>This is some text</p>
`;

/* for loos use array.map() as you need the array to be returned! */
return html`
	<fieldset>
		<legend ?hidden=${!this.legend}>${this.legend}</legend>
		${this._items.map(item => {
    		return html`
				<input type="radio" name="group" id=${item}>
				<label for=${item}>${item}</label>
			`
		})}
	</fieldset>		
`
```

### conditional expressions

```js
// if this.main is set call renderComponents()
// if not call renderPageNotFound()
return html`
			<main page=${ifDefined(this.location)}>
				${this.main 
					? renderComponents({blocks: this.main.blocks, imgDir: this.paths.img, lang: this.lang, log: true})
					: this.renderPageNotFound()
				}
			</main>
		`
}
```

```js
return html`
  <article class="post">
    <h3 class="meta">
      <span class="meta-title">${post.title}</span>
      ${this.parts.includes('date') 
        ? html`<span class="meta-date">${dateStr}</span>`
        : ''
      }
      <span class="meta-categories">${post.categories}</span>
    </h3>
  </article>
`
```



### attribute expressions

If the expression makes up the entire attribute value, you can leave  off the *quotes*. 

```js
// By default, an expression in the value of an attribute sets the attribute:
html`<div class=${this.textClass}>Stylish text.</div>`;

/* conditional attributes */
// using the ifDefined() directive:
html`<img src="/images/${ifDefined(this.imagePath)}/${ifDefined(this.imageFile)}">`; 


/* boolean attributes */
// The attribute is added if the expression evaluates to a truthy value, removed if it evaluates to a falsy value:
html`<div ?hidden=${!this.showAdditional}>This text may be hidden.</div>`; 
```

### property expressions

You can set a JavaScript property on an element using the `.` prefix and the property name:

```javascript
html`<input .value=${this.itemCount}>`
```

You can use this syntax to pass complex data down the tree to subcomponents. For example, if you have a `my-list` component with a `listItems` property, you could pass it an array of objects:

```js
html`<my-list .listItems=${this.items}></my-list>`
```

Note that the property name in this example—`listItems`—is mixed case. Although HTML *attributes* are case-insensitive, Lit preserves the case for property names when it processes the template.

### event listener expressions

```js
// using an arrow function:
html`<button @click=${() => { this.vote = 'up' }}></button>`
html`<input type="radio" @change=${evt => this.updateStatus(todo, evt.target.checked)></input>`
// using a method:
html`<button @click=${this.clickHandler}>Click Me!</button>`

/* In a Lit component, the event listener is automatically bound to the component, so you can use the this value inside the handler to refer to the component instance. */

clickHandler() {
	this.clickCount++; // refers to the component instance
}
```



Built-in directives
------------------------------

#### Styling

* `classMap` - sets a list of classes to an element based on an object
* `styleMap` - sets a list of style properties to an element based on an object

#### classMap

# Styles

```js
import {LitElement, html, css} from 'lit';

export class MyElement extends LitElement {
  static styles = css`
    p {
      color: green;
    }
  `;
  render() {
    return html`<p>I am green!</p>`;
  }
}
customElements.define('my-element', MyElement);
```

## Styling the component itself

style the component itself using special `:host` selectors. (The element that owns, or "hosts" a shadow tree is called the *host element*.)

To create default styles for the host element, use the `:host` CSS pseudo-class and `:host()` CSS pseudo-class function.

- `:host` selects the host element.
- `:host(selector)` selects the host element, but only if the host element matches *selector*.

Attributes & properties
=================================================

Lit manages your reactive properties and their corresponding attributes. In particular:

* *Reactive updates*. Lit generates a getter/setter pair for each reactive property. When a reactive property changes, the component schedules an update.
* *Attribute handling*. By default, Lit sets up an observed attribute corresponding to the property, and updates the property when the attribute changes. Property values can also, optionally, be reflected back to the attribute.

- To **observe** an attribute (set a property from an attribute), the attribute value must be converted from a string to  match the property type.
- To **reflect** an attribute (set an attribute from a property), the property value must be converted to a string.

### properties

```typescript
// native browser
export class MyTimer extends HTMLElement {
  get articles() {
      return this._articles
    }
    set articles(articles) {
      this._articles = articles
      this.render()
    }
}
	constructor() {
  	super()
    this.attachShadow({ mode: "open" })
  	this._articles = []
	}
}

// lit javascript
export class MyTimer extends LitElement {
  static properties = {
    duration: {},
    end: {state: true},
    remaining: {state: true},
  }
  //...
}

// lit typescript
export class MyTimer extends LitElement {
  @property() duration = 60
  @state() private end: number | null = null
  @state() private remaining = 0
  // ...
}
```

## options

### type

using the default converter

Indicates the type of the property. This is used only as a hint for the converter to determine how to convert the attribute to/from a property.
Lit has a default converter that handles *String*, *Number*, *Boolean*, *Array*, and *Object* property types.
To use the default converter, specify the type option in your property declaration:

```js
class MyElement extends LitElement {
    // Use the default converter
    static properties = {
      count: { type: Number },
    }
    
    constructor() {
    	super()
      this.count = 0
    }
}

// using an ARRAY
this.todos.push(todo) // doesn't trigger the reactive property lifecycle
/* Lit does not provide any interface to observe mutation of an array or an object. Observing mutation is quite complex task and is out of scope of lit-element. */
this.todos = [...this.todos, todo] // override the complete array instead of mutating it
```

### state

```js
class MyElement extends LitElement {
    static properties = {
        _active: {state: true}
    }

    constructor() {
        this._active = false
    }
}

/* state 
Set to true to declare the property as internal reactive state. Internal reactive state triggers updates like public reactive properties, but Lit doesn't generate an attribute for it, and users shouldn't access it from outside the component. */
```

### attribute

By default, Lit creates a corresponding observed attribute for all public reactive properties. An observed attribute can be used to provide an initial value for a property from markup. For example:

```html
<my-element myvalue="99"></my-element>
```

```js
class MyElement extends LitElement {
    static properties = {
        myAttribute: { attribute: true }, // default
        myData: { attribute: false }, // No observed attribute for this property
        // will not be initialized from attributes in markup
        // attribute changes won't affect it
        fuckOff: { attribute: 'yes-please' }, // Observed attribute will be called yes-please
    };

    constructor() {
        super();
        this.myData = {}
    }
}
```

### reflect: attribute reflection

Indicates if the property should reflect to an attribute. If true, when the property is set, the attribute is set using the attribute name determined according to the rules for the attribute property option and the value of the property converted using the rules from the converter property option.

```js
class MyElement extends LitElement {
    static properties = {
    	active: {type: Boolean, reflect: true},
    };

    static styles = css`
        :host {
          display: inline-block;
        }

        :host([active]) {
          border: 1px solid red;
        }`;

    constructor() {
        super();
        this.active = false;
    }

    render() {
        return html`
          <span>Active: ${this.active}</span>
          <button @click="${() =>
        (this.active = !this.active)}">Toggle active</button>
        `;
    }
}
customElements.define('my-element', MyElement);
```

## solutions

### toggleState

```js
class MyElement extends LitElement {
  static properties = {
    active: {type: Boolean, reflect: true},
  };

  static styles = css`
    :host {
      display: inline-block;
    }

    :host([active]) {
      border: 1px solid red;
    }`;

  constructor() {
    super();
    this.active = false;
  }

  render() {
    return html`
      <span>Active: ${this.active}</span>
      <button @click="${() =>
        (this.active = !this.active)}">Toggle active</button>
    `;
  }
}
```



### create array from attribute

```js
class MyElement extends LitElement {
    // Use the default converter
    static properties = {
        items: { type: String },
    }
	get _items() {
        // creates an array from the attribute which becomes an internal property
		return this.items.split(' ')
	}
}
```

### trigger change detection with arrays

```js
// Immutable data pattern.
// Treat objects and arrays as immutable. To remove an item from `myArray`, construct a new array:
this.myArray = this.myArray.filter((_, i) => i !== indexToRemove)

// Manually triggering an update. 
// Mutate the data and requestUpdate() to trigger an update directly
this.myArray.splice(indexToRemove, 1)
this.requestUpdate()
```

# Component composition

```js
render() {
   // building a large, complex component out of smaller, simpler components:
    return html`
    <top-bar>
    	<icon-button icon="menu" slot="nav-button"></icon-button>
      	<span slot="title">Fuzzy</span>
    </top-bar>
    `
}
```

## Passing data up and down

When exchanging data with subcomponents, the general rule is to follow the model of the DOM: **properties down, events up**.

## Passing data across the tree

Properties down and events up is a good rule to start with. But what if you need to exchange data between two components that don't have a direct descendant relationship? For example, two components that are siblings in the shadow tree?

### mediator pattern

In the mediator pattern, peer components don't communicate with each other directly. Instead, interactions are *mediated* by a third party.

# Guides

## Fetching async data

We could trigger our `fetch` in the constructor since it's run only once. But because it's a best practice to not perform any side effects there, it's better to use the `connectedCallback.` Because this method can be called multiple times during an element's lifecycle, we should be careful to trigger a `fetch` only when the data hasn't already been fetched before.

# Decorators

### property

This decorator should only be used for **public fields**. 
As public fields, properties should be considered as primarily settable by element users, either via attribute or the property itself.

Generally, properties that are changed by the element should be private or protected fields and should use the `state` decorator.

However, sometimes element code does need to set a public property. This should typically only be done in response to user interaction, and an  event should be fired informing the user; for example, a checkbox sets  its `checked` property when clicked and fires a `changed` event. Mutating public properties should typically not be done for  non-primitive (object or array) properties. In other cases when an  element needs to manage state, a private property decorated via the `state` decorator should be used. 

When needed, state properties can be initialized via public properties to facilitate complex interactions.

```typescript
import { property } from 'lit/decorators.js'
@property(options?)

// with decorators:
class MyElement extends LitElement {
  @property()
  mode: string // string type
  @property()
  name = 'anonymous' // literal string type

  @property({attribute: false})
  data = {} // initialize
}

// without decorators:
class MyElement extends LitElement {
  static properties = {
    name: {},
    mode: {type: String},
    data: {attribute: false},
  }
  constructor() {
    super()
    this.name = 'unonweb'
    this.data = {} // initialize
  }
}
```

### state

Declares a **private or protected reactive property** that still triggers updates to the element when it changes. 
It does not reflect from the corresponding attribute.

Properties declared this way must not be used from HTML or HTML  templating systems, they're solely for properties internal to the element. 
These properties may be renamed by optimization tools like closure compiler.

```typescript
import { state } from 'lit/decorators.js'

// with decorators:
class MyElement {
  @state()
  protected _active = false
}

// without decorators:
class MyElement {
  static properties = {
    _active: {state: true}
  }
	constructor() {
		this._active = false
}
```

### query

A property decorator that converts a class property into a getter that executes a querySelector on the element's renderRoot.

```js
import { query } from 'lit/decorators.js'
@query(selector, cache?)
// selector: A DOMString containing one or more selectors to match
// cache? An optional boolean which when true performs the DOM query only once and caches the result.
       
```

### queryAll

A property decorator that converts a class property into a getter that executes a querySelectorAll on the element's renderRoot.

```js
import { queryAll } from 'lit/decorators.js'
@queryAll(selector)

// ts
@query('input')
_input!: HTMLInputElement

// js
get _input() {
	return this.renderRoot?.querySelector('input') ?? null;
}
```

### queryAssignedElements

A property decorator that converts a class property into a getter that returns the `assignedElements` of the given `slot`. 
Provides a declarative way to use HTMLSlotElement.assignedElements.

```js
import { queryAssignedElements } from 'lit/decorators.js'
import { QueryAssignedElementsOptions } from 'lit/decorators.js'

@queryAssignedElements(options?)
// selector?: string
// CSS selector used to filter the elements returned. 
// For example, a selector of ".item" will only include elements with the item class.

// slot?: string
// Name of the slot to query. Leave empty for the default slot.
                       
```



### customElement

Class decorator factory that defines the decorated class as a custom element.

```typescript
import { customElement } from 'lit/decorators.js';

// with decorators:
@customElement('simple-greeting')
export class SimpleGreeting extends LitElement { /* ... */ }

// without decorators:
export class SimpleGreeting extends LitElement { /* ... */  }
customElements.define('simple-greeting', SimpleGreeting)
```

# Managing Data

## Tasks

```js
import { Task, TaskStatus } from '@lit/task';
// task.status
updated() {
		if (this._getDataTask.status === TaskStatus.COMPLETE) {
			this._litToc ??= this.querySelector('lit-toc')
			if (this._litToc) {
				this._litToc.requestUpdate()
			}
		}
	}
```

# Examples

```js
import { LitElement, html} from 'lit-element';
import { cTemplate } from './template/ctemplate';

@customElement('card-form')
export class cardFormComponent extends LitElement {

  render() {
    return cTemplate(this.validateForm, 'Validate');
  }

  validateForm() {
    alert('ok');
  }
}

/** template.ts **/
import {  html } from 'lit-element';

export const cTemplate = (onClick, label) => html`
  <div>
    <button class="button" @click="${onClick}">${label}</button>
  </div>
`;
```

```js
connectedCallback() {  
  super.connectedCallback();  
  this._timerInterval = setInterval(() => this.requestUpdate(), 1000);
} 

disconnectedCallback() { 
  super.disconnectedCallback();  
  clearInterval(this._timerInterval);
}
```

# Notes

How can I orchestrate event composition from outside (from a HTML centered approach)?

Example: Turn nested TOC within lit-posts into a hamburger menu

* **static** insert via light-dom
  * issue: without slots it's not possible to insert the element within the dynamically rendered parts
* **dynamic** rendering 
  * issue: how can I set the config for the element to be rendered?
    * solution: attributes
    * solution: json recipes
