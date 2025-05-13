CREATIONAL
=============================

The primal focus of these patterns is *object creation*. They deal with the idea of creating specific objects for a specific use-case and then hiding the creation logic or class implementation from us. They are just exposing interfaces to us. This category of patterns can further be classified into *Object-creation* and *Class-creation*.

* Singleton
* Factory
* Abstract Factory
* Builder
* Prototype


Singleton pattern
---------------------------------------

* Restricts the instantiation of a class to one object.
* Usually, the goal is to manage global application state.

Example: Office printer. If there are ten people in an office, and they all use one printer, ten computers share one printer (instance). By sharing one printer, they share the same resources.

#### implementation 1

```js
class Database {
	constructor() {
		// Check if our first instance has already been created
		// This alone disallows us from making more than one instance
		if (Database.instance instanceof Database) {
			return Database.instance;   
		}

		this.connectionURL = { name: "", options: {} }

		// This disallows modifying the instance we created
    	Object.freeze(this);

		// Make our class an instance of itself
    	Database.instance = this;
	}
	

	// Our connect method taking in two arguments
	connect(name, options) {
		this.connectionURL.name = name;
		this.connectionURL.options = options;
		console.log(`DB: ${name} connected!`);
	}

	// Disconnect method
	disconnect() {
		console.log(`${this.connectionURL.name} is disconnected!`);
	}
}

// Instantiating our class
const db = new Database()
const db1 = new Database()
// If two instances are created they are both the same:
console.log(db === db1) // true
```

* In the above code, adding or changing properties is not allowed anymore. 

#### implementation 2

The constraint imposed upon code that consumes our little singleton module here:  
* `const` # the consuming code cannot reassign UserStore
* `Object.freeze` # methods & properties cannot be changed, nor can new methods or properties be added to it. 
* `export` # allows us to know exactly where UserStore is used

```js
// The same but using classes
class UserStore {
	constructor(){
		this._data = [];
	}
	add(item){
		this._data.push(item);
	}
	get(id){
		return this._data.find(d => d.id === id);
	}
}
const instance = new UserStore();
Object.freeze(instance);
export default instance;
```

#### disadvantages

* Race condition. When two or more components or threads try to access this shared resource in our Singleton, we get the performance bottleneck where data might not be read correctly.
* Singleton is more like a global variable, so it is quite hard to test anything since every part of our application is coupled to it.


STRUCTURAL
==============================

These patterns are concerned with the relationship between entities. They deal with the composition of a class and object, meaning that a particular object will be used in another class, making new functionalities available. Don’t forget that this relationship has something to do with Inheritance where a class inherits the members of an existing class. The two main words here are *composition* and *inheritance*.

In summary, we create classes and find ways to relate them or make a relationship using composition. The design patterns that use this method are:

* Adapter
* Facade
* Bridge
* Proxy
* Flyweight


Adapter pattern
----------------------------------------------

* Works as a contract or bridge between two classes that aren’t compatible. 
* Take this pattern as a *wrapper* in the sense that it wraps two separate interfaces (e.g. two classes are connected) joining them together.

```js
// our adapter
import { first, middle, last } from "random-name";

class randomName {
	generateFirstName() {
		return first();
	}
	
	generateMiddleName() {
		return middle();
	}
	
	generateLastName() {
		return last();
	}
}

export default new randomName()

// We now use this adapter in our code:

import name from "./random-name";

class PlugComponent {
	constructor() {
		this.firstName = name.generateFirstName();
		this.middleName = name.generateMiddleName();
		this.lastName = name.generateLastName();
	}
	
	generateFullName() {
		return `${this.firstName} ${this.middleName} ${this.lastName}`
	}
}

const names = new PlugComponent()
console.log(names.generateFullName()) // Victor Victor Jonah
```

BEHAVIORAL
==================================


These patterns are most focused on *communication between objects*. They provide the solution for the developer to have decoupled and flexible code where they can make objects communicate with each other.

In summary, whenever you need communication between objects you should go for this category. The patterns that use this communication method are:

* Chain of Responsibility
* Command
* Interpreter
* Observer
* Null object


Null object pattern
-------------------------------------

This pattern helps us *avoid returning a null value* in our code. To be clear, this pattern encapsulates the behavior of null and returns the desired result expected by the client. Most of the time we are not allowed to make null references. This is the reason why we have to make null checks. These null checks can make our code look cluttered with if/else statements. We simply add this pattern to our project, and they help keep the client's code cleaner because he doesn’t have to add logic to check for null values anymore.

The Null object pattern works well when we do not want to return a null value to the client since it encapsulates our desired logic instead. This is my most used pattern as I use it to catch specific exceptions.

```js
class Cat {
	sound() {
		return 'meoow';
	}
}

class NullAnimal {
	sound() {
		return "not an animal";
	}
}

const getAnimal = (type) => {
  	return type === 'cat' ? new Cat() : new NullAnimal();
}

const results = ['cat', null]; 
const response = results.map((animal) => getAnimal(animal).sound());
// ["meoow", "not an animal"]
```

Observer pattern
----------------------------------

### Observer vs. Publisher/Subscriber pattern

The Observer pattern requires that the observer (or object) wishing to receive topic notifications must subscribe this interest to the object firing the event (the subject).

The Publish/Subscribe pattern however uses a topic/event channel which sits between the objects wishing to receive notifications (subscribers) and the object firing the event (the publisher).

An observer is notified directly by its subject whereas in the publisher/subscriber the subscriber is notified through a channel that sits in between the publisher and subscriber that relays the messages back and forth.


Publisher/Subscriber pattern
----------------------------------

* Involves a middleware that is also referred to as the *pub/sub broker*. 
* The pub/sub broker handles interactions between the publishers and the subscribers.
* Unlike the observer pattern, the pub/sub pattern allows multiple publishers and multiple subscribers.

#### implementation 1

```js
class PubSub {
	constructor() {
		this.events = {}
		// an object of arrays containing the functions which are called when a notification signal (the object's key) is used
	}
	subscribe(signal, subscriber) {
		// for adding event handler
		// if the signal is already present push a new item into it
		// if the signal is not yet present create a new array first and then push into it
		(this.events[signal] || (this.events[signal] = [])).push(subscriber);
		//console.log('on signal:', signal)
		//console.log('call: ', subscriber)
		return this;
	}
	publish(signal, ...args) {
		// for calling the event handlers for the specified event
		//(this.events[signal] || []).slice().forEach(lsn => lsn(arg));
		//(this.events[signal] || []).map(subscriber => subscriber(...args));
		if (this.events[signal]) {
			this.events[signal].map(subscriber => subscriber(...args))
		} else throw new ReferenceError(`${signal} doesn't exist in this.events[signal]`)
	}
	unsubscribe(signal, subscriber) {
		if (this.events[signal]) {
			// rebuild the stack of subscriber callbacks registered under 'signal' with all callbacks that pass this test:
			this.events[signal] = this.events[signal].filter(sub => sub !== subscriber)
		} else throw new ReferenceError(`${signal} doesn't exist in this.events[signal]`)
	}
}

const ps = new PubSub()

function showAll(...values) {
  	console.log(...values)
}

ps.subscribe('show', showAll)
ps.publish('show', 4, 3, 2, 1)
ps.unsubscribe('showc', showAll)
```

#### implementation 1: addendum

doesn't work!

```js
/* class PubSub {
	constructor() {
		this.events = {}
	}
	subscribe(signal, subscriber) {
		(this.events[signal] || (this.events[signal] = [])).push(subscriber);
		return this;
	}
	publish(signal, ...args) {
		if (this.events[signal]) {
			this.events[signal].map(subscriber => subscriber(...args))
		} else throw new ReferenceError(`${signal} doesn't exist in this.events[signal]`)
	}
	unsubscribe(signal, subscriber) {
		if (this.events[signal]) {
			this.events[signal] = this.events[signal].filter(sub => sub !== subscriber)
		} else throw new ReferenceError(`${signal} doesn't exist in this.events[signal]`)
	}
}

class One extends PubSub {
	constructor() {
		super()
		this.subscribe('two-one', this.oneResponds)
	}
	oneResponds(arg) {
		console.log('one received message: ', arg)
	}
}

class Two extends PubSub {
	constructor() {
		super()
		this.subscribe('one-two', this.twoResponds)
	}
	twoResponds(arg) {
		console.log('two received message: ', arg)
	}
}

let one = new One()
let two = new Two()
one.publish('one-two', 'hello two!')
two.publish('two-one', 'hello one!') */
```

#### implementation 2

```js
/* using functions */

function pubSub() {
	const subscribers = {}

	function subscribe(eventName, callback) {
		if (!Array.isArray(subscribers[eventName])) {
			subscribers[eventName] = []
		}
		subscribers[eventName].push(callback)

		const index = subscribers[eventName].length - 1

		return {
			unsubscribe() {
				subscribers[eventName].splice(index, 1) // replace one item at index
			},
  		}
	}
	
	function publish(eventName, data) {
		if (!Array.isArray(subscribers[eventName])) {
			return 	// This is a safeguard against potential crashes.
		}
		subscribers[eventName].forEach((callback) => {
			callback(data)
		})
	}

	return {
		publish,
		subscribe,
	}
}

/* using classes */

class PubSub {
	constructor() {
		this.subscribers = {}
	}
	subscribe(signal, callback) {
		if (!Array.isArray(this.subscribers[signal])) {
			this.subscribers[signal] = [] // initialize the array if necessary
		}
		this.subscribers[signal].push(callback)
	}
	publish(signal, data) {
		if (!Array.isArray(this.subscribers[signal])) {
			return 	// This is a safeguard against potential crashes.
		}
		this.subscribers[signal].forEach((callback) => {
			callback(data)
		})
	}
}

function showMeTheMoney(money) {
  	console.log(money)
}

const ps = new PubSub()
ps.subscribe('show-money', showMeTheMoney)
ps.publish('show-money', 55) // 55
```

#### implementation 3

```js
class Pubsub {
	constructor() {
		this.events = {};
	}

	subscription (eventName, func) {
		return {
			subscribe: () => {
				if (this.events[eventName]) {
					this.events[eventName].push(func);
				} else {
					this.events[eventName] = [func];
				}
				console.log(`${func.name} has subscribed to ${eventName} Topic!`)
			},

			unsubscribe: () => {
				if(this.events[eventName]){
					this.events[eventName] = this.events[eventName].filter((subscriber) => subscriber !== func);
					console.log(`${func.name} has unsubscribed from ${eventName} Topic!`)
				}
			}
		}
	} 


  publish(eventName, ...args) {
		const funcs = this.events[eventName];
		if (Array.isArray(funcs)) {
			funcs.forEach((func) => {
				func.apply(null, args);
			});
		}
	}
}

/* Usage */

function speak(param) {
  	console.log(`I am ${param}`);
}

function greetAll(x, y, z) {
  	console.log(`Hello ${x}, ${y}, ${z}`);
};

const pubsub = new Pubsub()

pubsub.subscription("greet", greetAll).subscribe() // greetAll has subscribed to greet Topic!
pubsub.subscription("sayName", speak).subscribe() // speak has subscribed to sayName Topic!
pubsub.subscription("sayName", greetAll).unsubscribe() // greetAll has unsubscribed from sayName Topic!

pubsub.publish("greet", "Lawrence Eagles", "John Doe", "Jane Doe"); // Hello Lawrence Eagles, John Doe, Jane Doe
pubsub.publish("sayName", "Lawrence Eagles"); // I am Lawrence Eagles
```