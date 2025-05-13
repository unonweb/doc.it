
* date string format: 
  https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#date_time_string_format
* Python: 
  https://strftime.org/ 

# solutions

```js
// calculate the time needed for some code to run:
let start = Date.now()
// do something
let diff = Date.now() - start
```

```js
// is date past?
Date.now() > Date.parse('2022-04-13T12:00:08.000Z')
```

```js
function isDatePast(date) {
  if (typeof date === 'string') {
    date = new Date(date)
  }
  
  const now = new Date()
  
  if (date - now >= 0) { // date is in future, or it is today
    return false
  }
  else {
    return true
  }
}
```
# constructor

```js
let today = new Date()
let date = new Date('Jul 12 2011') // You can pass a date string to the Date constructor to create an object for the specified date
```

# instance methods

##  toString()

```js
today.toString() // "Sun Feb 06 2022 18:07:30 GMT+0100 (Central European Standard Time)"
today.toDateString() // "Sun Feb 06 2022"
today.toTimeString() // "18:07:30 GMT+0100 (Central European Standard Time)"
```

## toLocaleString()

* `toLocaleString()` allows you to format your date the way you like, it won't put anything extra

```js
new Date().toLocaleString() // "5/22/2023, 2:30:33 PM"
new Date().toLocaleDateString() // "5/22/2023" 
new Date().toLocaleTimeString() // "6:07:30 PM"

// de
new Date().toLocaleString('de') // "22.5.2023, 14:30:33"
new Date().toLocaleString('de', { dateStyle: 'short'}) 	// "22.05.23"
new Date().toLocaleString('de', { dateStyle: 'long'}) 		// "22. Mai 2023"
new Date().toLocaleString('de', { dateStyle: 'full'}) 		// "Montag, 22. Mai 2023"
new Date().toLocaleString('de', { timeStyle: 'short'})		// "14:30"
new Date().toLocaleString('de', { dateStyle: 'long', timeStyle: 'short'}) // "22. Mai 2023 um 14:30" 
new Date().toLocaleString('de', { weekday: 'long' }) // Samstag
new Date().toLocaleString('de', { weekday: 'short' }) // Sa
// en
new Date().toLocaleString('en-GB', { timeZone: 'UTC' }) 	// "20/12/2012, 03:00:00"
new Date().toLocaleString('en', { dateStyle: 'short'}) 	// "5/22/23"
new Date().toLocaleString('en', { dateStyle: 'long'}) 		// "May 22, 2023"
new Date().toLocaleString('en', { dateStyle: 'full'})		// "Monday, May 22, 2023"
new Date().toLocaleString('en', { timeStyle: 'short'})		// "2:30 PM"
```

```js
today.toUTCString() // "Sun, 06 Feb 2022 17:07:30 GMT
today.toGMTString() // "Sun, 06 Feb 2022 17:07:30 GMT"
```

## get subcomponent

```js
today.getMonth() // 2 means March (zero based!)
today.getDate()
today.getFullYear()
```

# static methods

```js
Date.now() // Anzahl der Millisekunden die seit dem 1. Januar 1970 00:00:00 UTC vergangen sind.

Date.parse() // Parses a string representation of a date and returns the number of milliseconds since 1 January, 1970, 00:00:00 UTC

Date.UTC() // Accepts the same parameters as the longest form of the constructor (i.e. 2 to 7) and returns the number of milliseconds since January 1, 1970, 00:00:00 UTC, with leap seconds ignored.
```
