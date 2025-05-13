# Notes

* https://webcomponents.dev/blog/all-the-ways-to-make-a-web-component/board/

### Why use custom elements?

* **automatic callback invocation**

### attaching event listeners

**clone-with-event-listeners-issue**: If I clone a native button element with event listeners attached to it, like here:

```js
dropDown.append(this.content.cloneNode(true)) // this.content contains a button
```

I will loose these event listeners. 

But if I clone `un-button` I will benefit from the lifecycle callbacks when the element is attached to the DOM.

**connectedCallback()**

That means we can create and modify a custom element and then only once in the end connectedCallback() is fired

**constructor()**

Constructor is called every time a custom element is created (imperatively or declaratively)



# Shadow DOM

Shadow DOM is all about encapsulation.

### advantages

- **Isolated DOM tree:** The shadow DOM is self-contained and the outside cannot query elements on the inside 
  (`document.querySelector` wont return nodes from within the shadow tree)
- **Scoped CSS:** Styles defined within the shadow DOM will not leak out, and outside styles will not bleed in.
- **Composition:** Design a declarative, markup-based API for your component. Through the use of `<slot />` our elements can take outside nodes from the light DOM and place them in specific positions inside the shadow DOM.

### Element.attachShadow()

```js
Element.attachShadow(init) // attaches a shadow DOM tree to the specified element and returns a reference to its ShadowRoot.
Element.attachShadow({mode: 'open'}) // specifies the encapsulation mode for the shadow DOM tree.
// open: Elements of the shadow root are accessible from JavaScript outside the root
// closed: Denies access to the node(s) of a closed shadow root from JavaScript outside it
Element.attachShadow({mode: 'open', delegatesFocus: 'false'}) //

/* delegatesFocus: A boolean that, when set to true, specifies behavior that mitigates custom element issues around focusability. When a non-focusable part of the shadow DOM is clicked, the first focusable part is given focus, and the shadow host is given any available :focus styling. */
```



## HTMLSlotElement

The children aren't *moved* in the DOM tree, but they're rendered *as if* they were children of the `<slot>`

```html
<!-- default content -->
<slot name="my-text">My default text</slot>
If the slot's content isn't defined when the element is included in the markup, or if the browser doesn't support slots, the element just contains the fallback content "My default text". -->

<!-- unnamed slots -->
<slot></slot>
Note: An unnamed <slot> will be filled with all of the custom element's top-level child nodes that do not have the slot attribute. This includes text nodes.

```



css
---------------------------

Shadow DOM can include styles, such as `<style>` or `<link rel="stylesheet">`.

**Local styles** can affect:

- shadow tree,
- shadow host with `:host` and `:host()` pseudoclasses,
- slotted elements (coming from light DOM), `::slotted(selector)` allows to select  slotted elements themselves, but not their children.

**Document styles** can affect:

- shadow host (as it lives in the outer document)
- slotted elements and their contents (as that’s also in the outer document)

```css
:host {
/* In Shadow DOM this selector refers to the node or custom element that the Shadow Root is attached to. */
}

:host([vote=up]) .thumb_up {
	fill: green;
}
:host([vote=down]) .thumb_down {
	fill: red;
}

custom-element::part(foo) {
/* Styles to apply to the `foo` part */
/* ::part pseudo-element represents any element within a shadow tree that has a matching part attribute */
}
```


Best practices
-------------------------------------------

* **Place any children the element creates into its shadow root**
	Children created by your element are part of its implementation and should be private. Without the protection of a shadow root, outside JavaScript may inadvertently interfere with these children. 
* **Use `<slot>` to project light DOM children into your shadow DOM**
	Allow users of your component to specify content in your component as HTML children makes your component more composable. When a browser does not support custom elements, the nested content remains available, visible and accessible.
* **Set `:host {display: block, inline-block, flex}` unless you prefer the default of inline**
	Custom elements are display: inline by default, so setting their width or height will have no effect. This often comes as a surprise to developers and may cause issues related to laying out the page. Unless you prefer an inline display, you should always set a default display value.
* **Add a :host display style that respects the hidden attribute.**
	A custom element with a default display style, e.g. `:host { display: block }`, will override the lower specificity built-in `hidden` attribute. This may surprise you if you expect setting the hidden attribute on your element to render it display: none. In addition to a default display style, add support for hidden with `:host([hidden]) { display: none }`. 

### attributes & properties

* **Aim to keep primitive data attributes and properties in sync, reflecting from property to attribute, and vice versa.**
	You never know how a user will interact with your element. They might set a property in JavaScript, and then expect to read that value using an API like `getAttribute()`. If every attribute has a corresponding property, and both of them reflect, it will make it easier for users to work with your element. In other words, calling `setAttribute('foo', value)` should also set a corresponding foo property and vice versa. There are, of course, exceptions to this rule. You shouldn't reflect high frequency properties, e.g. currentTime in a video player. Use your best judgment. If it seems like a user will interact with a property or attribute, and it's not burdensome to reflect it, then do so. 

# SSR

Server Side Rendering

With Web Components there are several limitations when it comes to SSR:

- it's not possible to run the same code on the server since **Web Components rely on browser specific DOM APIs** that are not available unless you fire up a headless browser or an alternative DOM implementation. Both of these solutions bring a non-trivial overhead that is hard to justify.

# Component Composition

### Thinking In Web Components

https://levelup.gitconnected.com/getting-started-with-web-components-lit-part-1-911aa7058e9b

Writing an app with web components requires a different kind of thinking because all of the updated data must come *top-down* and it must flow through each component and its children without ever  “reaching up” into data from anywhere else. When a component needs to  tell another component about something (one that’s not its direct  child), the first component must send a message asynchronously to the  second component through the JS event system.

The components must not communicate in any other way other than by these  messages. When a message is received, a component can redraw itself.  When a parent component redraws, so do its children. This design  constraint is the result of trying to write and debug apps which *don’t* work this way. It prevents many errors that are common in other design  patterns. When architected correctly, web component apps are **magical** and beautiful because they appear to automatically update themselves on-screen with all the correct data.

### module loader technique

To prevent “import spaghetti,” use a module loader technique. 

* The loader exports all the modules you’ve written: `export * from '/path/...'`
* Then, then only the module loader itself needs to be loaded or imported (as a script in *index.html*) 
* Now it makes your full component library available on that page

### SPA & MVC

https://levelup.gitconnected.com/getting-started-with-web-components-lit-part-1-911aa7058e9b

Single page applications replace the idea of individual pages of code and UI with a single page, usually `index.html`. 

`index.html` acts as the **controller** in the MVC design pattern. Whatever **views** that page loads (inside its HTML `<body>`) will appear to the user to be the one single page they are viewing. Whatever data is available to any modules that `index` loads is the **model** part of MVC. If `index` can get to the data, it’s part of our model.

### Organizing Your App

https://levelup.gitconnected.com/getting-started-with-web-components-lit-part-1-911aa7058e9b

* **components**: Anything in the view must live in the components folder. Other Typescript functions that aren’t dependent on a particular view become their own modules and live in that folder.
* **global**: Stuff that *touches everything* (like startup stuff, loaders, fonts and styles) 
* **modules**