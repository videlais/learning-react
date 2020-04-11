# Event Listeners and Class Component State

## Reviewing JavaScript Events

Part of the document object model (DOM) in web browsers is support for HTML events. When an agent is interacting with a document, different common events occur. This includes the more common `click` events, but also extends through other things like page loading and even HTML5 support for controllers or even VR platforms. All of these different interfaces and actions all generate *events*.

In HTML, a user event occurs on an element and moves through what is known as *event bubbling* as it moves up from itself to its parent and continuing to the global **document** object. Events, then, start where they occurs, like with clicking, and move through the document unless stopped.

To know if an event has occurred or not, JavaScript in web browser has support for *event listeners*. These are callback functions setup to listen for specific events and then respond in some way. All advanced interactions with documents like moving elements around on the page or even loading new content dynamically works through event listeners.

Because events work on the element-level, every element has a large set of built-in attributes for listening for particular events. These all start with the word `on` and are following by the name of the event. For example, a function set as the value to the attribute `onClick` would be called if a `click` event happened on that event.

For example, to react to a particular `<p>` element being clicked, the code might look like the following:

```html
<p onClick='() => { console.log("Clicked!") }'>Click me!</p>
```

In the above case, the arrow function would call a function inside of it, *console.log()*. It would *only* be called if the particular event, `click`, happened. Otherwise, it would be ignored.

## React Synthetic Events

React creates *synthetic events* that act like real ones, but are filtered through its own set of events. These condense some of the events that can happen in web browsers, and also allows React to track everything and only update the page when the event would affect how either its own or another component's rendered elements.

These include, for example, for mouse events, the following list:

- onClick

- onContextMenu

- onDoubleClick

- onDrag

- onDragEnd

- onDragEnter

- onDragExit

- onDragLeave

- onDragOver

- onDragStart

- onDrop

- onMouseDown

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

While React (and web browsers) will have no problem with the value of a function being used as a callback function for the synthetic event of `onClick` in React, it will have a problem when called. The property *this.example* will not exist in the calling context! It only exists within the scope of the class and not for the event listener itself.

There are two solutions to this issue:

### Arrow Function Class Methods

TODO

### Arrow Function Listeners

### Binding `this`

## Understanding *this.state*

### Creating State

### Updating State

### Using **setState()**
