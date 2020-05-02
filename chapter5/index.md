# Event Listeners and Class Component State

- [Event Listeners and Class Component State](#event-listeners-and-class-component-state)
  - [Reviewing JavaScript Events](#reviewing-javascript-events)
  - [React Synthetic Events](#react-synthetic-events)
  - [Problems with `this`](#problems-with-this)
    - [Public Class Fields](#public-class-fields)
    - [Arrow Function Listeners](#arrow-function-listeners)
    - [Binding `this`](#binding-this)
  - [Understanding *this.state*](#understanding-thisstate)
    - [Updating State](#updating-state)
    - [Using **setState()**](#using-setstate)
    - [*setState()* Callback](#setstate-callback)

## Reviewing JavaScript Events

Part of the document object model (DOM) in web browsers is support for HTML events. When an agent is interacting with a document, different common events occur. This includes the more common `click` events, but also extends through other things like page loading and even HTML5 support for controllers or even VR platforms. All of these different interfaces and actions all generate *events*.

In HTML, an event occurs on an element and moves through what is known as *event bubbling* as it moves up from itself to its parent and continuing to the global **document** object. Events start where they occur, like with clicking, and move through the document unless stopped or they run out of parent elements.

To know if an event has occurred or not, JavaScript in web browser has support for *event listeners*. These are callback functions setup to listen for specific events and then respond in some way. All advanced interactions with documents like moving elements around on the page or even loading new content dynamically works through event listeners.

Because events work on the element-level, every one has a large set of built-in attributes for listening for particular events. These all start with the word `on` and are following by the name of the event. For example, a function set as the value to the attribute `onClick` would be called if a `click` event happened on that event.

For example, to react to a particular `<p>` element being clicked, the code might look like the following:

```html
<p onClick='() => { console.log("Clicked!") }'>Click me!</p>
```

In the above case, the arrow function would call a function inside of it, **console.log()**. It would *only* be called if the particular event, `click`, happened. Otherwise, it would be ignored.

## React Synthetic Events

React creates *synthetic events* that act like real ones, but are filtered through its own set of events. These condense some of the events that can happen in web browsers, and also allows React to track everything and only update the page when the event would affect how either its own or another component's rendered elements.

These include, for example, for mouse events, events like the following:

- onClick

- onContextMenu

- onDoubleClick

- onMouseEnter

- onMouseLeave

- onMouseMove

- onMouseOut

- onMouseOver

- onMouseUp

## Problems with `this`

When event listeners are used within classes in JavaScript, a unique issue arises. Because functions within a class are usually called within the scope of the class, the use of its `this` refers to the class itself. However, event listeners do not use this. Any functions called as part of an event listener is executed outside of its defined scope. This creates a problem.

Consider the following code:

**index.js:**

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";
  }
  
  clickListener() {
    console.log(this.example);
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
      </div>
    )
  }
}

export default App;
```

While React will have no problem with the value of a function being used as a callback function for the synthetic event of `onClick` in React, it will have a problem when called. The property *this.example* will not exist in the calling context! It only exists within the scope of the class and not for the event listener itself.

There are two solutions to this issue:

### Public Class Fields

Using a public class field arrow function allows a class function to reference the `this` of a class (where it is defined) even if it is run in a different context.

As this is the exact issue with using event listeners, such a solution makes it very popular with React developers.

Consider the following code that now uses a public class field:

**index.js:**

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";
  }
  
  clickListener = () => {
    console.log(this.example);
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
      </div>
    )
  }
}

export default App;
```

However, while it seems ideal, it also comes with two small problem. First, any classes that extend a class that uses public class fields get a property that has the value of a function, *not* a function itself. This is a small but often important difference for classes that inherit from other classes within a project.

The second is that it is an experimental feature. React supports it because Babel does, but not all JavaScript environments fully support it yet. Once it becomes part of the specification, adoption will improve and it will slowly show up in other environments like web browsers without needing help from tools like Babel to "translate" the code.

### Arrow Function Listeners

Event listeners in JavaScript in web browsers take the value of a function and run said function when an event happens. The key term "value" opens the door to using arrow functions in a new way: as event listeners that call other functions!

Another common way to avoid the issue with event listeners being called in a different context than a class is to use an arrow function as the "listening" function and then have it call something else.

Arrow functions take their `this` from where they are defined. As long as the arrow function is defined within the class, its own `this` will be the class itself.

Consider the following code:

**index.js:**

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";
  }
  
  clickListener() {
    console.log(this.example);
  }

  render() {
    return (
      <div>
        <p onClick={
          () => { this.clickListener() }
          }>Click me!</p>
      </div>
    )
  }
}

export default App;
```

In the above code, the arrow function is used as the event listener function. It then calls, using the `this` of the class, another function within the class.

While this technique is also frequently used in React code, it also comes with a problem to remember: each copy of the element with those exact attributes creates an extra function for the sole purpose of calling another function.

While it can be used in small amounts, it not recommended to generate large lists or create many components using the same functionality. Creating lots of small functions that simply call other functions is potentially a waste of application memory.

### Binding `this`

Early developers of React used a technique that is still fairly popular: binding `this`.

The function **bind()** can be used on any function to change its internal `this`. It then returns a new function based on the old one, but changed.

In the case of binding `this` in React, a function within a class is "re-binded" inside a **constructor()**. Before it is used, then, its own `this` is the same as that of the class in which it is defined.

For event listeners, this means that the `this` of the function will always be the class, even when the code is run in a different context.

Consider the following code:

**index.js:**

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";

    this.clickListener = this.clickListener.bind(this);

  }
  
  clickListener() {
    console.log(this.example);
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
      </div>
    )
  }
}

export default App;
```

While popular in some circles, the binding of `this` also creates its own problem: the original function's `this` is still the same. Anything class that inherits the function and wants to use it as an event listener will have to re-bind it again within its own constructor. This adds, with multiple events, multiple lines of code to re-bind each one's `this` to the class.

## Understanding *this.state*

*Components take care of themselves.*

While each component should only be concerned with its own elements, what happens if it needs to keep track of data somehow? In those cases, each component can have its own *state*.

State can be added to a component through creating a property *this.state* inside of a class component's **constructor()** (or as a public class field). Because it is part of the class, it can be accessed anywhere inside of it.

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";

    this.state = {
      counter: 0;
    }

  }
  
  clickListener = () => {
    this.state.counter++;
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
        {this.state.counter}
      </div>
    )
  }
}

export default App;
```

While the above code my seem like the answer, it is not. The reason why has to do with React and its use of **render()**. In the above code, the value of *this.state.counter* would be updated, but its new value would not be shown -- **render()** would need to be called again!

### Updating State

To help with this common task, the class **React.Component** has a special function designed for only updating state: **setState()**.

Inherited from **React.Component**, **setState()** seemingly only does one thing: updates *this.state*. However, it also does another thing internally: it also calls *render()*! Any use of *setState()* will update the internal *this.state* and have any updated values become part of the next call to *render()*. (This also, like all things React, allows it to collect potential changes and decide when to update the DOM to be most efficient.)

### Using **setState()**

Since React makes its own decision to update the *this.state* as part of additional *render()* calls, this create a new rule: do not change the values of *this.state* outside of *setState()*!

The reason for this rule is simple. If React has not yet updated what a class component is rendering, any changes to *this.state* outside of using *setState()* would be overwritten since the last render. In other words, changes would be missed between renders!

To avoid this, it is strongly recommended to edit values only inside of a call to *setState()* or, if needed, to make a copy of a particular property to protect the overall object from accidentally being changed before the next call to *render()*.

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";

    this.state = {
      counter: 0;
    }

  }
  
  clickListener = () => {
    this.setState(
      (state) => {
        return {
          counter: state.counter++
        }
      }
    );
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
        {this.state.counter}
      </div>
    )
  }
}

export default App;
```

In the above example, any call to the public class field *clickListener* would also call *this.setState()*. In the new code, the function *this.setState()* is being passed an arrow function whose job it is to update a property of *this.state* through returning an object with an updated property.

Using a function is one way to update *this.state*. *setState()* also accepts objects.

```javascript
import React from 'react';

class App extends React.Component {

  constructor(props) {
    super(props);

    this.example = "Hi!";

    this.state = {
      counter: 0;
    }

  }
  
  clickListener = () => {
    this.setState({counter: this.state.counter++});
  }

  render() {
    return (
      <div>
        <p onClick={this.clickListener}>Click me!</p>
        {this.state.counter}
      </div>
    )
  }
}

export default App;
```

In the above example, the call to *setState()* is given an object with the updated property and its new value. Internally, *setState()* would only update that single property and leave any others unchanged.

Depending on the need, either use of *setState()* could be a solution. For more complicated calculations or to change multiple properties, an arrow function could be used. For updating a single property, passing a single object might make more sense.

### *setState()* Callback

*setState()* also accepts an optional callback function as a second argument. This can be useful in those situations where the data of a state change is needed right after it happens.

Consider the limited following code example:

```javascript
import React from 'react';

class Day extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      day: 1,
      events: []
    }

  }
  
  setDay = (event) => {
    this.setState(
      {day: this.state.day + 1},
      () => {
        this.loadEvents();
      });
  }

  loadEvents() {
    // Load events based on the current day
    // Pull the day from this.state.day
  }

  render() {
    return (
      <div>
        <h2 onClick={this.clickListener}>Day {this.state.day}</h2>
        <ul>
        {
          this.events.forEach((entry) => {
            <li>{entry}</li>
          })
        }
        </ul>
      </div>
    )
  }
}

export default App;
```

In the above example, the next day is loaded based on what the current value of *this.state.day* is, calling the internal function *this.loadEvents()* as part of a callback to *setState()*. Whenever the user changed the day, all of the current events would be reloaded.

However, while code could work like this, it is *not recommended*. The callback function option is provided to fine-tune operations and other, more general functionality should be used in this same instance to make sure components are updated *before* potentially loading on more data and processing it somehow. This example is provided to show it could be done, not that it should.
