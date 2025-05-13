# HTMLTemplateElement

When you have to reuse the same markup structures repeatedly on a web page, it makes sense to use some kind of a template rather than repeating the same structure over and over again. This was possible before, but it is made a lot easier by  `<template>`

* `<template>` and its contents are not rendered in the DOM, but it can be referenced using JS

```html
<!-- html template -->
<template id="my-first-template">
  <h1>Hello world!</h1>
</template>
```

```js
// refer to template
const template = document.getElementById("my-paragraph")
document.body.appendChild(template.content) // template.content returns the template contents (a DocumentFragment)
```

This template does nothing except for describing the structure of our HTML fragment. Nothing ist shown - until we clone the content of our template and add the content via Javascript:


```js
// style 1:
const clone = template.content.cloneNode(true)
this.append(clone)
this.shadowRoot.append(clone) // with shadow dom
```

```js
// style 3:
this.append(template.content.cloneNode(true))
this.shadowRoot.append(template.content.cloneNode(true)) // with shadow dom
```

```js
const template = document.getElementById("todo-input");
const clone = template.content.cloneNode(true);
document.querySelector('.container').appendChild(clone);
template.innerHTML = "";
```

## create template

```js
export default class UnMenuX extends HTMLElement {

	cssPath = '/comp/un-menu-x.css'

	constructor() {
		super()

		this.attachShadow({ mode: 'open' });
		this.template = document.createElement('template')
		this.template.innerHTML = /* html */`
			<link href=${this.cssPath} rel="stylesheet">
			<div id="trigger" class="trigger active-element">
				<div class="bar-one"></div>
				<div class="bar-two"></div>
				<div class="bar-three"></div>
			</div>
			<div class="drop-down">
				<slot name="drop-down"></slot>
			</div>
		`

		this.shadowRoot.appendChild(this.template.content.cloneNode(true))
		
		let menuToggle = this.shadowRoot.querySelector('#menu__toggle');
		menuToggle.addEventListener('click', (evt) => {
			menuToggle.classList.toggle('active')
		})
	}

	connectedCallback() {
		// code
	}
}

customElements.define('un-menu-x', UnMenuX)
```

### Why templates?

The main advantage of templates is that **resources within the template like images are ignored until their content is added to the DOM**.
The content is not parsed or rendered by the browser while loading the page. The parser does only ensure that the contents are valid.