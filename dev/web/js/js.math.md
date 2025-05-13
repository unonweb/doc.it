
# solutions

## getRandomInt()

```js
Math.trunc(Math.random() * 1000) // random int between 0 and 1000
```

```js
function getRandomInt(min, max) {
  min = Math.ceil(min)
  max = Math.floor(max)
  return Math.floor(Math.random() * (max - min) + min) // The maximum is exclusive and the minimum is inclusive
}
```

## getRandomFloat()

```js
function getRandomFloat(min, max) {
  // returns a random number between the specified values. 
  // minimum is inclusive
  // maximum is exclusive
  return Math.random() * (max - min) + min
}
```

# static methods

## Math.trunc()

```js
Math.trunc(n) // Gibt den ganzzahligen Teil der Zahl x, ohne jede Nachkommastellen, zurück.
```

## Math.floor()

*rounds down* and returns the largest integer less than or equal to a given number

## Math.abs()

returns the absolute value of a number

```js
function difference(a, b) {
  return Math.abs(a - b);
}

console.log(difference(3, 5))
// expected output: 2

console.log(difference(5, 3))
// expected output: 2
```

## min() / max()

```js
Math.min() // returns the lowest value passed into it
// --> used to set a *maximum* value
Math.max() // returns the largest value passed into it
// --> used to set a *minimum* value

Math.min(2, 3, 1) 			// 1
const array1 = [2, 3, 1]
Math.min(...array1) 		// 1

/* set a min and a max value */

scaleVal = Math.min(Math.max(scaleMin, scaleVal), scaleMax);
// first Math.max() determines if scaleVal is higher than the minimum value
// if yes, Math.min() compares scaleVal to scaleMax
```

## Math.sign()

```js
// retuns whether a number is negative, positive or zero.

Math.sign(number)
// If positive, returns 1
// If negative, returns -1
// If zero, returns 0
```

## Math.random()

```js
Math.random() // returns a floating-point, pseudo-random number between 0 (inclusive) and 1 (exclusive)
Math.random() // a number from 0 to <1

function getRandomInt(max) {
  return Math.floor(Math.random() * max)
}

getRandomInt(3)) // 0, 1 or 2
```
