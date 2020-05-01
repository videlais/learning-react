# Class Component Lifecycle

- [Class Component Lifecycle](#class-component-lifecycle)
  - [Introducing Component Phases](#introducing-component-phases)
    - [Lifecycle Functions](#lifecycle-functions)
  - [Mounting](#mounting)
    - [*componentDidMount()*](#componentdidmount)
  - [Updating](#updating)
    - [*setState()*](#setstate)
    - [*componentDidUpdate()*](#componentdidupdate)
  - [Unmounting](#unmounting)
    - [*componentWillUnmount()*](#componentwillunmount)

## Introducing Component Phases

React has full control over when elements are rendered as part of components. As part of this control, it exposes three different *phases*.

In React, HTML elements are *mounted* when they are added to the document and real DOM. This happens after a class component is created but before they are updated as part of using functionality like *setState()*.

### Lifecycle Functions

- Mounting
  - *constructor()*
  - *render()*
  - *componentDidMount()*
- Updating
  - *setState()*
  - *componentDidUpdate()*
- Unmounting
  - *componentWillUnmount()*

## Mounting

The *Mounting* phase happens for a class component during a series of functions. This starts with the *constructor()*, follows through with a call to *render()*, and ends with *componentDidMount()*.

Because class components `extends` **React.Component**, the *constructor()* will always be the first function called. This is also where *props* is passed to the class component and also to its parent, **React.Component** via the function *super()*.

After *constructor()*, the function *render()* will be called for the first time. This will start the technical process of "mounting" and will add HTML elements or start the processing of any components referenced in its own *render()* function.

### *componentDidMount()*

The last function called during the mounting phase will be *componentDidMount()*. This will always be last. It is guaranteed that any HTML elements or other components and their own elements will be mounted *before* this is called in the class component using it.

```javascript
import React, { Component } from 'react';

class Example extends Component {
  componentDidMount() {
      console.log("This will be called at the end of the mounting phase!");
  }
  render() {
    return (
      <div>
        <p>Example text!</p>
      </div>
    );

  }
}

export default Example;
```

## Updating

The updating phase starts immediately after the mounting phase. As soon as elements are added to the document, the updating phase begins. Its main purpose is to react to different events and potentially re-render anything that may have changed.

The function *setState()* runs during the updating phase. Any calls to it will also call *render()* and thus re-render elements and other components. This "updates" the elements in the document as a result of user events in the browser.

### *setState()*

The only way to re-render elements and components in React is via the *setState()* function. Its purpose is to change the **state** of a class component. It is assumed that changes to **state** will also mean changes to the rendering of the elements and any other components inside of it.

As was reviewed earlier, it accepts either a function that is passed the current state and **props** or an object literal that is used to update the class's **state**.

### *componentDidUpdate()*

Like the use of *componentDidMount()*, the function *componentDidUpdate()* will be called at the end of any updating via the use of the *render()* function in a class component. It will not be called during the initial *render()* call during the mounting phase.

It will be passed **prevProps**, the previous **props** of the class component, and **prevState**, the previous **state** of the component.

## Unmounting

The last phase in a class component lifecycle is unmounting. This is the point where any HTML elements or components are removed from the document as part of the class component being deleted or otherwise removed itself.

### *componentWillUnmount()*

The very last function called in a class component will be *componentWillUnmount()*. This will be called right before the component is deleted and all of its elements are removed from the document.

If the class component needs to update one last thing, this is the function to use.
