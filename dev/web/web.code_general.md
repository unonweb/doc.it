
If your front-end is trivial and non-value adding, you can use:
* Webflow, Bubble, Rails/Django templates etc.

If your backe-end is trivial/non-value adding you can use:
* Firebase, 8base, Hasura, Airtable, Zapier etc.


CSS namespaces
------------------------------------------------------------------------

### conflicts & problems

If CSS class names are very general, there will be conflicts in both CSS and JS!

```js
let controls = document.getElementsByClassName("controls");
```

#### solution 1: containers

* hierarchical naming scheme
* disadvantage: only `document.querySelectorAll('#component-id .class')` would be safe to get the desired elements

```css
#component-id .class
#slides-gallery .slide
```

#### solution 2: prefixes

*Block, Element, Modifier* (BEM)

JS & CSS
-----------------------------------------------------------------


### toggle CSS classes with JS

* Vorteil zweier definierter schaltbarer Zustände (trigClassA, trigClassB) ist, dass die Funktionalität klar vom Aussehen (trigBaseClass) getrennt werden kann.

```js
let trigBaseClass 	= 'menu__drop-header';
let trigClassA 		= 'menu__drop-header--open';
let trigClassB 		= 'menu__drop-header--close';
let destBaseClass 	= 'menu__drop-content';
let destClassA 		= 'menu__drop-content--open';
let destClassB 		= 'menu__drop-content--close';
let trigElements 	= document.getElementsByClassName(trigBaseClass)
let destElements 	= document.getElementsByClassName(destBaseClass)
```

Sollten *trigBaseClass* und *destBaseClass* auf BEM basieren?
Nein, da 


Production optimization
=================================


After minification and Gzip compression, all of your *above-the-fold* styles, scripts, markup should ideally weigh less than *14kb* in total. Since 14 kb is roughly the amount of data a server can send in the first round trip. Staying under that threshold allows users to get everything above the fold in the first data packet they receive.