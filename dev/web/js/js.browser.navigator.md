
# window.navigator

```js
let userLang = navigator.language // returns a string representing the preferred language of the user, usually the language of the browser UI. 
```

```js
window.navigator.vibrate(200) // vibrate for 200ms
window.navigator.vibrate([100,30,100,30,100,30,200,30,200,30,200,30,100,30,100,30,100]) // # Vibrate 'SOS' in Morse.
```

## navigator.clipboard

```js
// copy content to the clipboard
const copyToClipboard = (content) => navigator.clipboard.writeText(content)
copyToClipboard("Hello fatfish")
```

## navigator.geolocation

### methods

```js
// The geolocation object provides the following methods:
getCurrentPosition() // retrieves the current geographic location
watchPosition() // retrieves periodic updates about the current geographic location
clearWatch() // cancels an ongoing watchPosition call
```
