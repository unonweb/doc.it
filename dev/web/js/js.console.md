
```js
/* log an object */

console.log(name, age, job, hobbies) // When the amount of printed information becomes very large, the information becomes unintuitive. Because we don’t know what it refers to!
console.log({ name, age, job, hobbies })

console.warn() // has a special yellow colour flag
console.error() // has a unique red error flag AND prints the stacked relationship of function calls.
console.table()

// Supply CSS styling to console.log.
// Note use of %c

console.log('%cMy red text', 'color: red')
console.log('%cMy bold and red text', 'font-weight: bold; color: red')

console.log('%sText') // Element is converted to a string
console.log('%dText') //
console.log('%iText') // Element is converted to an integer 
console.log('%fText') // Element is converted to a float
console.log('%oText') // Element is displayed with optimally useful formatting
console.log('%OText') // Element is displayed with generic JavaScript object formatting
console.log('%cText') // Applies provided CSS |
```
