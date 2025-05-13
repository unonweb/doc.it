
# window.history

```js
window.history.pushState({}, "", evt.target.href)
// Suppose https://mozilla.org/foo.html executes the following:
window.history.pushState({}, "", 'bar.html')
// This will cause the URL bar to display 
// https://mozilla.org/bar.html
// But it won't cause the browser to load bar.html or even check that bar.html exists


window.history.pushState(state, unused, url) // adds an entry to the browser's session history stack

// state //
/* An object associated with the new history entry created by pushState().

Whenever the user navigates to the new state, a 'popstate' event is fired, and the state property of the event contains a copy of the history entry's state object.

The state object can be anything that can be serialized. */

// URL //
/* The new history entry's URL is given by this parameter. 

Note that the browser won't attempt to load this URL after a call to pushState(), but it might attempt to load the URL later, for instance after the user restarts the browser. 

The new URL does not need to be absolute; if it's relative, it's resolved relative to the current URL. The new URL must be of the same origin as the current URL; otherwise, pushState() will throw an exception. 

This parameter is optional; if it isn't specified, it's set to the document's current URL. */
```

## popstate event

The **`popstate`** event of the [`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) interface is fired when the active history entry changes while the user navigates the session history. 
It changes the current history entry to that of the last page the user visited or, if [`history.pushState()`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState) has been used to add a history entry to the history stack, that history entry is used instead.

Note that just calling `history.pushState()` or `history.replaceState()` won't trigger a `popstate` event. The `popstate` event will be triggered by doing a browser action such as a click on the back or forward button (or calling `history.back()` or `history.forward()` in JavaScript).
