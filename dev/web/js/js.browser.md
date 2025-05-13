
CLIENT SIDE STORAGE
===========================

In fact, there are only three ways of storing data in the browser:

* *LocalStorage*
* *WebSQL*
* *IndexedDB*

### indexedDB vs localStorage

*localStorage* just stores strings, so to put an object in local storage the usual approach is to JSON.stringify it:

```js
myObject = {a: 1, b: 2, c: 3};
localStorage.setItem("uniq", JSON.stringify(myObject));
/* This is fine for finding the object with key uniq, but the only way to get the properties of myObject back 
out of local storage is to JSON.parse the object and examine it: */
var myStorageObject = JSON.parse(localStorage.getItem("uniq"));
window.alert(myStorageObject.b);
```

This is fine if you only have one, or a few objects, in local storage. But imagine you have a thousand objects, all of which have a property b, and you want to do something just with those ones where b==2. With local storage you'll have to loop through the entire store and check b on each item, which is a lot of wasted processing.

With *IndexedDB* you can store stuff other than strings in the value: "This includes simple types such as DOMString and Date as well as Object and Array instances." Not only that, but you can create indexes on properties of the objects that you stored in the value. So with IndexedDb you can put those same thousand objects in it but create an index on the b property and use that to just retrieve the objects where b==2 without the overhead of scanning every object in the store.


Web Storage API
------------------------------------------------------------------------------------

* Browsers that support web storage have the global objects `sessionStorage` and `localStorage` declared at the window level.  
* store key/value pairs
* much more intuitive than cookies
* *size*: Cookies are restricted to 4 kilobytes. Web storage provides far greater storage capacity  
  (Firefox 10MB)

#### how data is stored

* there is a *separate data store for each domain*
* opening via `file:///...` means that all sites opened that way *share the same storage*
* **Firefox**: */home/frida/.mozilla/firefox/ymame4bs.default-release/webappsstore.sqlite*
* **Chrome**: 
  * Windows: *\AppData\Local\Google\Chrome\User Data\Default\Local Storage*  
  * macOS: *~/Library/Application Support/Google/Chrome/Default/Local Storage*


#### use cases

* Caching static file-based resources with the *Cache API* so that those static pages are navigable without an internet connection
* Store *user inputs*  in the browser when there is poor or no internet connectivity and then sync to a remote database as soon as connection is detected.
  * Personalizing site preferences
  * Persisting previous site activity
    * store the contents of a shopping cart from a previous session
  * recent searches
  * form contents
* Store *response data* for faster initial load and then update later if there are any changes when connection is restored, following a stale-while-revalidate policy
* Store *data* that rarely changes on the server-side so the app doesn’t have to make server calls for such data all the time

#### limitations

* Data type: only *string*  
  Attempting to store a different data type will result in an automatic conversion into a string in most browsers. 
* Conversion into JSON, however, allows for effective storage of JavaScript objects:
  * `JSON.stringify()` # when storing 
  * `JSON.parse()` # when converting back
* limited storage size (5 MB)
* synchronous 
* not accessible from web workers
* It's tied to the computer/browser: LocalStorage lives on the user’s computer. In fact, it lives inside the user’s browser. If your user switches from Firefox to Chrome, their data is still inside Firefox. If you change your domain name, your app won’t be able to get to the data saved on the old domain. If your user goes to a different computer, the app will not have their data.

All of your web storage data is contained within two object-like structures inside the browser: *sessionStorage* and *localStorage*. The first one persists data for as long as the browser is open (the data is lost when the browser is closed) and the second one persists data even after the browser is closed and then opened again.

#### usage

```js
// init
myStorage = window.localStorage // not necessary of course

// Store value on browser for duration of the session
sessionStorage.setItem('key', 'value')
sessionStorage.getItem('key')

// Store value on the browser beyond the duration of the session
localStorage.setItem('key', 'value')
localStorage.getItem('key')
localStorage.removeItem('key')

// iteration
let keys = Object.keys(localStorage) 
// only returns the keys that belong to the object, ignoring the prototype.
for (let key of keys) {
	console.log(`${key}: ${localStorage.getItem(key)}`)
}

// retrieve value
localStorage[key]
localStorage[4] // '{"id":4,"text":"Werde von maren von hinten umarmt","status":false}'

// JSON

JSON.stringify() // when storing 
JSON.parse() //when converting back

function saveLocally(todo) {
    this.todoListLocal.setItem(todo.id, JSON.stringify(todo))
    // key = id
    // value = todo object
}

```


### StorageEvent

Window.storage

* fires when a storage area (localStorage) has been modified *in the context of another document*
* the event triggers on all window objects where the storage is accessible, *except the one that caused it*.
* a way for other pages on the domain using the storage to sync any changes that are made
* allows different windows from the same origin to exchange messages.

Imagine, you have two windows with the same site in each. So localStorage is shared between them.  
You might want to open this page in two browser windows to test the code below.  
If both windows are listening for window.onstorage, then each one will react on updates that happened in the other one.  

#### attributes

* `key` # Represents the key changed. Read only.
  * is null when the change is caused by the storage clear() method. 
* `newValue` # The new value of the key. Read only.
  * is null when the change has been invoked by storage clear() method or the key has been removed from the storage. 
* `oldValue` # The original value of the key. Read only.
  * is null when the key has been newly added and therefore doesn't have any previous value. 
* `storageArea` # Represents the Storage object that was affected. Read only.
* `url` # The URL of the document whose key changed. Read only.


IndexedDB API
-------------------------------------------------




Cache API
---------------------------------------------------

Designed for storing HTTP responses to specific requests, and is very useful for doing things like storing website assets offline so the site can subsequently be used without a network connection. Cache is usually used in combination with the Service Worker API, although it doesn't have to be

CSS
=========================

```js
// 1. INLINE STYLES
document.getElementById('target').style.color = 'tomato';
```

```js
// 2. GLOBAL STYLES
var style = document.createElement('style');
style.innerHTML = `
#target {
color: blueviolet;
}
`;
document.head.appendChild(style);
```

```js
// 3. CSSOM
var style = document.createElement('style');
document.head.appendChild(style);
style.sheet.insertRule('#target {color: darkseagreen}');
```

```js
// Create our shared stylesheet:
const sheet = new CSSStyleSheet();
sheet.replaceSync('a { color: red; }');

// Apply the stylesheet to a document:
document.adoptedStyleSheets.push(sheet);

// Apply the stylesheet to a Shadow Root:
const node = document.createElement('div');
const shadow = node.attachShadow({ mode: 'open' });
shadow.adoptedStyleSheets.push(sheet);
```



# customElements

## CustomElementRegistry

* provides methods for registering custom elements and querying registered elements. 
* To get an instance of it, use the `window.customElements` property.

```js
CustomElementRegistry.define() 
// Defines a new custom element
CustomElementRegistry.get() 
// Returns the constructor for the named custom element, or undefined if the custom element is not defined.
CustomElementRegistry.upgrade() 
// Upgrades a custom element directly, even before it is connected to its shadow root.
CustomElementRegistry.whenDefined()
// Returns an empty Promise that resolves when a custom element becomes defined with the given name. If such a custom element is already defined, the returned promise is immediately fulfilled.
```

DOM
==================

## Solutions

### siblings

```js
function getSiblings(elem) {
    // create an empty array
    let siblings = [];

    // if no parent, return empty list
    if (!elem.parentNode) {
        return siblings;
    }

    // first child of the parent node
    let sibling = elem.parentNode.firstElementChild;

    // loop through next siblings until `null`
    do {
        // push sibling to array
        if (sibling != elem) {
            siblings.push(sibling);
        }
    } while (sibling = sibling.nextElementSibling);
		
    return siblings;
};
```



### classes

#### toggle

```js
function toggleActive({srcBtn, btnTextActive, btnTextNotActive, destSel}) {
		
	let destElements = this.renderRoot.querySelectorAll(destSel)
	
	if (srcBtn.classList.contains('active')) {
		destElements.forEach(el => el.classList.remove('active'))
		srcBtn.classList.remove('active')
		srcBtn.textContent = btnTextNotActive
	} else {
		destElements.forEach(el => el.classList.add('active'))
		srcBtn.classList.add('active')
		srcBtn.textContent = btnTextActive
	}
}

function toggleClass(srcBtn, cls, destSel, selMode) {
	// selecting the next element with 'sel' starting from evt.target ensures that no other instances of this comp are affected
	let destElements = []

	if (selMode === 'all') destElements = this.renderRoot.querySelectorAll(destSel)
	if (selMode === 'first') destElements.push(this.renderRoot.querySelector(destSel))
	if (selMode === 'next') destElements.push(this.getNextSibWithSel(srcBtn, destSel))

	destElements.forEach(el => el.classList.toggle(cls))
}

function toggleClassHide(destSel) {
	let destElements = this.renderRoot.querySelectorAll(destSel)
	destElements.forEach(el => el.classList.toggle('hide'))
}
```

#### add

```js
function addClassToSel(cls, destSel) {
	let destElements = this.renderRoot.querySelectorAll(destSel)
	destElements.forEach(el => el.classList.add(cls))
}

function addClass(srcBtn, cls, destSel, selMode) {
	let destElements = []

	if (selMode === 'all') destElements = this.renderRoot.querySelectorAll(destSel)
	if (selMode === 'first') destElements.push(this.renderRoot.querySelector(destSel))
	if (selMode === 'next') destElements.push(this.getNextSibWithSel(srcBtn, destSel))

	destElements.forEach(el => el.classList.add(cls))
}
```

#### remove

```js
function rmClassFromSel(cls, destSel) {
	let destElements = this.renderRoot.querySelectorAll(destSel)
	destElements.forEach(el => el.classList.remove(cls))
}

function rmClass(srcBtn, cls, destSel, selMode) {
	let destElements = []

	if (selMode === 'all') destElements = this.renderRoot.querySelectorAll(destSel)
	if (selMode === 'first') destElements.push(this.renderRoot.querySelector(destSel))
	if (selMode === 'next') destElements.push(this.getNextSibWithSel(srcBtn, destSel))

	destElements.forEach(el => el.classList.remove(cls))
}
```

## structure

### solutions

#### avoid reflows

reflow is when the browser needs to process and draw the webpage, and it is one of the most expensive browser processes

Avoid reflows by creating a node that has not yet been added to the DOM. Then we will append our divs to this node. And finally, we will append this single node to the DOM which should trigger a single reflow.

```js
var c = document.createDocumentFragment()
for (var i=0; i<10000; i++) {
    var e = document.createElement("div")
    e.className = "test-div"
    c.appendChild(e)
}

document.body.appendChild(c)
```

#### wrap

```js
const button = document.createElement('button')
button.append(...this.childNodes);
```

#### remove children

```js
el.innerHTML = ""
```

* removes all the text and nodes inside the parent node
* may not be a suitable choice for high-performance applications because it invokes the browser's HTML parser to parse the new string and update the DOM

```js
el.textContent = ""
```

* performs better than innerHTML as the browser doesn't have to invoke the HTML parser and can immediately replace all child nodes of the element with a single text node.

```js
el.replaceChildren()
```

### insertBefore()

```js
Node.insertBefore(newNode, referenceNode) //  inserts a node before a reference node as a child of a specified parent node. 
// referenceNode # The node before which newNode is inserted. If this is null, then newNode is inserted at the end of parentNode's child nodes.
// If the given node already exists in the document, insertBefore() moves it from its current position to the new position. (That is, it will automatically be removed from its existing parent before appending it to the specified new parent.)
document.body.insertBefore(newNode, referenceNode);
```

```js
function insertAfter(newNode, existingNode) {
    Node.parentNode.insertBefore(newNode, existingNode.nextSibling);
}
```

### replaceChild()

```js
Node.replaceChild(newChild, oldChild); // newer API: ChildNode.replaceWith()
```

### appendChild()

```js
Node.appendChild("tag") // newer API: ParentNode.append()
```

### cloneNode()

```js
Node.cloneNode() // returns a duplicate of the node on which this method was called
Node.cloneNode(false) // only the node will be cloned. The subtree, including any text that the node contains, is not cloned.
Node.cloneNode(true) // the node and its whole subtree, including text that may be in child Text nodes, is also copied.
// Note that this has no effect on empty elements, such as the <img> and <input> elements.
```

### remove()

```js
Element.remove() // removes the element from the DOM
```

### replaceWith()

```js
Element.replaceWith(element) // Replaces this ChildNode in the children list of its parent with a set of Node or DOMString objects. DOMString objects are inserted as equivalent Text nodes.
```

### before()

```js
Element.before(element) // Inserts a set of Node or DOMString objects in the children list of this ChildNode's parent, just before this ChildNode. DOMString objects are inserted as equivalent Text nodes.
```

### after()

```js
Element.after(element) //Inserts a set of Node or DOMString objects in the children list of this ChildNode's parent, just after this ChildNode. DOMString objects are inserted as equivalent Text nodes.
```

### replaceWith()

 replaces this Element in the children list of its parent with a set of Node or string objects. String objects are inserted as equivalent Text nodes. 

### insertAdjacentHTML()

```js
Element.insertAdjacentHTML(position, html)

Element.insertAdjacentHTML('beforebegin', html) 
// Before the element. Only valid if the element is in the DOM tree and has a parent element.

Element.insertAdjacentHTML('afterend', html) 
// After the element. Only valid if the element is in the DOM tree and has a parent element.

Element.insertAdjacentHTML('afterbegin', html) 
// Just inside the element, before its first child.

Element.insertAdjacentHTML('beforeend', html) 
// Just inside the element, after its last child.
```

### append()

* allows you to also **append strings** (but doesn't render to html), whereas `Node.appendChild()` only accepts Node objects.
* has no return value, whereas `Node.appendChild()` returns the appended Node object
* can append several nodes and strings, whereas `Node.appendChild()` can only append one node. 

```js
ParentNode.append() // Inserts a set of Node objects or DOMString objects after the last child of the ParentNode. DOMString objects are inserted as equivalent Text nodes.
	// newer API (compared to *appendChild()*)
	// supports multiple arguments and appending strings.
```

```js
// Appending an element
let div = document.createElement("div");
let p = document.createElement("p");
div.append(p);

// Appending text
div.append("Some text");
console.log(div.textContent); // "Some text"
```

```js
// Append multiple elements
el.append(document.createElement('div'), 'Hello', document.createElement('span'))

// Append multiple elements
let imgs = $0.querySelectorAll('img')
let modal = $0.querySelector('un-img-lightbox')
modal.append(...imgs) // this will move the imgs from their original location
```

```js
// chaining with createElement only possible with appendChild
$0.appendChild(document.createElement('ul')).appendChild(document.createElement('li'))
```

### prepend()

```js
ParentNode.prepend() // Inserts a set of Node objects or DOMString objects before the first child of the ParentNode. DOMString objects are inserted as equivalent Text nodes.
```

### replaceChildren()

* replaces the existing children of a node with a specified new set of children
* provides a very convenient mechanism for emptying a node of all its children (call it on the parent node without any argument specified)
* enables you to easily transfer nodes between elements, without having to resort to verbose looping code

```js
ParentNode.replaceChildren(...elementList) // 

// emptying a node
myNode.replaceChildren();
```

## content

### innerHTML

* Reading `innerHTML` causes the browser to serialize the HTML or XML fragment comprised of the element's descendants. 
* If `innerHTML` inserts a `<script>` tag into the document – it becomes a part of HTML, but doesn’t execute.

```js
Element.innerHTML // a *DOMString* representing the markup of the element's content.
document.body.innerHTML = "" // erases the entire contents of a document
Element.innerHTML += "<button>Click</button>" // append HTML
```

In other words, innerHTML+= does this:

* The old contents is removed.
* The new innerHTML is written instead (a concatenation of the old and the new one).

As the content is removed and rewritten from the scratch, all images and other resources will be reloaded.

### outerHTML

* contains the full HTML of the element. That’s like innerHTML plus the element itself
* unlike innerHTML, writing to outerHTML does not change the element. Instead, it replaces it in the DOM.

### innerText

```js
HTMLElement.innerText 
// Represents the rendered text content of an element.  
// Only shows “human-readable” elements.
// is aware of styling and won’t return the text of “hidden” elements. 
// Moreover, since innerText takes CSS styles into account, reading the value of innerText triggers a reflow to ensure up-to-date computed styles. (Reflows can be computationally expensive, and thus should be avoided when possible.)
```

### textContent

* provides access to the text inside the element: only text, without `<tags>`
* the fastest way to change text
* its value is not parsed as HTML
* allows to write text the *safe way* (all symbols are treated literally)
* Setting textContent on a node removes all of the node's children and replaces them with a single text node with the given string value.

```js
el.textContent // gets the content of all elements, including `<script>` and `<style>` elements.   
el.textContent // returns every element in the node.  
```

## get

### performance

`getElementById()` and getElementsByClassName() are more than twice as fast as querySelector() and querySelectorAll().

### return single element

#### getElementById()

```js
document.getElementById() // 
```

#### properties

```js
// Return the first / last node which is a child of this ParentNode and is an Element
// or null if there is none. Read only.
ParentNode.firstElementChild
ParentNode.lastElementChild 

ParentNode.childElementCount;
```

```js
Node.firstChild // Returns a Node representing the first direct child node of the node, or null if the node has no child.
Node.lastChild  // Returns a Node representing the last direct child node of the node, or null if the node has no child

Node.nextSibling // includes whitespaces, linebreaks and comments.~~
Node.previousSibling // includes whitespaces, linebreaks and comments.~~
Element.nextElementSibling // better!

Node.parentNode // Returns a Node that is the parent of this node.
Node.parentElement // Returns an Element that is the parent of this node. If the node has no parent, or *if that parent is not an Element*, this property returns null.

Element.previousElementSibling // Returns the Element immediately prior to this node in its parent's children list, or null if there is no Element in the list prior to this node. Read only.
Element.nextElementSibling // Returns the Element immediately following this node in its parent's children list, or null if there is no Element in the list following this node. Read only.
```

#### closest()

```js
Element.closest(css) // Looks for the nearest ancestor that matches the CSS-selector. The elem itself is also included in the search. The ancestors together form the chain of parents from the element to the top. In other words, it goes up from the element and *checks each of its parents*. If it matches the selector, then the search stops, and the ancestor is returned.
```

```js
// get closest parent that matches a condition:
function closestParent(elem) {
	for (; elem && elem !== document; elem = elem.parentNode) {
		// condition 1
		if (elem.classList.value.includes(classCont)) return elem;
		// condition 2
		if (elem.matches(selector)) return elem;
	}
	return null
}
// alternative: 
Element.closest(css);
```

### return HTMLCollection

* A HTMLCollection is live - it is automatically updated when the underlying document is changed
* For this reason it is a good idea to make a copy (`Array.from`) to iterate over if adding, moving, or removing nodes.

```js
document.getElementsByTagName('tag')
document.getElementsByClassName() 
document.getElementsByTagName('*')
```

```js
ParentNode.children 
```

```js
getElementsByClassName('class1 class2')
document.getElementById('main').getElementsByClassName('test')
document.getElementsByClassName('test')[0]
```

### return NodeList

#### NodeList

```js
NodeList.item()
// Returns an item in the list by its index, or null if the index is out-of-bounds.
// An alternative to accessing nodeList[i] (which instead returns undefined when i is out-of-bounds). This is mostly useful for non-JavaScript DOM implementations.

NodeList.entries()
// Returns an iterator, allowing code to go through all key/value pairs contained in the collection. (In this case, the keys are integers starting from 0 and the values are nodes.)

NodeList.forEach()
// Executes a provided function once per NodeList element, passing the element as an argument to the function.

NodeList.keys()
// Returns an iterator, allowing code to go through all the keys of the key/value pairs contained in the collection. (In this case, the keys are integers starting from 0.)

NodeList.values()
// Returns an iterator allowing code to go through all values (nodes) of the key/value pairs contained in the collection.
```

```js
// classic for loop:
for (let i = 0; i < myNodeList.length; i++) {
  let item = myNodeList[i];
}
```

```js
// for of loop:
const list = document.querySelectorAll("input[type=checkbox]");
for (const item of myNodeList) {
  //
}
```

#### live

```js
document.getElementsByName() // 
```

```js
Node.childNodes 
// Returns a *live NodeList* 
// Containes all the children of this node (including elements, text and comments)
```

#### static

##### querySelector()

```js
ParentNode.querySelector() // Returns the first Element with the current element as root that matches the specified group of selectors.
ParentNode.querySelectorAll() // Returns a static NodeList representing a list of elements with the current element as root that matches the specified group of selectors.

// Pseudo-classes like `:hover` and `:active` are also supported:
document.querySelectorAll(':hover') // returns the collection with elements that the pointer is over now (in nesting order: from the outermost to the most nested one)

// select all h1 and the body element
document.querySelectorAll('h1, body')
// select all paragraph (p) elements in a document whose CSS class is either *warning* or *note*
let special = document.querySelectorAll( "p.warning, p.note" )
// selects the first element whose ID is one of *main*, *basic*, or *exclamation*
let el = document.querySelector( "#main, #basic, #exclamation" )

// get the first <style> element which either has no type or has type "text/css" in the HTML document body is returned:
const el = document.body.querySelector("style[type='text/css'], style:not([type])")
```

## test

### return boolean

#### contains()

```js
Node.contains(otherNode) // returns a Boolean value indicating whether a node is a descendant of a given node, i.e. the node itself, one of its direct children (childNodes), one of the children's direct children, and so on.
```

#### matches()

```js
Element.matches(css) // Checks if elem matches the given CSS-selector. It returns true or false

// example
for (let elem of document.body.children) {
    if (elem.matches('a[href$="zip"]')) {
        alert("The archive reference: " + elem.href );
    }
}
```

## text

### innerText vs textContent

`innerText` 

* is much more performance-heavy: it requires layout information to return the result
* since it takes CSS styles into account, reading its value triggers a reflow to ensure up-to-date computed styles
* only shows human-readable elements
* is aware of styling and won't return the text of hidden elements
* is defined only for HTMLElement objects, while textContent is defined for all Node objects

`textContent`

* returns the text content of the node and its descendants.
* returns the content of all elements, including `<script>` and `<style>` elements
* returns a clean version of all of the actual text, stripped of intervening tags
* has better performance than `innerText` and `innerHTML`
* can prevent XSS attacks.

```html
<div id="ex">
  <span>Hello <span style="display: none;">World</span></span>
</div>
<script>
  const el = document.getElementById('ex')
	el.innerText // Hello
	el.textContent // Hello World
</script>
```

## attributes

```js
Element.attributes // returns a live collection of all attribute nodes registered to the specified node  
// includes all the attributes of the element (standard and non-standard) as objects with name and value properties
for (let attr of elem.attributes) { 
    alert( `${attr.name} = ${attr.value}` );
}
```

```js
if (attributes instanceof NamedNodeMap) {
  for (let att of attributes) {
    attributes[att.name] = att.value
  }	
}
```

### toggleAttribute()

```js
HTMLElement.toggleAttribute(name, force); // toggles a Boolean attribute (removing it if it is present and adding it if it is not present) on the given element.
// name 	# A DOMString specifying the name of the attribute to be toggled. The attribute name is automatically converted to all lower-case when toggleAttribute() is called on an HTML element in an HTML document. 
// force 	# Optional. A boolean value to determine whether the attribute should be added or removed, no matter whether the attribute is present or not at the moment. 
```

### setAttribute()

```js
HTMLElement.setAttribute(name, value);
// name    # the attribute whose value is to be set
// value   # the value to assign to the attribute
this.button.setAttribute('aria-expanded', this.button.getAttribute('aria-expanded') === 'true' ? 'false' : 'true')
```

### removeAttribute()

removes the specified attribute from an element

### dataset

provides read/write access to custom data attributes (`data-*`) on elements. It exposes a map of strings (DOMStringMap) with an entry for each data-* attribute.

name conversion:
`data-date-of-birth` # HTML
`el.dataset.dateOfBirth` # JS

```html
<!-- define data in HTML -->
<button id="myBtn" data-action="search">Search</button>
<script>
	// access data in JS
	let btn = document.getElementById('myBtn')
	btn.dataset.action // --> search

	// define data in JS
	el.dataset.keyname // set and read the data attribute
	el.dataset.example = null // is converted into data-example="null"
  
  // get element with specific dataset
  document.querySelector('[data-action="search"]')
  const el1 = document.querySelector('[data-id="box1"]') // get first element with data-id = `box1`
  const elements = document.querySelectorAll('[data-id="box1"]') // get all elements with data-id = `box1`
  const el2 = document.querySelector('[data-id]') // get first element that has data-id attribute set
</script>
```

```js
// exists
if ('collapsible' in this.dataset) {
  // ...
}

// delete
if ('collapsed' in this.dataset) {
	delete this.dataset.collapsed
	this.dataset.expanded = ''
}

// toggle
el.dataset.active = (el.dataset.active === "true") ? "false" : "true";
```

### attributes vs properties

**Attributes are defined by HTML. Properties are defined by the DOM (Document Object Model).**

- A few HTML attributes have 1:1 mapping to properties. `id` is one example.
- Some HTML attributes don't have corresponding properties. `colspan` is one example.
- Some DOM properties don't have corresponding attributes. `textContent` is one example.
- Many HTML attributes appear to map to properties ... but not in the way you might think!

**Attributes *initialize* DOM properties and then they are done. Property values can change; attribute values can't.**

#### reflection

When a property reflects an attribute, the *attribute* is the source of the data. When you set the property, it's updating the attribute. When you read from the property, it's reading the attribute.

#### ex input

For example, when the browser renders `<input type="text" value="Bob">`, it creates a corresponding DOM node with a `value` property initialized to "Bob".

When the user enters "Sally" into the input box, the DOM element `value` *property* becomes "Sally". But the HTML `value` *attribute* remains unchanged as you discover if you ask the input element about that attribute: `input.getAttribute('value')` returns "Bob".

The HTML attribute `value` specifies the *initial* value; the DOM `value` property is the *current* value.

#### ex button

The `disabled` attribute is another peculiar example. A button's `disabled` property is `false` by default so the button is enabled. When you add the `disabled` attribute, its presence alone initializes the button's `disabled` property to `true` so the button is disabled.

Adding and removing the `disabled` attribute disables and enables the button. The value of the attribute is irrelevant, which is why you cannot enable a button by writing `<button disabled="false">Still Disabled</button>.`

Setting the button's `disabled` *property* disables or enables the button. The value of the *property* matters.

**The HTML attribute and the DOM property are not the same thing, even when they have the same name.**

## classes

```js
DOMTokenList.toggle(token)
DOMTokenList.toggle(token, force)
// force
// If included, turns the toggle into a one way-only operation. 
	// If false, then token will only be removed, but not added. 
	// If true, then token will only be added, but not removed.

Element.classList // Returns a *live DOMTokenList* collection of the class attributes of the element. Read only.
```

info
---------------------------------------

```js
Element.id // Returns a DOMString representing the id of the element.
Element.tagName // returns a String with the name of the tag for the given element. Read only.
```

```js
HTMLElement.offsetWidth 
// Returns the layout width of an element as an integer
HTMLElement.offsetHeight 
// Returns the height of an element, including vertical padding and borders, as an integer
HTMLElement.style 
// Returns a *CSSStyleDeclaration* object that contains a list of all styles properties for that element with values assigned for the attributes that are defined in the element's inline style attribute. Use it to *get and set the inline style* of an element.
HTMLElement.hidden = 'true' 
// Technically, hidden works the same as 'style="display:none"'. But it’s shorter to write.
```

```js
Node.baseURI // Returns a DOMString representing the base URL of the document containing the Node.
Node.ownerDocument // Returns the Document that this node belongs to.
```

## viewport

```js
Element.scrollLeft // *Number of pixels that an element's content is scrolled from its left edge*. If the element's direction is rtl (right-to-left), then scrollLeft is 0 when the scrollbar is at its rightmost position (at the start of the scrolled content), and then increasingly negative as you scroll towards the end of the content.

Element.scrollHeight // read-only property is a measurement of the height of an element's content, including content not visible on the screen due to overflow. 
document.documentElement.scrollHeight // 
```

### scrollIntoView()

scrolls the element's parent container such that the element on which scrollIntoView() is called is visible to the user  

```js
Element.scrollIntoView();
Element.scrollIntoView(alignToTop);
Element.scrollIntoView({ options });

let options = {
	behavior: "auto"|"smooth", // # Defines the transition animation. Defaults to *auto*.
	block: "start"|"center"|"end"|"nearest", // Defines vertical alignment. Defaults to *start*.
	inline: "start"|"center"|"end"|"nearest", // Defines horizontal alignment. Defaults to *nearest*
}

/* example */

let navGallery = document.getElementById('nav-gallery');
navGallery.addEventListener('click', function() {
	let gallery = document.getElementById('gallery');
	gallery.scrollIntoView({behavior: "smooth", block: "center"});
});
```

### focus()

```js
HTMLElement.focus(preventScroll) // sets focus on the specified element, if it can be focused.
// preventScroll # A Boolean value indicating whether or not the browser should scroll the document to bring the newly-focused element into view. Optional.
```

### getBoundingClientRect()

```js
Element.getBoundingClientRect() // Returns a *DOMRect* object providing information about the size of an element and its position relative to the viewport.
```

If you need the bounding rectangle relative to the top-left corner of the document, just add the current scrolling position to the `top` and `left` properties (these can be obtained using `window.scrollY` and `window.scrollX`) to get a bounding rectangle which is independent from the current scrolling position.

```js
const y = window.scrollY + document.querySelector('#elementId').getBoundingClientRect().top // Y
const x = window.scrollX + document.querySelector('#elementId').getBoundingClientRect().left // X

window.scrollTo(0, y)
```

```js
function isInViewport(element) {
    const rect = element.getBoundingClientRect();
    return (
        rect.top >= 0 &&
        rect.left >= 0 &&
        rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
        rect.right <= (window.innerWidth || document.documentElement.clientWidth)
    );
}
```



### attachShadow()

```js
Element.attachShadow(init) // attaches a shadow DOM tree to the specified element and returns a reference to its ShadowRoot.
Element.attachShadow({mode: 'open'}) // specifies the encapsulation mode for the shadow DOM tree.
// open: Elements of the shadow root are accessible from JavaScript outside the root
// closed: Denies access to the node(s) of a closed shadow root from JavaScript outside it
Element.attachShadow({mode: 'open', delegatesFocus: 'false'}) //

/* delegatesFocus: A boolean that, when set to true, specifies behavior that mitigates custom element issues around focusability. When a non-focusable part of the shadow DOM is clicked, the first focusable part is given focus, and the shadow host is given any available :focus styling. */
```

* Any autonomous custom element with a valid name
  `<article>`, `<aside>`, `<blockquote>`, `<body>`, `<div>`, `<footer>`, `<h1>`, `<h2>`, `<h3>`, `<h4>`, `<h5>`, `<h6>`, `<header>`, `<main>`, `<nav>`, `<p>`, `<section>`, `<span>`

### comments

* `Element.tagName` property exists only for Element nodes.
* `Node.nodeName` is defined for any Node:
  * for elements it means the same as tagName.
  * for other node types (text, comment, etc.) it has a string with the node type.


HTMLSpecialElement
-------------------------------------------

### HTMLInputElement

interface provides special properties and methods for manipulating the options, layout, and presentation of `<input>` elements.

### HTMLButtonElement

interface provides properties and methods (beyond the regular HTMLElement interface it also has available to it by inheritance) for manipulating `<button>` elements.

### HTMLDialogElement

#### methods

- `show()`: Opens the dialog non-modally
- `showModal()`: Opens the dialog modally
- `close()`: Closes the dialog and optionally returns a value to the caller

#### events

- The `close` event fires when the dialog closes
- The `cancel` event fires when the user cancels the dialog by pressing the `Escape` key

#### properties

- `open`: A boolean value that indicates whether the dialog is open (true) or closed (false)
- `returnValue`: A property that can be used to set or get the return value for the dialog element

### HTMLFormElement

interface represents a `<form>` element in the DOM.

```js
/* Document */
document.forms[0]; // the first form in the document
document.forms["form-2"] // by id
document.forms["user-data"] // by name

/* HTMLFormElement */
HTMLFormElement.elements // returns an *HTMLFormControlsCollection* listing all the form controls contained in the <form> element
HTMLFormElement.elements[0]; // by index
HTMLFormElement.elements['mail']; // by name
HTMLFormElement.elements['input-mail-1']; // by id (name & id are the same in this case)

HTMLFormElement.name // returns  the value of the form's name HTML attribute, containing the name of the form.

```

```html
<form name="user-data">
	<input type="date" name="date-of-birth">
    <input type="number" name="age" max="99" min="1">
    <input type="text" name="name" placeholder="name">
    <input type="submit">
</form>
<script>
	let birthday = oForm.elements["name"].value; 
</script>
```



* You can access a particular form control in the returned collection by using either an index or the element's `name` or `id`.

### HTMLTemplateElement

```js
templateElement.content // returns a <template> element's template contents
```

### HTMLImageElement

```js
new Image(width, height) // creates a new HTMLImageElement instance. 
// It is functionally equivalent to 
document.createElement('img')
```

### HTMLSlotElement

```js
HTMLSlotElement.assignedElements(flatten=false)
// Returns a sequence of the elements assigned to this slot (and no other nodes). If the flatten option is set to true, it returns a sequence of both the elements assigned to this slot, and the elements assigned to any other slots that are descendants of this slot. If no assigned elements are found, it returns the slot's fallback content.
// flatten - A boolean value indicating whether to return the assigned elements of any available child <slot> elements (true) or not (false). Defaults to false.

let slots = this.shadowRoot.querySelector('slot')
let elements = slots.assignedElements({flatten: true})
```

### HTMLSelectElement

* represents an HTML `<select>` element.

```js
// get all selected options in a multiple select field
Array.from(selectElement.selectedOptions).map(opt => opt.value)
```



```js
selectObject.add(option, index)
// option must be an option or optgroup element
// index position for where the new option element should be inserted

// Add a "Kiwi" option at the end of a drop-down list:
let x = document.getElementById("mySelect"); // x = selectObject
let option = document.createElement("option"); // option = option element
option.text = "Kiwi";
x.add(option); 
```

Document
------------------------------------------------------------

*EventTarget > Node > Document*

### properties


```js
/* meta */
Document.baseURI              // returns the absolute base URI of the document 
Document.cookie               // returns the document's cookie
Document.doctype              // returns the document's doctype 
Document.documentMode         // returns the mode used by the browser
Document.documentURI          // returns the URI of the document
Document.domain               // returns the domain name of the document server
Document.inputEncoding        // returns the document's encoding (character set)
Document.lastModified         // returns the date and time the document was updated
Document.readyState           // returns the (loading) status of the document
Document.referrer             // returns the URI of the referrer (the linking document) 
Document.strictErrorChecking  // returns if error checking is enforced
Document.URL                  // returns the complete URL of the document

/* single element */
document.documentElement    // returns the *html* element 
document.title              // returns the *title* element
document.body               // returns the *body* element
document.head               // returns the *head* element

/* dynamic */
Document.activeElement 		// returns the Element within the DOM that currently has focus. read-only.

/* all elements */
document.scripts            		// returns an *HTMLCollection* with all `<script>` elements
let scriptList = document.scripts 	// an HTMLCollection; use it just like an array to get all the elements in the list
document.links              		// returns all *area* and *a* elements that have a href attribute
document.embeds             		// returns all *embed* elements
document.forms // returns an *HTMLCollection* with all the <form> elements contained in the document.
document.images             		// returns all *img* elements
document.anchors            		// returns all *a* elements that have a name attribute
```

### createElement()

```js
let element = document.createElement('tag'); // create an HTML element
el.append(document.createElement('ul'))
el.append(document.createElement('ul').textContent = 'derf')
```

### createDocumentFragment()

 Creates a new empty DocumentFragment into which DOM nodes can be added **to build an offscreen DOM tree**. 

DocumentFragments are DOM Node objects which are never part of the main DOM tree. The usual use case is to create the document fragment, append elements to the document fragment and then append the document fragment to the DOM tree. 

**In the DOM tree, the document fragment is replaced by all its children.** 

## DOMTokenList

interface represents a set of space-separated tokens. Such a set is returned by... 

* `Element.classList`
* `HTMLLinkElement.relList`
* `HTMLAnchorElement.relList`
* `HTMLAreaElement.relList`
* `HTMLIframeElement.sandbox`
* `HTMLOutputElement.htmlFor`

### properties


* `DOMTokenList.length` # Is an integer representing the number of objects stored in the object. Read only.
* `DOMTokenList.value` # A stringifier property that returns the value of the list as a DOMString. 

### methods

```js
DOMTokenList.add(token1, token2) // Adds the specified token(s) to the list.
DOMTokenList.remove(token1, token2) // Removes the specified token(s) from the list
DOMTokenList.replace(oldToken, newToken) // Replaces token with newToken.
DOMTokenList.supports(token) // Returns true if a given token is in the associated attribute's supported tokens.
DOMTokenList.toggle(token [, force]) // Removes token from the list if it exists, or adds token to the list if it doesn't. Returns a boolean indicating whether token is in the list after the operation.
DOMTokenList.entries() // Returns an iterator, allowing you to go through all key/value pairs contained in this object.
DOMTokenList.forEach(callback [, thisArg]) // Executes a provided callback function once per DOMTokenList element.
DOMTokenList.keys() // Returns an iterator, allowing you to go through all keys of the key/value pairs contained in this object.
DOMTokenList.values() // Returns an iterator, allowing you to go through all values of the key/value pairs contained in this object.
DOMTokenList.contains(token);
```

### examples

```js
// <p class="a b c"></p>
let element = document.querySelector("p")
let classes = element.classList
element.classList.add("d")
element.textContent = `paragraph classList is "${classes}"`
```

HTML attributes & DOM properties
--------------------------------------------------

The *attribute-property* mapping is not one-to-one!

### DOM properties

```js
// DOM nodes are regular JavaScript objects. We can alter them.
document.body.myData = {
  name: 'Caesar',
  title: 'Imperator'
}

// We can also modify built-in prototypes like Element.prototype and add new methods to all elements:
Element.prototype.sayHi = function() {
  alert(`Hello, I'm ${this.tagName}`)
}
// They're inherited then
document.documentElement.sayHi() // Hello, I'm HTML
document.body.sayHi() // Hello, I'm BODY
```

If an attribute is non-standard, there won’t be a DOM-property for it
Is there a way to access such attributes?
Sure. All attributes are accessible by using the following methods:

```js
el.hasAttribute(name)         // checks for existence.
el.getAttribute(name)         // gets the value.
el.setAttribute(name, value)  // sets the value.
el.setAttribute('data-foo', 'Hello World!');
el.removeAttribute(name)   	// removes the attribute.
```

These methods operate exactly with what’s written in HTML.

### HTML attributes

In HTML, tags may have attributes. When the browser parses the HTML to create DOM objects for tags, it recognizes standard attributes and creates DOM properties from them. So when an element has *id* or another standard attribute, the corresponding property gets created. But that doesn’t happen if the attribute is *non-standard*.

#### read a non-standard property

```html
<body something="non-standard">
  <script>
    alert(document.body.getAttribute('something')); // non-standard
  </script>
</body>
```

### property-attribute synchronization

```html
<input>
<script>
    let input = document.querySelector('input');

    // attribute => property
    input.setAttribute('id', 'id');
    alert(input.id); // id (updated)

    // property => attribute
    input.id = 'newId';
    alert(input.getAttribute('id')); // newId (updated)
</script>
```

DOM classes
-------------------------------------------

DOM nodes are regular JavaScript objects.

* `console.log(elem)` # shows the element DOM tree.
* `console.dir(elem)` # shows the element as a DOM object, good to explore its properties.

### Classes

* `EventTarget` # is the root *abstract* class. It serves as a base, so that all DOM nodes support events
  * `Node` # is also an *abstract* class, serving as a base for DOM nodes. It provides the core tree functionality: parentNode, nextSibling, childNodes and so on (they are getters). Objects of Node class are never created. But there are concrete node classes that inherit from it, namely: *Text* for text nodes, *Element* for element nodes and more exotic ones like *Comment* for comment nodes.
    * `Element` # is a base class for DOM elements. It provides element-level navigation like nextElementSibling, children and searching methods like getElementsByTagName, querySelector. A browser supports not only HTML, but also XML and SVG. The Element class serves as a base for more specific classes: SVGElement, XMLElement and HTMLElement.
      * `HTMLElement` # is finally the basic class for all HTML elements. It is inherited by concrete HTML elements:
        * `HTMLInputElement` # the class for *input* elements
        * `HTMLBodyElement` – the class for *body* elements
        * `HTMLAnchorElement` – the class for *a* elements
        * …and so on, each tag has its own class that may provide specific properties and methods.

ETC
===========


#### change css variable with javascript

```js
document.documentElement.style.setProperty('--text-color', textColor);
document.documentElement.style.setProperty('--element-color', elementColor);
```

Console
------------------------------------------------

#### dir()
`console.dir(object);` # displays an interactive list of the properties of the specified JavaScript object. The output is presented as a hierarchical listing with disclosure triangles that let you see the contents of child objects.

DATA TYPES
===============


HTMLCollection
------------------

* An HTMLCollection represents a generic collection (*array-like object* similar to *arguments*) of elements
* It's not an array but it can be converted into an array
* An HTMLCollection in the HTML DOM is *live*; it is automatically updated when the underlying document is changed.
* This interface is called *HTMLCollection* for historical reasons (before the modern DOM, collections implementing this interface could only have HTML elements as their items).
* Changes in the DOM will reflect in the array as the changes occur. If an element selected by this array no longer qualifies for the selector, it will automatically be removed. Be aware of this for iteration purposes.

```js
let aElements = document.getElementsByTagName('a')

/* convert into array */

aElements = Array.from(aElements)

/* methods */

aElements.namedItem(id)

// Returns the specific node whose ID or, as a fallback, name matches the string specified by name. Matching by name is only done as a last resort, only in HTML, and only if the referenced element supports the name attribute. Returns null if no node exists by the given name.

// An alternative to accessing collection[name] (which instead returns undefined when name does not exist). This is mostly useful for non-JavaScript DOM implementations.

```

CSSStyleDeclaration
-----------------------

The CSSStyleDeclaration interface represents an *object* that is a CSS declaration block, and exposes style information and various style-related methods and properties.  

It's returned by...   

```js
HTMLElement.style // deals with the inline styles of a single element
document.styleSheets[0].cssRules[0].style // the CSSStyleSheet API
Window.getComputedStyle() // exposes the CSSStyleDeclaration object as a read-only interface.
```

### common methods

#### setProperty()

```js
CSSStyleDeclaration.setProperty(property, value, priority) // modifies an existing CSS property or creates a new CSS property in the declaration block

// Alternative way:
// If the priority argument isn't required you can use a special simpler syntax 
// for setting a CSS property on a style declaration object:
el.style.cssPropertyName = 'value'

// Attention!
el.style.minWidth = '300px' // css property is camel cased
el.style.setProperty('min-width', '300px') // css property is spelled as usual
```

```js
CSSStyleDeclaration.removeProperty(property) // removes a property from the CSS declaration block
CSSStyleDeclaration.getPropertyValue() // returns the property value given a property name
```


# SHADOWROOT

```js
ShadowRoot.adoptedStyleSheets // value is an array of CSSStyleSheet() instances that must have been created using the CSSStyleSheet() constructor within the context of the shadow root's parent Document.
```

SERVICE WORKER
============================

## Basics

Service workers essentially act as *proxy servers* that sit between web applications, the browser, and the network (when available).  

* enable the creation of effective offline experiences
* intercept network requests
* take appropriate action based on whether the network is available
* update assets residing on the server

A service worker is run in a worker context: it therefore has *no DOM access*, and runs on a different thread to the main JavaScript that powers your app, so it is non-blocking. It is designed to be *fully async*; as a consequence, APIs such as synchronous XHR and Web Storage can't be used inside a service worker.

* It can receive information from the server even when the application is not running.  
  It shows notifications in the PWA application without opening in the mobile browser.
* It cannot directly access the DOM.  
  Therefore, the *PostMessage* (send data) and *Message* (receive data) Event Listener method is used to communicate with the webpage.
* it only has permission to access files in the same directory and sub-directories.

Lifecycle
--------------------------------

* Registration
* Download
* Install
* Activate

After that, it is updated when:

* A navigation to an in-scope page occurs.
* An event is fired on the service worker and it hasn't been downloaded in the last 24 hours.

### 1. registration

`ServiceWorkerContainer.register()` # registers a service worker. If successful, your service worker will be downloaded to the client and attempt installation/activation (see below) for URLs accessed by the user inside the whole origin, or inside a subset specified by you.

```js
window.onload = () => {
	'use strict';
	if ('serviceWorker' in navigator) {
		navigator.serviceWorker.register('./sw.js');
	}
}
```

### 2. download

The service worker is immediately downloaded when a user first accesses a service worker–controlled site/page.  

### 3. install

#### as a dependency

Ideal for: CSS, images, fonts, JS, templates… basically anything you'd consider static to that "version" of your site.
These are things that would make your site entirely non-functional if they failed to be fetched, things an equivalent platform-specific app would make part of the initial download

```js
const PRECACHE = 'precache-v1
const PRECACHE_URLS = [
	'/css/whatever-v3.css',
    '/css/imgs/sprites-v6.png',
    '/css/fonts/whatever-v8.woff',
    '/js/all-min-v4.js',
]

// The install handler takes care of precaching the resources we always need.
self.addEventListener('install', evt => {
	evt.waitUntil(
    // extends the installing stage until the promise is resolved
		caches.open(PRECACHE)
			.then(cache => cache.addAll(PRECACHE_URLS))
			.then(self.skipWaiting())
        	// skips installing stage jumping directly to activation stage 
        	// without waiting for currently controlled clients to close
	)
})

self.addEventListener('install', evt => {
	evt.waitUntil(
    	caches.open(PRECACHE)
        	.then(cache => return cache.addAll(PRECACHE_URLS))
    )
})
```

#### not as dependency

```js
self.addEventListener('install', evt {
	evt.waitUntil(
    	caches.open('mygame-core-v1').then(cache => {
      		cache.addAll 
    		// levels 11–20
        	()
      return cache.addAll
        // core assets and levels 1–10
        ()
    }),
  )
})
```

#### 4. activation

Once the service worker is successfully installed, it converts to the activation phase. If there is an open page controlled by the previous service worker, the new service worker enters the *waiting state*. The new service worker is activated only when no pages are loaded in the old service worker. A service worker is not activated immediately after installation.

A service worker will only be active in these cases:

* If no service worker currently active.
* If the user refreshes the webpage

## Work of a Service Worker

### intercept fetchEvent

```js
self.addEventListener('fetch', evt => {
	// Let the browser do its default thing
    // for non-GET requests.
    if (evt.request.method != 'GET') return

    // Prevent the default, and handle the request ourselves.
    evt.respondWith(async function() {
        // Try to get the response from a cache.
        const cache = await caches.open('dynamic-v1')
        const cachedResponse = await cache.match(evt.request)

        if (cachedResponse) {
            // If we found a match in the cache, return it, but also
            // update the entry in the cache in the background.
            evt.waitUntil(cache.add(evt.request))
            return cachedResponse
        }

        // If we didn't find a match in the cache, use the network.
        return fetch(evt.request)
    }())
})
```




Properties
-------------------------------------

```js
ServiceWorkerGlobalScope.caches // Contains the CacheStorage object associated with the service worker.
ServiceWorkerGlobalScope.clients // Contains the Clients object associated with the service worker.
ServiceWorkerGlobalScope.registration // Contains the ServiceWorkerRegistration object that represents the service worker's registration.
```

## Examples

```js
self.addEventListener('fetch', evt => {
	console.log('fetchEvent: ', evt)
	evt.respondWith(
    // prevents the browser's default fetch handling
    // allows you to provide a promise for a Response yourself.
		caches.match(evt.request).then(cachedResponse => {
			return cachedResponse || fetch(evt.request);
		}).catch(err => {
			console.log(err)
		})
	)
})

self.addEventListener('fetch', evt => {
	console.log('fetchEvent: ', evt)
	evt.respondWith(
        caches.match(evt.request).then(cachedResponse => {
			if (cachedResponse) return cachedResponse
		}).catch(err => {
			console.log(err)
		})
	)
})
```



Events
---------------------

#### common methods

```js
waitUntil() // Extends the lifetime of the event. It is intended to be called in the *install* EventHandler for the installing worker and on the *activate* EventHandler for the active worker.
```

#### event types
```js
ServiceWorkerGlobalScope.oninstall 
// is fired when a *ServiceWorkerRegistration* acquires a new ServiceWorkerRegistration.installing worker.
ServiceWorkerGlobalScope.onfetch 
// Occurs when *fetch()* is called
ServiceWorkerGlobalScope.onactivate 
// Occurs when a ServiceWorkerRegistration acquires a new ServiceWorkerRegistration.active worker.
```

#### fetchEvent

```js
fetchEvent.respondWith() // prevents the browser's default fetch handling, and allows you to provide a promise for a Response yourself.
fetchEvent.request // returns the Request that triggered the event handler.
```


### cache

```js
this.addEventListener('install', function(event) {
	event.waitUntil(
		caches.open('v1').then(function(cache) {
		return cache.addAll([
			'/sw-test/',
			'/sw-test/index.html',
			'/sw-test/style.css',
			'/sw-test/app.js',
			'/sw-test/image-list.js',
			'/sw-test/star-wars-logo.jpg',
			'/sw-test/gallery/',
			'/sw-test/gallery/bountyHunters.jpg',
			'/sw-test/gallery/myLittleVader.jpg',
			'/sw-test/gallery/snowTroopers.jpg'
		]);
		})
	);
});
```

# SOLUTIONS

### Finding/Fixing Unintended Body Overflow

```javascript
var docWidth = document.documentElement.offsetWidth;

[].forEach.call(
  document.querySelectorAll('*'),
  function(el) {
    if (el.offsetWidth > docWidth) {
      console.log(el);
    }
  }
);
```

WEB AUDIO 
============================

WEB WORKERS
============================

* represents a background task
* run a script operation in a background thread separate from the main execution thread of a web application.  

The advantage of this is that laborious processing can be performed in a separate thread, allowing the main (usually the UI) thread to run without being blocked/slowed down.

### constructor

`var myWorker = new Worker(url, options);` # creates a Worker object that executes the script at the specified URL. This script must obey the same-origin policy.

* *url*  
  A USVString representing the URL of the script the worker will execute. It must obey the same-origin policy.
* *options*  
  An object containing option properties that can be set when creating the object instance. Available properties are as follows:
  * `type` # specifies the type of worker to create: `classic` (def) or `module`
  * `credentials` # specifies the type of credentials to use for the worker. The value can be `omit` (def), `same-origin`, or `include`
  * `name` # specifies an identifying name for the *DedicatedWorkerGlobalScope* representing the scope of the worker, which is mainly useful for debugging purposes.

WINDOW
===========

size
-----------------------------------------------------------

#### width

* `let intVw = window.innerWidth;` # returns the interior width of the window in pixels. This includes the width of the vertical scroll bar, if one is present. More precisely, innerWidth returns the width of the window's layout viewport.
* `let outVw = window.outerWidth;` # returns the width of the outside of the browser window. It represents the width of the whole browser window including sidebar (if expanded), window chrome and window resizing borders/handles.

```js
let intVw = window.innerWidth; // returns the interior width of the window in pixels. 
// This includes the width of the vertical scroll bar, if one is present. 
// More precisely, innerWidth returns the width of the window's layout viewport.
let outVw = window.outerWidth; // returns the width of the outside of the browser window. 
// It represents the width of the whole browser window including sidebar (if expanded), window chrome and window resizing borders/handles.
```

#### height

```js
let intVh = window.innerHeight // the viewport into which the browser draws a web page

let outVh = window.outerHeight // height of the whole browser window, including any sidebar, window chrome, and window-resizing borders/handles

document.querySelector('html').clientHeight // height of the window minus its horizontal scroll bar and any borders
```

## timeout / interval

### setTimout()

```js
setTimeout(callback, delay)
let timeoutID = setTimeout(callback, delay, arg1, arg2)
let timeoutID = setTimeout(callback, delay)
```

* *timeoutID*   # a numerical ID, which can be used in conjunction with clearTimeout to cancel the timer.
* *callback*    # the function to be executed after the timer expires.
* *delay*       # the number of milliseconds by which the function call should be delayed.
* *arg1, arg2*  # are additional arguments passed to the function specified by function.

```js
function greet(){
    alert('Howdy!')
}
setTimeout(greet, 2000)

// arrow function:
setTimeout(() => { 
  alert('Howdy!')
}, 2000)
```

### clearTimeout() 

cancels a timeout previously established by calling setTimeout()

```js
clearTimeout(timeoutID)
```

### setInterval()

```js
const intervallID = setInterval(callback, delay) // Repeatedly calls the callback function with a fixed delay (in milliseconds) between each call. 
// returns an interval ID which uniquely identifies the interval, so you can remove it later by calling 
clearInterval(intervallID)
```

## misc

### getSelection()

```js
window.getSelection() // returns a Selection object representing the range of text selected by the user or the current position of the caret.

// get the mouse selection
const getSelectedText = () => window.getSelection().toString()
getSelectedText()
```

### postMessage()

enables cross-origin communication between Window objects; e.g., ***between a page and a pop-up*** that it spawned, or ***between a page and an iframe*** embedded within it.

```js
postMessage(message)
postMessage(message, options)
postMessage(message, targetOrigin)
postMessage(message, targetOrigin, transfer)

document.querySelector('iframe').contentWindow.postMessage('text', '*');
document.querySelector('iframe').contentWindow.postMessage(JSON.stringify('hello'), 'http://localhost:5173'); 
```



```js
window.addEventListener('message', (event) => {
  event.data // The object passed from the other window
  event.origin // The origin of the window that sent the message
  event.source // A reference to the window object that sent the message
  
	if (event.origin !== "http://example.org:8080") return;
	
}, false)
```

## matchMedia()

* You can use the returned media query to perform both instantaneous and event-driven checks
* `.matches` is perfect for one-time instantaneous checks but is unable to continuously check for changes

```js
// isMobile?
const isMobile = window.matchMedia("only screen and (max-width: 760px)").matches;
```

```javascript
const mediaQuery = window.matchMedia('(min-width: 768px)')
if (mediaQuery.matches) {
  // do something
}
```

```javascript
// Create a condition that targets viewports at least 768px wide
const mediaQuery = window.matchMedia('(min-width: 768px)')

// Add the callback function as a listener to the query list.
mediaQuery.addEventListener('change', handleMinWidth768);

// Initial check
handleMinWidth768(mediaQuery)

function handleMinWidth768(e) {
  // Check if the media query is true
  if (e.matches) {
    // Then log the following message to the console
    console.log('Media Query Matched!')
  }
}
```

## animation

### requestAnimationFrame()

Before `requestAnimationFrame` was available, this style was used:

```js
setInterval(function() {
	// animiate something
}, 1000/60);
```

Tells the browser that you wish to perform an animation and requests that the browser calls a specified function to update an animation before the next repaint. The method takes a callback as an argument to be invoked before the repaint.

* *callback*  
  The function to call when it's time to update your animation for the next repaint. The callback function is passed one single argument, a *DOMHighResTimeStamp* similar to the one returned by performance.now(), indicating the point in time when requestAnimationFrame() starts to execute callback functions.
* *return value*  
  A request id, that uniquely identifies the entry in the callback list. You can pass this value to `window.cancelAnimationFrame()` to cancel the refresh callback request.

> Your callback routine must itself call requestAnimationFrame() again if you want to animate another frame at the next repaint. requestAnimationFrame() is 1 shot.

* The number of callbacks is usually 60 times per second, but will generally match the display refresh rate in most web browsers
* requestAnimationFrame() calls are paused in most browsers when running in background tabs or hidden iframe

The callback method is passed a single argument, a *DOMHighResTimeStamp*, which indicates the current time (based on the number of milliseconds since time origin). When multiple callbacks queued by requestAnimationFrame() begin to fire in a single frame, each receives the same timestamp even though time has passed during the computation of every previous callback's workload

> Be sure to always use the first argument (or some other method for getting the current time) to calculate how much the animation will progress in a frame, otherwise the animation will run faster on high refresh rate screens.

#### simple example

```js
function repeatOften() {
	// Do whatever
	requestAnimationFrame(repeatOften);
}
requestAnimationFrame(repeatOften);
```

#### complete example

```js
const bird = document.getElementById('animate-me');
let start;

function step(timestamp) {
	if (start === undefined) start = timestamp;
	const elapsed = timestamp - start; // milliseconds since start

	// this one moves the bird forward, but not further than 200
	// 1000 * 0.1 = 100 (half the way after one second)
	bird.style.transform = `translateX(${Math.min(0.1 * elapsed, 200)}px)`;
	// this one moves the bird instantly to 200px:
	// bird.style.transform = "translateX(200px)";

	if (elapsed < 2000) { // Stop the animation after 2 seconds
		window.requestAnimationFrame(step);
	}
}

window.requestAnimationFrame(step);
```

window.location
----------------------------------------

The Location interface represents the location (URL) of the object it is linked to. Changes done on it are reflected on the object it relates to.

The window.location object manages the address loaded by the browser.  Manipulating the location object is how you manage page redirects using  JavaScript.

```js
window.location.hash // used for single page apps and single page websites
window.location.host // "www.manueldieterich.de"
window.location.hostname // similar to host
window.location.href // "https://www.manueldieterich.de/de/uber-mich/" 
window.location.origin // "https://www.manueldieterich.de"
window.location.pathname // "/de/uber-mich/"
window.location.port // if a port is included in the URL
window.location.protocol // "https:"

/* METHODS */

window.location.search // allows you to interrogate the queryString
// --> prefer
URLSearchParams // or
URL.searchParams // which make it easy to parse out the parameters from the querystring

window.location.reload() // refresh a webpage (using the same URL) using JavaScript from cache
window.location.reload(true) // reload from the server without using the cache

window.location.assign() // sets the location object to the new URL
// Loads the resource at the new URL and preserves the previous entry in the browser's navigation history. This means the user can hit the back button and go to the original page. 

window.location.replace() // triggers a redirect, which we will explore in depth
// The replace method does the same thing, except the original or 'current' resource is not retained in the browser's history. This means the user cannot hit the back button to go to the original page.
```

XHR
=====================

*XMLHttpRequest* objects are used to interact with servers; retrieve resources from a server via JavaScript.   
You can retrieve data from a URL without having to do a full page refresh. This enables a Web page to update just part of a page without disrupting what the user is doing. XMLHttpRequest is used heavily in *AJAX* programming.  
If your communication involves receiving event data or message data from a server, consider using server-sent events through the *EventSource* interface. For full-duplex communication, *WebSockets* may be a better choice.

### constructor

`const xhr = new XMLHttpRequest();` # creates an XMLHttpRequest object; this object must be prepared by at least calling `open()` to initialize it before calling `send()` to send the request to the server.

### how to

```js
// 1. create an XMLHttpRequest object:  
const xhr = new XMLHttpRequest();
// 2. open the request with an url:  
xhr.open('GET', '/code/imgs.json');
// tell XHR that the server will be returning JSON which should be converted behind the scenes into a JavaScript object:
xhr.responseType = 'json';
// 3. send the request:
xhr.send();
// receive the response
xhr.onload = function() {
    const imgs = request.response;
	// do more stuff like function calls
}
```

#### examples

```js
// example 1:
function xhrGet(url) {
	const xhr = new XMLHttpRequest();
	xhr.open('GET', url, 'false');
	xhr.responseType = 'json';
	xhr.send();
	return xhr;
}
let xhr = xhrGet(url);
xhr.onload = function() {
	const xhrResponse = xhr.response;
	console.log('Received xhr: ', xhrResponse);
}

// example 2:
function getRessource(url){

	var THEANSWER;
	const xhr = new XMLHttpRequest();
	xhr.open('GET', url, 'false');
	xhr.responseType = 'json';
	xhr.onload = function() {
		THEANSWER = xhr.response;
		console.log('Value from the original response: ' + THEANSWER);
	}
	return THEANSWER;
}
let THEANSWER = getRessource(url);
```

methods
-------------

### request

`xhr.open(method, url, async?, user?, psw?)` # initializes a newly-created request, or re-initializes an existing one; specifies the request 

* *method*   
  the request type GET or POST 
* *url* 
  the URL to send the request to.
* *async*  
  true (asynchronous) or false (synchronous)
* *user*  
  optional user name to use for authentication purposes
* *psw*  
  optional password to use for authentication purposes

`xhr.abort()`       # cancels the current request
`xhr.send()`        # sends the request to the server; used for GET requests
`xhr.send(string)`  # sends the request to the server; used for POST requests
`xhr.setRequestHeader(header, value)` # adds a label/value pair to the header to be sent header: specifies the header name; value: specifies the header value

### response

`xhr.getAllResponseHeaders()`   # returns header information
`xhr.getResponseHeader()`       # returns specific header information


properties
--------------------------------------

`xhr.onreadystatechange` # An EventHandler that is called whenever the readyState attribute changes.
`xhr.responseType` # Is an enumerated value that defines the response type.

* `document`    # The response is an HTML Document or XML XMLDocument
* `json`        # The response is a JavaScript object created by parsing the contents of received data as JSON.
* `text`        # The response is a text in a DOMString object.