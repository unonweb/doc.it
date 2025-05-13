
# LINKS

- the native web GmbH: Warum JavaScript so seltsam ist:
  https://www.youtube.com/watch?v=VNYIYqqaOcc
# ARRAYS

* Array Class: a global object that is used in the construction of arrays
* an array is a collection of elements (basically only a list of elements between [])
* arrays in JS are not a type on their own
* arrays are objects
* arrays can hold any value, even values of different types
* index starts with zero

## create

```javascript
const myArray = [] // using the literal syntax
const myArray = Array(10) // creates an array with a length of ten (filled with empty slots)
const myArray = new Array()
let arr = new Array(10).fill(6) // Array(10) [ 6, 6, 6, 6, 6, 6, 6, 6, 6, 6 ]
```

## destructure

```js
const foo = ['one', 'two', 'three']
const [red, yellow, green] = foo
console.log(red) // "one"
console.log(yellow) // "two"
console.log(green) // "three"
```

## rest / spread

```js
// insert elements of one array into any position of another array:
var parts = ['shoulders', 'knees']
var lyrics = ['head', ...parts, 'and', 'toes'] // ["head", "shoulders", "knees", "and", "toes"]

// insert elements of one array at the end of another array:
var arr1 = [0, 1, 2]
var arr2 = [3, 4, 5]
arr1.push(...arr2)

// merge only if defined:
let merged = [
  ...nav.imgs ?? '',
  ...footer.imgs ?? ''
] 


/* spread an object replacing a specific key */
// {id: 4, text: 'Mit Maren eine kurzes Päuschen machen', status: false}
if (item.id === updatedTodo.id) return { id: item.id, text: item.text, status: status }
if (item.id === updatedTodo.id) return { ...updatedTodo, status } 
// inserts all keys from updatedTodo an then replaces one value using the 'status' key
```

## loop

* best practice: use normal `for` loops when iterating over arrays.
* `for ... in` loops are often incorrectly used to loop over the elements in an Array. This is however very error prone because it does not loop from 0 to length - 1 but over all the present keys in the object and its prototype chain. A for-in loop applied to an array in some cases (especially with DOM elements) returns more members than a regular for loop.

```js
for (let i=0; i < arr.length; i++)  // works fastest, old-browser-compatible.
for (let item of arr)               // the modern syntax for items only,
for (let i in arr)                	// never use !!!
```

## merge

```js
// mutable style
const heroes = ['Batman', 'Superman']
const villains = ['Joker', 'Bane']

heroes.push(...villains) // ['Batman', 'Superman', 'Joker', 'Bane']

// immutable style
const all1 = heroes.concat(villains) // ['Batman', 'Superman', 'Joker', 'Bane']
```

## compare

* don’t use the `==` operator
  because in JS arrays are objects and **objects are not compared based on their values but based on the references of the variables**
* Arrays are objects in JavaScript, so `===` only returns true if the arrays are the same reference

```js
let array1 = [11, 22, 33]
let array2 = [11, 22, 33]
console.log(array1 == array2) // false

// array.toString()
console.log(array1.toString() === array2.toString()) //true
```

* compare them item-by-item in a loop or using iteration methods

```js
function arraysEqual(a, b, ignoreOrder = true) {
  if (a === b) return true;
  if (a == null || b == null) return false;
  if (a.length !== b.length) return false;

  // If you don't care about the order of the elements inside
  // the array, you should sort both arrays here.
  // Please note that calling sort on an array will modify that array.
  // you might want to clone your array first.

  for (var i = 0; i < a.length; ++i) {
    if (a[i] !== b[i]) return false;
  }
  return true;
}
```

```javascript
const a = [1, 2, 3];
const b = [4, 5, 6];
const c = [1, 2, 3];

function areArraysEqual(a, b) {
  return Array.isArray(a) && Array.isArray(b) && a.length === b.length && a.every((val, index) => val === b[index]);
}

arrayEquals(a, b); // false
arrayEquals(a, c); // true
```


internals
---------------

An array is a special kind of object. They extend objects providing special methods to work with *ordered collections* of data and also the `length` property. But at the core it’s still an object. The engine tries to store its elements in the *contiguous memory area*, one after another, just as depicted on the illustrations in this chapter, and there are other optimizations as well, to make arrays work really fast.

For instance, it is copied by reference:

```js
let fruits = ["Banana"]
let arr = fruits; // copy by reference (two variables reference the same array)
alert( arr === fruits ); // true
arr.push("Pear"); // modify the array by reference
alert( fruits ); // Banana, Pear - 2 items now
```

## solutions

```js
// first filter all item that don't have a todo.date property
// second extract the todo.date property and use it to fill the new array
let todoDates = this.todoListDB.filter(todo => todo.date !== '').map(todo => todo.date)

function changeValuePosition(arr, init, target) {
    [arr[init], arr[target]] = [arr[target], arr[init]]; 
    return arr
}



class Solutions {
	constructor() {
		this.todos = JSON.parse(localStorage.getItem('todos')) || [] // get locally or create empty array
	}

	editTodoShort(id, updatedText) {
		// if the id matches insert updatedText while keeping all other properties
		// if the id doesn't match return the entire todo item
		// (return statement is implicit in this arrow function)
		this.todos = this.todos.map(todo =>
			todo.id === id ? { id: todo.id, text: updatedText, complete: todo.complete } : todo
		)
	}

	editTodoLong(id, updatedText) {
		// the old todos is replaced by the array returned by the map() function
		this.todos = this.todos.map((item) => {
			if (item.id === id) return { id: item.id, text: updatedText, complete: item.complete }
			else return item
		})
	}

	deleteTodo(id) {
		// filter returns a new array with all elements that pass the test
		this.todos = this.todos.filter(todo => todo.id !== id)
	}

	addTodo(todoText) {
		const todo = {
			// this.todos[this.todos.length - 1] is accessing the last item of the array
			// then it reads out the id and increases it by 1
			id: this.todos.length > 0 
      	? this.todos[this.todos.length - 1].id + 1 
      	: 1,
			text: todoText,
			complete: false,
		}

		this.todos.push(todo)
	}

	toggleTodo(id) {
		this.todos = this.todos.map(todo =>
			todo.id === id ? { id: todo.id, text: todo.text, complete: !todo.complete } : todo
		)
	}
    
	updateTodoStatus(updatedTodo, status) {	
		this.todoList = this.todoList.map(item => {
			// if (item.id === updatedTodo.id) return { id: item.id, text: item.text, status: status }
			// shorter is:
            if (item.id === updatedTodo.id) return { ...updatedTodo, status }
			else return item
		})
	}
    
    moveTodo(updatedTodo, dir) {
		//let id = Number(evt.target.dataset.todoId)
		
		let srcIndex = this.todoList.findIndex(item => item._id === updatedTodo._id) // old position
		let destIndex // new position

		if (dir === 'up') {
			if (srcIndex === 0) return
			destIndex = srcIndex-- // new position is one index up
		}
		if (dir === 'down') {
			if (srcIndex === this.todoList.length - 1) return
			destIndex = srcIndex++ // new position is one index down
		}

		this.todoList = this.changeArrayOrder(this.todoList, srcIndex, destIndex)
		//console.dir(this.todoList)
		this.requestUpdate()
	}
    
    extractArrayFromObject() {
        let obj = 
            {
                anyProperty: 0,
                arr: [
                    {
                        doc: 'this is the information we want'
                        id: '34358df'
                        rev: '#1234'
                    },
                    {
                        doc: 'this is the information we want'
                        id: 'dlfk459'
                        rev: '#14324'
                    }
                ]
            }
        
        let extractShort = obj.arr.map(item => item.doc)
        
        let extractLong = []
		for (let item in obj.arr) {
			extractLong.push(obj.arr[item].doc)
		}
        
    }
    
}
```

### get

#### common items

```js
// get items that show up in every single sub-array
let categories = ["workshops", "körper"]

let posts = [
  [ "workshops", "körper" ],
  [ "workshops" ]
]

categories.filter(c => posts.every(p => p.includes(c)))
```

```js
function getCommonItems(arr) {

    // an array to hold the count of each elements in the input elements
    let lookupArray = [];

    // an array to hold the common elements in all the array
    let commonElementArray = [];

    // iterates through each elements in the array to find the common elements
    for (let arrayIndex = 0; arrayIndex < arr.length; arrayIndex++) {
        for (let childArrayIndex = 0; childArrayIndex < arr[arrayIndex].length; childArrayIndex++) {

            // check whether we have already find the current element
            if (lookupArray[arr[arrayIndex][childArrayIndex]]) {
                // we have already seen this element, so increment count by one
                lookupArray[arr[arrayIndex][childArrayIndex]]++;
            } else {
                // this is a new element so set the count to 1
                lookupArray[arr[arrayIndex][childArrayIndex]] = 1;
            }

            // check the updated count of the current element in the look up table, if the 
            // count is same as the number of input arrays, then its a common element
            if (lookupArray[arr[arrayIndex][childArrayIndex]] == arr.length) {

                // this is a common element, push it to the array
                commonElementArray.push(arr[arrayIndex][childArrayIndex]);
            }
        }
    }

    //  console.log(commonElementArray);
    return commonElementArray;
}
```

#### unique items

```js
const uniqueArray = (array) => {
    return Array.from(
        array.reduce((set, e) => set.add(e), new Set())
    )
}

// OR simply
const uniqueArray = (array) => Array.from(new Set(array))
```

get unique items from array of objects

```js
const data = [
  { group: 'A', name: 'SD' }, 
  { group: 'B', name: 'FI' }, 
  { group: 'A', name: 'MM' },
  { group: 'B', name: 'CO'}
];
Array.from(new Set(data.map((item) => item.group)))
const unique = [...new Set(data.map(item => item.group))]; // [ 'A', 'B']
```

get unique items from array of primitives

```js
const s = new Set([...newArr, ...oldArr]) // create set from multiple arrays
const unique = Array.from(new Set([...newArr, ...oldArr])) // create unique array from multiple arrays
```

```js
const arr = [2019, 2020, 2019, 2018, 2020, 2021, 2030, 2020, 2019]

arr.filter((value, index, self) => {
    return self.indexOf(value) === index
}) 

// [ 2019, 2020, 2018, 2021, 2030 ]

// goes through each item/index of the array
// indexOf() returns the FIRST index at which a given element can be found in the array
// returns true if the index of the FIRST occurence of 'value' matches the CURRENT index
// --> true for every first match of 'value'
// --> false for every subsequent match
```

#### min / max

```js
function arrMax(arr) {
    // find the highest number in an array:
  	return Math.max.apply(null, arr)
    // Math.max.apply(null, [1, 2, 3]) is equivalent to Math.max(1, 2, 3)
}

function arrMin(arr) {
    // find the lowest number in an array:
  	return Math.min.apply(null, arr)
    // Math.min.apply(null, [1, 2, 3]) is equivalent to Math.min(1, 2, 3)
}
```

```js
function getMaxValueInArrOfObjs(arr = [], prop = '') {
  const max = arr.reduce((prev, current) => {
      return (prev[prop] > current[prop]) ? prev : current
  })
  return max
}

function getMinValueInArrOfObjs(arr = [], prop = '') {
  const min = arr.reduce((prev, current) => {
      return (prev[prop] < current[prop]) ? prev : current
  })
  return max
}
```

#### average

```js
// get the average of multiple numbers
const average = (...args) => args.reduce((a, b) => a + b, 0) / args.length

average(0, 1, 2, -1, 9, 10) // 3.5
```

#### nested values

```js
let result = arrOfObjects.map(a => a.foo)
// returns an array with all 'foo' properties
// if an obj within our array doesn't have the 'foo' property 'undefined' is included
// so the resulting array will have the same size

result = result.filter(item => item !== undefined)
// filters out undefined values

// solution:
arrOfObjects.map(a => a.foo).filter(item => item !== undefined)
```



```js
posts = [
  {
    id: 1
    categories: [
    	{
    		name: 'first'
  		},
  		{
    		name: 'second'
  		},
    ]
  },
	{
  	id: 2
    categories: [
    	{
    		name: 'first'
  		},
  		{
    		name: 'second'
  		},
    ]
  },
]
// array of objects that contains an array of objects (categories) where we want a specific value (name)
posts.map(p => p.categories.map(cat => cat.name).join(" "))
// go through all posts, go through all categories, 
// --> return the category's name
// --> convert the categories-array into a string 
```

### remove

#### single item

```js
this.todos = this.todos.filter(todo => todo.id !== id)
```

#### duplicates

```js
let uniqueBlockTypes = new Set(blockTypes) // filter out duplicates

let array = [100, 23, 23, 23, 23, 67, 45]; 
let outputArray = Array.from(new Set(array)); // [100, 23, 67, 45]
```

#### empty strings

```js
let arr = ['345345', '', '', '34']
arr.filter(item => item) // [ "345345", "34" ]
```

### updated nested property

```js
let changedBlockIndex = args.originalDoc.blocks.findIndex(block => block.id === changedBlockID) // get index of updated block
args.originalDoc.blocks[changedBlockIndex].contentHTML = contentHTML // update doc
```

### sort

#### sort by date

```js
let posts = [
  { title: "Neuerscheinung: Stadt – Migration – Moral", date: "2022-08-09T12:55:28.000Z" },
  { title: "Urban Moral Economies", date: "2023-02-14T23:00:00.000Z" },
  { title: "News about William", date: "2023-05-24T10:41:52.020Z" },
]

posts.sort((a,b) => Date.parse(a.date) - Date.parse(b.date)) // sorts in ascending order
posts.sort((a,b) => Date.parse(b.date) - Date.parse(a.date)) // sorts in descending order

posts.forEach(post => console.log(post.date)) // show result
```

#### sort arr by obj value

```js
function sortArrByObjVal(arr = [], prop = '') {
	// sorts an array of objects by a given properties value
	return arr.sort((a, b) => {
		return a[prop] - b[prop]
	})
},

function sortArrByObjVal(arr = [], prop = []) {
	// sorts an array of objects by one or multiple property values in ascending order
	if (!prop.length || !arr.length) throw ReferenceError('one argument is empty')
	if (!Array.isArray(arr)) throw TypeError('is not an array')
	if (!Array.isArray(prop)) prop = [prop] // if it's not an array, turn it into one

	prop.forEach(p => {
		arr.sort((a, b) => {
			return a[p] - b[p]
		})
	})
	return arr
}
```

```js
function sortArrByObjVal(arr = [], prop = []) {
	// sorts an array of objects by one or multiple property values in ascending order
	// more verbose version
	// empty values are sorted after anything else
	if (!prop.length || !arr.length) throw ReferenceError('one argument is empty')
	if (!Array.isArray(arr)) throw TypeError('is not an array')
	if (!Array.isArray(prop)) prop = [prop]

	prop.forEach(p => {
		arr.sort((a, b) => {

			if (!a[p]) return 1
			if (!b[p]) return -1 // sort empty values after anything else

			if (a[p] === b[p]) return 0 // don't change if equal
			if (a[p] > b[p]) return 1 // if a is bigger b is sorted before a (ascending)
			if (a[p] < b[p]) return -1 // if a is smaller b is sorted after a (ascending)

		})
	})
	return arr
}
```

```js
function sortArrayByObjValue(arr = [], prop = '', order = '') {
  // sorts an array of objects by a given properties value
	return arr.sort((a, b) => {
    switch (order) {
			case 'ascending':
      case 'asc':
      case '':
				return (a[prop] > b[prop]) ? -1 : 1
				break;
		case 'descending':
    case 'desc':
        return (a[prop] > b[prop]) ? 1 : -1
				break;  		
		}
	})
}
```

### test

```js
const hasChanged = [
  !areArraysEqual(data.customElements, originalDoc?.customElements),
  !areArraysEqual(data.headHTML, originalDoc?.headHTML)
].some(item => item)
```

### convert

#### array to string

```js
// reduce array to a single string
let arr = ['itemOne', 'itemTwo']
arr.reduce((acc, curr) => acc += `, ${curr}`) // "itemOne, itemTwo"
arr.join(', ') // "itemOne, itemTwo"
```

### shuffle

```js
const shuffleArray = array => array.sort(() => Math.random() - 0.5)

shuffleArray([ 1, 2,3,4, -1, 0 ]) // [3, 1, 0, 2, 4, -1]
```

## test

```js
function testString(str) {
	let regex = [
    /^(100|[1-9]?\d)%$/, // 0% - 100%
    /^(1000|\d{1,3})$/, // '0px - 1000px'
  ]
  let messages = [
    '0% - 100%',
    '0px - 1000px'
  ]

  let index = regex.findIndex(re => re.test(str))
  
	if (index === -1) return `Der Wert muss eine der folgenden Bedingungen erfüllen: ${messages.join(' ')}`
  else return true
}
```

static methods
-----------------------------------------------------------------

#### from()

returns a new Array instance; creates a new, shallow-copied Array instance from an array-like or iterable object.

`Array.from()` lets you create Arrays from:

* array-like objects (objects with a length property and indexed elements); or
* iterable objects (objects such as Map and Set).

```js
// Create an array from a HTMLCollection
let elements = Array.from(document.getElementsByClassName('class'));

// Create an array based on a property of DOM Elements 
const images = document.getElementsByTagName('img'); 
const sources = Array.from(images, image => image.src);
const insecureSources = sources.filter(link => link.startsWith('http://'));
```

#### isArray()

Arrays do not form a separate language type. They are based on objects.  
So `typeof` does not help to distinguish a plain object from an array:

```js
typeof {}; // object
typeof []; // same
Array.isArray({}); // false
Array.isArray([]); // true
```

instance methods
--------------------------------------------------

### return a value

* return one match: if you only need a single value, use `find()`
* return multiple matches: if you need to find/return multiple values use `filter()`

#### common tipps

```js
// separate the testing function
const hasFiveOrMore = el => el.count >= 5
const result = arr.find(hasFiveOrMore)
```

#### common examples

```js
const numbers = [5, 12, 8, 130, 44];
// find()
numbers.find(item => item > 10); // --> 12
// findIndex()
const isLargeNumber = (element) => element > 13;
console.log(numbers.findIndex(isLargeNumber)); // --> 3
```

#### find()

```js
arr.find(item => item.id === id) // returns the first element in the provided array that satisfies the provided testing function. If no values satisfy the testing function, undefined is returned. 
```

#### reduce()

used to calculate a single value based on the array.

```js
let value = arr.reduce(callback, initial)
let value = arr.reduce((accumulator, current) => /* code */)
let value = arr.reduce((previous, current, index, array) => /* code */, initial)
```

* *accumulator* or *previous* # the result of the previous function call, equals *initial* the first time (if initial is provided).
* *item*  or *current* # the current array item.
* *initial* # if there’s no initial, then reduce takes the first element of the array as the initial value and starts the iteration from the 2nd element.

The first time that the callback is run there is no "return value of the previous calculation". If supplied, an initial value may be used in its place. Otherwise the array element at index 0 is used as the initial  value and iteration starts from the next element (index 1 instead of  index 0).

```js
const val = array.reduce((acc, cur) => update(acc, cur), initialValue);

// Is equivalent to:
let val = initialValue;
for (const cur of array) {
  val = update(val, cur);
}
```

```js
let arr = [1, 2, 3, 4, 5];
let result = arr.reduce((accumulator, current) => accumulator + current, 0) // 15
// 0+1  = 1
// 1+2  = 3
// 3+3  = 6
// 6+4  = 10
// 10+5 = 15
```

```js
// sum up
const tasks = [
	{'name': 'Edit article', 'duration' : 120},
	{'name': 'Work out','duration' : 60},
	{'name': 'Procrastinate on Duolingo', 'duration': 240}
];

totalTime = tasks.reduce((previous, current) => previous + current.duration, 0)  // 420
```

```js
// concatenate
arr = [
  { id: "6470396bf85f0620e91028a2", name: "Presentations" },
  { id: "6470396bf85f0620e91028a2", name: "Veröffentlichungen" },
]
arr.reduce((accumulator, current) => accumulator + `${current.name} `, '') // "Presentations Veröffentlichungen "
```

```js
// calculate inner min-content size of an element
const minimumSize = Array.from(temp1.children).reduce((acc, curr) => acc += curr.clientWidth, 0)
```

```js
// convert array into 'array string'
let tags = [ 'blog', 'cats' ] 
tags.reduce((accumulator, current) => `["${accumulator}", ` + `"${current}"]`) // '["blog", "cats"]'
```

```js
let langs = [ 'de', 'en', 'es' ] 
langs.reduce((accumulator, current) => `${accumulator}|${current}`) // 'de|en|es'
```



```js
function multiply(...args) {
	return args.reduce((a, b) => a * b)
}

/* EXAMPLE 4 */
userNames = allUsers.reduce((sum, user) => sum += `${user.lastName} `, '').trimEnd()
// * extracts the lastName property from each user object
// * and concatenates it with the next one
// * starting from an empty string

// TROUBLESHOOTING (return statement)
result = arr.reduce((sum, current) => sum + current, 0);    // works
result = arr.reduce((sum, current) => { return sum + current}, 0);  // works!
result = arr.reduce((sum, current) => {sum + current}, 0);  // doesn't work!
```

### return an index

#### findIndex()

returns the *index of the first element* in the array that satisfies the provided testing function.  
Otherwise, it returns -1, indicating that no element passed the test. 

```js
arr.findIndex(item => condition)
arr.findIndex(callback, thisArg)

let todoList = [ 
  { id: 1, text: "Write a love letter to Maren", finished: true, },
	{ id: 2, text: "Pick some flowers (for Maren)", finished: false, },
	{ id: 3, text: "Kiss Maren", finished: false, }
]

todoList.findIndex(item => {
  return item.id === 2 // don't forget the RETURN statement!
})
todoList.findIndex(item => item.id === 2 ) // here RETURN is not necessary
```

#### indexOf()

* similar to `findIndex()`, but checks each element for equality with the value instead of using a testing function.

```js
arr.indexOf(searchElement, fromIndex) // returns the first index at which a given element can be found in the array
// or -1 if it is not present
```

Default: start searching from the beginning of the array, and stop at the end of the array. But you can pass in a position as a second argument to skip the starting elements to be included in the search

##### finding all the occurrences of an element

```js
const indices = []
const array = ['a', 'b', 'a', 'c', 'a', 'd']
const element = 'a'
let idx = array.indexOf(element);
while (idx !== -1) {
  indices.push(idx)
  idx = array.indexOf(element, idx + 1)
}
console.log(indices)
// [0, 2, 4]
```

### return an item

#### find()

returns the *value of the first element* in the provided array that satisfies the provided testing function.  

```js
arr.find(item => condition)
arr.find(callback, thisArg)

class UserStore {
	constructor(){
		this._data = []
	}
	get(id){
		return this._data.find(d => d.id === id)
	}
}
```

### return a boolean

#### every()

returns *true if all elements in the array pass the test* implemented by the provided function. 

```js
arr.every(item => condition)
arr.every(callback, thisArg)
```

#### some()

returns *true if at least one element in the array passes the test* implemented by the provided function. 

```js
arr.some(item => condition)


let posts = importedPosts[lang].docs.filter(post => {
	return post.categories.some(cat => includedCat.includes(cat.id))
})

filterByCategory(catName, posts) {
  // a posts may have multiple categories
  // if at least one of them matches 'catName' include the post
	return posts.filter(p => p.categories.some(c => c.name === catName))
}
```

```js
const hasChanged = [
  !areArraysEqual(data.customElements, originalDoc?.customElements),
  !areArraysEqual(data.headHTML, originalDoc?.headHTML)
].some(item => item)
```

#### includes()

```js
arr.includes(value, fromIndex) // returns true if value is found within the array 
//(or the part of the array indicated by the index fromIndex, if specified)
```

### return an array

#### concat()

* merge two or more arrays
* does not change the existing arrays
* returns a new array

#### map()

* Calls the function for each element of the array and returns the array of results
* returns a new Array *of the same size*
* Preferable if you favor functional programming because it returns an entirely new Array — leaving the original array unchanged.
* Used for creating a new array from an existing one
* Remember to *include a return statement in your callback*. If you don't, you'll get a new array filled with undefined.
* Since map builds a new array, calling it without using the returned array is an anti-pattern; use forEach or for...of instead. 

```js
arr.map((item, index, arr) => console.log(item, index, array))
```

```js
// EXAMPLES:

let characters = ["Bilbo", "Gandalf", "Nazgul"]
characters.map(item => item.length) // Array(3) [ 5, 7, 6 ]
characters.map(item => item.toUpperCase()) // Array(3) [ "BILBO", "GANDALF", "NAZGUL" ]
```

#### flat()

* creates a new array with all sub-array elements concatenated into it recursively up to the specified depth. 

```js
const arr1 = [0, 1, 2, [3, 4]];
arr1.flat() // expected output: Array [0, 1, 2, 3, 4]
```

#### flatMap()

* returns a new array formed by applying a given callback function to each element of the array, and then flattening the result by one level.
* identical to a `map()` followed by a `flat()` of depth 1, but slightly more efficient than calling those two methods separately.

```js
const arr1 = [1, 2, [3], [4, 5], 6, []]
const flattened = arr1.flatMap(num => num)
console.log(flattened) // [1, 2, 3, 4, 5, 6]
```

#### fill()

changes all elements in an array to a static value, from a start index (default `0`) to an end index (default `array.length`).  It returns the modified array.

```js
arr.fill(value, start, end)
let arr = new Array(10).fill(6) 
// Array(10) [ 6, 6, 6, 6, 6, 6, 6, 6, 6, 6 ]
```

#### filter()

* ***returns a new array with all elements that pass the test*** implemented by the provided function
* Takes each element in an array and it applies a conditional statement against it. 
* If the condition returns true, the element gets pushed to the output array. 
* If the condition returns false, the element does not get pushed to the output array.
* You have to ***include a return statement which returns a boolean value***

```js
arr.filter(item => condition)
arr.filter(callback)

/* multiple tests */

let usaAndFree = inventory.filter(item => item.Location === "USA").filter(item => item.Shipping === "Free"); 
// the second line works on the array returned by the first filter
// multiple tests connected by AND:
let markAndEngland = users.filter(x => x.name == 'Mark' && x.address == 'England');

const students = [
  { name: 'Quincy', grade: 96 },
  { name: 'Jason', grade: 84 },
  { name: 'Alexis', grade: 100 },
  { name: 'Sam', grade: 65 },
  { name: 'Katie', grade: 90 }
]

// get all the students whose grades are >= 90
const studentGrades = students.filter(student => student.grade >= 90) 

// filter()
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length > 6); 
// --> Array(3) [ "exuberant", "destruction", "present" ]

function handleDelete(deleteId) {
	this.todoList = this.todoList.filter(todo => {
		return todo.id !== deleteId 
        // if todo.id doesn't match deleteId the corresponding todo is returned
	})
}

const includedPosts = posts.filter(post => {
	return post.categories.some(cat => includedCat.includes(cat.id)) 
  // select post if it has at least one category that matches with those in includedCat
})
```

#### slice()

* Returns a shallow copy of a portion of an array into a new array object selected from start to end 
* start includes, end not included 
* start and end represent the index of items in that array. 
* The original array will not be modified. 

```js
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];
console.log(animals.slice(2, 4)); // ["camel", "duck"]
console.log(animals.slice(2)); // ["camel", "duck", "elephant"]
```

#### splice() 

* changes the contents of an array by removing or replacing existing elements and/or adding new elements *in place*
* Returns an array containing the deleted elements

```js
arr.splice(start, deleteCount, item1, item2, itemN)
```

* *start*: 
  index at which to start changing the array
* *deleteCount*
  An integer indicating the number of elements in the array to remove from start. 
  If omitted all elements from start to the end of the array will be deleted
  If deleteCount is 0 or negative, no elements are removed. In this case, you should specify at least one new element (see below). 
* *item1*, *item2*: 
  The elements to add to the array, beginning from start.
  If you do not specify any elements, splice() will only remove elements from the array.

```js
const months = ['Jan', 'March', 'April', 'June'];
// just insert (at index 1), don't remove (0):
months.splice(1, 0, 'Feb');			// ["Jan", "Feb", "March", "April", "June"]
// replace 1 element at index 4
months.splice(4, 1, 'May'); 		// ["Jan", "Feb", "March", "April", "May"]
// just delete one item at index
months.splice(2, 1) 				// ["Jan", "Feb", "April", "May"]
```

### return a string

#### join()

creates and returns a new string by concatenating all of the elements in an array (or an array-like object), separated by commas or a specified separator string.  

```js
arr.join(separator)
let a = ['Wind', 'Water', 'Fire'];
a.join();      // 'Wind,Water,Fire'
a.join(', ');  // 'Wind, Water, Fire'
a.join(' + '); // 'Wind + Water + Fire'
a.join('');    // 'WindWaterFire'
```

#### toString()

* returns a string with array values separated by commas.
* does not change the original array.

### stack & queue

#### stack vs queue

* queue: `shift`/`unshift`
* stack: `pop`/`push`  
* stacks run fast, while queues are slow.

`fruits.shift();` # take 1 element from the start
The shift operation must do 3 things:

* Remove the element with the index 0.
* Move all elements to the left, renumber them from the index 1 to 0, from 2 to 1 and so on
* Update the `length` property

`fruits.pop();` # take 1 element from the end

* does not need to move anything, because other elements keep their indexes. That’s why it’s blazingly fast.
* The similar thing with the push method.

#### end: push / pop

array.push(item) # push items into an array (add item to the end of array); 

```js
  // returns the new array length:
  var fruits = ["Banana", "Orange", "Apple", "Mango"];
  var x = fruits.push("Kiwi"); // x is 5
```

### transform

* When we need to iterate over an array – we can use `forEach`, `for` or `for..of`.
* When we need to iterate and return the data for each element we use `map`

The difference is that map() utilizes return values and actually returns a new Array of the same size.

#### forEach()

* Calls a provided function on each element in your array. This callback is allowed to mutate the calling array.
* *returns undefined* - it doesn't chain with other array methods
* more performant than `map()` in terms of operations per second
* use it for side effects (if you do not need to use the returned array)

```js
arr.forEach((item, index, array) => {
	// ... do something with item
});

// ----- EXAMPLES

["Bilbo", "Gandalf", "Nazgul"].forEach(alert);
["Bilbo", "Gandalf", "Nazgul"].forEach((item, index, array) => {
    alert(`${item} is at index ${index} in ${array}`);
});

// array of DOM elements:

elements = Array.from(document.getElementsByClassName('class'))
elements.forEach( (item, index) => {
    // output the array contents:
    console.log(item + ' has index: ' + index)
    // style an item of the array:
    item.style.backgroundColor = "#92a8d1";
})

// array of strings:

var fruits = ["apple", "orange", "cherry"];
fruits.forEach(myFunction);

function myFunction(item, index) {
	document.getElementById("demo").innerHTML += index + ":" + item + "<br>";
}
```

### order

#### sort()

* sorts the elements of an array *in place*
* by default **sorts values as strings** ('apple' comes before 'banana')
* if numbers are sorted as strings, `sort()` will produce incorrect results

#### custom compare function

requires you to return certain values:

* `-1`: if the value on the left is less than the value on the right; then `a` is sorted before `b`
* `0`: if the value on the left is equal to the value on the right; then no changes are done with the sort order of the two values
* `1`: if the value on the left is greater than the value on the right; then `b` is sorted  before `a`

```js
// The compare function has the following form:
function compare(a, b) {
	if (a < b) {
    	return -1
  	}
  if (a > b) {
    return 1
  }
  // a must be equal to b
  return 0
}
```

```js
// To compare numbers instead of strings, the compare function can subtract b from a
function compareNumbers(a, b) {
    // a = 3, b = 5, return -2 --> a before b
    // a = 5, b = 3, return  2 --> b before a
    // a = 5, b = 5, return  0
  	return a - b // sort the array in ascending order (because if result is negative a will be sorted before b)
    return b - a // sort the array in descending order
}

[40, 100, 1, 5, 25, 10].sort((a, b) => a - b) // ascending order
[40, 100, 1, 5, 25, 10].sort((a, b) => a - b) // descending order
```

##### Fisher Yates Method

The above example, *array*.sort(), is not accurate, it will favor some numbers over the others.
The most popular correct method, is called the Fisher Yates shuffle, and was introduced in data science as early as 1938!
In JavaScript the method can be translated to this:

```js
const points = [40, 100, 1, 5, 25, 10];

for (let i = points.length -1; i > 0; i--) {
  let j = Math.floor(Math.random() * i)
  let k = points[i]
  points[i] = points[j]
  points[j] = k
}
```

##### examples

```js
/* EX 1 */

let scorecard = [
// an array of object that contains the scores of some players in a card game
	{ name: "Zahir", score: 23, age: 34},
  { name: "Kabir", score: 23, age: 15},
  { name: "Kunal", score: 42, age: 45}, 
  { name: "Arnav", score: 29, age: 64}
]

// sort by value
scorecard.sort((a, b) => {
  	return a.value - b.value
})

// sort by name
items.sort((a, b) => {
  	let nameA = a.name.toUpperCase(); // ignore upper and lowercase
  	let nameB = b.name.toUpperCase(); // ignore upper and lowercase
  	if (nameA < nameB) return -1
  	if (nameA > nameB) return 1

  	// names must be equal
  	return 0;
})

/* EX 2 */

// We also have a variable by the name of selfName that contains the name of a particular player:
const selfName = 'Arnav'

// We are required to write a function that sorts the scorecard array in alphabetical order and makes sure the object with name same as selfName appears at top (at index 0).

function sorter(a, b) {
    if(a.name === selfName) return -1 //  a is sorted before b
    if(b.name === selfName) return 1  // b is sorted before a
   
   	return a.name < b.name ? -1 : 1;
}
```

```js
function sortArrayByObjValue(arr, prop) {
    // sorts an array of objects by a given properties value
    	return arr.sort((a, b) => {
        	return a[prop] - b[prop]
	})
}
```

```js
function sortArrayByObjValue(arr = [], prop = '', order = '') {
  // sorts an array of objects by a given properties value
	return arr.sort((a, b) => {
    switch (order) {
			case 'ascending':
      case 'asc':
      case '':
				return (a[prop] > b[prop]) ? -1 : 1
				break;
		case 'descending':
    case 'desc':
        return (a[prop] > b[prop]) ? 1 : -1
				break;  		
		}
	})
}
```

#### reverse()

reverses an array *in place*

instance properties
-----------------------------------------------------------------------------------------

### length

```js
let fruits = ['apple', 'banana', 'orange']
fruits.length // 3
fruits[fruits.length] = "Kiwi" // appends "Kiwi" to fruits 
fruits[fruits.length - 1] // accesses the last item of the array
```

#### A word about `length`

The length property automatically updates when we modify the array.  
To be precise, it *is actually not the count of values in the array, but the greatest numeric index plus one*.  
For instance, a single element with a large index gives a big length:

```js
let fruits = []
fruits[123] = "Apple"
alert(fruits.length) // 124
```

Another interesting thing about the length property is that *it’s writable*.  
If we increase it manually, nothing interesting happens. But if we decrease it, the array is truncated. The process is irreversible, here’s the example:

```js
let arr = [1, 2, 3, 4, 5]
arr.length = 2 // truncate to 2 elements
alert( arr ) // [1, 2]
arr.length = 5 // return length back
alert( arr[3] ) // undefined: the values do not return
```

So, the simplest way to clear the array is: `arr.length = 0`

ASYNC
===================

## async in js

JavaScript can only do one thing at a time. Any "async" code isn't  async in the same sense as it is in languages that support  multi-threading and can be executing different code at the same time in  different threads. When you write async code in JavaScript, it's more  like scheduling something to be executed synchronously when the browser  gets a chance.  

When you use `await`, it pauses execution of code there  and hands over control to whatever else is scheduled to execute at that  point, and it picks up execution at that point again later on.  

## async vs sync


Well, there are two very important aspects of *synchronous* functions:

* They return values
* They throw exceptions

Both of these are essentially about composition. That is, you can feed the return value of one function straight into another, and keep doing this indefinitely. More importantly, if at any point that process fails, one function in the composition chain can throw an exception, which then bypasses all further compositional layers until it comes into the hands of someone who can handle it with a `catch`.

Now, in an *asynchronous* world, ***you can no longer return values***: they simply aren’t ready in time. Similarly, ***you can’t throw exceptions***, because nobody’s there to catch them. So we descend into the so-called “callback hell,” where composition of return values involves nested callbacks, and composition of errors involves passing them up the chain manually.

The point of promises is to give us back *functional composition* and *error bubbling* in the async world. They do this by saying that your functions should return a promise, which can do one of two things:

* Become fulfilled by a value
* Become rejected with an exception

In other words, the following asynchronous code:

```js
getTweetsFor("domenic") // promise-returning function
    .then(function (tweets) {
        var shortUrls = parseTweetsForUrls(tweets);
        var mostRecentShortUrl = shortUrls[0];
        return expandUrlUsingTwitterApi(mostRecentShortUrl); // promise-returning function
    })
    .then(httpGet) // promise-returning function
    .then(
        function (responseBody) {
            console.log("Most recent link text:", responseBody);
        },
        function (error) {
            console.error("Error with the twitterverse:", error);
        }
    );
```

parallels the synchronous code:

```js
try {
    var tweets = getTweetsFor("domenic"); // blocking
    var shortUrls = parseTweetsForUrls(tweets);
    var mostRecentShortUrl = shortUrls[0];
    var responseBody = httpGet(expandUrlUsingTwitterApi(mostRecentShortUrl)); // blocking x 2
    console.log("Most recent link text:", responseBody);
} catch (error) {
    console.error("Error with the twitterverse: ", error);
}
```

* Note in particular how errors flowed from any step in the process to our `catch` handler, without explicit by-hand bubbling code.

### return values from async functions

```js
async function printThis(statement) {  
  console.log(statement)
  return true
} 

const ret = printThis("hello world")
console.log(ret) // hello world // Promise { true }

/* If you are interested in the return value from an async function, just wait till the promise resolves. 
Use then or wrap function in await. */

async function printThis(statement) {
  console.log(statement)
  return true
}

const ret = printThis("hello world").then(ret => console.log(ret))
// hello world
// true
```

promises
-------------

### overview

Promises represent ***values that don’t exist at the moment of the computation but that may be available later.***

```js
let p = new Promise((resolve, reject) => {
  	// executor (the producing code)
    setTimeout(() => resolve("done"), 1000) // resolve
})

let p = new Promise((resolve, reject) => {
  	// executor (the producing code)
    setTimeout(() => reject(new Error("Whoops!")), 1000) // reject
})

/* using .then */
p.then(result => {
    console.log('result: ', result) // "done"
}, error => {
    console.log('error: ', error)
})

/* using .then and .catch */
p.then(result => {
    console.log('result: ', result) // "done"
}).catch(error => {
    console.log('error: ', error)
})

/* async myFunction() always returns a promise */

async getAllDocsFromDB(db) {
    let allDocs = []

    db.allDocs({include_docs: true}).then(response => {
        for (let row in response.rows) {
            allDocs.push(response.rows[row].doc)
        }
    })
    return allDocs // a promise
}
```

### executor

The function passed to new Promise is called the *executor*.  
When new Promise is created, the executor runs automatically. It contains the producing code which should eventually produce the result.

Its arguments *resolve* and *reject* are callbacks provided by JavaScript itself. Our code is only inside the executor.  
When the executor obtains the result, it should call one of these callbacks:

* `resolve(value)`  # if the job finished successfully, with result value.
* `reject(error)`   # if an error occurred, error is the error object.

The executor runs automatically and attempts to perform a job.  
When it is finished with the attempt it calls *resolve* if it was successful or *reject* if there was an error.

```js
let promise = new Promise((resolve, reject) => {
	// the function is executed automatically when the promise is constructed
  	setTimeout(() => resolve("done"), 1000) // resolve
    setTimeout(() => reject(new Error("Whoops!")), 1000) // reject
});
```

### consumers: then, catch, finally

A Promise object serves as a link between the executor and the consuming functions, which will receive the result or error. Consuming functions can be registered (subscribed) using methods `.then`, `.catch` and `.finally`.

#### .then

```js
promise.then(
  function(result) { 
      /* handle a successful result */ 
  },
  function(error) {
      /* handle an error */ 
  }
)

/* arrow syntax */
p.then(result => {
    console.log('result: ', result) // "done"
}, error => {
    console.log('error: ', error)
})

/* using .then and .catch */
p.then(result => {
    console.log('result: ', result) // "done"
}).catch(error => {
    console.log('error: ', error)
})
```

* **First argument** of .then is a function that runs when the promise is resolved, and receives the result.
* **Second argument** of .then is a function that runs when the promise is rejected, and receives the error.

#### .catch

```js
// If we’re interested only in errors, then we can use `null` as the first argument:
p.then(null, f)
// Or we can use...
p.catch(f) 
// ...which is exactly the same:
```

The call `.catch(f)` is a complete analog of `.then(null, f)`, it’s just a shorthand.

You can always attach a catch function to the end of a big promise chain, and any errors that occur along the way will show up at the end. This avoids endless `if (err) {}` checking in the callback world.

#### .finally

Just like there’s a finally clause in a regular try {...} catch {...}, there’s finally in promises.  
The call `.finally(f)` is similar to `.then(f, f)` in the sense that f always runs when the promise is settled: be it resolve or reject.

finally is a good handler for performing cleanup, no matter what the outcome is.

#### We can attach handlers to *settled* promises

If a promise is pending, .then/catch/finally handlers wait for it. Otherwise, if a promise has already settled, they just run:

```js
    // the promise becomes resolved immediately upon creation
    let promise = new Promise(resolve => resolve("done!"));
    promise.then(alert); // done! (shows up right now)
```

Note that this makes promises more powerful than the real life “subscription list” scenario. If the singer has already released their song and then a person signs up on the subscription list, they probably won’t receive that song. Subscriptions in real life must be done prior to the event.

Promises are more flexible. *We can add handlers any time*: if the result is already there, they just execute.

### examples

```js
function loadScript(src) {
  return new Promise(function(resolve, reject) {
    let script = document.createElement('script');
    script.src = src;

    script.onload = () => resolve(script);
    script.onerror = () => reject(new Error(`Script load error for ${src}`));

    document.head.append(script);
  });
}

// usage:
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
  script => alert(`${script.src} is loaded!`),
  error => alert(`Error: ${error.message}`)
);

promise.then(script => alert('Another handler...'));
```

async / await
-----------------

### async/await

```js
async function sayHelloWithDelay(name) {
    return await new Promise((resolve) => setTimeout(() => resolve('Hello')))
}
```

```js
async function simulateAPIrequest(id = '') {
    return await new Promise((resolve) => setTimeout(() => resolve(`Got id "${id}"`)))
}
```

```js
async function myFunction() {
  let result = await somethingThatReturnsAPromise();
  console.log(result); // cool, we have a result
}

/* using try/catch */

async function myFunction() {
  	try {
    	let fulfilledValue = await somethingThatReturnsAPromise()
    	// if the promise resolves, we can immediately interact with it on the next line
  	} catch(err) { 
		// if the promise is rejected we can catch it here
    	console.log(err)
  }
}

/* return a promise */

async function resolveXAfterY(x, y) {
	const result = await new Promise((resolve) => {
    	setTimeout(() => resolve(x), y)
    })
    return result
}

/* detailed explanation */

async function foo() {
   const result1 = await new Promise((resolve) => setTimeout(() => resolve('1')))
   const result2 = await new Promise((resolve) => setTimeout(() => resolve('2')))
}
foo()

/* 1. The first line of the body of function foo is executed synchronously, with the await expression configured with the pending promise. Progress through foo is then suspended and control is yielded back to the function that called foo. */

/* 2. Some time later, when the first promise has either been fulfilled or rejected, control moves back into foo. The result of the first promise fulfillment (if it was not rejected) is returned from the await expression. Here 1 is assigned to result1. Progress continues, and the second await expression is evaluated. Again, progress through foo is suspended and control is yielded. */

/* 3. Some time later, when the second promise has either been fulfilled or rejected, control re-enters foo. The result of the second promise resolution is returned from the second await expression. Here 2 is assigned to result2. Control moves to the return expression (if any). The default return value of undefined is returned as the resolution value of the current promise. */
```

#### async

- special syntax to work with promises in a more comfortable fashion
- only mark a function `async` if you need to use `await` inside the function (most of the time)
- `async` before a function means one simple thing: **always return a promise**. 
  other values are wrapped in a resolved promise automatically.
- The return value of an async function is implicitly wrapped in `Promise.resolve` - if it's not already a promise itself
- an async function without an `await` expression will run synchronously. 
  If there is an await expression inside the function body, however, the async function will always complete asynchronously.


```js
async function giveMeOne() {
    return 1;
}
giveMeOne().then(alert)

// ... is the same as:

async function giveMeOne() {
	return Promise.resolve(1)
}
myFunction().then(alert) // 1
```

An `async` function **always** returns a promise:

- When no return statement defined, or a return statement without a value, it returns a resolving promise, equivalent to `return Promise.Resolve()` 
- When a return statement is defined with a value, it will return a resolving promise with the given return value, equivalent to `return Promise.Resolve("My return String")`
- When an error is thrown, a rejected promised will be returned with the thrown error, equivalent to `return Promise.Reject(error)` 

#### await

* suspends the function execution until the promise settles, and then resumes it with the promise result. 
  The JavaScript engine can do other jobs in the meantime: execute other scripts, handle events, etc.
* It’s just a more elegant syntax of getting the promise result than `promise.then`, easier to read and write.
* **the code after each `await` expression can be thought of as existing in a `.then`  callback**
* **use `await` for sequencing multiple asynchronous operations**
* It can only be used inside an async function within regular JavaScript code; however it can be used on its own with JavaScript modules.

```js
// works only inside async functions
let value = await promise
console.log(value)

// ES 6
promise.then(result => )
```

##### places where it's pointless to use `await`

```js
async function getKey(someArg) {
    let key = await getFromDatabase(someArg);
    return key;
}
```

The `await` here isn't doing anything useful. You're not sequencing multiple `async` operations and you're not doing any processing on the return value. You can accomplish the exact same code by just returning the promise  directly:

```js
async function getKey(someArg) {
    return getFromDatabase(someArg);
}
```

And, if you know that `getFromDatabase()` never throws synchronously, you can even remove the `async` from the declaration:

```js
function getKey(someArg) {
    return getFromDatabase(someArg);
}
```

#### error handling

* automatically catching both rejected promises and synchronous exceptions

#### async execution order

```js
async function sequentialStart() {
    console.log('==SEQUENTIAL START==')
    // 1. Execution gets here almost instantly
    const slow = await resolveAfter2Seconds()
    console.log(slow) // 2. this runs 2 seconds after 1.

    const fast = await resolveAfter1Second()
    console.log(fast) // 3. this runs 3 seconds after 1.
}

async function concurrentStart() {
    console.log('==CONCURRENT START with await==');
    const slow = resolveAfter2Seconds() // starts timer immediately
    const fast = resolveAfter1Second() // starts timer immediately

    // 1. Execution gets here almost instantly
    console.log(await slow) // 2. this runs 2 seconds after 1.
    console.log(await fast) // 3. this runs 2 seconds after 1. 
    // and immediately after 2. (since fast is already resolved)
}

async function parallel() {
    console.log('==PARALLEL with await Promise.all==')

    // Start 2 "jobs" in parallel and wait for both of them to complete
    await Promise.all([
        (async()=>console.log(await resolveAfter2Seconds()))(),
        (async()=>console.log(await resolveAfter1Second()))()
  ])
}
```

### comparison with promises

```js
/* CREATE PROMISE: ES6 */

function resolveXAfterY(x, y) {
    return new Promise(resolve => {
        setTimeout(() => resolve(x), y)
    })
}

/* CREATE PROMISE: ES7 */

async function resolveXAfterY(x, y) {
	const result = await new Promise(resolve => {
    	setTimeout(() => resolve(x), y)
    })
    return result
}

/* HANLDE PROMISE: ES6 */

// style 1
let p = resolveXAfterY(10, 2000)
p.then(result => console.log('resolved: ', result)))
// style 2
resolveXAfterY(6, 2000).then(result => console.log('resolved: ', result))

/* HANLDE PROMISE: ES7 */

async function f1() {
    var result = await resolveXAfterY(10, 2000);
    console.log(result) // 10
}
f1()
```

#### ex 1

```js
/* promise chain */
function getProcessedData(url) {
	return downloadData(url) // returns a promise
    	.catch(e => {
        	return downloadFallbackData(url)  // returns a promise
    	})
        .then(v => {
        	return processDataInWorker(v)  // returns a promise
    })
}
/* async */
async function getProcessedData(url) {
	let v
    try {
    	v = await downloadData(url)
    } catch(e) {
        v = await downloadFallbackData(url)
    }
    return processDataInWorker(v)
}
```

#### ex2

```js
var db = new PouchDB('mydb')

/* using .then */

db.post({}).then(result => { 
    // post a new doc
    return db.get(result.id)          // fetch the doc
}).then(doc => {
    console.log(doc)                  // log the doc
}).catch(err => {
    console.log(err)                  // log any errors
})

/* using .async */

try {
    let result = await db.post({})
    let doc = await db.get(result.id)
    console.log(doc)
} catch(err) {
    console.log(err)
}
```

#### ex3

```js
/* Here we want to get() a document by _id if it exists, 
or return a new document if it doesn't */

/* using .then */

db.get('docid').catch(err => {
    if (err.name === 'not_found') {
        return {} // new doc
    }
    throw err // some error other than 404
}).then(doc => {
    console.log(doc)
})

/* using .async */

let doc
try {
    doc = await db.get('docid');
} catch (err) {
    if (err.name === 'not_found') {
        doc = {};
  } else {
        throw err; // some error other than 404
  }
}
console.log(doc);
```

### loops

#### async map()

```js
async function sayHelloWithDelay(name) {
    return await new Promise((resolve) => setTimeout(() => resolve('Hello')))
}

let ids = ["id_1", "id_2", "id_3"];
let dataById = ids.map(async (id) => {
	const data = await simulateAPIrequest(id)
  return { id, data }
})

await Promise.all(dataById)

// compact version

await Promise.all(ids.map(async (id) => {
  const data = await simulateAPIrequest(id);
  return { id, data };
}))
```



Let's say that we want to insert some documents into the database, but sequentially. That is, we want the promises to execute one after the other, not concurrently.

#### using ES6 promises

```js
// Here we have to roll our own promise chain:

var p = Promise.resolve()
var docs = [{}, {}, {}]

docs.forEach(doc => {
    p = p.then(() => {
        return db.post(doc)
    })
})

p.then(() => {
    // now all our docs have been saved
})

// if you do...
docs.forEach(doc => {
    p = p.then(db.post(doc));
})
// ...then the promises will actually execute concurrently, which can lead to unexpected results.
```

#### using ES7

```js
// With ES7, though, we can just use a regular for-loop:

let docs = [{}, {}, {}];

for (let i = 0; i < docs.length; i++) {
    let doc = docs[i]
    await db.post(doc)
}
```

### patterns

#### retry

```js
function wait(timeout) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve()
    }, timeout);
  });
}

async function requestWithRetry(url) {
  const MAX_RETRIES = 10;
  for (let i = 0; i <= MAX_RETRIES; i++) {
    try {
      return await request(url);
    } catch (err) {
      const timeout = Math.pow(2, i);
      console.log('Waiting', timeout, 'ms');
      await wait(timeout);
      console.log('Retrying', err.message, i);
    }
  }
}
```

#### intermediate values

Three asynchronous functions depend on each other the following way:

* functionA returns a Promise
* then functionB needs that value 
* functionC needs the resolved value of both functionA‘s and functionB‘s Promise

```js
async function executeAsyncTask () {
  const valueA = await functionA();
  const valueB = await functionB(valueA);
  return function3(valueA, valueB);
}
```

#### parallel requests

execute several asynchronous tasks at once and then use their values at different places

```js
async function executeParallelAsyncTasks () {
  const [ valueA, valueB, valueC ] = await Promise.all([ functionA(), functionB(), functionC() ]);
  doSomethingWith(valueA);
  doSomethingElseWith(valueB);
  doAnotherThingWith(valueC);
}
```



### gotchas

* Be careful to wrap your code in try/catches, or else a promise might be rejected, in which case the error is silently swallowed. (!)

CLASSES & PROTOTYPES
=====================================

Classes
--------------------------------------

* Class syntax is syntactical sugar — behind the scenes, it still uses a prototype-based model. 
* *Classes are functions, and functions are objects in JavaScript*.
* the body of a class is executed in *strict mode*
* The `constructor()` method is called automatically by `new`, so we can initialize the object there.
* Only the properties that are *constructed* are part of the instantiated object itself. All the rest belongs to the prototype

```js
class MyClass {
	
  // static properties
	static prop1 = 'static property'
	
  // private properties
	#privateProperty
	
  // instance properties (that don't depend on the constructor)
	prop2 = 1
	prop3 = 2
	prop4 /* This one is a property that this class will have - I don't need to look at the constructor to know about it */

	// the constructor is only about *constructing* the object
	constructor(someArg) {
		this.prop4 = someArg
	}

	// instance methods (are separated from the rest of the properties and construction logic)
	method1 = () => {}
	method2() { ... }
	
	// static methods
	static method3 = () => {}
}
```

### static initialization block

A class can have any number of `static {}` initialization blocks in its class body. 
These are evaluated, along with any interleaved static field initializers, in the order they are declared.

The scope of the static block is nested within the lexical scope of the class body, and can access the private instance variables of the class.

```js
class ClassWithSIB {
  static field = 'A static field'
  static {
    console.log(this.field) // access a public static field from within the static block
  }
}

```

### static fields

* static fields: useful for caches, fixed-configuration, or  any other data you don't need to be replicated across instances
* static methods: useful for utility functions for an application

```js
class myClass {
  constructor() {
    
  }

  static staticProperty = 'static property'
  static staticMethod() {
    console.log('static method has been called.')
  }
  
  instanceMethod() {
    myClass.staticMethod()
  }
}
```

#### the value of this in static methods

```js
class Animal {
  static eat() {
    return this // the Animal class
  }
  speak() {
    return this // the Animal object
  }
}
```

### public instance fields

instance fields exist on every created instance of a class. 

* By declaring fields up-front, class definitions become more self-documenting, and the fields are always present.
* instance fields of a class are added before the respective constructor runs - so you can access the fields' values within the constructor.

```js
class Rectangle {
  height = 0 // default value
  width
  constructor(height, width) {
    this.height = height
    this.width = width
  }
}
```

### private instance fields

* **Note:** Private fields can only be declared up-front in a field declaration
* By defining private fields, you ensure that your classes' users can't depend on internals, which may change from version to version.

```js
class User {
	#password // can only be accessed from within the class
	#readable = false
	#writeable = true

	constructor() {
		// code
	}

	get password() {
		if (!this.#password) console.log('No password set')
		if (this.#readable) return this.#password
		else console.log('Password is not allowed to read')
	}

	set password(value) {
		if (value.length < 8) {
			console.log('too short')
			return
		}
		if (value.includes('#')) {
			console.log('# is a forbiden character')
			return
		}
		if (this.#writeable) this.#password = value
	}
}
let udo = new User()
udo.#private // error
udo.private // works 
udo.private = ''

```

### attention & troubleshooting

#### automatic strict mode

* As the body of a class is executed in *strict mode* you have to *declare variables before using them*!

```js
class Trigger {
	// ...
	someMethod() {
		// wrong:
		for (el of this.elements) { // el is not defined!!!
			console.log(el);
		}
		// right:
		for (let el of this.elements) {
			console.log(el);
		}
	}
}
```

### instanceof

To find out whether an object is an instance of another one. We can use instanceof:

```js
object instanceof Class // gives true or false
```

### inheritance: extends & super

* Using extends means *adding depth to the protoype chain*.
* One benefit is that *when you need to change a method or property that multiple classes share, you can change the parent class, instead of each subclass*.


```js
class Child extends Parent { 	// makes the methods of the parent class available inside the child class.  
	constructor(sharedProp, ownProp) {
		super(sharedProp);		// calls the constructor of the parent class and hands over its arguments
		this.ownProp = ownProp	// In a constructor(), 'super' must always be called before 'this'
	}

	super.method(arguments);	// access the parent’s methods.
}

// ex:

class Cat extends Animal {
	constructor(name, usesLitter) {
		super(name); // arguments shared with parent class
		this._usesLitter = usesLitter; // argument specific to cat class
  	}
}

```

Getters & setters
----------------------------------------------------------

### Two kinds of object properties

* *data properties*: All properties that we’ve been using until now were data properties.
* *accessor properties*: They are essentially functions that execute on getting and setting a value, but look like regular properties to an external code.

### Accessor properties

From the outside, an accessor property looks like a regular one. That’s the idea of accessor properties.  

* `get` binds an object property to a function that will be called when that property is looked up.
* We don’t call a getter as a function, we read it normally: the getter runs behind the scenes.
* read-only properties: **If you define just a getter for a property and no setter, then the property becomes read-only**

```js
let obj = {
	get propName() {
		// getter, the code executed on getting obj.propName
	},
	set propName(value) {
		// setter, the code executed on setting obj.propName = value
	}
};

// ex1

const language = {
  	set current(name) {
    	this.log.push(name);
  	},
  	log: []
}

language.current = 'EN';
console.log(language.log); // ['EN']

language.current = 'FA';
console.log(language.log); // ['EN', 'FA']
```

```js
// ex2
class User {	
	constructor(name, surname) {
		this.name = name
		this.surname  = surname
	}
	// using a getter
	get fullName() {
		return `${this.name} ${this.surname}`;
	}
	// using a method
	getFullName() {
		return `${this.name} ${this.surname}`;
	}
}
alert(user.fullName); // John Smith
alert(user.getFullName()) // John Smith
```

### wrap data properties with accessors

Getters/setters can be used as wrappers over “real” property values to gain more control over operations with them.

> “public access interfaces” for accessing private *variables* or *fields* of objects

```javascript
// If we want to forbid too short names for `user`, we can have a setter `name` and keep the value in a separate property `_name`:
let user = {
  get name() {
    return this._name;
  },

  set name(value) {
    if (value.length < 4) {
      alert("Name is too short, need at least 4 characters");
      return;
    }
    this._name = value;
  }
};

user.name = "Pete";
alert(user.name); // Pete

user.name = ""; // Name is too short...
```

Usage: Arrays, Objects, Classes
-------------------------------------------

#### an array of objects

```js
// create:
users = [
	{name: "John", age: 30},
	{name: "Fred", age: 32}
]
// retrieve:
users[1].name // Fred
// add:
users.push({name: "Linda", age: 23})
```

#### an array of objects of a class

```js
class User {
	constructor(name, age) {
		this.name = name;
		this.age = age;
	}
}
users = []
users.push(new User("John", 35))
```

### container class

#### ex: Players/Player

```js
class Player {
// An individual player. Holds properties and behavior for one player
	constructor(name) {
		this.name = name;
	}
	play() {
		console.log(this.name, "plays")
	}
}

class Players {
// Class that holds a collection of players and properties and functions for the group
	constructor(){
		this.players = []
	}
	// create a new player and save it in the collection
	newPlayer(name){
		let p = new Player(name)
		this.players.push(p)
		return p
	}
	get allPlayers(){
		return this.players
	}
	// this could include summary stats like average score, etc. For simplicy, just the count for now
	get numberOfPlayers(){
		return this.players.length
	}
}

let league = new Players()
league.newPlayer("Mark")
league.newPlayer("Roger")

// list all the players
console.log(league.numberOfPlayers + " Players)
console.log(league.allPlayers)

// make them do something
league.allPlayers.forEach(player => player.play())
```

#### ex: Family/Members

```js
class Family {
	
	members = []
  	
	  constructor(family) {
    	this.family = family
  	}

	addMember(sex, name) {
		if (sex === 'male') this.members.push(new Son(name))
		if (sex === 'female') this.members.push(new Daughter(name))
		console.log(`${sex} family member added with name: ${name}`)
	}

	removeMember(name) {
		this.members = this.members.filter(item => item.name !== name)
	}
}

class Daughter {
	sex = 'female'
	constructor(name) {
		super()
		this.name = name
	}
}

class Son {
	sex = 'male'
	constructor(name) {
		super()
		this.name = name
	}
}

let nonner = new Family('nonner')
```

Prototypes
--------------------------------------------------------------------------------

```js
// constructor
function Cat(name) {
	this.name = name
}

Cat.prototype.meow = function meow() {
	console.log(this.name + 'meow !')
}

let hector = new Cat('Hector')
hector.meow() // Hector meow !

// --- using PROTOTYPES ---

function Book(name) { 
   this.name = name
}
function newBook(name) {
   Book.call(this, name)
}
newBook.prototype = Object.create(Book.prototype);
const book1 = new newBook("The Alchemist");

// --- using CLASSES ---

class Book {
   constructor(name) {
      this.name = name
   }
}
class newBook extends Book { 
   constructor(name) {
      super(name);
   }
}
const book1 = new newBook("The Alchemist");
```

### classes vs prototypes

A class defines all of the properties that characterize a set of objects.
An instance has the same properties as its parent.
A prototype based language like JavaScript does not have this distinciton. It just has objects.

One of the things that differs a lot from how it would work with ‘classical’ classes is that *prototypes are references*. 

```js
// If you create a lot of cats and do on one of them
delete cat.__proto__.meow
// ...you would remove the capacity to meow to all your cats, because they all share the same reference to the Cat prototype.
```


### performance

Der Zugriff auf Eigenschaften, die sich weit oben in der Prototypenkette befinden, kann negativen Einfluss auf die Performance haben. Beim Iterieren über die Eigenschaften von Objekten wird über jede aufzählbare Eigenschaft iteriert, die auf der Prototypenkette vorkommt. Um zu prüfen, ob ein Objekt eine Eigenschaft an sich selbst definiert hat und nicht von der Prototypenkette geerbt hat, muss die Funktion `hasOwnProperty` benutzt werden, die alle Objekte vom Object.prototype erben.

**`hasOwnProperty` ist das einzige Konstrukt in JavaScript das beim Umgang mit Eigenschaften von Objekten nicht die Prototypenkette durchläuft.**

### inheriting properties

Objekte in JavaScript sind dynamische "Behälter" von Eigenschaften, welche auch seine "eigenen Eigenschaften" (own properties) genannt werden. JavaScript-Objekte haben eine Verknüpfung zu einem Prototypobjekt. Beim Versuch auf eine Objekteigenschaft zuzugreifen, wird die Eigenschaft nicht nur in dem Objekt selbst, sondern auch in seinem Prototyp, dem Prototyp des Prototyps, usw. gesucht. Dies wird so lange fortgesetzt, bis eine Eigenschaft mit entsprechendem Namen gefunden wurde oder das Ende der Prototypenkette erreicht ist.

## Mixins

* https://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/

> "A mixin is **a class containing methods that can be used by other classes without a need to inherit from it**." - Wikipedia

A *mixin* provides methods that implement a certain behavior, but we do not use it alone, we use it to add the behavior to other classes.

> A mixin is an abstract subclass; i.e. a subclass definition that may  be applied to different superclasses to create a related family of  modified classes.

JS = single-inheritance

Some other languages allow multiple inheritance. **JS does not support multiple inheritance**, but mixins can be implemented by copying  methods into prototype.

> "I would argue all inheritance should be mixin inheritance - subclassing is just a degenerate form of mixin application" - Justin Fagnani

We can use mixins as a way to augment a class by adding multiple behaviors, like event-handling as we have seen above.

### issues

#### name collisions

Mixins may become a point of conflict if they accidentally overwrite existing class methods. So generally one should think well about the  naming methods of a mixin, to minimize the probability of that happening.

#### constructor()

Since a mixin likely does not know what superclass it's being applied to, and therefore its super-constructor signature, calling `super()` can be tricky. The best way to deal with this is to always pass along all constructor arguments to `super()`, either by not defining a constructor at all, or by using the spread operator: `super(...arguments)`.

This means that passing arguments specifically to a mixin's  constructor is difficult. One easy workaround is to just have an  explicit initialization method on the mixin if it requires arguments.

#### super()

One of the biggest benefits is that `super` works inside methods of the subclass and the mixins. Since we don't ever over*write* methods on classes or mixins, they're available for `super` to address.

`super` calls can be a little unintuitive for those new to mixins because the superclass isn't known at mixin definition, and  sometimes developers expect `super` to point to the declared superclass (the parameter to the mixin), not the mixin application.

* two mixins can define the same method, and as long as they call `super`, both of them will be invoked then applied.

### definition & application

- ***mixin definition***: The definition of a class that may be applied to different superclasses.
  The mixin definition is really a *subclass factory*,  parameterized by the superclass, which produces mixin applications.
- ***mixin application***: The application of a mixin definition to a specific superclass, producing a new subclass.
   A mixin application sits in the inheritance hierarchy between the subclass and superclass.

The real, and only, difference between a mixin and normal subclass is  that a normal subclass has a fixed superclass, while a mixin definition  doesn't yet have a superclass. Only the *mixin applications* have their own superclasses. You can even look at normal subclass  inheritance as a degenerate form of mixin inheritance where the  superclass is known at class definition time, and there's only one  application of it.

### mixins through class expressions

We rely on two features of JavaScript classes:

1. `class` can be used as an expression as well as a  statement. 
   As an expression it returns a new class each time it's  evaluated. (sort of like a factory!)
2. The `extends` clause accepts arbitrary expressions that return classes or constructors.

The key is that classes in JavaScript are first-class: they are values that can be passed to and returned from functions.

### expressions vs declarations

Basically, just like you can create a function with an expression:

```js
function myFunction() {}      // function declaration
var myFunction = function(){} // function expression
```

you can do the same with classes:

```js
class MyClass {}             // class declaration
var MyClass = class {}       // class expression
```

* The **expression is evaluated at runtime**
* The **declaration is executed beforehand**

### example

```js
// mixin definition
function LoggingMixin(superClass) {
	return class Logging extends superClass {
		constructor() {
			super();
			console.log(`${this.tagName} was created`);
		}
	}
}
```

The cool thing about it is that you can define the whole class beforehand and only decide on which class it should extend by the time you call the function:

```js
class A {}
class B {}
// mixin application
var ExtendingA = LoggingMixin(A)
var ExtendingB = LoggingMixin(B)
```

If you want to mix multiple classes together, because ES6 classes only  support single inheritance, you need to create a chain of classes that  contains all the classes you want to mix together.

So let's say you want to create a class C that extends both A and B, you could do this:

```js
class A {}
class B extends A {}
class C extends B {}  // C extends both A and B
```

The problem with this is that it's very static. If you later decide  you want to make a class D that extends B but not A, you have a problem.

But with some smart trickery using the fact that classes can be  expressions, you can solve this by creating A and B not directly as  classes, but as class factories (using arrow functions for brevity):

```js
class Base {} // some base class to keep the arrow functions simple
var A = (superclass) => class A extends superclass
var B = (superclass) => class B extends superclass
var C = B(A(Base))
var D = B(Base)
```

Notice how we only decide at the last moment which classes to include in the hierarchy.

### naming

Mixins in this project are named with an initial capital: `SampleMixin`, not `sampleMixin`. The initial capital is intended to suggest their status as partial classes.

The mixin name should generally end in "Mixin". By convention, the mixin's name *without* the "Mixin" part should be used as the name of the returned class:

```js
const SampleMixin = (base) => class Sample extends base { ... }
```

Here, `SampleMixin` is the visible name of the function that can be applied to a class to get back an extended class. The class name `Sample` is actually not required by JavaScript, but is helpful for debugging.  It allows the debugger to show you a meaningful name when you inspect  the component's prototype chain at runtime.

CONTROL FLOW
==============

Conditional
------------------------------

### if ... else if ... else

```js
// long version
if (condition) {
	// statements1
} else if (condition) {
	// statements2
} else {
	// statements3
}

// short version
if (condition) // statements1
else if (condition) // statements2
else // statements3
```

#### shortening tricks

```js
// return [long]:
function sumTo(n) {
  if (n == 1) {
    return n
  } else {
  	return n + sumTo(n - 1);
  }
}

// return [short]:
function sumTo(n) {
  if (n == 1) return n;
  return n + sumTo(n - 1);
}

// reverse boolean [long]:
if (arr[i] === false) arr[i] = true;
else if (arr[i] === true) arr[i] = false;

// reverse boolean [short]:
arr[i] = !arr[i];
```

### switch

* can replace multiple `if` checks.
* gives a more descriptive way to compare a value with multiple variants
* any expression can be a switch/case argument

```js
switch(x) {
	case 'value1':  // if (x === 'value1')
		// code
		break
	case 'value2':  // if (x === 'value2')
 	 	// code
		break
	default:
		// code
		break
}

// Tipp: Use curly braces inside JavaScript switch statements:
function reducer(state = {}, action) {
	switch (action.type) {
		case "SET_PROPERTY": {
			let { key, value } = action.payload;
			return Object.assign({}, state, { [key]: value });
		}
		case "REMOVE_PROPERTY": {
			let { key } = action.payload;
			state = Object.assign({}, state);
			delete state[key];
			return start;
		}
	}
}
```

* The value of x is checked for a strict equality to the value from the first case (value1) then to the second (value2) and so on.
* If the equality is found, switch starts to execute the code starting from the corresponding case, until the nearest break (or until the end of switch).
* If there is no `break` then the execution continues with the next case without any checks.
* If no case is matched then the default code is executed (if it exists).


Tests/Logic
--------------------------------------------

### collection of tests

#### type

```js
if (Math.trunc(n) != n) // integer?

if (Array.isArray(a)) 				// array?
if (typeof u === 'undefined') // undefined?
if (typeof s === "string") 		// string?
if (typeof n === 'number') 		// number?
if (typeof b === "boolean") 	// boolean?
if (typeof f === "function") 	// function?

if (!arr.length) // not empty?
  
/* STRING = NUMBER? */
if (isNaN('12')) 
  console.log('is not a number')
} else {
  console.log('is a number')
}
    
/* THROW ERRORS */
if (!prop || !arr) throw ReferenceError('argument missing')
if (!Array.isArray(arr)) throw TypeError('arr is not an array')

/* CONVERSION */
if (!Array.isArray(prop)) prop = [prop] // if prop is not an array convert it into one

/* ITERABLE...? */

const isIterable = object =>
	object != null && typeof object[Symbol.iterator] === 'function'

let el = document.querySelector('div')
let arr = []
isIterable(el) // --> false
isIterable(arr) // --> true
```

#### null | undefined

```js
if (!n) // falsy? (undefined, null)
```

#### html element

```js
/* HTMLElement...? */

if (trigElements instanceof HTMLElement)

if (!(elements instanceof HTMLElement) && typeof elements === "string") {
	// elements is no instance of HTMLElement but it's a string...
	elements = document.querySelectorAll(elements)
}
```

#### error handling

```js
/* --- ERROR HANDLING --- */

// throw an error...
// if var is undefined
if (!arg) {
    throw new SyntaxError(`'${arg}' is undefined or empty`);
}
// if object doesn't have property:
if (!object.property) {
    throw new SyntaxError("Incomplete data: property missing!");
}
// if it's not an array:
if (!(typeof arr === 'object' && typeof arr.slice === 'function')) { 
	throw new TypeError(`This function requires an array. You passed a ${typeof arr}`);
}
// if it's not an element:
if (el.nodeType !== Node.ELEMENT_NODE) {
	throw new TypeError(`'${el}' is not an html element!`)
}

// check if err is not a SyntaxError:
(!(err instanceof SyntaxError))
```

#### arrays

##### arrays are objects

```js
/* ARRAY...? */

// instanceof
let arr = [1, 2, 3]
if (arr instanceof Array) // true
if (arr instanceof Object) // true

/* Arrays do not form a separate language type. They are based on objects.  
So `typeof` does not help to distinguish a plain object from an array: */

typeof {} // object
typeof [] // same
Array.isArray({}) // false
Array.isArray([]) // true

if (!(typeof arr === 'object' && typeof arr.slice === 'function'))
```

#### objects

```js
// check if property exists:
(object.property)
// alternatively to avoid the non-existing property problem:
console.log(user?.address?.street); // undefined (no boolean test!)
```

#### object of arrays

```js
/* if object doesn't contain 'key' create an array first and then push callback into that array */

// style 1
(object[key] || (object[key] = [])).push(callback)

// style 2:
if (!Array.isArray(object[key])) object[key] = []
object[key].push(callback)
```

### implicit conversion (truthy | falsy)

* a *condition* is evaluated and converted to a boolean by `while` and `if`
* `==` always converts (to matching types) before comparison
* `===` forces comparison of values *and* type

### falsy

```js
if (false)
if (0)
if (-0)
if (0n)
if ('' || "" || ``) // empty string
if (null)
if (undefined)
if (NaN)
```

### truthy

Everything else is **truthy**. That includes:

```js
if ('0') // a string containing a single zero
if ('false') // a string containing the text “false”
if ([]) // an empty array
if ({}) // an empty object
if (function(){}) //an “empty” function
```



```js
if (0) // falsy
if (1) // truthy
// so … 
while (i != 0) // … is the same as
while (i) // … because 0 is evaluates to false anyway

0 == "";        // true
1 == "1";       // true
1 == true;      // true

0 === "";       // false
1 === "1";      // false
1 === true;     // false

if (variable) { 
	// evaluates to true if variable's value is not:
  // null
	// empty string (“”)
	// undefined
	// NaN
	// false
	// 0
}
```


### compare a string to multiple values

```js
// using if
if (fruit == 'banana' || fruit == 'lemon') {
    handleYellowFruit();
}
// using switch
switch (fruit) {
    case 'banana':
    case 'lemon':
    case 'mango':
    case 'pineapple':
        handleYellowFruit();
}
```


Loops / Iteration
------------------------------

### break

`break` # breaks the loop  

The combination “infinite loop + break as needed” is great for situations when a loop’s condition must be checked not in the beginning or end of the loop, but in the middle or even in several places of its body.

### continue

`continue` # skip the current iteration

In contrast to `break`, `continue` does not terminate the execution of the loop entirely, but instead:

- In a `while` loop, it jumps back to the condition.
  In a `for` loop, it jumps to the update expression.
  In a `for` loop, it jumps to the next iteration.

- #### example: decrease nesting


```js
// even vs. odd values
// continue helps decrease nesting; instead of … 
for (let i = 0; i < 10; i++) {
	if (i % 2) {
		alert(i);
	}
}
// … it's shorter to write … 
for (let i = 0; i < 10; i++) {
	if (i % 2 == 0) continue; // if true, skip the remaining part of the body
	alert(i); // 1, then 3, 5, 7, 9
}
// for even values of i, continue stops executing the body
// passing control to the next iteration
// so alert is only called for odd values.
```

### for

`for (initialization; condition; final expression) { // code to be executed}`

loop through an array:
```js
let myArray = ["one", "two", "three", "four"];
for (let i = 0; i < myArray.length; i++) { 
	console.log(myArray[i]);
}
```

#### optimize for loops

Loops can become very slow if you don’t do them right. One of the most common mistake is to read the `length` attribute of an array at every iteration:

```js
for(var i = 0; i < array.length; i++){
	// doSomeThingWith(array[i]);
}

// This means that every time the loop runs, JavaScript needs to read the length of the array.
// You can avoid that by storing the length value in a different variable:

var all = array.length;
for(var i = 0; i < all; i++) {
	// doSomeThingWith(names[i]);
}
```

### for ... of

loops through the ***values** of an object*

```js
for (variable of iterable) {
  // statement
}
```

#### iterate over arrays

```js
list = ['a', 'b', 'c']
// iterate over the value:
for (const item of list) {
  console.log(item)
}

// get the index as well, using `entries()`:
for (const [index, item] of list.entries()) {
  console.log(index, item)
}
// use let instead of const, if you reassign the variable inside the block:
for (let value of iterable) {
  value += 1
  console.log(value)
}
```

### for ... in

* loops through the ***keys** of an object*

```js
for (key in object) {
  console.log(object[key]);
}
```
* Only for iterating over keys in an object/map/hash (google javascriptguide)
* `for ... in` loops incorrectly used to loop over the elements in an Array. This is however very error prone because it does not loop from 0 to length - 1 but over all the present keys in the object and its prototype chain. A for-in loop applied to an array in some cases (especially with DOM elements) returns more members than a regular for loop.

### do ... while

```js
do {
  statement
  statement
} while (condition);
```

### switch

```js

/* using IF: */

if (x == "value1") action1();
else if (x == "value2") action2();
else if (x == "value3") action3();
else defaultAction();

/* using SWITCH: */

switch (expression) {
  case value1:
    //Statements executed when the result of expression matches value1
    [break;]
  case value2:
    //Statements executed when the result of expression matches value2
    [break;]
  default:
    //Statements executed when none of the values match the value of the expression
    [break;]
}
```
* *default*: optional; if provided, this clause is executed if the value of expression doesn't match any of the case clauses.



ERROR HANDLING
==================================

try...catch...finally
----------------------

* Usually, a script “dies” (immediately stops) in case of an error, printing it to console.
* `try..catch` allows us to “catch” errors so the script can, instead of dying, do something more reasonable.
* `catch` is a single place for all error handling
* The finally clause works for any exit from `try..catch`. That includes an explicit `return`

```js
/* --- BASIC --- */

try {
	/* define a block of code to be tested for errors while it is being executed. */
} catch(err) {
  	/* define a block of code to be executed, if an error occurs in the try block. */
    // receives an error object
} finally {
    /* Block of code to be executed regardless of the try / catch result */
}

/* --- ADVANCED  --- */

// catch should only process errors that it knows and “rethrow” all others.

try {
  	// code...
}
} catch (err) {
	if (err instanceof ValidationError) {
		alert("Invalid data: " + err.message);
	} else if (err instanceof SyntaxError) {
		alert("JSON Syntax Error: " + err.message);
	} else {
		throw err; // unknown error, rethrow it (**)
	}
}
```

1. First, the code in `try {...}` is executed.
2. If there were no errors, then `catch(err)` is ignored: the execution reaches the end of `try` and goes on, skipping `catch`.
3. If an error occurs, then the `try` execution is stopped, and control flows to the beginning of `catch(err)`.  
   `err`  will contain an error object with details about what happened.

#### ex1

```js
// Without proper error handling the script would die if "json" is malformed
let json = "{ bad json }";

try {
	let user = JSON.parse(json); // <-- error!
	console.log(user.name); // <-- error! 
} catch(err) {
	console.warn("Our apologies, the data has errors, we'll try to request it one more time." );
	console.warn(err.name);
	console.warn(err.message);
}
```

#### ex2

```js
num = 12.5; // change this to get different results
diff, result;

function fib(n) {
	if (n < 0 || Math.trunc(n) != n) {
    	throw new Error("Must not be negative, and also an integer.");
  	}
  	return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}

start = Date.now();

try {
  console.log('try...')
	result = fib(num);
} catch (err) {
	console.log('catch...')
	console.log('Error Message: ', err.message)
	result = 0;
} finally {
	console.log('finally...')
  diff = Date.now() - start;
}

console.log(result || "error occurred");
console.log(`execution took ${diff}ms`);
```

### conditional catch

```js
try {
  myroutine(); // may throw three types of exceptions
} catch (err) {
    if (err instanceof TypeError) {
        // statements to handle TypeError exceptions
    } else if (err instanceof RangeError) {
        // statements to handle RangeError exceptions
    } else if (err instanceof EvalError) {
        // statements to handle EvalError exceptions
    } else {
        // statements to handle any unspecified exceptions
        logMyErrors(err); // pass exception object to error handler
    }
}

/* A common use case for this is to only catch (and silence) a small subset of expected errors, and then re-throw the error in other cases: */

try {
  myRoutine()
} catch (err) {
    if (err instanceof RangeError) {
        // statements to handle this very common expected error
    } else {
        throw err;  // re-throw the error unchanged
    }
}
```

throw & error objects
-------------------------------------------------------

### error object

```js
err.name // ReferenceError, TypeError,...
err.message // Error details.
// There are other non-standard properties available in most environments. One of most widely used and supported is:
err.stack // Current call stack: a string with information about the sequence of nested calls that led to the error. Used for debugging purposes. 
```

### throw

throws a user-defined exception. 

Execution of the current *function will stop* (the statements after throw won't be executed), and control will be passed to the first catch block in the call stack. If no catch block exists among caller functions, the program will terminate. 

`throw err` # generates an error; *err* may be:

* a primitive
* a normal object (with *name* and *message* properties)
* an instance of a built-in Error class
* an instance of a custom error class (that extends built in Error class)

Technically, we can use anything as an error object. That may be even a primitive, like a number or a string, but it’s better to use objects, preferably with *name* and *message* properties (to stay somewhat compatible with built-in errors).  

```js
let customErr = {
	name: "custom error",
	message: "this is a custom error message"
}
throw customErr;
```

### built-in error constructors

built-in constructors for standard errors:  
`Error`, `SyntaxError`, `ReferenceError`, `TypeError`,...  
We can use them to create error objects as well.

```js
throw new Error("My message");
throw new SyntaxError("My message");	// represents an error when trying to interpret syntactically invalid code
throw new ReferenceError("My message"); // represents an error when a non-existent variable is referenced.
throw new TypeError("My message");
```


#### custom error classes

Technically our custom error classes don’t need to inherit from `Error`. But if we inherit, then it becomes possible to use `obj instanceof Error` to identify error objects.

```js
class ValidationError extends Error {
	constructor(message) {
    	super(message); // (1)
    	this.name = "ValidationError"; // (2)
  	}
}
```

1. Here we call the parent constructor. JavaScript requires us to call `super` in the child constructor. The parent constructor sets the message property.
2. The parent constructor also sets `name` to "Error", so here we reset it to the right value.

#### example

```js
// What if json is syntactically correct, but doesn’t have a required name property?

let json = '{ "age": 30 }'; // <-- error! (incomplete data)
try {
	let user = JSON.parse(json); // <-- no errors
    if (!user.name) {
        throw new SyntaxError("Incomplete data: no name");
    }
    alert(user.name); 
} catch(err) {
    alert("JSON Error: " + err.message); // JSON Error: Incomplete data: no name
}
```

* Now `catch` became a single place for all error handling: both for JSON.parse and other cases.

### catch by outer function

#### sync

```js
// works!
function testFn(fn) {
  try{
    fn()
  } catch(err) {
    console.log("Error caught: ", err.message)
  }
}

function innerFn(){
  throw Error("Error from Synchronous Fn")
}

testFn(innerFn) // Error caught:  Error from Synchronous Fn
```

#### async

```js
// doesn't work!
function testFn(fn) {
  try{
    fn()
  } catch(err) {
    console.log("Error caught: ", err.message)
  }
}

async function innerAsyncFn(){
  throw Error("Error from Asynchronous Fn")
}

testFn(innerAsyncFn) // Uncaught (in promise) Error: Error from Asynchronous Fn
```

Why? **An error thrown from async function is a rejected promise**

### async error handling

So, when an error occurs in a synchronous function it's an exception, but when an error occurs in a `Promise` its an asynchronous error or a promise rejection.

Basically, **exceptions are synchronous errors** and **rejections are asynchronous errors**

For a rejected promise wrapping an uncaught error, we have two options to handle it:

```js
// Use .catch() chain function to catch the error within the promise chain:
function testFn(fn) {
  try {
    fn().catch(e => {
      console.log('Promise chain .catch():', e.message)
    })
  } catch(err) {
    console.log("Error caught: ", err.message)
  }
}

async function innerAsyncFn() {
  throw Error("Error from Asynchronous Fn")
}

testFn(innerAsyncFn) // Promise chain .catch():  Error from Asynchronous Fn
```

```js
// Change testFn to an async function and use await to wait for the resolution of the rejected promise:
async function testFn(fn) {
  try {
    await fn()
  } catch(err) {
    console.log("Error caught: ", err.message)
  }
}

async function innerAsyncFn() {
  throw Error("Error from Asynchronous Fn")
}

testFn(innerAsyncFn) // Error caught:  Error from Asynchronous Fn
```


### rethrowing

* `catch` should only process errors that it knows and “rethrow” all others.

```js
let json = '{ "age": 30 }'; // incomplete data
try {
	let user = JSON.parse(json);
	if (!user.name) {
		throw new SyntaxError("Incomplete data: no name");
	}
	blabla(); // unexpected error
	alert(user.name);
} catch(err) {
	if (err instanceof SyntaxError) {
		alert( "JSON Error: " + err.message );
	} else {
		throw err; // rethrow (*)
	}
}
```

The *“rethrowing”* technique can be explained in more detail as:

* `catch` gets all errors.
* In the `catch(err) {...}` block we analyze the `err` object
* If we don’t know how to handle it, we do (another) `throw err`

#### analyze the err object

```js
if (err instanceof SyntaxError) {
	console.log()
}
// if it's not the error type catch is prepared for ...
if (!(err instanceof SyntaxError)) { 
	throw err; // ... rethrow it
}
```

#### multiple levels of try...catch

```js
// multiple levels of try...catch:

function readData() {
	let json = '{ "age": 30 }';
	try {
    	blabla(); // error!
  	} catch (err) {
	    if (!(err instanceof SyntaxError)) {
      		throw err; // rethrow (don't know how to deal with it)
   		}
	}
}
try {
	readData();
	} catch (err) {
  		alert( "External catch got: " + err ); // caught it!
	}  
```


global catch
------------------------------------------------------------------------------------------------------------------------------

*Environment-specific*! The information from this section is not a part of the core JavaScript.  
How to log the error and show something to the user if it occurs outside of `try...catch`?

### node

```js
process.on("uncaughtException")
```

### browser

we can assign a function to the special `window.onerror` property, that will run in case of an uncaught error:

```js
window.onerror = function(message, url, line, col, error) {
	console.warn(`${message}\n At ${line}:${col} of ${url}`);
  // message: error message
  // url: of the script where error happened
  // line
  // col: line and column numbers where error happened
  // error: error object
};
```

The role of the global handler `window.onerror` is usually not to recover the script execution – that’s probably impossible in case of programming errors, but to send the error message to developers.

#### error logging as a service

There are also web-services that provide error-logging for such cases, like https://errorception.com or http://www.muscula.com.

They work like this:

1. We register at the service and get a piece of JS (or a script URL) from them to insert on pages.
2. That JS script sets a custom window.onerror function.
3. When an error occurs, it sends a network request about it to the service.
4. We can log in to the service web interface and see errors.

#### unhandled rejection

```javascript
window.addEventListener('unhandledrejection', function(event) {
  // the event object has two special properties:
  alert(event.promise); // [object Promise] - the promise that generated the error
  alert(event.reason); // Error: Whoops! - the unhandled error object
});

new Promise(function() {
  throw new Error("Whoops!");
}); // no catch to handle the error
```

FUNCTIONS
=============

## general

* a function is an instance of the Function type

### hoisting

* When declared with a function expression, functions are not hoisted to the top of the current scope.  
  `const sayHello = function () { console.log('hello') }`
* Because of these two variations, function hoisting can potentially be confusing, and should not be used.  
  Always declare your functions before you use them.

## instance methods

### call() vs apply()

call() accepts an argument list, while `apply()` accepts a single array of arguments

```js
// apply()
func.apply(this, ['eat', 'bananas'])
// call()
func.call(this, 'eat', 'bananas')
```

### call()

* With `call()`, an object can use a method belonging to another object.
* With `call()`, you can write a method once and then inherit it in another object, without having to rewrite the method for the new object.
* calls a function with a given `this` value and `arguments` provided individually.
* allows for a function/method belonging to one object to be assigned and called for a different object
* provides a new value of `this` to the function/method. 
* `call()` is almost equivalent to a normal function call, except that `this` is passed as a normal parameter instead of as the value that the function was accessed on.

```js
const person = {
  fullName() {
    return `Full name: ${this.firstName} ${this.lastName}`
  },
  setNewSalary(newSalary) {
    return `Salary of "${this.firstName} ${this.lastName}" now set to: ${newSalary}`
  }
  
}
const person1 = {
  firstName:"John",
  lastName: "Doe"
}
const person2 = {
  firstName:"Mary",
  lastName: "Doe"
}

person.fullName.call(person1) // "John Doe"
person.fullName.call(person2) // "Mary Doe"
person.setNewSalary.call(person1, 25)
```

```js
let greeting = 'Hi'
let messenger = {
    greeting: 'Hello'
}
function greet(name) {
    console.log(`${this.greeting} ${name}`)
}
greet.call(this,'John') // "Hi John"
greet.call(messenger,'John') // "Hello John"
// now 'this' references the messenger object, not the global object
```

#### use `call` to invoke an anonymous function

In this example, we create an anonymous function and use call to invoke it on every object in an array.  
The main purpose of the anonymous function here is to add a print function to every object, which is able to print the correct index of the object in the array.  
Passing the object as this value is not strictly necessary, but is done for explanatory purpose.  

```js
const animals = [
  { species: 'Lion', name: 'King' },
  { species: 'Whale', name: 'Fail' }
];

for (let i = 0; i < animals.length; i++) {

  (function(i) {
    this.print = function() {
      console.log('#' + i + ' ' + this.species
                  + ': ' + this.name);
    }
    this.print();
  }).call(animals[i], i);
}
```

### apply()

calls the specified function with a given `this` value, and `arguments` provided as an array (or an array-like object).

* the first arguments to apply is what `this` will refer to

```js
someFunction.apply(thisArg)
someFunction.apply(thisArg, argsArray)
```

```js
function log() {
    console.log.apply(console, [new Date(), ...arguments])
}

console.log('no time') // no time
log('timestamp') // 2023-04-25T05:17:01.815Z timestamp
```

```js
const log = console.log
console.log = function() {
    log.apply(console, [new Date(), ...arguments])
}

console.log('timestamp') // 2023-04-25T05:17:01.815Z timestamp
log('no time') // no time
```



### bind()

```js

```

creates a new bound function. Calling the bound function generally results in the execution of the function it wraps, which is also called the target function. The bound function will store the parameters passed — which include the value of this and the first few arguments — as its internal state. These values are stored in advance, instead of being passed at call time.

`Function.prototype.bind()` # lets you specify the value that should be used as `this` for all calls to a given function.

* The simplest use of bind() is to make a function that, no matter how it is called, is called with a particular this value.

```js
const module = {
  x: 42,
  getX: function() {
    return this.x
  }
}

const unboundGetX = module.getX // extract a method from an object
const boundGetX = unboundGetX.bind(module)
unboundGetX() // undefined - The function gets invoked at the global scope
boundGetX() // 42
```

A common mistake is to extract a method from an object, then to later call that function and expect it to use the original object as its `this`.  
However, the original object is usually lost. Creating a bound function from the function, using the original object, neatly solves this problem:


recursion
------------------------------------

### recursion vs iteration

* *recursion depth* equals the maximal number of context in the stack.
* *memory requirements*: Contexts take memory. The recursive `pow()` raising to the power of *n* actually requires the memory for *n* contexts.
* A loop-based algorithm is more memory-saving: The iterative `pow()` uses a single context changing i and result in the process. Its memory requirements are small, fixed and do not depend on n.

### recursion

Recursion is a programming pattern that is useful in situations 

* when a task can be naturally split into several tasks of the same kind, but simpler. 
* when a task can be simplified into an easy action plus a simpler variant of the same task. 
* to deal with certain data structures.

#### base case

`if` the base case is `true`, the recursion ends and the task is complete. `if` the base case is `false`, the function calls itself again.

#### ex: join array

```js
function joinEntries(arr){
   if (arr.length <= 1) {
      return arr[0] // base case
   } else {
      return arr[0] + joinEntries(arr.slice(1)) // recursive case
   }
}
```

Each time the `else` statement runs, it extracts the first element in the array with `arrayName[0]`, then it calls itself with the remaining array elements (`arrayName.slice(1)`)

#### example: pow()

```js
// Iterative thinking:
function pow(x, n) {
  let result = 1
  // multiply result by x n times in the loop
  for (let i = 0; i < n; i++) {
    result = result * x
    // result *= x
  }
  return result
}
alert( pow(2, 3) ) // 8
```

```js
// Recursive thinking:
function pow(x, n) {
  console.log(`pow() called with x=${x} and n=${n}`)
  if (n == 1) {
    return x;
  } 
  else {
    // recursively calls itself until n == 1
    return x * pow(x, n - 1); // x * x * x ...
  }
}
console.log(pow(2, 3)); // 2 * 2 * 2 = 8
```

```js
// Short version:
function pow(x, n) {
  return (n == 1) ? x : (x * pow(x, n - 1));
}
```

#### example sumTo()

Task: Write a function sumTo(n) that calculates the sum of numbers 1 + 2 + ... + n.

```js
// Recursive version:
function sumTo(n) {
  if (n == 1) return n;
  return n + sumTo(n - 1);
}

// Iterative version:
function sumTo(n) {
  let result = 0;
  for (let i = 1; i <= n; i++) {
    result += i;
  }
  return result;
}
sumTo(100)
```

#### ex transformAll

```js
function transformAll({ type = '', style = {}, children = [] }) {
  const result = { type, style: transformStyleObject(style), children }
  if (Array.isArray(result.children)) {
    result.children = result.children.map(transformAll)
  }
  return result
}
```

1. transformAll takes a single object that represents an HTML DOM element.
2. Transforms the style attributes of that element (which is our goal for every HTML DOM element in our case)
3. Checks if there are nested elements by checking the element's children property
4. If there is, this function will loop through each children and re-call itself transformAll on each child.
5. This starts the recursion and will loop through every object it can find through children no matter how deep the tree goes.

#### ex renderFsStructure

```js
function renderFsStructure(allCollection) {
  // allCollection comes from eleventy
	let fsStructure = {
		root: []
	}

	for (const doc of allCollection) {
		let parts = doc.filePathStem.split('/')
		parts = parts.slice(1, parts.length) // slice off '' // ['cult', 'lyrics', 'about-me']
		if (parts.length === 1) {
			fsStructure.root.push(parts[0])
		}
		else if (parts.length >= 2) {
			let pointer = fsStructure
			for (let i = 0; i < parts.length; i++) {
				const part = parts[i]
				if (!pointer[part]) {
					if (i <= parts.length - 3) {
						pointer[part] = {}
					}
					else if (i == parts.length - 2) { // vorletztes
						pointer[part] ??= {}
						pointer[part].root = []
					}
					else if (i == parts.length - 1) { // letztes
						pointer.root ??= []
						let url = Buffer.from(doc.url).toString()
						pointer.root.push(url)
					}
				}
				pointer = pointer[part]
			}
		}
		else {
			console.error(parts)
		}
	}

	let list = renderHTML(fsStructure)

	return list

	function renderHTML(obj) {
		if (Array.isArray(obj)) {
			let listItems = obj.map(item => {
				if (item === 'root') return ''
				else {
					return /* html */`<li><a href="${item}">${path.basename(item)}</a></li>`
				}
			}).join('');
			
			return /* html */`${listItems}`
		}
		else if (typeof obj === 'object') {
			return Object.entries(obj).map(([key, value]) => {
				return /* html */`
					<ul>
						${(key !== 'root') ? /* html */`<li class="dir">${key}</li>` : ''}
						${renderHTML(value)}
					</ul>`;
			}).join('');
		}
		return '';
	}
}
```



### recursive traversals

Another great application of the recursion is a recursive traversal.  

#### example: sumSalaries()

Imagine, we have a company. The staff structure can be presented as an object:

```js
let company = {
    sales: [{name: 'John', salary: 1000}, {name: 'Alice', salary: 1600 }],
    development: {
        sites: [{name: 'Peter', salary: 2000}, {name: 'Alex', salary: 1800 }],
        internals: [{name: 'Jack', salary: 1300}]
    }
};
```

Now let’s say we want a function to *get the sum of all salaries*. How can we do that?  
If we put 3-4 nested subloops in the code to traverse a single object, it becomes rather ugly.  
As we can see, when our function gets a department to sum, there are two possible cases:

* Either it’s a “simple” department with an array of people – then we can sum the salaries in a simple loop.
* Or it’s an object with N subdepartments – then we can make N recursive calls to get the sum for each of the subdeps and combine the results.

The 1st case is the base of recursion, the trivial case, when we get an array.  
The 2nd case when we get an object is the recursive step. A complex task is split into subtasks for smaller departments. They may in turn split again, but sooner or later the split will finish at (1).  

```js
function sumSalaries(department) {
    if (Array.isArray(department)) { // case (1)
        return department.reduce((prev, current) => prev + current.salary, 0); // sum the array
    } else { // case (2)
        let sum = 0;
        for (let subdep of Object.values(department)) {
            sum += sumSalaries(subdep); // recursively call for subdepartments, sum the results
    }
    return sum;
    }
}
console.log("RESULT: ", sumSalaries(company)); // 7700

// My Version:
function sumSalaries(department) {
    // base case:
    if (Array.isArray(department)) {
      	console.log("Base Case (array found): ", department);
        let value = department.reduce((prev, current) => {
            console.log(`\t${prev} + ${current.salary} = ${prev + current.salary}`);
            return prev + current.salary
        }, 0);
        console.log(`\t...adding: ${value}`);
        return value
    // recursive case:
    } else {
      	console.log("Recursive case (object found): ", department);
        let sum = 0;
        for (subdep in department) {
          	console.log("Loop: entering ", department[subdep]);
            sum += sumSalaries(department[subdep]);
        }
    // finally:
    return sum;
    }
    console.log(`Current Sum: ${sum}`);
}
sumSalaries(company)
```


types
---------------------

### arrow functions 

* Arrow functions do not have `arguments`
* *better suited for callbacks*: As arrow functions inherit the value of `this` from the context in which they are defined (lexical scope)
* *unusable for object methods*: Arrow functions don’t bind `this` to the object that called them. They just use the value of this in the scope in which they were defined. In this case, that’s the global object.

> Function expressions are best for object methods. Arrow functions are best for callbacks or methods like map, reduce, or forEach.

#### arrow functions and named functions

The *function* keyword creates the variable *sum*.
As an arrow function doesn't use this keyword you have to create the variable by hand:

```js
/* multiple parameters */

function sum(a,b) {		// named function
    return a+b
}
let sum2 = (a,b) => {	// arrow function
    return a+b
}
let sum3 = (a,b) => a+b // simplified	


/* one parameter */

function isPositive(number) {				// named function
    return number >= 0 
}
let isPositive3 = (number) => number >= 0	// arrow function

let isPositive3 = number => number >= 0		// simplified	


/* no parameter */

function randomNumber() {				// named function
    return Math.random
}

let randomNumber2 = () => Math.random 	// arrow function
```

* multiple parameters are given in parentheses
* the arrow denotes that a function body follows simplifications:
* omit `{}`
  * if there's just one line of code returning something
  * everything after the arrow is assumed a return
* omit `()`
  * if there's just one parameter

#### arrow functions and anonymous functions

```js
document.addEventListener ('click', function() {
    console.log('Click')
})
// With anonymous functions the conversion into arrow functions has most benefits:
document.addEventListener ('click', () => console.log('Click'))
```

### IIFE

**immediately-invoked function expression**, Kapselfunktion

```js
(function() {
    // code
})();
```

How:

* `function() { … }` # creates an an anonymous function
* `(function() { … })` # wraps that function in parentheses
* `()` # execute that function using the call operator
  Why:
* used to create a separate scope


### higher-order functions

A function that creates another function:

```js
  function greaterThan(n) {
    return m => m > n; 
  } 
  let greaterThan10 = greaterThan(10);
  console.log(greaterThan10(11)); // → true
```

And we can have functions that change other functions.

```js
  function noisy(f) { // We are expecting f to be a function
    return (...args) => { // noisy returns an anonymous function
      console.log("calling with", args);
      // Our anonymous function then takes f (It can use f because its defined inside noisy, see closures for more details) and invokes it with the argument arg and stores the result in a variable named val. 
      let result = f(...args);
      console.log("called with", args, ", returned", result);
      return result;
    }; 
  }
  noisy(Math.min)(3, 2, 1); 
  // → calling with [3, 2, 1]
  // → called with [3, 2, 1] , returned 1
```

Pass one function into another:

```js
  function foo(bar) {
      bar();
  }
  function testing() {
      alert("testing got called");
  }
  foo(testing); // ... passing the function *testing* into *foo*. The line *bar()*; calls the function.
```


#### filter an array using a higher-order function

```js
  // applies a function named test to all items of the arrays
  // when calling test must be given like: "itemOfArray => condition" 
  function filter(array, test) {
    let passed = [];
    for (let element of array) {
      if (test(element)) { passed.push(element); }
    }
    return passed;
  }
  filter(allHeaders, header => header.tagName == "H2") // returns all H2 headers
```

### generator functions

* make *lazy iterators*
* create data streams with ease

Generator functions behave differently from regular ones. When such function is called, it doesn’t run its code. Instead it returns a special object, called “generator object”, to manage the execution.

* Generators are iterable

### factory functions

#### classes vs factory functions

TodoModel as a Class

```js
class TodoModel {
    constructor(){
        this.todos = [];
        this.lastChange = null;
    }
    
    addToPrivateList(){
        console.log("addToPrivateList"); 
    }
    add() { console.log("add"); }
    reload(){}
}
```

TodoModel as a Factory Function

```js
function TodoModel(){
    var todos = [];
    var lastChange = null;
        
    function addToPrivateList(){
        console.log("addToPrivateList"); 
    }
    function add() { console.log("add"); }
    function reload(){}
    
    return Object.freeze({
        add,
        reload
    });
}
```

## parameters

* Function **parameters** are the **names** listed in the function definition.
* Function **arguments** are the real **values** passed to (and received by) the function.
* JS function definitions do not specify data types for parameters.
* JS functions do not perform type checking on the passed arguments.
* JS functions do not check the number of arguments received.

arguments
---------------------

### arguments (object)

**`arguments`**

* ist ein Array-ähnliches Objekt, das auf die übergebenen Parameter einer Funktion verweist.
* lokal in allen Funktionen verfügbar (nicht in *arrow functions*!)
* hat keine Eigenschaften eines Arrays mit Ausnahme von *arguments.length*

```js
function showArgs(a, b) {
    for (let arg of arguments) {
        console.log(arg)
    }
}
// Konvertierung zu einem echten Array:
var args = Array.from(arguments)
var args = [...arguments]
```

### default parameters

Default function parameters allow *named parameters* to be initialized with default values **if no value or *undefined* is passed**.

```js
function doSomething(index = 0, testing = true) { 
	console.log(index)
  console.log(testing)
}
doSomething() // 0, true
doSomething(5, false) // 5, false
```

#### ||

returns the first *truthy* value

```js
function doSomething(index, testing) {
	index = index || 0
  testing = testing || true // now it won't be possible to set it to 'false'
  console.log(index)
  console.log(testing)
}
doSomething() // 0, true
doSomething(5, false) // 5, TRUE (!)
```

#### ??

returns the first *defined* value

```js
function doSomething(index, testing) {
	index = index ?? 0
  testing = testing ?? true
  console.log(index)
  console.log(testing)
}
doSomething() // 0, true
doSomething(5, false) // 5, false
```

#### ??=

returns the first *defined* value

```js
function doSomething(index, testing) {
  // set default values
  index ??= 0
  testing ??= true
  // do something
  console.log(index)
  console.log(testing)
}
doSomething() // 0, true
doSomething(5, false) // 5, false
```

```js
function doSomething(options = {}) {
  // set default values
	options.index ??= 0
  options.testing ??= true
  options.user ??= {}
  options.user.name ??= 'Default User'
  // do something
  console.log(options.index)
  console.log(options.testing)
  console.log(options.user.name)
}
doSomething() // 0, true, 'Default User'

let options = {
  index: 5,
  testing: false,
  user: {
    name: 'Eva'
  }
}
doSomething(options) // 5, false, 'Eva'
```



```js
// Here we not only declare a default object parameter, but also default object properties:
function createElement (tag = 'div', { content = 'Very default', classNames = ['module-text', 'special'] } = {}) {
		const element = document.createElement(tag)
		const text = document.createTextNode(content)
		element.classList.add(...classNames)
		element.appendChild(text)
		return element
}
```

```js
// assign a default value:
let variable = foo || 'Hello!'

class Example {
	constructor(items) {
		this._items = items || []  // very useful
	}
}
```

### named arguments (using objects)

* we are no longer tied to *positional arguments*
* we no longer need to pass lists of nulls or whatever to match the position of the sixth argument: 
  `someFunction(arg1, arg2, null, null, arg5)`

```js
// background:
let someObject = { a: 1, b: 2, c: 3 }
let { a: a, b: b, c: c } = someObject // destructuring assignment
let { a, b, c } = someObject // shorthand
// a = 1, b = 2, c = 3

function perform({ one = null, two = null, skipSomething = false } = {}) {
// The entire object has a default value of {}. 
// This allows invoking perform() without any arguments. 
// Without the default value of {} invoking perform() would throw Cannot read property 'one' of undefined since it would be trying to destructure undefined.
// Skip it when you want to ensure an object is passed to the function.
}

function perform(two, { one = null, skipSomething = false } = {}) {
// if 'two' is a mandatory argument it makes sense to put it a positional parameter instead
}

function objArgument({
	endpoint,
	method = 'GET',
	getParams = {},
	headers = ['Content-Type: text-plain'],
	callback = () => {},
	timeout = 5,
	authRequest = true,
	} = {}) { 
// we also provide some default value (an empty object) for the whole object and also for the getParams parameter. 
// You should always do this to avoid nasty bugs when trying to access nested properties on undefined objects.
  	
	if (callback) callback()
	console.log(`endpoint: ${endpoint}, method: ${method}, callback: ${callback}`);
}
```

#### Unpacking fields from objects passed as a function parameter

```js
const user = {
	id: 42,
	displayName: 'jdoe',
	fullName: {
		firstName: 'John',
		lastName: 'Doe'
	}
};

function userId({id}) {
  	return id
}

function whois({displayName, fullName: {firstName: name}}) {
  	return `${displayName} is ${name}`
}

console.log(userId(user)); // 42
console.log(whois(user));  // "jdoe is John"
```

### rest and spread

Rest-Syntax sieht genauso aus wie die Spread-Syntax und wird für das Destrukturieren von Arrays und Objekten eingesetzt. 
Rest-Syntax ist sozusagen das Gegenteil von Spread-Syntax:

* *Spread expands an array into a list*
  * `...` in a function call or alike
  * used to pass an array to functions that normally require a list of many arguments.
* *Rest gathers the rest of the list of function arguments into an array*
  * `...` at the end of function parameters
  * used to create functions that accept any number of arguments.

#### rest

```js
function test(a, b, ...data) {
    console.log('a: ', a);
    console.log('b: ', b);
    for (d of data) {
        console.log('d: ', d);
    }
}
```

#### spread

```js
// Für Funktionsaufrufe:
myFunction(...iterableObj);
// Für Array-Literale:
[...iterableObj, 4, 5, 6]

// pass elements of an arrays as arguments into a function
function myFunction(x, y, z) { 
	// ...
}
var args = [0, 1, 2];
myFunction(...args);

// Spread kann für jedes beliebige Argument der Argumentliste verwendet werden; auch mehrmals
function myFunction(v, w, x, y, z) { }
var args = [0, 1];
myFunction(-1, ...args, 2, ...[3])
```

#### array insertion using spread 

```js
// insert elements of one array into any position of another array
var parts = ['shoulders', 'knees'];
var lyrics = ['head', ...parts, 'and', 'toes']; // ["head", "shoulders", "knees", "and", "toes"]

// insert elements of one array at the end of another array
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);
```

## return values

```js
// returning multiple values from an function using an object:

function getNames() {
  // get names from the database or API
  let firstName = 'John' 
  let lastName  = 'Doe';
  
 	return {
  	'firstName': firstName,
    'lastName': lastName
  };
  
  // Since the names of the properties are the same as the variables, 
  // you can shorten it using the object literal syntax extensions in ES6 as follows:
  return { firstName, lastName }
}

let { firstName, lastName } = getNames()
```

Closures
------------------------------------------

* A closure is the combination of a function and the lexical scope within which that function was declared.

* The lexical scope of a function is simply the parent scope of that function, no matter whether it is another function or the global scope

  (However, to modify global variables is not a good practice and that’s why we usually see the use of closures like *a function inside another function*)

* This scope consists of any local variables that were in-scope at the time the closure was created.

### use case

Closures are useful because they let you associate data (the lexical environment) with a function that operates on that data. This has obvious parallels to object-oriented programming, where objects allow you to associate data (the object's properties) with one or more methods.

Consequently, you can use a closure anywhere that you might normally use an object with only a single method.

Situations where you might want to do this are particularly common on the web. Much of the code written in front-end JavaScript is event-based. You define some behavior, and then attach it to an event that is triggered by the user (such as a click or a keypress). The code is attached as a callback (a single function that is executed in response to the event).

Since closures have access to the variables in the outer function, they are usually used for two things:

* To control side effects
* To create private variables

### ex: counter

```js
// modern version
function createCounter(initialCounter) {
  let counter = initialCounter;
  return () => ++counter;
};

const incrementCounter = createCounter(2);
incrementCounter() // 3
incrementCounter() // 4
```

### ex: adder

* In essence, *makeAdder* is a function factory. It creates functions that can add a specific value to their argument.
* *add5* and *add10* are both closures. They share the same function body definition, but store different lexical environments.  
  In add5's lexical environment, x is 5, while in the lexical environment for add10, x is 10.

```js
// old school
function createAdder(x) {
  return function(y) {
    return x + y;
  };
}
// modern
function createAdder(x) {
  return (y) => x + y
}
let add5 = createAdder(5)
add5(2)  // 7
let add10 = createAdder(10)
add10(2) // 12
```

### access nested functions

```js
function nest1(text) {
	console.log(text);
	function nest2(text){
		console.log(text);
	}
	nest1.nest2 = nest2; // create an object and save the nested function definition as a property
}
nest1("here is nest1");
nest1.nest2("here is nest2");
```

MAPS
================================

The Map object 

* *holds key-value pairs*
* *remembers the original insertion order of the keys*
* *Any value (both objects and primitive values) may be used as either a key or a value*

May use objects as keys! The same does not count for an object. A string as a key in an object is fine, but we can’t use another Object as a key in Object.

* is an iterable object giving iterators for keys and values
* keeps the *insertion order*

That means:

* Über Maps kann ohne weiteres mit `for-of`-Schleifen iteriert werden
* `Map.prototype.keys()` liefert einen Iterator über alle Keys
* `Map.prototype.values()` liefert einen Iterator über alle Values
* In allen drei Fällen werden die Werte in exakt der Reihenfolge ausgespuckt, in der Sie in die Map eingefügt wurden


### Objects vs. Maps

| Subject             | Map                                                          | Object                                                       |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Accidental Keys** | A Map does not contain any keys by default. It only contains what is explicitly put into it. | An Object has a prototype, so it contains default keys that could collide with your own keys if you're not careful. |
| **Key Types**       | A Map's keys can be any value (including functions, objects, or any primitive). | The keys of an Object must be either a String or a Symbol.   |
| **Key Order**       | The keys in Map are ordered in a simple, straightforward way: A Map object iterates entries, keys, and values in the order of entry insertion. | Although the keys of an ordinary Object are ordered now, they didn't used to be, and the order is complex. As a result, it's best not to rely on property order. |
| **Size**            | The number of items in a Map is easily retrieved from its size property. | The number of items in an Object must be determined manually. |
| **Iteration**       | A Map is an iterable, so it can be directly iterated.        | Iterating over an Object requires obtaining its keys in some fashion and iterating over them. |
| **Performance**     | Performs better in scenarios involving frequent additions and removals of key-value pairs. | Not optimized for frequent additions and removals of key-value pairs. |

```js
// using maps:

const m = new Map();
m.set("a", 1);
m.set("b", 2);

for (const [key, value] of m) {
  console.log(`key: ${key}, value: ${value}`)
}
const keys = m.keys();   // > Iterator "a", "b"
const vals = m.values(); // > Iterator 1, 2

// using objects:
const o = {};
o.a = 1;
o.b = 2;

for (const [ key, value ] of o) {
  console.log(`key: ${key}, value: ${value}`)
} // Klappt nicht
for (const key in o) {} // Bester Ersatz, aber bezieht Prototypen mit ein
const keys = Object.keys(o); // > Array "a", "b"
const vals = Object.values(o); // > Array 1, 2 (seit ES2017)
```

### constructor

**`new Map(iterable)`**

* *iterable*: An Array or other iterable object whose elements are key-value pairs.   
  For example, arrays with two elements, such as ``[[ 1, 'one' ],[ 2, 'two' ]]``. Each key-value pair is added to the new Map. 

```js
let myMap = new Map([
	[1, 'one'],
	[2, 'two'],
	[3, 'three'],
])
```

instance methods
------------------

### set()

* The correct way for storing data in a Map

```js
myMap.set(key, value) // Adds or updates an element with a specified key and a value to a Map object.

let myMap = new Map()
// Add new elements to the map
myMap.set('bar', 'foo')
myMap.set(1, 'foobar')
// Update an element in the map
myMap.set('bar', 'baz')
// Since the set() method returns back the same Map object, you can chain the method call:
myMap.set('bar', 'foo')
	 .set(1, 'foobar')
	 .set(2, 'baz');
```

### get()

```js
myMap.get(key) // Returns a specified element from a Map object. If the value that is associated to the provided key is an object, then you will get a reference to that object and any change made to that object will effectively modify it inside the Map object.
```

### has()

```js
myMap.has(key) // Returns true if an element with the specified key exists in the Map object; otherwise false
```

### keys()

```js
myMap.keys() // Returns a new Iterator object that contains the keys for each element in the Map object in insertion order.
```



Examples
------------------------------------------

```js
let myMap = new Map()
let myObj = {first: 1, second: 2}
let myEl = document.querySelector('div')

myMap.set(myEl, myObj) // set an element as the key and an object as the value
myMap.get(myEl) // returns myObj
myMap.get(myEl).first // returns 1
myMap.get(myEl).first = 99 // sets a new value for the 'first' property of 'myObj'
```

MODULES
===============================================

* NodeJS: CommonJS
* Browser: ES modules

## basics

### scope

* *Module-level scope*: Each module has its own top-level scope. In other words, top-level variables and functions from a module are not seen in other scripts.
* In the browser, independent top-level scope also exists for each `<script type="module">`

### order & relation

* If the same module is imported into multiple other places, its code is executed only the first time. Then exports are generated, and then they are shared between importers, so if something changes an exported object, other modules will see that. Such behavior allows us to configure modules on first import. We can setup its properties once, and then in further imports it’s ready.

## environment

### npm vs browser

#### bare module specifiers

```js
// LitElement uses bare module specifiers to import modules from the lit-html library, like this:
import {html} from 'lit-html'
```

Browsers currently only support loading modules from URLs or relative paths, not bare names referring to e.g. an npm package, so the *build system needs to handle them*: either by transforming the specifier to one that works for ES modules in the browser, or by producing a different type of module as output.

*Webpack* automatically handles bare module specifiers; for *Rollup*, you'll need a plugin (@rollup/plugin-node-resolve).

Why bare module specifiers? Bare module specifiers let you import modules without knowing exactly where the package manager has installed them. A standards proposal called Import maps would let browsers support bare module specifiers. In the meantime, bare import specifiers can easily be transformed as a build step. There are also some polyfills and module loaders that support import maps.

### html

```html
<script type="module" src="path.js"></script>
```

* Module scripts are always *deferred*, for both external and inline scripts.
  * downloading external module scripts doesn’t block HTML processing, they load in parallel with other resources.
  * module scripts wait until the HTML document is fully ready (even if they are tiny and load faster than HTML and then run.
  * relative order of scripts is maintained: scripts that go first in the document, execute first.

#### import.meta

An object containing meta information about the current module.  
Its content depends on the environment. In the browser, it contains the url of the script, or a current webpage url if inside HTML:

* `import.meta.url`

## export / import

```js
// module.js
// --------------------------------
export const name1 = 'me', name2 = 'you'

const count = 0

export function next() { 
  return ++count 
}

function squared() { 
  return Math.pow(count, 2) 
}

export default function() { 
  return count 
}

export { 
	squared 
}
```

```js
// import style 1
import * as module from './module.js';
console.log(`${module.next()} ${module.squared()}`);
console.log(`${module.next()} ${module.squared()}`);
console.log(`${module.default()} ${module.squared()}`);
// import style 2
import { next, squared, default as current } from './simple.mjs';
```

```js
/* math.js */
// --------------------------------

// contains basic arithmetic algorithm
let sumAll = (a, b) => {return a + b;}
let subtractAll = (a, b) => {return a - b;}
let divideAll = (a, b) => {return a / b;}
let multiplyAll = (a, b) => {return a * b;}
let findModulus = (a, b) => {return a % b;}

// export each module individually:
export let sumAll = (a, b) => {return a + b}

// use a single export statement:
export { sumAll, subtractAll, divideAll, multiplyAll, findModulus }

// export default values
export default {sumAll, subtractAll, multiplyAll, divideAll}
```



```js
/* main.js */
// --------------------------------

// import individually or multiple modules at once
import { sumAll } from './math.js'
import { sumAll, subtractAll, divideAll } from './math.js'

// import modules as *aliases* (namespace import)
import * as math from './math.js'
let sum = math.sumAll(3, 9)

/* Namespace imports are an alternative to named imports. If we namespace-import a module, it becomes an object whose properties are the named exports. */

// import *default* values
import math from './math.js'
let sum = math.sumAll(5, 2))
```

```js
/* Syntactic pitfall: named importing is not destructuring!
Both named importing and destructuring look similar: */
import {foo} from './bar.mjs' // import
const {foo} = require('./bar.mjs') // destructuring
```

### static import

* Modules are imported with either absolute or relative references and must start with  
  `/`, `./`, or `../`
* Curly braces are used for named exports
* Default (unnamed) exports don't need curly braces

### default exports

* Each module can have at most one default export. The idea is that the module *is* the default-exported value.
* Avoid mixing named exports and default exports
* The most common use case for a default export is a module that contains a single function or a single class.

```js
// --- EXPORT default ---
let un = {}
un.evt = {}
un.evt.handleHideEl = function(el) {
	/* ... */
}
export default un

// --- IMPORT default ---
import unlib from './unlib_module.js'		// a default import *is* the module
import { unlib } from './unlib_module.js'	// reach into a named import
unlib.evt.handleHideEl(el)
```

Note the syntactic difference:   
the *curly braces* around named imports indicate that we are reaching *into* the module, while a default import *is* the module.

### import css

```js
import todoAppCSS from './todo_app.css' assert { type: 'css' }; // currently not supported in Firefox
import todoAppCSS from './todo_app.css' // returns '...blocked because of a disallowed MIME type (“text/css”)''
```

### import performance

```js
import * as lib from 'amazing-lib' // import everything and assign it to a variable called lib
import { func1 } from 'amazing-lib' // import everything and destructuring only what we need
// In both cases all the library content is being imported
```

## dynamic imports

* dynamic imports heavily affect **Cumulative Layout Shifts**

```js
async () => {
    let module = await import('./module.js')
    // do something with module
})
```

```js
// module
let foo = 'bar'
function func() { }
export {foo, func}

// the same:
import * as modStatic from './module.js' // static import
let modDynamic = await import('module.js') // dynamic import
```

```js
// export
export function hi() {
  alert(`Hello`)
}
export function bye() {
  alert(`Bye`)
}
// dynamic import
let { hi, bye } = await import('./say.js')
hi()
bye()

// export default
export default function() {
  alert("Module loaded (export default)!")
}

// dynamic import default
// ...in order to access it, we can use default property of the module object:
let obj = await import('./say.js')
let say = obj.default
// or, in one line: 
let { default: say } = await import('./say.js')
say()
```

## re-export

```js
// another-file.js
// ----------------------

export function increaseSalary(salary) { // named export
  return salary + 100;
}

export const department = 'accounting' // named export

export default function multiply(a, b) { // default export
  return a * b
}

// index.js
// --------------------

export { increaseSalary, department, default } from './another-file.js'
// re-exports the exported members of another-file.js
```

MISC
===================

debugger
-------------------

add the `debugger` keyword in your code. This causes JavaScript execution in browsers to pause so you can debug.


strict mode
-------------

`"use strict";`

### Not Allowed in Strict Mode

* Using a variable, without declaring it, is not allowed
* Duplicating a parameter name is not allowed:  
  `function x(p1, p1) {};`
* Writing to a read-only property is not allowed
* For security reasons, `eval()` is not allowed to create variables in the scope from which it was called
* `this` refers to the object that called the function.  
  What if the object is not specified?  
  * strict mode: functions will return *undefined* 
  * normal mode: functions will return the global object (window)


void
-----------------------------

The void operator evaluates the given expression and then returns *undefined*.

#### Non-leaking Arrow Functions

Arrow functions can cause unintended side effects by returning the result of a function call that previously returned nothing.  
To be safe, when the return value of a function is not intended to be used, it can be passed to the `void` operator to ensure that (for example) changing APIs do not cause arrow functions' behaviors to change.

```js
button.onclick = () => void doSomething();
// This ensures the return value of doSomething changing from undefined to true will not change the behavior of this code.
```


this
----------------

* differences between *strict* mode and *non-strict* mode.
* runtime binding: In most cases, the value of this is determined by how a function is called

It has different values depending on where it is used:

- In a object method, `this` refers to the **owner object**.
- Alone, `this` refers to the **global object**.
- In a function, `this` refers to the **global object**.
- In a function, in strict mode, `this` is `undefined`.
- In an event, `this` refers to the **element** that received the event.
- Methods like `call()`, and `apply()` can refer `this` to **any object**.

#### Global context

In the global execution context (outside of any function), `this` refers to the *global object* whether in strict mode or not.  
You can always easily get the global object using the global `globalThis` property, regardless of the current context in which your code is running.

#### Function context

* `this` is bound to different values based on the *context* in which the function is called
* In a function, `this` refers to the **global object**.
* In a function, in strict mode, `this` is `undefined`.
* The context is (roughly) the object that calls the function
* depends on *how it is called*
* As it is bound to the context (the object that calls a function), it can lead to some very awkward *issues with callbacks*.

```js
function f1() {
	return this
}
// In a browser:
f1() === window // true
// In Node:
f1() === globalThis // true

let person = {
	age: 25,
	showThis() {
		console.log(this) // this = person
    console.log(this.age) // 25
	}
}

let person = {
	show() {
    function showNestedThis() {
			console.log(this) // this = window 
    }
    showNestedThis()
	}
}
person.show()
```

#### this in arrow functions

* arrow functions inherit the value of `this` from the context in which they are defined
* arrow functions use the value of this in their *lexical scope*
* the scope is all the variables visible to a function where it is defined
* depends on *how it is defined*

```js
function Person() {
	this.age = 0

	showAge() { 
		console.log(this.age)	// foo - This function is called by Person.
	
		setTimeout(function() {
			console.log(this.age)	// undefined - This function is called by setTimeout. It's context is the window object
		}, 1000)
  	}

	// arrow function
	setInterval(() => {
		console.log(this.age) 	// foo - Refers to the person object
	}, 1000);
}
var p = new Person()
```

However, the second function is called by setTimeout — so its context is different. Its context is actually a Timeout object in Node or the window object in browsers.

#### this in classes

In a constructor function `this` is a substitute for the new object. The value of this will become the new object when a new object is created.

```js
class auto {
    constructor(hersteller,modell) {
        this.hersteller = hersteller
        this.modell = modell
    }
}

let car_1 = new auto('ford','focus')
car_1.hersteller // ford
```

#### this is objects

`this` is the object that "owns" the code.

```js
let user = {
  name: "John",
  age: 30,

  toString() {
    return `name: ${this.name}, age: ${this.age}`
  }
};
alert(user); // {name: "John", age: 30}
```

Compatibility
-----------------

### Object detection

check if the browser supports the object (method, array or property) you want to use.   
This script relies on the *document.images* array

```js
if (document.images) { do something with the images array }
if (window.focus) { window.focus() } // means: "If the focus method is supported"
if (window.focus()) // means: "If you can put the focus on the window" and assumes that it's supported.
```


OBJECTS
================================

## limitations

* *Objects can only store keys as strings*. Therefore, you can't store a reference to an HTML element as a key

## solutions

```js
// obj with id keys and array values
// task: merge all array values

let obj = {
  id34583074t56j: [ 1, 2, 3, 4, 5 ],
  idskdfgjk56gl6: [ 7, 8, 9, 10 ],
}

let merge = Object.values(obj).reduce((acc, curr) => [...acc, ...curr]) 
// [ 1, 2, 3, 4, 5, 7, 8, 9, 10 ]
```

### delete undefined values

```js
// destructive
Object.keys(obj).forEach(key => obj[key] === undefined ? delete obj[key] : {});

// the same
Object.keys(obj).forEach(key => {
  if (obj[key] === undefined) {
    delete obj[key];
  }
});
```

### extract properties

```js
/* ex obj 1 */
let obj = { 
  name: 'Joe', 
  age: 6, 
  pet: 'dog',
  address: {
    city: 'New York',
    street: 'Sunset Boulevard',
    number: 122,
  }
}

let arr = [
  { name: 'Joe', age: 6, pet: 'dog'  },
  { name: 'Jane', age: 5, pet: 'bird'  }
]
```

```js
/* anonymous arrow function being called instantly */
let subsetObj = (({ name, pet }) => ({ name, pet }))(obj) // extracting name, pet into a new object
```

```js
/* temporary variables */
let { name, pet } = obj
let subsetObj = { name, pet }
```

#### extract property from nested obj

```js
// Task: extract all urls from this object

let site = {}
site.urls = {
  "65534215e9026f928a66c756": {
    "en": "/en/veroffentlichungen",
    "de": "/de/veroffentlichungen"
  },
  "653bb463931572f3c62ede52": {
    "en": "/",
    "de": "/"
  },
  "65523859b6ed54283341219a": {
    "en": "/en/uber-mich",
    "de": "/de/uber-mich"
  },
  "653bb41a931572f3c62ede16": {
    "en": "/en/forschung",
    "de": "/de/forschung"
  }
}
let allURLs = [] 
Object.values(site.urls).map(item => Object.values(item).forEach(item => allURLs.push(item)))
// ['/en/veroffentlichungen', '/de/veroffentlichungen', '/', '/', ... ]
```

#### get extract from array of objects

```js
posts.map(item => {
    let { id, html } = item // properties to extract
    let subset = { id, html }
    return subset
})
```

```js
arrOfObjs.map(item => {
    return { id: item.id } 
})

arrObjs.map(item => sub = { id: item.id }) 
```

### get subset obj

#### getPosSubset

```js
function getPosSubset(obj, ...keys) {
  // returns an obj with the given properties picked
  return Object.fromEntries(keys.filter(key => key in obj).map(key => [key, obj[key]]));
}

arr.map(obj => getPosSubset(obj, 'pet')) // [ { pet: 'dog' }, { pet: 'bird'} ]
```

```js
/* Considering that an object to pick contains extra keys, it's generally more efficient to iterate over keys from a list rather than object keys, and vice versa if keys need to be omitted. */

function getPosSubset(obj, ...keys) {
	const subset = keys
		.filter(key => key in obj) // line can be removed to make it inclusive
		.reduce((obj2, key) => (obj2[key] = obj[key], obj2), {}); 
	return subset
}

arr.map(obj => getPosSubset(obj, 'pet')) // [ { pet: 'dog' }, { pet: 'bird'} ]
```

```js
function getPosSubset(obj, ...keys) {
  const subset = Object.fromEntries(keys
  	.filter(key => key in obj) // line can be removed to make it inclusive
  	.map(key => [key, obj[key]]));
	return subset
}

arr.map(obj => getPosSubset(obj, 'pet')) // [ { pet: 'dog' }, { pet: 'bird'} ]
getPosSubset(obj.address, 'number') // get nested property --> 122
```

```js
function inclusivePickProperty(obj, ...keys) {
  return Object.fromEntries(keys.map(key => [key, obj[key]]))
}
```

#### getNegSubset

```js
function getNegSubset(obj, ...keys) {
  // returns an obj excluding the given keys
  return Object.fromEntries(Object.entries(obj).filter(([key]) => !keys.includes(key)))
}

arr.map(obj => getNegSubset(obj, 'pet', 'age')) // [ { name: 'Joe' }, { name: 'Jane'} ]
```

```js
/* Considering that an object to pick contains extra keys, it's generally more efficient to iterate over keys from a list rather than object keys, and vice versa if keys need to be omitted. */

function getNegSubset(obj, ...keys) {
	const subset = Object.keys(obj)
		.filter(key => keys.indexOf(key) < 0)
		.reduce((obj2, key) => (obj2[key] = obj[key], obj2), {});
  return subset
}
arr.map(obj => getNegSubset(obj, 'pet', 'age')) // [ { name: 'Joe' }, { name: 'Jane'} ]
```

```js
function extractFromObj = (obj, deleteFromOrg = false, ...keys) {
	const newObject = Object.assign({})
  Object.keys(obj).forEach(key => {
  	if(keys.includes(key)){
			newObject[key] = obj[key]
      if (deleteFromOrg) delete obj[key] // deletes them from the original object.
    }
	})
	return newObject
}

function extractFromObj(obj, searchKeys = [], subkey = '') {
  console.log(keys)
  const newObj = {}
    Object.keys(obj).forEach(key => {
    	if (searchKeys.includes(key)) {
      	// console.log('key included: ', key)
        if (subkey) newObject[key] = obj[key][subkey]
        else newObject[key] = obj[key]
       }
		})
  return newObj
}
```

### check

#### isEmpty()

```js
/* check if object is empty */
function isEmpty(obj) {
	return Object.keys(obj).length === 0
}
```

### convert

```js
// convert obj into array of objs
Object.entries(obj).map(entry => { 
    return { [entry[0]]: entry[1] }
})
```



### create html-attributes-string

```js
let attStr = ''
Object.entries(atts).forEach(([key, value]) => {
  attStr += `${key}="${value}" `)
}
```



### deal with json

#### ready!

* works
* get a json property
* transform it
* insert a new property right after it

```js
// get a json property (startPattern), transform it and insert a new property right after it
let json = {
	"en": {
		"globalType": "first richTextContent",
		"createdAt": "2022-11-17T10:45:08.439Z",
		"updatedAt": "2022-11-17T10:45:08.439Z",
		"id": "637610b43b18b324c9f2d900"
	},
	"de": {
		"globalType": "second richTextContent",
		"createdAt": "2022-11-17T10:45:08.439Z",
		"updatedAt": "2022-11-17T10:45:08.439Z",
		"id": "637610b43b18b324c9f2d900"
	}
}

let obj = {
  id: "6391fefbd6b9cc572979bbf1",
  name: "test",
  category: "633568dc924a2d9cf3cd1607",
  contentRichText: [
    {
      children: [
        {
          text: "Hey my love",
        },
      ],
      type: "h3",
    },
    {
      children: [
        {
          text: "some text for you",
        },
      ],
    },
  ],
  variation: [
  ],
  images: [
  ],
  relatedProducts: [
  ],
  createdAt: "2022-12-08T15:12:59.749Z",
  updatedAt: "2022-12-08T22:53:59.949Z",
}

function extractValuesOfJSON(jsonStr, searchProp, nextProp) {
  // returns all values of searchProp instances
  if (typeof jsonStr !== 'string') {
    console.log('converting jsonStr')
    jsonStr = JSON.stringify(jsonStr) // convert json if not passed as a string
  }
  
  let startPattern = `${searchProp}\":` // this the property we are looking for
	let endPattern = `,\"${nextProp}` // the next comma serves as a boundary
  let re = new RegExp(`(?<=${startPattern}).*?(?=${endPattern})`, 'g') // matches everything between two json properties
  let matches = jsonStr.match(re)
  console.log(matches.length, 'matches found')
  return matches
}

// main
let jsonStr = JSON.stringify(obj)
let searchProp = 'contentRichText'
let nextProp = 'variation'
let newProp = 'contentHTML'
let matches = extractValuesOfJSON(jsonStr, searchProp, nextProp) // extract from obj or json
let newJsonStr = jsonStr

for (let match of matches) {
  console.log(`searching match "${match}"`)
	let matchPos = newJsonStr.indexOf(match) // get start index of this match
	let insertPos = newJsonStr.indexOf(',', matchPos + matchPos.length) + 1 // get end index of this match (a comma in json)
	let transformedMatch = '<html>hey</html>' // transform
	let insertStr = `\"${newProp}\": \"${transformedMatch}\",`
	console.log(`insert ${insertStr.slice(0, 10)}... at ${insertPos}` )
	newJsonStr = newJsonStr.slice(0, insertPos) + insertStr + newJsonStr.slice(insertPos) // insert transformedMatch
}

JSON.parse(newJsonStr)
```

#### getNestedValues()

* maybe need to modify end pattern

```js
function getNestedValues(jsonStr = '', searchProp = '', delimiter = '\"', log = false) {
  // returns all values of searchProp instances
  // '\"' <-- delimiter for values without "
	if (typeof jsonStr !== 'string') {
	  if (log) console.log('converting jsonStr')
	  jsonStr = JSON.stringify(jsonStr) // convert json if not passed as a string
	}
							
	let startPattern = `${searchProp}\":\"` // this the property we are looking for
	let re = new RegExp(`(?<=${startPattern}).*?(?=${delimiter})`, 'g') // matches everything between two json properties
	let matches = jsonStr.match(re)
	if (log) console.log(matches.length, 'matches found')
	return matches
}

// usage examples
let blockTypes = [
	...getNestedValues(pages['de'].docs, 'blockType'),
	...getNestedValues(headers['de'].docs, 'blockType'),
	...getNestedValues(menus['de'].docs, 'blockType'),
]
let uniqueBlockTypes = new Set(blockTypes) // filter out duplicates


function getNestedValues(jsonStr, searchProp) {
	// returns all values of searchProp instances
	if (typeof jsonStr !== 'string') {
	  console.log('converting jsonStr')
	  jsonStr = JSON.stringify(jsonStr) // convert json if not passed as a string
	}
	let startPattern = `${searchProp}\":` // this the property we are looking for
	let endPattern = `,\"` // the next comma serves as a boundary
	let re = new RegExp(`(?<=${startPattern}).*?(?=${endPattern})`, 'g') // matches everything between two json properties
	let matches = jsonStr.match(re)
	console.log(matches.length, 'matches found')
	return matches
}
```

### access nested property dynamically

```js
function resolveObjPath(path, obj) {
    return path.split('.').reduce(function(prev, curr) {
        return prev ? prev[curr] : null
    }, obj || self)
}

resolveObjPath("document.body.style.width")
resolveObjPath("style.width", document.body)
resolveObjPath("part.0.size", someObject) // even use array indexes
resolveObjPath('properties.that.do.not.exist', {hello:'world'}) // returns null when intermediate properties are not defined

// returns a reference to the same obj
resolveObjPath('html.headers', obj) === obj.html.headers // true
```

### deal with nested objects

#### ex obj

```js
let obj = {
	lvl1StrA: 'val1A',
	lvl1StrB: 'val1B',
  lvl1StrC: 'val1C',
  posts: [
  	{ findMe: 'findMe' },
    { postA: 'postA' },
    { postB: 'postB' },
  ],
	lvl1ObjA: {
		lvl2StrA: 'val2A',
    findMe: 'findMe',
		findMe: 'findMe',
		lvl2ObjA: {
			lvl3Str: 'val3A',
			findMe: 'findMe',
		},
		lvl3ObjB: {
			findMe: 'findMe',
      findMe: 'findMe'
		}
	}
}
```

#### ready

##### get property by string

* source: stack overflow
* should work

```js
function getPropByString(obj, propString) {
  // 'foo.bar.baz'
  if (!propString) return obj

  let prop, props = propString.split('.')

  for (var i = 0, iLen = props.length - 1; i < iLen; i++) {
    console.log('prop =', props[i])
    prop = props[i] // foo
    let candidate = obj[prop] // obj["foo"]
    if (candidate !== undefined) obj = candidate
    else break // break the loop entirely leaving i = the matching prop
  }
  return obj[props[i]]
}

getPropByString(obj, 'prop1C.prop2A')

var obj = {
  foo: {
    bar: {
      baz: 'x'
    }
  }
}

console.log(getPropByString(obj, 'foo.bar.baz')); // x
console.log(getPropByString(obj, 'foo.bar.baz.buk')); // undefined
```

#### not ready yet

##### search value of nested property

```js
function searchPropInObj(obj, searchProp) {
  for (let key in obj) {
    let value = obj[key]
    //console.log(`${key}: ${value}`)
    if (key === searchProp) {
      // value found
			console.log(`found: ${key}: ${value}`)
    }
    if (typeof value === 'object') {
      // if there's a nested object descend into it
      searchPropInObj(value, searchProp) // descent into obj.posts
    }
  }
}

/* update all instances of property in object */
function _getPathsToObjProp(obj, searchProp) {
	// returns an array with the property chain that leads to each property found
	let matchingPaths = []
	let currentPath = []

	function search(obj, searchProp) {
		//console.log('search()')
		//console.log('currentPath: ', currentPath.toString())
		for (let key in obj) {
			let value = obj[key]
			//console.log(`${key}: ${value}`)
			if (key === searchProp) {
				// value found
				console.log(`property "${searchProp}" found at: "${currentPath}"`)
        if (path.length === 1) obj[path[0]][prop] = value
        if (path.length === 2) obj[path[0]][path[1]][prop] = value
        if (path.length === 3) obj[path[0]][path[1]][path[2]][prop] = value
        if (path.length === 4) obj[path[0]][path[1]][path[2]][path[3]][prop] = value
				//matchingPaths.push([...currentPath])
				//console.log('matchingPaths: ', matchingPaths)
			}
			if (typeof value === 'object') {
				currentPath.push(key) // [prop1C, prop2C]
				//console.log('descend down to: ', currentPath)
				search(value, searchProp) // descent into obj.posts
			}
		}
	}
	search(obj, searchProp)
	return matchingPaths
}
```

##### get paths to object property

```js
function getPathsToObjProp(obj, searchProp) {
	// PROBLEMS:
	// - can't handle arrays
	// returns an array with the property chain that leads to each property found
	let matchingPaths = []
	let currentPath = []

	function searchObj(obj, searchProp) {

    Array.isArray(obj) {
     for (let i = 0; i < arr.length; i++) {
      let value = arr[i]
      if (value === searchProp) {
          // match on this lvl
          console.log(`[match]: "${searchProp}" found.`)
          currentPath.push(i) // add array index to currentPath 
          matchingPaths.push([...currentPath])
      }
      else if ( 
    }
    
		for (let key in obj) {
			console.log('checking path: ', currentPath)
			let value = obj[key]
      
			if (key === searchProp) {
				// match on this lvl
				console.log(`[match]: "${searchProp}" found.`)
				matchingPaths.push([...currentPath])
			}
			else if (Array.isArray(obj[key])) {
        // LVL DOWN ARRAY
				currentPath.push(key)
				console.log(`--> enter array: `, obj[key])
				searchArr(value, searchProp)
			}
			else if (typeof value === 'object') {
				// LVL DOWN OBJECT
				currentPath.push(key)
				console.log(`--> enter object: `, obj[key])
				search(value, searchProp)
			}
			else if (currentPath.length !== 0) {
				// LVL UP
        console.log('nothing found [lvl up]')
				//if (!isNaN(currentPath[currentPath.length - 1])) currentPath.pop()
				currentPath.pop() // remove last lvl
        if (prevArr === currentPath[currentPath.length - 1]) {
          console.log('<-- removing array as we have reached the last item')
          currentPath.pop() // two lvl up if current lvl is an array and nothing was found inside of it
        }
			}
			else {
				console.log('nothing found [continue on same lvl]')
			}
		}
	}
	search(obj, searchProp)
	return matchingPaths
}

getPathsToObjProp(obj, 'findMe')
```

##### get paths to object property and update

```js
function updatePropInObj(originalObj, searchProp, newValue) {
	// returns an array with the property chain that leads to each property found
	let matchingPaths = []
	let currentPath = []

	function getPropertyPaths(obj, searchProp, newValue) {
		//console.log('getPropertyPaths())')
		//console.log('currentPath: ', currentPath.toString())
		for (let key in obj) {
			let value = obj[key]
			//console.log(`${key}: ${value}`)
			if (key === searchProp) {
				// value found
				matchingPaths.push([...currentPath])
				//console.log('matchingPaths: ', matchingPaths)
				//console.log(`property "${searchProp}" found at: `, currentPath)
				//console.log(currentPath)
			}
			if (typeof value === 'object') {
				//console.log('descend down to: ', currentPath)
				currentPath.push(key) // [prop1C, prop2C]
				getPropertyPaths(value, searchProp) // descent into obj.posts
			}
		}
	}

	function updateProperties(matchingPaths) {
		matchingPaths.forEach(p => {
			if (p.length === 0) originalObj[searchProp] = newValue
			else if (p.length === 1) originalObj[p[0]][searchProp] = newValue
			else if (p.length === 2) originalObj[p[0]][p[1]][searchProp] = newValue
			else if (p.length === 3) originalObj[p[0]][p[1]][p[2]][searchProp] = newValue
			else if (p.length === 4) originalObj[p[0]][p[1]][p[2]][p[3]][searchProp] = newValue
		})
		console.log(`property ${searchProp} updated to ${newValue} at: `, matchingPaths)
	}

	getPropertyPaths(obj, searchProp)
	updateProperties(matchingPaths)
}

updatePropInObj(obj, 'findMe', 'gotYou')
```

create
---------------------

```js
var myObject = {} // creates an empty object
var myObject = new Object() // old school

if (cond) var x = {greeting: 'hi there'} 
// creates an object and assigns it to the variable x only if the expression *cond* is true:
```

If you have variables in-scope with the same name as your intended property names, you can omit the colon and the value in the object literal creation:

```js
const userName = 'echo'
const avatar = 'echo.png'
const user = {
  userName,
  avatar
} // { "avatar": "echo.png",   "userName": "echo" }
```

## method chaining

return the same object instance for method chaining:

```js
const userName = 'echo'
const avatar = 'echo.png'
const user = {
  userName,
  avatar,  setUserName (userName) {
    this.userName = userName;
    return this;
  }
}

user.setUserName('Foo').userName // "Foo"
```

## factory functions

If you need to create many objects, you’ll want to combine the power of object literals and factory functions (*Literals for One, Factories for Many*).

Let’s turn our user object into a createUser() factory:
(arrow functions (=>) have an implicit return feature: if the function body consists of a single expression, you can omit the return keyword)

```js
const createUser = ({ userName, avatar }) => ({
  userName,
  avatar,
  setUserName (userName) {
    this.userName = userName;
    return this;
  }
})
```

**Be careful when you return object literals.** By default, JavaScript assumes you want to create a function body when you use braces, e.g., { broken: true }. If you want to use an implicit return for an object literal, you’ll need to disambiguate by wrapping the object literal in parentheses:

```js
const noop = () => { foo: 'bar' }
noop() // undefined
// foo: is interpreted as a label, and bar is interpreted as an expression that doesn’t get assigned or returned

const createFoo = () => ({ foo: 'bar' })
createFoo() // { foo: "bar" }
// here the parentheses force the braces to be interpreted as an expression to be evaluated, rather than a function body block
```

## desctructure

```js
let obj = { name: 'Joe', age: 6, pet: 'dog'  }
let { name, pet } = obj 
// creates two variables name and pet
// with the values of obj.name and obj.pet

function doSomething(args) {
  const { value, data, siblingData, originalDoc, operation, req } = args
  // creates all those local variables extracting their values from the 'args' obj
}
```

### destructure function parameters

Pay special attention to the function signature:

```js
const createUser = ({ userName, avatar }) => console.log(userName, avatar)
```

In this line, the braces `({ ... })` represent object destructuring. This function takes one argument (an object), but destructures two formal parameters from that single argument, userName, and avatar. Those parameters can then be used as variables in the function body scope. You can also destructure arrays:

```js
// short version:
const swap = ([first, second]) => [second, first] 

// long version:
const swap = ([first, second]) => {
  return [second, first]
}

swap([1, 2]) // [2, 1]
```

And you can use the rest and spread syntax (...varName) to gather the rest of the values from the array (or a list of arguments), and then spread those array elements back into individual elements:

```js
const rotate = ([first, ...rest]) => [...rest, first]
rotate([1, 2, 3]) // [2, 3, 1]
```

We can also compute the value of keys to assign to:

```js
const arrToObj = ([key, value]) => ({ [key]: value })
arrToObj([ 'foo', 'bar' ]) // { "foo": "bar" }
```

### default values

Each destructured property can have a *default value*. The default value is used when the property is not present, or has value `undefined`. 
It is not used if the property has value `null`.

```js
const [a = 1] = [] // a is 1
const { b = 2 } = { b: undefined } // b is 2
const { c = 2 } = { c: null } // c is null
```

## merge

### merge with spread operator

* Use if you want to merge objects while retaining the properties from both. 
* It allows us to easily combine the elements of one object into  another.

```js
const circle = {
    radius: 10
}

const style = {
    backgroundColor: 'red'
}

const solidCircle = {
    ...circle,
    ...style
}
console.log(solidCircle) // { radius: 10, backgroundColor: 'red' }
```



assign
--------------------------

### ES6 property shorthands

```js
/* --- VARIABLES --- */

const a = 'foo', b = 42, c = function () {}

// pre ECMAScript 2015:
const alphabet = {
	a: a,
	b: b,
	c: c
}
// post ECMAScript 2015:
const alphabet = { a, b, c }

/* --- METHODS --- */

// pre ECMAScript 2015:
let obj = {
	myMethod: function(params) {},
}
// post ECMAScript 2015:
let obj = {
	myMethod(params) {},
}
```

### variables as keys

```js
// Klammernotation
obj['name'] = 'Simon'
var name = obj['name']

// define nested obj with variables
let prop = 'price'
let op = 'equals'
let val = 45
let obj = {}
obj[prop] = {[op]: val}
// ... is the same as:
let obj = {
  "price": {
    "equals": 45
  }
}

// man kann eine Variable benutzen, um einen Schlüssel zu definieren
var user = prompt('was ist dein Schlüssel?')
obj[user] = prompt('was ist dein Schlüssel?')
```
Beide sind wieder semantisch äquivalent. Die zweite Methode hat den Vorteil, dass der Name der Eigenschaft als String zur Verfügung gestellt wird, was bedeutet, dass dieser zur Laufzeit berechnet werden kann. Jedoch verhindert diese Methode, dass einige JavaScript-Engines "Minifier Optimierungen" durchführen können.

Ab ECMAScript 2015 können Schlüssel mit Variablen definiert werden, indem eckige Klammern verwendet werden. 

```js
// es ist möglich
{[phoneType]: 12345}
// zu verwenden, statt 
var userPhone = {}
userPhone[phoneType] = 12345
```

## reference

**Objects are Passed by (Copy-)Reference** (pointers to objects)!

In JS, object references are values. Because of this, objects will behave like they are passed by **reference:**
If a function changes an object property, it changes the original value.

"Objects" are not values in JavaScript, and cannot be "passed".
All the values that you are dealing with are references.

**changes to object properties are visible (reflected) outside the function.**

```js
let obj = {
  a: "hello"
}

function modify(o) {
  // changes made to the object will be visible to you after the function is done
  o.a += " world"
}

function reAssign(o) {
  // if you re-assign the object inside of your function, 
  // this will not be visible outside of the function since it was only a copy of the reference you changed.
  o = {
    a: "hello world"
  }
}

modify(obj) // "hello world"
reAssign(obj) // "hello"
```

### regarding the size of objects

Objects are passed to functions as pointers in Javascript. So, **it costs no more to pass a pointer to a large object than passing a pointer to a small object** and there are no copies of the object made when it is passed as a function argument. The memory usage of the function that is being called won't change whether the object passed to it is large or small.

Now *if the function makes an explicit copy of the object it is passed (like with `Object.assign()` or with destructuring), then the memory use of the function will be affected* by how big the object is that it's making a copy of.

## copy

```js
const person = {
    firstName: 'John',
    lastName: 'Doe'
}

// spread ...
// (shallow copy)
let p1 = {
    ...person
}

// Object.assign()
// (shallow copy)
let p2 = Object.assign({}, person)

// JSON.parse
// (deep copy)
let p3 = JSON.parse(JSON.stringify(person))
```

Both spread (`...`) and `Object.assign()` perform a shallow copy while the JSON methods carry a deep copy.

### pitfall nested objects

When you have a nested object (or array) and you copy it, nested objects inside that object will not be copied, since they are only pointers / references. Therefore, **if you change the nested object, you will change it for both instances**, meaning you would end up doing a **shallow copy again**.

## compare

In JS, objects are always stored by reference. That means **one object is strictly equal another only if they both point to the same object in memory**.

### equal

```js
let a = { a: 20, b: 10 };
let b = { b: 10, a: 20 };
const hasChanged = Object.entries(a).sort().toString() !== Object.entries(b).sort().toString()
```

```js
JSON.stringify(o1) === JSON.stringify(o2) // key order matters
JSON.stringify(Object.entries(a).sort()) == JSON.stringify(Object.entries(b).sort()) // key order doesn't matter

function areObjEqual(a, b) {
  // Object.entries(a) returns the [key, value] pairs as array
  // sorts the elements of an array in place
  JSON.stringify(Object.entries(a).sort()) == JSON.stringify(Object.entries(b).sort())
}

function areObjEqual(a, b, { ignoreKeys = [] } = {}) {
  // Object.entries(a) returns the [key, value] pairs as array
  // sorts the elements of an array in place
  // doesn't work recursively
  // only works on flat objects
  let aMod = Object.entries(a).filter(([key]) => !ignoreKeys.includes(key)).sort()
  let bMod = Object.entries(b).filter(([key]) => !ignoreKeys.includes(key)).sort()
  return JSON.stringify(aMod) == JSON.stringify(bMod)
}

areObjEqual(a, b, { ignoreKeys: ['__v', 'updatedAt', '_id'] })

#6E98AA
```

* not all types are representable in JSON
  
  The `JSON.stringify()` function converts dates to strings, and ignores keys whose value is `undefined`, which can lead to surprising results.

```js
export default function areObjEqual(a, b, ignoreKeys = [], user = '', { logDiff = true } = {}) {
	// * order doesn't matter

	let diffKey = ''

	try {
		const areEqual = iterate(a, b, ignoreKeys)
		if (!areEqual && logDiff) {
			console.log(`objects differ in "${diffKey}"`, user, __filename, 7)
		}
		return areEqual
	
		function iterate(a, b) {

			if (a !== null && typeof a === 'object' && Object.keys(a).length > 0) {
				return Object.keys(a).length === Object.keys(b).length && Object.keys(a).every(key => {
					if (ignoreKeys.includes(key)) {
						return true
					} else {
						if (iterate(a[key], b[key], ignoreKeys) === true) {
							return true
						} else {
							diffKey = key
							return false
						}
					}
				})
			} else {
				return a === b
			}
		}	
	} catch (error) {
		log(error.stack, user, __filename, 3)
	}
}
```

```js
function isDeepEqual(object1, object2) {

  const objKeys1 = Object.keys(object1);
  const objKeys2 = Object.keys(object2);

  if (objKeys1.length !== objKeys2.length) return false;

  for (var key of objKeys1) {
    const value1 = object1[key];
    const value2 = object2[key];

    const isObjects = isObject(value1) && isObject(value2);

    if ((isObjects && !isDeepEqual(value1, value2)) ||
      (!isObjects && value1 !== value2)
    ) {
      return false;
    }
  }
  return true;
};

const isObject = (object) => {
  return object != null && typeof object === "object";
};
```



### diff

```js
/*!
 * Find the differences between two objects and push to a new object
 * (c) 2019 Chris Ferdinandi & Jascha Brinkmann, MIT License, https://gomakethings.com & https://twitter.com/jaschaio
 * @param  {Object} obj1 The original object
 * @param  {Object} obj2 The object to compare against it
 * @return {Object}      An object of differences between the two
 */

function getObjDiff(obj1, obj2) {

    // Make sure an object to compare is provided
    if (!obj2 || Object.prototype.toString.call(obj2) !== '[object Object]') {
        return obj1;
    }

    // Variables
  
    var diffs = {};
    var key;
  
    // Methods

    /**
     * Check if two arrays are equal
     * @param  {Array}   arr1 The first array
     * @param  {Array}   arr2 The second array
     * @return {Boolean}      If true, both arrays are equal
     */
    var arraysMatch = function (arr1, arr2) {

        // Check if the arrays are the same length
        if (arr1.length !== arr2.length) return false;

        // Check if all items exist and are in the same order
        for (var i = 0; i < arr1.length; i++) {
            if (arr1[i] !== arr2[i]) return false;
        }

        // Otherwise, return true
        return true;

    };

    /**
     * Compare two items and push non-matches to object
     * @param  {*}      item1 The first item
     * @param  {*}      item2 The second item
     * @param  {String} key   The key in our object
     */
    var compare = function (item1, item2, key) {

        // Get the object type
        var type1 = Object.prototype.toString.call(item1);
        var type2 = Object.prototype.toString.call(item2);

        // If type2 is undefined it has been removed
        if (type2 === '[object Undefined]') {
            diffs[key] = null;
            return;
        }

        // If items are different types
        if (type1 !== type2) {
            diffs[key] = item2;
            return;
        }

        // If an object, compare recursively
        if (type1 === '[object Object]') {
            var objDiff = getObjDiff(item1, item2);
            if (Object.keys(objDiff).length > 0) {
                diffs[key] = objDiff;
            }
            return;
        }

        // If an array, compare
        if (type1 === '[object Array]') {
            if (!arraysMatch(item1, item2)) {
                diffs[key] = item2;
            }
            return;
        }

        // Else if it's a function, convert to a string and compare
        // Otherwise, just compare
        if (type1 === '[object Function]') {
            if (item1.toString() !== item2.toString()) {
                diffs[key] = item2;
            }
        } else {
            if (item1 !== item2 ) {
                diffs[key] = item2;
            }
        }

    };

    // Compare our objects

    // Loop through the first object
    for (key in obj1) {
        if (obj1.hasOwnProperty(key)) {
            compare(obj1[key], obj2[key], key);
        }
    }

    // Loop through the second object and find missing items
    for (key in obj2) {
        if (obj2.hasOwnProperty(key)) {
            if (!obj1[key] && obj1[key] !== obj2[key] ) {
                diffs[key] = obj2[key];
            }
        }
    }

    // Return the object of differences
    return diffs;

};
```

iterate
------------------------

### for in 

```js
for (let key in object) {
    // also returns the keys of the prototype
    console.log(key)
    console.log(`${key}: ${object[key]}`)
    console.log(key,":", object[key])
}
```

### Object.keys()

```js
let keys = Object.keys(localStorage)
// only returns the keys that belong to the object, ignoring the prototype.
for (let key of keys) {
	console.log(`${key}: ${localStorage.getItem(key)}`)
} 
```

### Object.values()

* preferred style

```js
Object.values(obj) // converts obj into an array (keys get lost)

for (const size of Object.values(doc.sizes)) {
  // ....
}	
```



## instance methods

### hasOwnProperty()

**`Object.hasOwn()` is recommended over `hasOwnProperty()` in browsers where it is supported**

* can be called on *most* JavaScript objects, because most objects descend from Object
* will only return true for *direct* properties

```js
obj.hasOwnProperty()

const example = {};
example.hasOwnProperty('prop')   // false

example.prop = 'exists'
example.hasOwnProperty('prop')   // true - 'prop' has been defined

example.prop = null
example.hasOwnProperty('prop')   // true - own property exists with value of null

example.prop = undefined
example.hasOwnProperty('prop')   // true - own property exists with value of undefined

example.hasOwnProperty('toString')         // false

// The `in` operator will return true for direct or inherited properties:
'prop' in example		                       // true
'toString' in example                      // true
'hasOwnProperty' in example                // true

// arrays
const fruits = ['Apple', 'Banana','Watermelon', 'Orange'];
fruits.hasOwnProperty(3);   // true ('Orange')
fruits.hasOwnProperty(4);   // false - not defined
```



static methods
-----------------------------------------------------

### summary

```js
Object.getOwnPropertyNames(obj) // returns an array of all properties (including non-enumerable properties)
Object.getPrototypeOf(object) // get the parent
Object.freeze(obj)

// get all methods of an object:
let objMethods = Object.getOwnPropertyNames(Object.getPrototypeOf(myObject))
```

```js
const obj = {
	a: 'somestring',
	b: 42,
	c: false
}
Object.keys(obj) // ["a", "b", "c"]
Object.values(obj) // ["somestring", 42, false]
Object.entries(obj) // [["a", "somestring"], ["b", 42], ["c", false]]

Object.entries(obj).forEach(([key, value]) => console.log(`${key}: ${value}`))
// convert an obj with multiple properties 
// into an array of objects
// that hold on property each
let arrOfObjs = Object.entries(obj).map(([key, value]) => {
  let o = {}
  o[key] = value
  return o
})
```

### Object.assign()

* copies all enumerable own properties from one or more source objects to a target object
* returns the modified target object
* properties in the target object are overwritten by properties in the sources if they have the same key
* creates a shallow copy of the source objects

```js
Object.assign(target, ...sources)
```

```js
let oldObject = {
	name: 'John',
  age: 35,
  skills: ['html', 'css', 'js'],
  contact: { email: 'john@slingacademy.com', phone: 123 },
};

// return a new obj:
let newObject = Object.assign({}, oldObject, { job: 'developer' })
console.log(oldObject === newObject) // false

// return the old obj:
let newObject = Object.assign(oldObject, { job: 'developer' })
console.log(oldObject === newObject) // true
```



### Object.keys()

```js
Object.keys() 
// Returns an array of a given object's *own* enumerable property names, iterated in the same order that a normal loop would.
```

### Object.values()

```js
Object.values() 
// Returns an array containing the values that correspond to all of a given object's *own* enumerable string properties.
```

### Object.entries()

```js
Object.entries() 
// Returns an array containing all of the [key, value] pairs of a given object's *own* enumerable string properties.

let obj = { foo: bar, baz: 42 }
Object.entries(obj).forEach(entry => console.log(entry)) // Array [ "id", "64c774b724856550eb1d0534" ]
Object.entries(obj).forEach(([key, value]) => console.log(key)) // id
Object.entries(obj).forEach(entry => console.log(`${entry[0]}="${entry[1]}"`)) // foo="bar", baz="42"
Object.entries(obj).forEach(([key, value]) => console.log(`${key}: ${value}`)) // "foo: bar", "baz: 42"

// for ... of loop
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key} ${value}`) // "a 5", "b 7", "c 9"
}
```

```js
// convert object with html attributes into a string:
let attributes = {
  slot: "img",
  class: "category__img",
  id: "12124353452"
}
Object.entries(attributes).map(entry => `${entry[0]}='${entry[1]}'`).reduce((prev, curr) => `${prev} ${curr}`)
// "slot='img' class='category__img' id='12124353452'" 

// additionally filter all undefined entries:
Object.entries(obj).filter(entry => entry[1]).map(entry => `${entry[0]}='${entry[1]}'`).reduce((prev, curr) => `${prev} ${curr}`)
```



### Object.fromEntries()

Returns a new object from an iterable of [key, value] pairs. (The reverse of `Object.entries`)

OPERATORS
==========================


Binary bitwise operators
----------------------------------------

Bitwise operators treat their operands as a set of 32 bits (zeros and ones) and return standard JavaScript numerical values.

```js
& // Bitwise AND
| // Bitwise OR
^ // Bitwise XOR
```

## ||

**Logical OR**

```js
// assign a default value:
let variable = foo || 'Hello!'

class Example {
	constructor(items) {
		this._items = items || []  // very useful
	}
}

// conditional error message:
console.log(result || "error occurred")
// short circuit evaluation:
let expr = true
(expr) || console.log('alternative') // exp is evaluated
let expr = false
(expr) || console.log('alternative') // alternative
```

* `||` returns the first *truthy* value.
* `??` returns the first *defined* value

### short-circuit evaluation

```js
(expr) || alt
// alt is only evaluated if expr is false
```

## &&

**Logical AND**

## ??

**Nullish Coalescing Operator**

* (only) *returns the second operand when the first one evaluates to either* ***null*** or ***undefined*** (but no other falsy values); otherwise returns its first operand;
* short syntax for selecting a first “defined” variable from the list

```js
x = a ?? b
// is a short equivalent to:  
x = (a !== null && a !== undefined) ? a : b
// or
if (a !== null && a !== undefined) x = a
else x = b

let obj = {
  name: 'udo',
  address: {
    street: 'Ludwig'
  }
}

let number 
number = obj.address.number ?? '45' // --> 45
number = obj.direction.number ?? 45 // --> TypeError
number = obj?.direction?.number ?? 45 // --> 45

let test 
test = '' || 'fg' // fg
test = '' ?? 'fg' // ''
test = undefined ?? 'fg' // fg
test = null ?? 'fg' // fg

// chainable

let args = {
  value: 'title'
}

let title = args.valued ?? args.valuesd ?? args.value // 'title'
```

### difference to ||

* `||` returns the first truthy value.
* `??` returns the first defined value

## ??=

**nullish coalescing assignment**

```js
x ??= y // only assigns y if x is nullish (null or undefined)
// is equivalent to
x ?? (x = y)
```

### default values

You can use the nullish coalescing assignment operator to apply default values to object properties. 
Compared to using destructuring and default values, `??=` also applies the default value if the property has value `null`.

```js
function config(options) {
  options.duration ??= 100 // only assigns the default value if x is nullish (null or undefined)
  options.speed ??= 25
  return options
}

config({ duration: 125 }) // { duration: 125, speed: 25 }
config({}) // { duration: 100, speed: 25 }
```



?
------------------------------------

### conditional assignment

* falsy expressions are: *false*, *null*, *NaN*, *0*, the empty string (*""*), and *undefined*

```js
let result = (condition) ? ifTrue : ifFalse
// *condition* is evaluated: if it’s truthy then value *ifTrue* is returned, otherwise value *ifFalse*
let accessAllowed = (age > 18) ? ifTrue : ifFalse

let age = prompt('age?', 18);
let msg = 
  (age < 3) ? 'Hi, baby!' :
	(age < 18) ? 'Hello!' :
	(age < 100) ? 'Greetings!' :
	'What an unusual age!';
let newOrder = (todo.order > 1) ? todo.order-- : todo.order // If todo.order is greater than 1 substract 1 and save it to newOrder. If not use the old value.
```

### examples

```js
// with arrays
let b = true
att = [
    (b) ? 'true' : 'false',
    (!b) ? 'true' : 'false'
]
// Array [ "true", "false" ]

// adding to strings
let state = ''
state += (state) ? ' more' : 'more' // "more"
state += (state) ? ' more' : 'more' // "more more"
```

### shortening of conditional constructs

```js
var direction;
if(x > 100){
	direction = 1;
} else {
	direction = -1;
}

// ... can be shortened to a single line:
var direction = (x > 100) ? 1 : -1;
```

### shortening of functions

```js
let todos = [
	{id: 1, text: 'Run a marathon', complete: false},
	{id: 2, text: 'Plant a garden', complete: false},
]

function editTodoShort(id, updatedText) {
	todos = todos.map((item) => 
		item.id === id ? {id: item.id, text: updatedText, complete: item.complete} : item
		// if (item.id === id) is true a new item object is returned
		// if (item.id === id) is false the old item object is returned
	)
}

function editTodoLong(id, updatedText) {
	todos = todos.map((item) => {
		if (item.id === id) return {id: item.id, text: updatedText, complete: item.complete}
		else return item
	})
}

/* more examples */

function getFee(isMember) {
  	return (isMember ? '$2.00' : '$10.00');
}
```

## ?.

**Optional Chaining operator**

* permits reading the value of a property located deep within a chain of connected objects without having to expressly validate that each reference in the chain is valid.
* returns *undefined* instead of an error if a property doesn't exist
* cannot be used on a non-existent root object. It does not replace checks like if `(typeof a == "undefined")`.

The *non-existing property* problem:

```js
obj.val?.prop
obj.val?.[expr]
obj.func?.(args)

let user = {} // a user without "address" property
console.log(user.address.street) // Error!

// return undefined instead of an error:
console.log(user.address ? user.address.street : undefined); // undefined
// gets awful with nested properties:
console.log(user.address ? user.address.street ? user.address.street.name : null : null);
// using ?.
console.log(user?.address?.street); // undefined
// cannot be used on a non-existent root object
if (typeof nonexistent.innerHTML == "undefined") { console.log("undefined") }
```

* The optional chaining `?.` stops the evaluation *if the part before* `?.` *is undefined* or *null* and returns that part.
* Use `?.` only where it’s ok that something doesn’t exist.  
  For example, if the *user* object must exist, but *address* is optional, then we should write `user.address?.street`, but not `user?.address?.street`  
  So, if *user* happens to be undefined due to a mistake, we’ll see a programming error about it and fix it. Otherwise, coding errors can be silenced where not appropriate, and become more difficult to debug.
* The optional chaining works only for declared variables.  
  If there’s no variable *user* at all, then `user?.anything` triggers an error

## in operator

returns `true` if the specified property is in the specified object or its prototype chain

```js
// Arrays
const trees = ["redwood", "bay", "cedar", "oak", "maple"];
0 in trees; // true
3 in trees; // true
6 in trees; // false
"bay" in trees; // false (you must specify the index number, not the value at that index)
"length" in trees; // true (length is an Array property)

// Predefined objects
"PI" in Math; // true

// Custom objects
const mycar = { make: "Honda", model: "Accord", year: 1998 };
"make" in mycar; // true
"model" in mycar; // true
mycar.make = undefined;
"make" in mycar; // true
```


Assignment operators
---------------------------------------------

An assignment operator assigns a value to its left operand based on the value of its right operand.

```js
= 		// Assignment operator.
*= 		// Multiplication assignment.
**= 	// Exponentiation assignment.
/= 		// Division assignment.
%=		// Remainder assignment.
+=		// Addition assignment.
-=		// Subtraction assignment
<<=		// Left shift assignment.
>>=		// Right shift assignment.
>>>=	// Unsigned right shift assignment.
&=		// Bitwise AND assignment.
^=		// Bitwise XOR assignment.
|=		// Bitwise OR assignment.
&&=		// Logical AND assignment.
||=		// Logical OR assignment.
??=		// Logical nullish assignment.

[a, b] = [1, 2]
{a, b} = {a:1, b:2}

/* Destructuring assignment allows you to assign the properties of an array or object to variables using syntax that looks similar to array or object literals. */
```

Arithmetic operators
---------------------------------------------

### %

* remainder (Rest einer Division)
* returns the remainder left over when one operand is divided by a second operand. 
* always takes the sign of the dividend.

```js
12 % 5	//  2
-12 % 5	// -2
4 % 2	//  0
-4 % 2	// -0
```


Comparison operators
------------------------------------------

The result of evaluating an equality operator is always of type Boolean based on whether the comparison is true.

```js
/* Equality operators */

==		// Equality operator.
!=		// Inequality operator.
===		// Identity operator.
!==		// Nonidentity operator.

/* Relational operators */

in 			// determines whether an object has a given property.
instanceof	// determines whether an object is an instance of another object.
<			// Less than operator.
>			// Greater than operator.
<=			// Less than or equal operator.
>=			// Greater than or equal operator.
```

#### ==

* Two objects are equal == only if they’re references to the same object.
* If one of arguments of == is an object, and the other one is a primitive, then the object gets converted to primitive, as explained in the chapter Object to primitive conversion.
* …With an exception of null and undefined that equal == each other and nothing else.

# PERFORMANCE

## arrays vs objects

associative arrays are much slower then arrays with numbered indexes, because associative arrays work by doing string comparisons, which are  much, much slower then number comparisons!

```js
// 1. array
var id = 29938;
var result = a1.find(x => x.id === id)
// 2. object
var id = '29938';
var result = o[id];
// 3. associative array (object)
var id = 29938;
var result = a2[id];
```

SETS
======================================

* a special type collection – *set of values* (without keys), where each value may occur only once
* optimized internally for uniqueness checks

The main feature is that repeated calls of `set.add(value)` with the same value don’t do anything.  
That’s the reason why each value appears in a Set only once.

## solutions

```js
let string = ""
let myset = new Set()
myset.add(3)
myset.add(" Wise ")
myset.add(" Men ")
myset.forEach(value => string += value)  // → "3 Wise  Men "
```

## iteration

We can loop over a set either with `for..of` or using `forEach`:

```js
let set = new Set(["oranges", "apples", "bananas"])

// for..of
// didn't work for me in Typescript Payload project
for (let value of set) { 
  console.log(value)
}
// for..of
// solution:
let arr = Array.from(set)
for (let value of arr) { 
  console.log(value)
}

// the same with forEach:
set.forEach((value, valueAgain, set) => {
  console.log(value)
})
```

Note the funny thing. The callback function passed in `forEach` has 3 arguments: a *value*, then the same value *valueAgain*, and then the target object.  Indeed, the same value appears in the arguments twice.

That’s for compatibility with Map where the callback passed forEach has three arguments. Looks a bit strange, for sure. But may help to replace Map with Set in certain cases with ease, and vice versa.


create
--------------------------

* `new Set(iterable)` # creates the set, and if an iterable object is provided (usually an array), copies values from it into the set.

```js
const s = new Set([...newArr, ...oldArr]) // create set from multiple arrays
```

## compare

```js
const a = new Set([1,2,3]);
const b = new Set([1,3,2]);

const areSetsEqual = (a, b) => a.size === b.size && [...a].every(value => b.has(value));

console.log(areSetsEqual(a,b))

function areSetsEqual(a, b) {
  return a.size === b.size && [...a].every(value => b.has(value))
}
```

## convert

### from array

```js
let arr = [55, 44, 65]
let brr = [15, 12, 65]
let set = new Set(arr)
// multiple arrays --> one set
let set = new Set([...arr, ...brr])
```

### to array

Converting a Set to an Array is convenient for using array methods that aren't on sets, like filter() or reduce(). The most concise way to convert a Set to an array is using the spread operator as follows.

```javascript
const set = new Set(['a', 'b', 'c']);

[...set]; // ['a', 'b', 'c']
[...set].map(c => c.toUpperCase()); // ['A', 'B', 'C']
```

### to string

```js
let string = "";
let myset = new Set();
myset.add(3);
myset.add(" Wise ");
myset.add(" Men ");

myset.forEach(value => string += value);
console.log(string); // → "3 Wise  Men "
```

instance methods
-------------------------

```js
set.add(value)    // adds a value, returns the set itself.
// only inserts if there isn't an element with the same value already in the Set
set.delete(value) // removes the value, returns true if value existed at the moment of the call, otherwise false.
set.has(value)    // returns true if the value exists in the set, otherwise false.
set.clear()       // removes everything from the set.
```

```js
// Delete any point with `x > 10`.
set1.forEach((point) => {
  if (point.x > 10) {
    set1.delete(point);
  }
});
```

instance properties
---------------------------------------

* `set.size`          # is the elements count.


examples
-----------------

```js
let set = new Set();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

// visits, some users come multiple times
set.add(john);
set.add(pete);
set.add(mary);
set.add(john);
set.add(mary);

// set keeps only unique values
alert( set.size ); // 3

for (let user of set) {
  alert(user.name); // John (then Pete and Mary)
}
```

STRINGS
========================

## solutions

### encoding

```js
// ascii -> utf-8
Buffer.from("brÃ¼cke.jpeg", 'ascii').toString() // brücke.jpeg
```

### text format

#### capitalize each word

```js
function capitalizeWords(str) {
  return str.replace(/\b\w/g, match => match.toUpperCase())
}
```

### url

#### isValidURL()

```js
const urlPattern = /(?:https?):\/\/(\w+:?\w*)?(\S+)(:\d+)?(\/|\/([\w#!:.?+=&%!\-\/]))?/;

function isValidURL(urlString) {
  const urlPattern = new RegExp('^(https?:\\/\\/)?'+ // validate protocol
  '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|'+ // validate domain name
  '((\\d{1,3}\\.){3}\\d{1,3}))'+ // validate OR ip (v4) address
  '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*'+ // validate port and path
  '(\\?[;&a-z\\d%_.~+=-]*)?'+ // validate query string
  '(\\#[-a-z\\d_]*)?$','i'); // validate fragment locator
  
  return !!urlPattern.test(urlString);
}
```

#### replaceUmlauts()

```js
str.replaceAll('ö', 'oe').replaceAll('ä', 'ae').replaceAll('ü', 'ue')
```

#### slugify()

```js
// Slugify a string
function slugify(str) {
    str = str.replace(/^\s+|\s+$/g, '');

    // Make the string lowercase
    str = str.toLowerCase();

    // Remove accents, swap ñ for n, etc
    var from = "ÁÄÂÀÃÅČÇĆĎÉĚËÈÊẼĔȆÍÌÎÏŇÑÓÖÒÔÕØŘŔŠŤÚŮÜÙÛÝŸŽáäâàãåčçćďéěëèêẽĕȇíìîïňñóöòôõøðřŕšťúůüùûýÿžþÞĐđßÆa·/_,:;";
    var to   = "AAAAAACCCDEEEEEEEEIIIINNOOOOOORRSTUUUUUYYZaaaaaacccdeeeeeeeeiiiinnooooooorrstuuuuuyyzbBDdBAa------";
    for (var i=0, l=from.length ; i<l ; i++) {
        str = str.replace(new RegExp(from.charAt(i), 'g'), to.charAt(i));
    }

    // Remove invalid chars
    str = str.replace(/[^a-z0-9 -]/g, '') 
    // Collapse whitespace and replace by -
    .replace(/\s+/g, '-') 
    // Collapse dashes
    .replace(/-+/g, '-'); 

    return str;
}
```

### files

#### removeFileExtension

```js
let filename = '059f81d8e3218e0693898b21f45b71fc.webp'
filename = filename.substring(0, filename.lastIndexOf('.'))

filename = filename.split('.').slice(0, -1).join('.'); 
```

#### get directory names

```js
'/doc/audio/musik/genelec'.replace('/doc/', '').replace('/genelec', '').split('/')
// (2) ['audio', 'musik']
```



### insert

#### insert string at index

```js
// using slice()
let str = 'Kohlendioxid'
let insert = 'stoff'
str = str.slice(0, 6) + insert + str.slice(6) // style A
str = `${str.slice(0, 6)}${insert}${str.slice(6)}` // style B
```

### get

#### getIndexesOf()

```js
function getIndexesOf(str, pattern) {
	let indexes = []
	let lastMatchIndex = 0
	while (true) {
    console.log(`trying with ${pattern}, ${lastMatchIndex}`)
		lastMatchIndex = str.indexOf(pattern, lastMatchIndex + 1) // +1 important
		console.log(lastMatchIndex)
		if (lastMatchIndex > -1) {
			indexes.push(lastMatchIndex)
    } else {
			break
		}
	}
	return indexes
}

// usage
let matchIndexes = getIndexesOf(docStr, 'contentHTML') // [ 2661, 5382 ]
```

#### getClosestIndex()

```js
function getClosestIndex(str = '', indexes = [], pattern) {
  let lastDist
  let closestIndex
  indexes.forEach(srcIndex => {
    let destIndex = str.indexOf(pattern, srcIndex) // get index of pattern
    let currentDist = destIndex - srcIndex // get current distance
    if (lastDist && Math.abs(currentDist) < Math.abs(lastDist)) {
      // if lastDist is set and currentDist is smaller than lastDist...
      closestIndex = srcIndex
    }
    console.log(`distance of index ${srcIndex} to pattern: ${currentDist}`)
    lastDist = currentDist
  })
  return closestIndex ?? indexes[0]
}

// usage
let closestIndex = getClosestIndex(docStr, matchIndexes, blockID)
```

### cut

#### ### cutFromIndexToPattern()

```js
function cutSubStrFromIndexToPattern(str = '', startIndex = 0, pattern = '') {
	let substr = str.slice(startIndex, str.indexOf(pattern, startIndex))
  return substr
}

let substr = cutSubStrFromIndexToPattern(docStr, closestIndex, '"\,') // that seams to be a good delimiter in json strings
```

### remove

```js
str.replace(/\t/g,' ') // remove tabs
str.replace(/ +/g, ' ') // remove extra white space
str.replace(/[\r\n]+/gm, "") // remove line breaks

html.replace(/\s+/g, " ") // remove all kinds of surplus white spaces and replace them with a single one
```

#### removeFirstOccurence()

```js
function _removeFirstOccurrence(str, searchstr)       {
	let index = str.indexOf(searchstr)
	if (index === -1) return str
  	return str.slice(0, index) + str.slice(index + searchstr.length)
}
```

### json strings

#### test json

```js
let obj = {
                                        contentRichText: [
                                            {
                                                type: "p",
                                                children: [
                                                    {
                                                        text: "Mein Name ist Manuel Dieterich und ich bin Soziologe. Dabei interessieren mich soziale Prozesse, gesellschaftliche Zusammenhänge und im allgemeinen die Sozio-Logik des menschlichen Zusammenlebens. Dieser versuche ich mit qualitativen Methoden auf den Grund zu gehen, hauptsächlich als Ethnograf, der in Grounded Theory geschult ist. In meinen Forschungen habe ich mich mit vielfältigen Themen auseinandergesetzt, wie etwa Diversität und soziale Ungleichheit, das Wechselspiel von Bedrohungsdynamiken und Zusammenleben, Migration und Flucht, Moralisierungen und Polarisierungen, Städte und Nachbarschaftsbeziehungen, sowie mit sozialen Konflikten, Kritik und Rechtfertigungen.",
                                                    },
                                                ],
                                            },
                                            {
                                                type: "p",
                                                children: [{ text: "" }],
                                            },
                                            {
                                                type: "p",
                                                children: [
                                                    {
                                                        text: "Aktuell arbeite ich als wissenschaftlicher Mitarbeiter und Doktorand am SFB 923 Bedrohte Ordnungen der Universität Tübingen. In meinem Dissertationsprojekt geht es um die Dynamiken und Veränderungen der nachbarschaftlichen Gruppenbeziehungen aufgrund von Bedrohungskommunikation in zwei ungleichen und diversen Stadtteilen in Johannesburg.",
                                                    },
                                                ],
                                            },
                                            {
                                                type: "p",
                                                children: [{ text: "" }],
                                            },
                                            {
                                                type: "p",
                                                children: [
                                                    {
                                                        text: "An der Universität Tübingen bin ich Mitglied des interdisziplinären Forschungsnetzwerks UnKUT . In diesem Rahmen diskutieren wir regelmäßig fächerübergreifend unsere aktuellen Forschungs- und Publikationsarbeiten und organisieren verschiedene Veranstaltungen, wie Vorträge, Diskussionen oder Retreats. Außerdem habe ich als Teil der Kritischen Uni Tübingen (KUT) schon zahlreiche Vorträge und selbstorganisierten Lesekreise mitgestaltet.",
                                                    },
                                                ],
                                            },
                                            {
                                                type: "p",
                                                children: [{ text: "" }],
                                            },
                                            {
                                                type: "p",
                                                children: [
                                                    {
                                                        text: "Von November 2019 – September 2020 war ich visiting scholar am ACMS (African Center for Migration and Society) und an der School of Geography, Archeology and Environmental Studies, beide an der Wits Universität in Johannesburg. Meine parallel dazu laufende Feldforschung in Johannesburg musste ich aufgrund der Corona-Pandemie unterbrechen. Von Februar bis April 2021 konnte ich den unterbrochenen Feldaufenthalt fortsetzen und war visiting scholar am Centre for Diversity Studies, ebenfalls an der Wits Universität. Ich bin außerdem Mitglied in der Deutschen Gesellschaft für Soziologie (DGS) seit 2020 und seit 2022 Mitglied der International Sociological Association (ISA) (RC03 - Community Research & RC31 - Sociology of Migration).",
                                                    },
                                                ],
                                            },
                                        ],
                                        contentHTML: null,
                                        useAdvancedConfig: false,
                                        advancedConfig: {
                                            floatImg: "right",
                                            deactivate: false,
                                        },
                                        id: "63e27d4dd182f614584fdd33",
                                        blockType: "rich-text",
                                    }
let jsonStr = JSON.stringify(obj)
```

#### find and replace json property

```js
// find a property (that appears mutliple times) within a json string based on its 'neighbourhood' to a reference property or value and cut it out

let ref = '63e27d4dd182f614584fdd33'
let searchProp = 'contentHTML'

let matchIndexes = getIndexesOf(jsonStr, 'contentHTML')
let closestIndex = getClosestIndex(jsonStr, matchIndexes, ref)
let jsonDelimiter = ',\"' // '"\,'
let substr = cutSubStrFromIndexToPattern(jsonStr, closestIndex, jsonDelimiter)

function getIndexesOf(str, pattern) {
	let indexes = []
	let lastMatchIndex = 0
	while (true) {
    console.log(`trying with ${pattern}, ${lastMatchIndex}`)
		lastMatchIndex = str.indexOf(pattern, lastMatchIndex + 1) // +1 important
		console.log(lastMatchIndex)
		if (lastMatchIndex > -1) {
			indexes.push(lastMatchIndex)
    } else {
			break
		}
	}
	return indexes
}

function getClosestIndex(str = '', indexes = [], pattern) {
  let lastDist
  let closestIndex
  indexes.forEach(srcIndex => {
    let destIndex = str.indexOf(pattern, srcIndex) // get index of pattern
    let currentDist = destIndex - srcIndex // get current distance
    if (lastDist && Math.abs(currentDist) < Math.abs(lastDist)) {
      // if lastDist is set and currentDist is smaller than lastDist...
      closestIndex = srcIndex
    }
    console.log(`distance of index ${srcIndex} to pattern: ${currentDist}`)
    lastDist = currentDist
  })
  return closestIndex ?? indexes[0]
}

function cutSubStrFromIndexToPattern(str = '', startIndex = 0, pattern = '') {
	let substr = str.slice(startIndex, str.indexOf(pattern, startIndex))
  return substr
}
```

```js
getAlternateLocaleURLs(pageURL = '', otherURLs = []) {
		// get all URLs that only differ in the language code
	return otherURLs.filter(otherHref => (otherHref !== pageURL && otherHref.replace(/\/(de|en)\//, '') === pageURL.replace(/\/(de|en)\//, '')))
}
```

### compare

```js
function getStrDiff(a, b) {
  
  let result = {
    state : true,
    diffs : []
  }

  if(a===b) return result;
  
  result.state = false;

  for (let index = 0; index < Math.max(a.length,b.length); index++) {
    if (a[index] !== b[index]) {
        result.diffs.push({index: index, old: a[index], new: b[index]})
    }
  }
  
  return result;
}
```

### interpolate

```js
function Prop(obj, is, value) {
    if (typeof is == 'string')
        is = is.split('.');
    if (is.length == 1 && value !== undefined)
        return obj[is[0]] = value;
    else if (is.length == 0)
        return obj;
    else {
        var prop = is.shift();
        // Forge a path of nested objects if there is a value to set
        if (value !== undefined && obj[prop] == undefined) obj[prop] = {}
        return Prop(obj[prop], is, value);
    }
}

function interpolate(str, obj) {
  /*
  	Task:
    	Replace ${exProp} in str with value of obj.exProp
  */
  return str.replace(/\$\{(.+?)\}/g, (match, p1) => { 
   	/*
   		Replacement function.
   		Called for each match of ${} with the matched substring and 
   	*/
    return Prop(obj, p1) 
  })
}

// usage
let templateObj = { title: 'First Post', id:44, richText: 'Jo this is my first post' }
let templateStr = '<article class="post" id="${id}"><header><h1>${title}</h1></header><section class="content">${richText}</section>\n</article>'

interpolate(templateStr, templateObj)
```



## template literals

Template literals are string literals allowing embedded expressions. You can use multi-line strings and string interpolation features with them. 

```js
`string text ${expression} string text`
```

You can perform more complex expressions as well:

```js
const string = `something ${1 + 2 + 3}`
// conditional
const string2 = `something ${doSomething() ? 'x' : 'y' }`
for (let i = 1; i <= 100; i++) {
	console.log(`Door ${i} is ${doors[i-1] ? 'open' : 'closed'}`);
}
```

and strings can span over multiple lines:

```js
const string3 = `Hey
this
string
is awesome!`
```

## tagged template literals

```js
function html(literals, ...vars) {
  // ...vars captures all the variables used in that template literal
  let raw = literals.raw // array of all the string portions of the template literal
}
```

### solutions

#### renderHTML

```js
function html(literals, ...vars) {
	let raw = literals.raw
	let result = ''
	let i = 1
	let len = arguments.length
	let str
	let variable

	while (i < len) {

		str = raw[i - 1]
		variable = vars[i - 1]
		result += str + variable // combine our strings with our variables
		i++
	}

	result += raw[raw.length - 1]

	return result
}

function render(data, template) {
	/* 
		Task:
		- Call the given template function with data
		Arguments: 
		- data: The data to use
		- template: html tagged template function
	*/
	if (!template) return
  
	if (typeof data === 'string') {
		// Handle primitive type:
		return template(data)
	} 
  else if (typeof data === 'object' && !Array.isArray(data)) {
		// Handle object:
		return template(data)
	} 
  else if (Array.isArray(data)) {
		// Handle array:
		return data.map(item => template(item)).join('')
	}
}
```

```js
// test with primitive type
let name = 'John Doe'
let nameTemplate = (name) => html`<p>${name}</p>`

render(name, template)
html`<p>${name}</p>`
```

```js
// test with obj
let person = {
  firstName: 'Joe',
  lastName: 'Bodoni',
  job: 'Mechanic',
  age: 26
}

let personTemplate = (person) => html`
  <p>Name: ${person.firstName} ${person.lastName}</p>
  <p>Age: ${person.age}</p>
  <p>Job: ${person.job}</p>`

render(person, personTemplate)
```

```js
// test with array
let people = [
  {
    firstName: 'Joe',
    lastName: 'Bodoni'
  },
  {
    firstName: 'Ellen',
    lastName: 'Vanderbilt'
  },
  {
    firstName: 'Sam',
    lastName: 'Anderson'
  }
]

let peopleTemplate = (person) => html`
  <li>
    ${person.firstName} ${person.lastName}
  </li>`

render(people, peopleTemplate)
```

```html
${logo}
<div class="flex">
	${albumCover}
</div>
${nextShows}
```



instance methods
------------------

### template literal

#### raw()

get the raw string form of template literals — that is, substitutions like `${foo}` are processed, but escape sequences like `\n` are not.

```js
`Hi\n${2 + 3}!`
/* 
"Hi
5!" 
*/
String.raw`Hi\n${2 + 3}!`
// "Hi\\n5!"
```

### return string

#### slice() vs. substring ()

* Negative numbers: With `slice()`, when you enter a negative number as an argument, the `slice()` interprets it as counting from the end of the string. With `substring()`, it will treat a negative value as zero.
* A big difference with `substring()` is that if the 1st argument is greater than the 2nd argument, `substring()` will swap them. `slice()` returns an empty string if the 1st argument is greater than the 2nd argument.

#### slice()

```js
str.slice(startIndex, endIndex) // extracts a section of a string and returns it as a new string
// startIndex: index of the first character to INCLUDE in the returned substring
// endIndex: optional; before which to end extraction. The character at this index will not be included

// using negative indexes:
let str = 'The morning is upon us.'
str.slice(-3)      // returns 'us.'
```

```js
  const destID = evt.target.href.slice((evt.target.href.lastIndexOf('#') + 1))
```

#### substring()

```js
str.substring(startIndex, endIndex)
// extracts characters from startIndex up to but not including endIndex
// * startIndex: index of the first character to include in the returned substring
// * endIndex: index of the first character to exclude from the returned substring


const str = 'Mozilla'
str.substring(1, 3) // "oz"
str.substring(str.length - 5)) // "zilla"
```

#### replace()

```js
const newStr = str.replace(pattern, replacement) // returns a new string with some or all matches of a pattern replaced by a replacement. The original string is left unchanged
// * 	pattern can be a string or a RegExp  
// *	If pattern is a string, only the first occurrence will be replaced
// * 	replacement can be a string or a function to be called for each match

let re = /(\w+)\s(\w+)/
let str = 'John Smith'
let newstr = str.replace(re, '$2, $1') // Smith, John


html = html.replace(/\t/g,' ') // remove tabs
html = html.replace(/ +/g, ' ') // remove extra white space
html = html.replace(/[\r\n]+/gm, "") // remove line breaks
```

##### replacement function

* will be invoked after the match has been performed
* the  function's return value will be used as the replacement string

```js
str.replace(/(\a+)(\b+)/, (match, p1, p2) => {
  // in this case:
  // p1 is the match for \a+
  // p2 is the match for \b+
}
```

```js
// replacement function
let templateStr = '${logo} <div class="flex"> ${albumCover} </div> ${nextShows}'
let replacements = {
    logo: '<img class="logo" id="3456456ui">',
    albumCover: '<img class="album" id="3456456ui">',
}

templateStr.replace(/\$\{(.+?)\}/g, (match, p1) => {
    // match: ${logo} this will be replaced
    // p1: logo
    return replace[p1]
})
```

#### repeat()

returns a new string which contains the specified number of copies of the string on which it was called, concatenated together.
**`str.repeat(count)`** # 

```js
'abc'.repeat(2)     // 'abcabc'
```

#### toLowerCase()

```js
str = str.toLowerCase() // returns a new string representing the calling string converted to lower case.
```

#### trim()

```js
str = str.trim() // returns a new string with all kinds of whitespaces removed from both ends of *str* 
// including: space, tab, no-break space and all the line terminator characters (LF, CR)
```

#### trimStart()

```js
str = str.trimStart() // removes whitespace from the beginning of a string and returns a new string
```

#### trimEnd()

```js
str = str.trimEnd() // removes whitespace from the end of a string and returns a new string
```

### return index

#### search()

* works with regex patterns

```js
str.search(regexp) // returns the index of the first match between the regular expression and the given string
// or -1 if no match was found
```

#### indexOf()

* only works with string patterns

```js
str.indexOf(substr) // searches str and returns the index of the first occurrence of the specified substring
str.indexOf(substr, startIndex) // returns the first occurrence of the specified substring at an index GREATER than or equal to the specified number.

/* ex */
const str = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?'
let indexFirstOcc = str.indexOf('dog') // 40 <-- index of first "dog" from the beginning
str.indexOf('dog', (indexFirstOcc + 1)) // 52 <-- index of second "dog" starting from the first
```

#### lastIndexOf()

* returns `-1` if not found

```js
str.lastIndexOf(substr) // returns the index of the last occurrence of the specified substring
str.lastIndexOf(substr, startIndex) // returns the last occurrence of the specified substring at an index LESS than or equal to the specified number

const str = 'The quick brown fox jumps over the lazy dog. If the dog barked, was it really lazy?'
str.lastIndexOf('dog') // index of the first "dog" from the end is 52"

const str = 'www.rjuschka.de'
str.slice(0, str.lastIndexOf('.')) // www.rjuschka
```

### return array

#### split()

```js
str.split('separator') // takes a pattern and divides a string into an ordered list of substrings by searching for the pattern, puts these substrings into an array, and returns the array.

const str = 'The quick brown fox jumps over the lazy dog.'
const words = str.split(' ') 
console.log(words[3]) // "fox"
```

### return boolean

#### startsWith()

```js
str.startsWith(searchString, position) // returns true if the given characters are found at the beginning of the string
```

#### endsWith()

```js
str.endsWith(searchString, endPosition) // returns true if the given characters are found at the end of the string, including when searchString is an empty string; otherwise, false
```



#### includes()

* performs a case-sensitive search to determine whether one string may be found within another string, returning true or false
* case sensitive

```js
str.includes(searchString, position)
// Returns true if searchString is found anywhere within str; otherwise, false
// *position* is optional (position within str at which to begin searching for searchString)

// case insensitive
str.toLowerCase().includes(searchStr)
```

### regex

#### match()

**`str.match(regexp)`**

* If you only want the first match found, you might want to use `RegExp.exec()` instead.
* If you need to know if a string matches a regular expression RegExp, use `RegExp.test()`.

Return value:
An Array whose contents depend on the presence or absence of the global (g) flag, or null if no matches are found.

* If the `g` flag is used, all results matching the complete regular expression will be returned, but capturing groups will not.
* if the g flag is not used, only the first complete match and its related capturing groups are returned. In this case, the returned item will have additional properties as described below.

#### matchAll()

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

#### replace()

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

# TYPES

## js weirdness

```js
typeof null // "object"
typeof undefined // "undefined"
```



VARIABLES
=================================


Scope
--------------------------------------

### scope basics

* JavaScript has *function scope*: Each function creates a new scope.
* Each scope can “look out” into the scope around it. The exception is when multiple bindings have the same name—in that case, code can see only the innermost one. 
* child scopes have access to parent scopes, but not vice versa.
* all scopes have access to the global scope. 
* Global scope lives as long as your application lives.
* Local Scope lives as long as your functions are called and executed.

```js
// Global Scope
function someFunction() {
    // Local Scope #1
    function someOtherFunction() {
        // Local Scope #2
    }
}
```

#### global

* Variables created *without a declaration keyword* (var, let, or const) are always global, even if they are created inside a function.
* global scope = window object
  * all global variables belong to the window object: check `window`
  * global variables can be used (and changed) by all scripts in the page (and in the window).

#### local

* local variables are created when a function starts, and deleted when the function is completed.
* Function arguments work as local variables inside functions.
* code between curly braces `{}`
* `let`, `const`

```js
if (true) {
    // global scope:
    var name = 'Hammad';
    // local scope:
    let likes = 'Coding';
    const skills = 'JavaScript and PHP';
}
console.log(name); // logs 'Hammad'
console.log(likes); // Uncaught ReferenceError: likes is not defined
console.log(skills); // Uncaught ReferenceError: skills is not defined
```

#### lexical

* *lexical* refers to the fact that lexical scoping uses the location where a variable is declared within the source code to determine where that variable is available. Nested functions have access to variables declared in their outer scope.


### dealing with scope

#### object container

* help to avoid polution of the global scope
* drawback: all properties are *public*

#### private scope (Kapselfunktion)

* help to avoid polution of the global scope
* drawback: all properties are *private*

In JavaScript there is no such thing as public or private scope. However, we can emulate this feature using *closures* and *immediately-invoked function expressions* (Kapselfunktion).  
To keep everything separate from the global we must first encapsulate our functions within a function like this:

```js
(function () {
  // private scope
})();
```

#### other examples

```js
const Book = function(getTitle, getAuthor) { 
   	// Private variables / properties  
   	let title = getTitle; 
   	let author = getAuthor;
	// Public method 
   	this.giveTitle = function() {
    	return title;
   }
   	// Private method
   	const summary = function() {
    	return `${title} written by ${author}.`
   	}
	// Public method that has access to private method.
   	this.giveSummary = function() {
    	return summary()
   	} 
}
const book1 = new Book('Hippie', 'Paulo Coelho');
book1.giveTitle();		// "Hippie"
book1.summary(); 		// Uncaught TypeError: book1.summary is not a function
book1.giveSummary(); 	// "Hippie written by Paulo Coelho."


// using class syntax
class Mag {
    constructor(title, author) {
		// private prop
		author = author;
		// public prop
		this.title = title;
		// private meth
        const summary = function() {
            return `${title} written by ${author}.`
        }
        // public meth
        this.giveAuthor = function() {
            return author;
        }
        this.giveSummary = function() {
            return summary()
        }
    }
}
```

### The Revealung Module Pattern

* combination of object containers and private scopes
* allows us to scope our functions using both *public* and *private* scopes in an object.
* Kurz gesagt gibt die Kapselfunktion (immediately-invoked function expression) ein Objekt nach draußen, bevor sie sich beendet.
* central principle: *all functionality and variables should be hidden unless deliberately exposed*
  How:
* Es wird eine Kapselfunktion verwendet
* Das Neue ist, dass diese Funktion einen Wert zurückgibt, der in einer Variable gespeichert wird
* Dieser Wert ist ein anObjekt, welches mittels *return* nach draußen gegeben wird. An dem Objekt hängen die öffentlichen Eigenschaften und Methoden:

```js
var Module = (function () {
    function _privateMethod() {
        // do something
    }
    function publicMethod() {
        // do something
    }
    return {
        publicMethod: publicMethod,
    }
})();
```

* One convention is to begin private functions with an underscore, and returning an anonymous object containing our public functions. This makes them easy to manage in a long object.


Assignment
------------------------------------------

### conditional assignment: ?

conditional assignment; assign a variable depending on a condition; ternary operator  

```js
let variable = condition ? ifTrue : ifFalse
// condition is evaluated: if it’s truthy then value 1 is returned, otherwise value 2
```

### assign a default value

```js
let variable = foo || 'Hello!'
let variable = value ?? value
let height = 100 ?? height // set height=100, if height is null or undefined
```


### modify in-place

modify-and-assign operators exist for all arithmetical and bitwise operators: `+=`, `-=`, `*=`, `/=` ...

```js
let n = 2;
n += 5; // now n = 7 (same as n = n + 5)
n *= 2; // now n = 14 (same as n = n * 2)
```

### increment / decrement

can only be applied to variables; using it on a value like `5++` will give an error  
`++` 	#  increases a variable by 1  
`--`	# decreases a variable by 1

* *prefix* form (`++counter`) returns the new value (increase and immediately use the result)
* *postfix* form (`counter++`) returns the old value (prior to increment/decrement).

```js
// instead of...
counter = counter + 1;
// you can write...
counter += 1;
// or even shorter...
counter++
```

### desctructuring assignment

... ermöglicht es, Daten aus Arrays oder Objekten zu extrahieren, und zwar mit Hilfe einer Syntax, die der Konstruktion von Array- und Objekt-Literalen nachempfunden ist.

#### ...with arrays

```js
let a, b, rest
[a, b] = [10, 20]

console.log(a)     // 10
console.log(b)     // 20

[a, b, ...rest] = [10, 20, 30, 40, 50]

console.log(rest) // Array [30,40,50]
```

```js
let arr = ["Frida", "Kalo"]

// destructuring assignment:
let [name, surname] = arr
// let name = arr[0]
// let surname = arr[1]

// assign to an object:
let user = {}
[user.name, user.surname] = arr

alert(firstName) // Frida
alert(surname)  // Kalo
```

```js
const a = [1,2,3,4,5]
const [first, second] = a
// declares the variable *first* with the value *1*
// declares the variable *second* with the value *2*
```

#### ...with objects

In its basic form it allows you to refer to the properties of an object directly:

```js
var o = { first: 1, second: 2 }   // erstellt ein Objekt mit zwei properties
var { first, second } = o         // erstellt zwei Variablen mit dem Inhalt von o.first sowie o.second
console.log(first)              // 1 (same as o.first)
console.log(second)             // 2 (same as o.second)
o.first = "changed";            // Achtung: Nur o.first wird geändert!  
console.log(first)              // 1
```

Given an object, you can extract just some values and put them into named variables:

```js
const person = {
  firstName: 'John',
  lastName: 'Doe',
  actor: true,
  age: 54,
}
const {firstName: name, age} = person
// declares the variable *name* with the value *John*
// declares the variable *age* with the value *54*
```

## reference vs value

### Pass by value (Primitive Type)

* `a` is allocated a memory (say 0x001) and `b` creates a *copy* of the value in memory (say 0x002).
* So changing the value of a variable doesn't affect the other, as they both resides in two different locations. 

```js
let a = 3
let b = a 			// assign b to a
// a = 3
// b = 3
a = 4						// change a
// a = 4
// b = 3				// b isn't changed
```

### Pass by reference (objects)

- JS engine assigns the object to the variable `c`, it points to some memory say (0x012)
- when `d = c`, in this step d points to the same location (0x012).
- changing the value of any changes value for both the variable.
- functions are objects

```js
let c = { "name" : "john" }
let d = c 								// assign d to c

// { "name" : "john" }
// { "name" : "john" }

c.name = "doe" 						// change c
d.name // "doe"    				// d is changed, too

/*  Special case */
c = { "name" : "jane" }
c.name // "jane"    
d.name // "doe"

// * The equal(=) operator sets up new memory space or address
```