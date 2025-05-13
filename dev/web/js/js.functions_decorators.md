
# DECORATORS

Decorators are actually nothing more than functions that return another function.

Decorators provide a way to add both annotations and a meta-programming syntax for class declarations and members.
They're placed immediately before the code being decorated.

At present, the only types of decorator that are supported are on classes and members of classes. 
This includes properties, methods, getters, and setters.

```js
@log()
@immutable()
class Example {
  @time('demo')
  doSomething() {
    //
  }
}
```

This defines a class and applies three decorators — two to the class itself, and one to a property of the class.

## Class decorators

Class decorators are applied to the entire class definition all in one go. 
**The decorator function is called with a single parameter which is the constructor function being decorated.**

Note that this is applied to the constructor function and not to each instance of the class that is created. This means that if you want to manipulate the instances you need to do so yourself by returning a wrapped version of the constructor.

In general, these are less useful than class member decorators, because everything you can do here you can do with a simple function call in exactly the same way. Anything you do with these needs to end up returning a new constructor function to replace the class constructor.

### ex: @log()

let’s write one that logs the constructor parameters:

```javascript
function log(Class) {
  // we're accepting a class as our argument
  // and we're returning a new function that will act as the constructor
  return (...args) => {
    // ...args gathers all arguments into an array
    // these are the arguments passed to the constructor
    console.log(args) // the actual 'decoration'
    return new Class(...args)
  }
}

// use it:

@log // is implicitly called with the following constructor
class Example {
  constructor(name, age) {
  }
}

const ex = new Example('Graham', 34) // [ 'Graham', 34 ]
console.log(ex) // Example {}
```

## Class member decorators

Property decorators are applied to a single member in a class —  whether they are properties, methods, getters, or setters. This decorator function is called with three parameters:

- `target`: the class that the member is on.
- `name`: the name of the member in the class.
- `descriptor`: the member descriptor. This is essentially the object that would have been passed to [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty).

```js
function readonly(target, name, descriptor) {
  descriptor.writable = false // our 'decoration'
  return descriptor
}

class Example {
  a() {}
  @readonly
  b() {}
}

const ex = new Example()
ex.a = 1
ex.b = 2 // TypeError: Cannot assign to read only property 'b' of object '#<Example>'
```

### ex: log() decorator

```javascript
function log(target, name, descriptor) {
  const original = descriptor.value
  
  if (typeof original === 'function') {
    // so descriptor is a class method
    descriptor.value = function(...args) {
      // now we replace the original method
      console.log(`Arguments: ${args}`)
      try {
        const result = original.apply(this, args)
        // apply() calls the specified function with a given `this` value, and `arguments` provided as an array
        console.log(`Result: ${result}`)
        return result
      } catch (e) {
        console.log(`Error: ${e}`)
        throw e
      }
    }
  }
  return descriptor
}

class Example {
  @log
  sum(a, b) {
    return a + b
  }
}

const ex = new Example()
ex.sum(1, 2)
// Arguments: 1,2
// Result: 3
```

### ex: log(name) decorator

```js
function log(name) {
  return function decorator(t, n, descriptor) {
    const original = descriptor.value
    if (typeof original === 'function') {
      descriptor.value = function(...args) {
        console.log(`Arguments for ${name}: ${args}`)
        try {
          const result = original.apply(this, args)
          console.log(`Result from ${name}: ${result}`)
          return result
        } catch (e) {
          console.log(`Error from ${name}: ${e}`);
          throw e
        }
      }
    }
    return descriptor
  }
}
```

This is getting more complex now, but when we break it down we have this:

- A function, `log`, that takes a single parameter: `name`.
- This function then returns a function that *is itself a decorator*.

This is identical to the earlier `log` decorator, except that it makes use of the `name` parameter from the outer function.

This is then used as follows:

```js
class Example {
  @log('some tag') // is evaluated straight away
	// then the response from that is used as the decorator for the follwing method:
  sum(a, b) {
    return a + b
  }
}

const e = new Example()
e.sum(1, 2)
// Arguments for some tag: 1,2
// Result from some tag: 3
```

Straight away we can see that this allows us to distinguish between  different log lines using a tag that we’ve supplied ourselves.

This works because the `log('some tag')` function call is evaluated straight away, and then the response from that is used as the decorator for the `sum` method.

## function vs class decorators

### function decorators

The concept of decorators is not new in JavaScript because higher-order functions are a form of function decorators.
Function decorators are functions. They take a function as an argument and return a new function that enhances the function argument without modifying it.


```js
// general structure
function decorate(fn) {
	return function(arg) {
		console.log('decoration')
		fn(arg); // call the function
		console.log('decoration')
	}
}
// a function that's going to be decorated
function logger(msg) {
	console.log(msg)
}

const decoratedLogger = decorate(logger);
decoratedLogger('hey')

// decoration
// hey
// decoration
```

### class decorators

The pattern used in function decorators cannot easily be used on JavaScript classes.
Decorators use a special syntax whereby they are prefixed with an @ symbol and placed immediately above the code being decorated, as seen below:

```js
function log(fn) {
	// the decorator
	return function() {
		console.log("Logged at: " + new Date().toLocaleString());
		return fn();
	}
}
class Person {
	constructor(name, age, job) {
		this.name = name;
		this.age = age;
		this.job = job;
	}
	getBio() {
		return `${this.name} is a ${this.age} years old ${this.job}`;
	}
}

// creates a new person
let man = new Person("Lawrence", 20, "developer");

// decorates the getBio method
let decoratedGetBio = log(man.getBio); 
decoratedGetBio(); // TypeError: Cannot read property 'name' of undefined at getBio
```

We tried to decorate the getBio method using the function decorator technique, but it does not work. We get a TypeError because when the getBio method is called inside the log function, the this variable refers the inner function to the global object.