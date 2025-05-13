Model
--------------------------

This is where the data is stored for your app. The model is decoupled from the views and controllers and has deliberate ignorance from the wider context. Whenever a model changes, it will notify its observers that a change has occurred using an *Event Dispatcher*.

* Validation rules: enforce that data coming into the application meets the constraint of the design of the system required. A misconception is to do this validation on the controller level.

* Models should not use variables that are tied to end users’ inputs or requests. This should be left for the controller to pass to the model
* Avoid embedding presentational code in the model. This is because presentational code varies based on end users’ input so this should be left to the view of MVC


View
------------------------------------

This part of your App has access to the *DOM* and is responsible for setting up *Event Handlers*. 
The view is also responsible for the presentation of the HTML.

Controller
------------------------------------

The controller is the glue between the model and the view. The controller processes and responds to events set off by either the model or view. It updates the model whenever the user manipulates the view, and can also be used to update the view whenever the model changes. 

* handle authentication and session management

As a rule of thumb, the Controller should be as minimal as possible - it is only responsible for translating HTTP requests into Model actions and selecting the right View - the Model should provide all the behavior it can without handling the HTTP requests or output formatting details.

* Web applications: the controller will *handle the requests* coming into your application. 
* Mobile applications: the controller will *handle the state* of your application for your views


Event Dispatcher
------------------------------------

The Event Dispatcher is an object that allows you to attach an unlimited number of functions/methods to it. When you finally call the notify method on that Event object, every method you attached to that Event will be ran. You will see this happening a lot in the source code below. 

### To Do List App 

Model:
* will hold the list of tasks and be responsible for any actions taken upon each task object.

View:
* Whenever a user enters in a new task through the input field, the view will use an Event Dispatcher to notify the controller, then the controller will update the model. This allows for swift decoupling of the view from the model.

Controller:
* In this tutorial, you will be updating the view directly from your model by dispatching an event.
* When the user clicks the add task button, the click is forwarded to the controller, and the controller modifies the model by adding the task to it.
* Any other decision making or logic can also be performed here, such as: saving the data using HTML local storage, making an asynchronous save to the database/server, and more.

Example: Todo App
===================================

### boilerplate

```js
class Model {
  constructor() {}
}

class View {
  constructor() {}
}

class Controller {
  constructor(model, view) {
    this.model = model
    this.view = view
  }
}

const app = new Controller(new Model(), new View())
```

### model

It's the simplest of the three parts. It doesn't involve any events or DOM manipulation. It's just storing and modifying data.
As we can see, the model only deals with the actual data, and modifying that data. It doesn't understand or have any knowledge the input - what's modifying it, or the output - what will end up displaying.

```js
class Model {
	constructor() {
		// The state of the model, an array of todo objects, prepopulated with some data
		this.todos = [
			{id: 1, text: 'Run a marathon', complete: false},
			{id: 2, text: 'Plant a garden', complete: false},
		]
	}

	addTodo(todoText) {
		const todo = {
			// if 
			id: this.todos.length > 0 ? this.todos[this.todos.length - 1].id + 1 : 1,
			text: todoText,
			complete: false,
		}

		this.todos.push(todo)
	}

	// Map through all todos, and replace the text of the todo with the specified id
	editTodo(id, updatedText) {
		// the old todos is replaced by the  
		this.todos = this.todos.map((todo) =>
			todo.id === id ? {id: todo.id, text: updatedText, complete: todo.complete} : todo,
		)
	}

	// Filter a todo out of the array by id
	// arr.filter() returns a new array with all item that pass the test implemented by the provided function.  

	deleteTodo(id) {
		this.todos = this.todos.filter((todo) => 
			todo.id !== id
		)
	}

	// Flip the complete boolean on the specified todo
	toggleTodo(id) {
		this.todos = this.todos.map((todo) =>
			todo.id === id ? {id: todo.id, text: todo.text, complete: !todo.complete} : todo,
		)
	}
}
```

### view

Neither the controller nor the model should know anything about the DOM, HTML elements, CSS, or any of that. Anything relating to it should be in the view.

The first thing I'll do is just make helper methods to retrieve an element and create an element.

```js
class View {
	constructor() {}

	// Create an element with an optional CSS class
	createElement(tag, className) {
		const element = document.createElement(tag)
		if (className) element.classList.add(className)

		return element
	}

	// Retrieve an element from the DOM
	getElement(selector) {
		const element = document.querySelector(selector)

		return element
	}
}
```

Now in the constructor, I'm going to set up all the things I need for my view. That'll be:

* The root element of the app - #root
* The title heading - h1
* A form, input and submit button for adding a todo - form, input, button
* The todo list - ul
