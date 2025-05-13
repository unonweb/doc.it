# Start

```bash
npm install typescript -g
npm install typescript --save-dev # add it as a development dependency to our project (final output is standard JavaScript so no need as a run time dependency)

npm install add-dependencies typescript --save-dev

tsc --init # generate a tsconfig.json file as a starting point.
```

## ts-node

```sh
npm install typescript ts-node # install

npx ts-node # open REPL
npx ts-node script.ts # run script

npx ts-node -T
npx ts-node --transpileOnly # transpile down to JavaScript without checking for any TypeScript errors
```

#### passing options to node

* node options must be passed directly to node; they cannot be passed to the ts-node binary nor can they be specified in tsconfig.json

```sh
node -r ts-node/register server.ts # invoke node directly and install ts-node via --require/-r
# this is useful if you need to pass options to node because
```

# Config

tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es5", // translate newer JavaScript constructs down to an older version
    "allowJs": true, // accept JavaScript files as inputs
    "module": "commonjs",
    "sourceMap": true,
    "outDir": "./dist",
    "strict": true,
    "alwaysStrict": true,
    "noImplicitReturns": true, // prevents you from forgetting to return at the end of a function
    "noImplicitAny": true, // if you don't want TypeScript to silently infer 'any' for a type
    // flag any implicit any as an error
    "noFallthroughCasesInSwitch": true, // if you never want to forget a break statement between cases in a switch block
    "experimentalDecorators": true
  },
  "include": ["./src/**/*"] // read in any files it understands in the src directory
}
```

## moduleResolution

This configures how the compiler tries to find your modules (resolve them). There are two strategies for doing so: classic or node. I set this to node because I am used to how node resolves modules. Node is now Typescript’s default mode of module resolution, i.e. classic is mostly present for backwards compatibility.

## target

Target is related to the *output* of your code, i.e. the compiled code targets a specific language variant. If you specify ES5 (not to be confused with ES2015), the compiled code can be run by ES5-compliant browsers and engines like Node. If you specify ES6 (== ES2015), the compiled code cannot be run by browsers or engines that do not yet support ES6 (ES2015) features.

## lib

Also related to the *output* of your code, this tells the compiler which language features are available when the compiled code is run. In most cases this would be the same as “target”, except if you polyfilled the runtime environment. For example, you might have polyfilled the Promise class in an ES5 environment.

Note that as opposed to Babel, Typescript does not actually down-compile all language features such as Promises. It does down-compile a few language constructs (rather than APIs), such as async/await.

# TS

## build-in types

primitive types available in JavaScript: `boolean`, `bigint`, `null`, `number`, `string`, `symbol`, and `undefined`

TypeScript extends this list with a few more:

```typescript
any // allow anything
// use whenever you don’t want a particular value to cause typechecking errors.

unknown // ensure someone using this type declares what the type is
never // it’s not possible that this type could happen
void // a function which returns `undefined` or has no return value
```

### arrays

```typescript
/* generic syntax */
let numArr: Array<number> = [1, 2, 3]
let strArr: Array<string> = ["infintbility", "aguidehub"]
let boolArr: Array<boolean> = [true, false]
let strNumArr: Array<string|number> = ['Apple', 2, 'Orange', 3, 4, 'Banana']
let dateArr: Array<Date> = [new Date(), new Date()]
/* shorthand syntax */
Type[] // --> an array of Type
any[]
let strNumArr: (string|number)[] = ['Apple', 2, 'Orange', 3, 4, 'Banana']
let numArr: number[] // [1, 2, 3] array of numbers
let strArr: string[] // ["1", "2", "3"] array of strings
Date[]
```



## implicit types

```typescript
let helloWorld = "Hello World" // let helloWorld: string
```

## type annotations

### anonymous

```typescript
/* objects */
function greet(person: { name: string; age: number }) {
  return "Hello " + person.name
}

/* variables */
let message: string = 'Hello World'
// we don’t always have to write explicit type annotations.
let message = 'Hello World'
```

### named

If you want to use the same type more than once and refer to it by a single name you need to define a *type alias*.

```typescript
// a type alias
type Person = {
  name: string;
  age: number;
}

function greet(person: Person) {
  return "Hello " + person.name
}

// naming a union type:
type ID = number | string
```

## object types

### interface

```typescript
// Explicitly describe this object’s shape using an `interface` declaration:
interface User {
  name: string;
  id: number;
  color?: string; // optional
  [propName: string]: any; // User can also have any number of other properties
}

// Then declare that a object conforms to the shape of your new interface by using syntax like : TypeName after a variable declaration:
const max: User = {
  name: "Max",
  id: 0,
}

// If you provide an object that doesn’t match the interface you have provided, TypeScript will warn you.

class UserAccount {
  name: string
  id: number
 
  constructor(name: string, id: number) {
    this.name = name
    this.id = id
  }
}
 
const user: User = new UserAccount("Murphy", 1) // makes sure that the object created by new UserAccount matches the requirements of the User interface
```

### type vs interface

There are two syntaxes for building types: Interfaces and Types. 
You should prefer `interface`. Use `type` when you need specific features.

Almost all features of an `interface` are available in `type`, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

> use `interface` until you need to use features from `type`

### index signature

Sometimes you don’t know all the names of a type’s properties ahead of time, but you do know the shape of the values.
In those cases you can use an index signature to describe the types of possible values.

* An index signature key type must be either `string` or `number`
* in `[key: string]: any` the key property name is just used for placeholder since it is enclosed in square brackets.

```typescript
interface Pages {
	[index: string]: TemplateResult; // as returned by lit's html`Hello` function
}

const pages: Pages = {
	home: html`This is the HOME page`,
	about: html`This is the ABOUT page`
}

interface SimpleObject {
  [key: string]: any // An object with string keys whose values can be any data type. 
}

interface StringArray {
  [index: number]: string;
}
```

### generic object types

Generic object types are often **some sort of container type** that work independently of the type of elements they contain. It’s ideal for data structures to work this way so that they’re re-usable across different data types.

```typescript
interface Box {
	contents: any
}

// We can make a generic Box type which declares a type parameter.

interface Box<Type> { 
  contents: Type;
}
// read this as “A Box of Type is something whose contents have type Type”
let box: Box<string>;
```



## functions

```typescript
// return type annotations
function getAdminUser(): User {
}

// parameter type annotations
function deleteUser(user: User, date: Date) {
}
```

## literal types

```typescript
/* string literals */
// -------------------------------
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left")
printText("G'day, mate", "centre")

/* numberic literals */
// -------------------------------
export const maxInterval: 12 

function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1
  // returns 0 if a equals b
  // returns 1 if a bigger than b
  // returns -1 if a smaller than b
}

/* combining literal and non-literal types */
// -------------------------------
interface Options {
  width: number
}
function configure(x: Options | "auto") {
  // ...
}
configure({ width: 100 })
configure("auto")
configure("automatic")
```



## type assertions

```typescript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas")
```



## appending types

```typescript
type Person {
   name: string,
   age?: number
}
type Actions {
   someFunc: Function
}
class MyClass {
   constructor(myObj: Person & Actions) { … }
  // myObj is an object that has both the Person types and the Action functions
}

// The beauty is that it gets deconstructed at compile time, so that if from the call here the object looks something like
type MyObj {
   name: string,
   age?: number,
   someFunc: Function
}
// ...and pass that to MyClass is perfectly valid
```

## componing types

With TypeScript, you can create complex types by combining simple  ones. There are two popular ways to do so: **unions**, and **generics**.

### unions

With a union, you can declare *that a type could be one of many types*

```typescript
// For example, you can describe a `boolean` type as being either true or false:
type MyBool = true | false
```

#### use case: string literals

A popular use-case for union types is to describe the set of `string` or `number` literals that a value is allowed to be:

```typescript
type Name = "Bob" | "Sam" | "Joe" 
// if you try to assign a different value to a variable with type Name, you’ll get an error
// Just remember though that this is only a compile time check
// any user input from your interface don’t have to follow these rules.

function getValue(): number | "default" { … }
// returns a number or "default" (?)
```

#### use case: functions returns

```typescript
function someFunc(input: string): string | number { … }
// return a string or number (but not a null)
// Whenever you call this, your return will always be valid, no need for null/undefined checks
// (unless interfacing with external code, however you should catch this and add undefined/null to your possible return type).
function someFunc(input: string): string | null { … }
// return a string or null
```

So once you start doing this, you’ll definitely want to enable the following in your *tsconfig.json* file:

```json
"strictNullChecks": true
```

#### use case: function arguments

you can make a function return different values depending on whether it is passed a string or an array:

```typescript
function wrapInArray(obj: string | string[]) {
  if (typeof obj === "string") {
    return [obj] // return as array
  }
  return obj
}
```

### generics

Generics provide variables to types. 
A common example is an array. An array without generics could contain anything. An array with generics  can describe the values that the array contains.

```typescript
type StringArray = Array<string>
type NumberArray = Array<number>
type ObjectWithNameArray = Array<{ name: string }>

let store: Store<AppState> = makeStore()
```

You can declare your own types that use generics:

```typescript
interface Backpack<Type> {
  add: (obj: Type) => void
  get: () => Type
}
 
// This line is a shortcut to tell TypeScript there is a
// constant called `backpack`, and to not worry about where it came from.
declare const backpack: Backpack<string>
 
// object is a string, because we declared it above as the variable part of Backpack.
const object = backpack.get()
 
// Since the backpack variable is a string, you can't pass a number to the add function.
backpack.add(23)
```

## optional properties

A optional property really says is that **if the property *is* set, it better have a specific type**.

We can also read from those properties - but when we do under `strictNullChecks = true`, TypeScript will tell us they’re potentially undefined.

```typescript
// anonymous
function testMethod(optionArg?: any): any { … }
// adding a ? to an optional parameter is equivalent to the following
function someFunc(val: string | undefined): string { … }

// named
interface PaintOptions {
  shape: Shape;
  xPos?: number;
  yPos?: number;
}
```

## non-null assertion operator

The non-null assertion operator tells the TypeScript compiler that a value typed as optional cannot be `null` or `undefined`. 
For example, if we define a variable as possibly a `string` or `undefined`, the `!` operator tells the compiler to ignore the possibility of it being undefined.

```typescript
let x: string | undefined // a variable that is defined as possibly null or undefined
function printString (str ?: string) { … } // a function that accepts an optional argument
```

### ex: using a variable of type string | null for a function that accepts string

Let’s say we defined a variable `word` with the type as `string | null`. This means throughout our code, `word` can either hold a `string` value or a `null` value.

If we attempt to use a function only available to `string` types on `word`, TypeScript will reject it because there is a possibility in our code that `word` holds a `null` value type:

```typescript
let word : string | null = null // word can either hold a string value or a null value
const num = 1
if (num) {
    word = "Hello World!"    
}
console.log(word.toLowerCase()) // Error: Object is possibly 'null'.ts(2531)
```

Using the `!` non-null assertion operator, we can tell TypeScript we are certain `word` will never be `null` (or `undefined`), so it can confidently apply string functions to it:

```typescript
console.log(word!.toLowerCase())
```

With this small addition, the compiler no longer believes there is a possibility that word is null.

TypeScript is *usually* pretty good at working out if you have done null/undefined checks before using a variable, but if for some reason it isn’t playing nice, add a ! to the end of your variable to indicate that it’s fully defined and not null at this point. 
This should also be a warning sign to you that the code may need some minor refactoring.

```typescript
someObject!.func('blah')


```

## decorators

Decorators provide a way to add both annotations and a meta-programming syntax for class declarations and members.
They're placed immediately before the code being decorated.

### tsconfig

```json
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}
```

# js > ts

### directories

Rename your **.js files to .ts** (TypeScript is a superset of JavaScript, so with the right *tsconfig.json* settings, it should just compile and work, but now you have the ability to optionally add types to your variables).

#### seperate js-input from ts-output

```json
{
  "compilerOptions": {
    "outDir": "./built", 
    "allowJs": true, // accept JavaScript files as inputs
    "target": "es5" // translate newer JavaScript constructs down to an older version
  },
  "include": ["./src/**/*"] // read in any files it understands in the src directory
}
```



Or separate our input files to prevent TypeScript from overwriting them.

### add types to library code

Any libraries that you are using, you’ll need to grab type definitions for:

```bash
npm install @types/<library> --save-dev # usually the way to install type definitions for a library
# Some libraries will already include TypeScript definitions 
# (you’ll usually get a warning when you try to install a type definition for these).
```

### add types to your own code

```json
{
  "compilerOptions": {
    ...
    "noImplicitAny": true,
		"noImplicitReturns": true
  }
}
```

This will generate a whole lot of errors in your IDE/compilation.

Now go through and add the corresponding type or ***any*** to all your variables and methods. And make sure all paths of a function return a value properly.

```typescript
// Old code
var test
function testMethod(arg) { … }
// New code
var test: any
var test: string
function testMethod(arg: any): any { … }
```

### add optional parameters

```typescript
// Old code
function testMethod(optionalArg) { … }// New code
function testMethod(optionArg?: any): any { … }
```
