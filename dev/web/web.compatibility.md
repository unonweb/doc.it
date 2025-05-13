JavaScript disabled?
-----------------------------------------------

#### CSS for when JavaScript is Enabled

```js
document.documentElement.className = "js"
/* This adds a class to the root <html>, so you could (for example) do something like hide a <div> only when JavaScript is enabled. */
```
```css
.js div#id { 
	display: none; 
}
```