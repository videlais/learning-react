# Adding Contexts

- [Adding Contexts](#adding-contexts)
  - [Problem with Repeating Attributes](#problem-with-repeating-attributes)
  - [Creating Contexts](#creating-contexts)
  - [Providers and Class Consumers](#providers-and-class-consumers)
    - [Static Class Contexts](#static-class-contexts)
  - [Function Consumers](#function-consumers)

## Problem with Repeating Attributes

In complex projects, there is often a need to pass "down" a value from a parent component to its children who then pass it down to its children. Such a value is added as an attribute to the initial children who, in turn, must also add the same attribute for its own children, repeating the attribute through a tree of components.

```javascript
class App extends React.Component {
  render() {
    return <Example repeatingAttribute="Hi" />;
  }
}

function Example(props) {
  return (
      <Another repeatingAttribute={props.repeatingAttribute} />
  );
}

function Another(props) {
  return (
    <p>{`The repeatingAttribute value is ${props.repeatingAttribute}.`}</p>
  );
}

```

In the above code, a *repeatingAttribute* is set. This is passed down from `<App />` to its children, who all pass it to their children. It is also very wasteful code! Multiple components are "passing down" the *repeatingAttribute* without using it and are acting as a relay for a value for a grandchild component to use.

To solve this common issue, React allows for creating *contexts*.

## Creating Contexts

A *context* is a way to create a global store that exists in connection to a tree of components. It can be created and then values provided by parents whose children can then consume and use them.

```javascript
const MyContext = React.createContext(defaultValue);
```

The function *createContext()* creates the ability to add a context. This a store any components can use. However, in order for it to be "passed down", it must be used across multiple files.

**src/components/Context/index.js:**

```javascript
import React from 'react';

const MyContext = React.createContext();

export default MyContext;
```

Each file that imports the example `Context/index.js` files gets what it exports, a **Context**. If parent components need to provide values or child components consume them, this is where it starts.

## Providers and Class Consumers

Each **Context** provides two other components, **Provider** and **Consumer**. When used together, a `<Context.Provider>` component "wraps" a parent component and "passes down" a value to its children. Any child can subscribe (consume) the context and its value. If they do, they will be re-rendered whenever the value changes in the parent (or even grandparent!) updates it.

**src/components/App.js:**

```javascript
import React, { Component } from 'react';
import ExampleComponent from './components/ExampleComponent';
import MyContext from './components/Context';

class App extends Component {

  render() {
    return (
      <MyContext.Provider value={"Hey, there!"}>
        <ExampleComponent />
      </MyContext.Provider>
    )
  }
  
}

export default App;
```

In the above code, `<App>` returns a **Context.Provider** component that is "wrapping" its child, `<ExampleComponent>`. (The context is provided by the file in `src/components/Context/index.js`.)

**src/components/ExampleComponent/index.js:**

```javascript
import React, { Component } from 'react';
import AnotherComponent from '../AnotherComponent';

class ExampleComponent extends Component {
  render() {
    return (
      <div>
        <AnotherComponent />
      </div>
    );
  }
}

export default ExampleComponent;
```

In the above code, the component `<ExampleComponent>` does not consume the context (and, accordingly, does not `import` it). All the component returns is another component, `<AnotherComponent>`.

**src/components/AnotherComponent/index.js:**

```javascript
import React, { Component } from 'react';
import MyContext from '../Context';

class AnotherComponent extends Component {
  render() {
    return (
      <p>{this.context}</p>
    )
  }
}

AnotherComponent.contextType = MyContext;

export default AnotherComponent;
```

In the above code, the component `<AnotherComponent>` *does* use the context. It can import the context the same as `<App>` did. However, internally, things are different now.

Earlier in the tree (in `<App>`), a **Provider** was used. This set *value* for the context. Any children that subscribe to the context (call *React.createContext()*) *after* a **Provider** is used become subscribers to the value.

**Note:** If a **Provider** was used in this component, it would then become the "provider" for its own children, overwriting the current *value* with its own.

The above class also uses different *.contextType*. There is a line after the class but before it is exported. This augments the class with a new property, *contextType*, and sets its value to the context.

Using such a line may seem strange, but this changes the *class*, not an instance of it. Using something like `this.contextType = MyContext` would not work -- it would change the object, not the class itself.

### Static Class Contexts

Babel supports using the keyword `static` for class fields in JavaScript ES6. A context can thus be set as "static" to the class itself, avoiding the issue with instances.

```javascript
import React, { Component } from 'react';
import MyContext from '../Context';

class AnotherComponent extends Component {

  static contextType = MyContext;

  render() {
    return (
      <p>{this.context}</p>
    )
  }
}

export default AnotherComponent;
```

## Function Consumers

Class components have the options of using *contextType* or using the `static` keyword. *What about function components?*

Function components can use another component provided by **Context**: **Consumer**. The `<Context.Consumer>` component is used to "wrap" functions and provides the same access and re-rendering based on subscriber status provided to class components.

The child of `<Context.Consumer>` *must* be a function. Anything returned by this function becomes tied to the *value* of the context. If it changes, the returned JSX inside the child function will re-render.

```javascript
import React from 'react';
import MyContext from '../Context';

function AnotherComponent() {
  return (
    <MyContext.Consumer>
      {
        (value) => {
          return <p>{value}</p>;
        }
      }
    </MyContext.Consumer>
  )
}

export default AnotherComponent;
```
