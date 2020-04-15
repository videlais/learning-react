# Function Components

- [Function Components](#function-components)
  - [More than Class Components](#more-than-class-components)
  - [Components Get **props**](#components-get-props)
  - [React Hooks](#react-hooks)
  - [`useEffect()`](#useeffect)
  - [Function Values As Components](#function-values-as-components)
  - [Combining Class and Functional Components](#combining-class-and-functional-components)

## More than Class Components

Throughout this book, the phrase "class component" has been used. This was purposeful. In React, there are more than class. There are also *function* components.

*Everything is a component.*

One of the core rules of React is that everything is a component. This goes for classes as well as functions. Anything can be a component as long as it follow another rule: it must return JSX.

Like class components, it returns (via *render()*) some JSX, so as long as the function also returns JSX, it is considered a component by React.

## Components Get **props**

For class components, the attributes of its element form become **props**. Internally, this becomes *this.props*. For functions, this does not get translated. This is simply **props**.

**src/components/Example/index.js:**

```javascript
import React from 'react';

function Example(props) {
  return (
    <div>
      <p>Hi,{this.props.name}!</p>
    </div>
  )
}

export default Example;
```

**src/App.js:**

```javascript
import React, { Component } from 'react';
import Example from './components/Example';

class App extends Component {
  render() {
    return (
      <div>
        <Example name="Dan" />
      </div>
    )
  }
}

export default App;
```

## React Hooks

Function components do not have access to **state**. They can have their own variable of *this.state*, of course, but the function *setState()* does not exist -- it is not inherited from **React.Component**, after all. Thus, changing the state of a function would not cause a re-rendering of its elements.

To help with this, React introduced new functionality: hooks. To provide state-like functionality, hooks can be used to introduce state-like behavior using a new function, *useState()*.

The function *useState()* accepts the initial value of a value and returns an array. The first position is the variable and the second is a function for changing that value. These are used through the destructing assignment operation to name them.

Consider the following line:

```javascript
const [counter, setCounter] = useState(0);
```

To help with naming them, it is generally recommended to name the first the variable and the second in a pattern of "set" with the name of the variable after that. Such a pattern helps with it matching the existing usage of *setState()* within class components.

```javascript
import React, { useState } from 'react';

function Example() {
  const [counter, setCounter] = useState(0);
  return (
    <div>
      <p onClick={() => { setCounter(counter + 1) } }>Click me!</p>
    </div>
  )
}

export default Example;
```

In the above code, the event listener calls the *setCounter()* function and increases the current value, *counter*, whenever it is called.

Internally, every use of *useState()* will also re-render whatever **Example** returns. This will act like *setState()* would in a class component.

## `useEffect()`

For a class component, there are three phases of their lifecycle: mounting, updating, and unmounting. For function components, this is reduced. There is "rendering" and "after rendering." Whatever is returned from a function component is mounted. To help with the "after rendering" actions, React defines a new term, *effects*.

In React, an *effect* is something that *affects* the document in some way. In a class component, such code would appear as part of *componentDidMount()* or *componentDidUpdate()*. It would be some functionality that updated the document as a result of the state being updated.

Because function components do not have an explicit state, a new function, *useEffect()*, can be used inside of the function. It accepts a callback function that will be called after every update inside of the function component.

```javascript
import React, { useState, useEffect } from 'react';

function Example() {

  const [counter, setCounter] = useState(0);
  
  useEffect(() => {
    document.title = `You clicked ${counter} times`;
  });
  
  return (
    <div>
      <p onClick={() => { setCounter(counter + 1) } }>Click me!</p>
    </div>
  )
}

export default Example;
```

In the above code, the function *useEffect()* will be called after the initial render and then any time the function returned by *useState()* is called. It is guaranteed to be called after an update and after any components are updated.

The second argument to *useEffects()* is an array of values. Function components do not have state. However, when used with the *useState()* function, values can be tested. If they have changed since the last render, React knows to render. (Since these are not automatically tracked in a function component, these must be optionally supplied to *useEffect()*.)

```javascript
import React, { useState, useEffect } from 'react';

function Example() {

  const [counter, setCounter] = useState(0);
  
  useEffect(
    () => {
      document.title = `You clicked ${counter} times`;
    },
    [counter]
  );
  
  return (
    <div>
      <p onClick={() => { setCounter(counter + 1) } }>Click me!</p>
    </div>
  )
}

export default Example;
```

## Function Values As Components

In JavaScript, functions are a type of value. This means that variables can hold functions as values and be called as such. Since React supports function components, this also means that variables can, themselves, be a component when they are the values of functions.

Consider the following code:

```javascript
import React from 'react';

const Example = () => {
    return (
        <div>
          <p>Hi!</p>
        <div>
    )
};

export default Example;
```

In the above code, the value of the variable *Example* is an arrow function that is, itself, a function component. As long as it is exported correctly, however, React cannot tell the difference. It is still a function and thus a valid component value to have.

## Combining Class and Functional Components

In React, there are no differences between components that are classes and those that are functions. Their usages dictate what and how they can be used.

In situations where a more lightweight solution might be needed, a function component could be used. If limited state is needed, this might also be a good place to use a function component.

However, in those cases where more complicated code is needed and advanced usages of state and event listeners, a class component would generally be a better solution.

With *useState()* and *useEffect()*, function components can use much of the same general functionality of class components. While not designed for the same purpose, they can be used in places to reduce the general size of the code.
