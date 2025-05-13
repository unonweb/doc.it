
* use `test()` or `search()` if  you want to know whether a pattern is found in a string
* use `exec()` or `match()` for more information (but slower execution)
* https://regexr.com/

# solutions

## validateTime()

```js
function validateTimeFormat(val) {
	let isValid = /^([0-1][0-9]|2[0-3]):([0-5][0-9])$/.test(val)
  if (isValid) {
  	return true
	} else {
  	return 'Die Eingabe entspricht nicht dem Format HH:MM'
}
```

## validateOrigin

```js
function isValidOrigin(str) {
	const regexp = /^[a-z0-9]+([\-\.]{1}[a-z0-9]+)*\.[a-z]{2,6}$/i;
	if (regexp.test(str)) {
		return true;
	}
	else {
		return false;
	}
}

console.log(is_domain('www.example.com'));
console.log(is_domain('www.npm.co.uk'));
console.log(is_domain('http://www.example.com'));
console.log(is_domain('https://www.example.com'));
console.log(is_domain('www.example.com'));
```

## url

```js
str.matchAll(regexp) // returns an array of all results matching a string against a regular expression, including capturing groups

// without 'g' flag 
// - returns first match and capturing groups
"/de/healinground/".match(/\/(de|en)\//) 						// [ "/de/", "de" ] - second match is capturing group
"/de/healinground/".match(new RegExp('/(de|en)/')) 	// [ "/de/", "de" ]
// wit 'g' flag 
// - returns all matches no capturing groups
"/de/healinground/".match(new RegExp('/(de|en)/', 'g')) // [ "/de/" ]
```

## number

```js
/^[0-9]$/.test('1') // true
/^[0-9]$/.test('12') // false
/^[0-9]$/.test('1a') // false
```

# syntax

## `/`

 Indicates that the following character should be treated specially, or "escaped". It behaves one of two ways.

* **literal > special** : For characters that are usually treated literally, indicates that the next character is special and not to be interpreted literally. 

  ```js
  let re = /b/	// literal character "b"
  let re = /\b/ // character becomes special (--> word boundary)
  ```

* **special > literal**: For characters that are usually treated specially, indicates that next character **is not special** and should be interpreted literally.

  ```js
  let re = /a*/ 	// special character (0 or more occurrences of the preceding character)
  let re = /a\*/ 	// literal character "*"
  ```

## `/g`

```js
str.match(/[A-Z]/g)		// matches 
str.match(/findMe/g).length // number of occurences of 'findMe'
```

## lookBehind & lookAhead

```js
let str 
let re

re = /(?<=startStr)(.*)(?=endString)/
re = /(?<=This is).*?(?=sentence)/ 		// <-- this worked for me!
?<= 	// lookBehind
?=		// lookAhead

str = 'single occurence of the preceding character'
re = /(?<=single )(.*)(?= pre)/
str.match(re) 	// "occurence of the"

re = /(?<=This is).*?(?=sentence)/
// using a lazy quantifier between the two lookarounds. Adding a ? makes the star lazy. 
  
objStr.match(/(?<=contentRichText)(.*)(?=contentHTML)/)

let startPattern = 'contentRichText\":'
let endPattern = ',\"contentHTML'
let re = new RegExp(`(?<=${startPattern}).*?(?=${endPattern})`)
objStr.match(re)
```

## `?s`

```js
let str 
let re
str = 'This is just\na simple sentence.'
re = /This is(?s)(.*)sentence/
str.match(re)
```

## alphanumeric

```js
let re

re = /\w/		// matches a single alphanumeric character from the basic Latin alphabet, including the underscore
// equivalent to
re = /[A-Za-z0-9_]/
```

## whitespace

```js
let re

re = /\s/ 	// matches a single white space character
// including space, tab, form feed, line feed, and other Unicode spaces
re = /\S/		// matches a single character other than white space
re = /\S*/		// matches anything that is not white space
```

## digits

```js
let re
re = /\d/		// matches any digit
// equivalent to 
re = /[0-9]/
```

## flags

```js
const re = /pattern/flags
const re = new RegExp("pattern", "flags")
const re = /\w+\s/g // one or more characters followed by a space

g 	// global search
m 	// allows ^ and $ to match newline characters
i		// case-insensitive search
```

## capturing groups

```js
'Hey "Udo"'.match(/^Hey "(.*)"/)[1] // 'Udo'
```

## char classes

```js
[abcd]	// same as [a-d]
[a-d]	// same as [abcd]

/green|red/ // matches "green" in "green apple" and "red" in "red apple"

\w		// matches any alphanumeric character from the basic Latin alphabet, including the underscore. 
		// equivalent to [A-Za-z0-9_]
\W 		// matches any character that is not a word character from the basic Latin alphabet. 
		// equivalent to [^A-Za-z0-9_]

\d 		// equivalent to [0-9]
```

```js
/* WHITESPACE */

\s		// matches a single white space character, including space, tab, form feed, line feed, and other Unicode spaces.

\t 		// matches a horizontal tab
\r 		// matches a carriage return
\n 		// matches a linefeed
\f 		// matches a form-feed
```

## quantifiers

```js
x* 		// "0 or more occurrences of the preceding item"
x+ 		// "1 or more occurrences of the preceding item"
x? 		// "0 or 1 occurrences of the preceding item"
x{n} 	// "n" occurrences of the preceding item
x{n,m} // at least "n", at most "m" occurrences of the preceding item
```

```js
/^Hey ".*"$/.test('Hey "Udo"')
```

## negation

```js
let re

re = [^xyz] // A negated or complemented character class; matches anything that is NOT enclosed in the brackets
re = [^a-c]
```

```js
/^(?!.*foobar)/.test('foobar@bar.de') // look from the start of the string if the string 'foobar' can be "seen". If it can be "seen" there is no* match.

// explanation:
^          # start of the string 
(?!        # start negative look-ahead
  .*       # zero or more characters of any kind (except line terminators)
  foobar   # foobar
)          # end negative look-ahead
```

# create

## regex literal

consists of a pattern enclosed between slashes:  

```js
const re = /ab+c/i // literal notation
// OR
const re = new RegExp("ab+c", "i") // constructor with string pattern as first argument
// OR
const re = new RegExp(/ab+c/, "i") // constructor with regular expression literal as first argument
```

* Provides compilation of the regular expression when the script is loaded
* Use this if the regular expression remains constant (this can improve performance)
* **can not be used with variables**

## regex object

```js
let re = new RegExp('ab+c')

// use variables in regex
const regex = new RegExp(`ReGeX${testVar}ReGeX`)
string.replace(regex, "replacement")

let pattern="cd"
let repeats=3
new RegExp(`${pattern}{${repeats}}`, "g")
```

```js
/* lookBehind & lookAhead */
let startPattern = 'contentRichText\":'
let endPattern = ',\"contentHTML'
let re = new RegExp(`(?<=${startPattern}).*?(?=${endPattern})`)
objStr.match(re)
```

* Provides runtime compilation of the regular expression. 
* Use this when you know the regular expression pattern will be changing, or you don't know the pattern and are getting it from another source, such as user input.

## insert variable

```js
let element = 'un-nav'
let re = new RegExp(`.*\/${element}\/.*`)
re.test('/srv/web/resources/un-nav/index.html') // true
const regex = new RegExp("ReGeX" + testVar + "ReGeX") // concatenation
```

# escape

#### literal vs constructor

When using the constructor function, the normal string escape rules (preceding special characters with `\` when included in a string) are necessary.

```js
// The following are equivalent:
const re = /\w+/
const re = new RegExp("\\w+")

const re = /\w+\s/g
const re = new RegExp("\\w+\\s", "g")
```

instance methods
====================

### test()

* use whenever you want to know whether a pattern is found in a string
* returns `true` if the specified regexp matches the test string

```js
regexObj.test(str) // true if there is a match between the regular expression and <str>  

const str = 'hello world!'
const result = /^hello/.test(str) // true

const re = /hsl\([0-9]{1,3}, [0-9]{1,3}%, [0-9]{1,3}%\)/g
re.test(testString) // true | false
```

### replace()

```js
const newStr = str.replace(pattern, replacement) // returns a new string with some or all matches of a pattern replaced by a replacement. The original string is left unchanged
// * 	pattern can be a string or a RegExp  
// *	If pattern is a string, only the first occurrence will be replaced
// * 	replacement can be a string or a function to be called for each match.

let re = /(\w+)\s(\w+)/
let str = 'John Smith'
let newstr = str.replace(re, '$2, $1') // Smith, John

html = html.replace(/\t/g,' ') // remove tabs
html = html.replace(/ +/g, ' ') // remove extra white space
html = html.replace(/[\r\n]+/gm, "") // remove line breaks
```

# examples

```js
const re = /ab*c/ // a single "a" followed by zero or more "b"s followed by "c"
const re = /abc/ // matches the exact sequence "abc"

"cbbabbbbcdebc".match(/ab*c/)
// match a single "a" followed by zero or more "b"s followed by "c"
// "abbbbc"

const re = /(\w+)\s(\w+)/
const str = "Maria Cruz"
const newstr = str.replace(re, "$2, $1")
console.log(newstr) // "Cruz, Maria"
```

## transkriptionen

```js
str.replace(/\n\n( #.*# )/g, '$1\n\n').replaceAll('[P1]:', 'I:').replaceAll('[P2]:', 'B:')
```

## url

```js
// url
"/de/produktionen/".replace(/\/(de|en)\//, '') // 	"produktionen/" - replaces '/de/' or '/en/' with ''
"/de/produktionen/".replace(/\/(de|en)/, '') // 		"/produktionen/" - replaces '/de' or '/en' with ''
"/de/produktionen/".replace(/(de|en)/, '') // 			"//produktionen/"

"/de/healinground/".match(/\/(de|en)\//) // [ "/de/", "de" ]
```

## hsl

```js
// hsl
const str = 'hsl(210, 50%, 40%)'
const re = /hsl\([0-9]{1,3}, [0-9]{1,3}%, [0-9]{1,3}%\)/g
re.test(str) // true
```

```js
// literal slash
const re = /\/example\/[a-z]+/i // matches "/example/" followed by one or more alphabetic characters
// (the backslashes before each slash make them literal)
```

### numbers and units

```js
const re = /^(100|[1-9]?\d)%$/

/*
	^ asserts the start of the string.
	(100|[1-9]?\d) matches either "100" or any number between 1 and 99.
		100 matches the number 100
		[1-9]? matches an optional digit between 1 and 9 (optional because of the `?` quantifier)
		\d matches any digit (0-9)
	$ asserts the end of the string
	*/
```

```js
/^(100|[1-9]?\d)%$/, // 0% - 100%
```

```js
// string ends with 'px' or with '%'
const regex = /(px|%)$/;
console.log(regex.test("100px"));    // true
console.log(regex.test("50%"));      // true
console.log(regex.test("10px"));     // true
console.log(regex.test("20%"));      // true
console.log(regex.test("20em"));     // false
console.log(regex.test("abc"));      // false
```

```js
// string ends with 'px'
/px$/
```

### selectors

```js
// turn levels into heading selectors
levels = levels.map(lvl => (/^[0-9]$/.test(lvl)) ? `H${lvl}` : lvl) // [1,2,3] --> ['H1','H2','H3']
```
