# Introducing React

## ReactDOM

React is known as one of the faster front-end frameworks. One of the ways it achieves this is through its own "shadow DOM." React keeps track of an internal document object model (DOM) and only updates the real DOM in a document when absolutely necessary. This means that updates to the document only occur when they need to and no unnecessary changes or updates happen.

This makes it "react" faster because it will combine updates and make them all at once instead of updating at different times. This also means that React expects developers to be aware of when they are making changes and to know React may delay things to make code more efficient overall.

One of the ways React keeps track of its own DOM is through a object known as **ReactDOM**. At the very center of all React code is call to the function *render()* as part of the object **ReactDOM**.

Most React projects, at their very core, contain the following line of code:

```javascript
ReactDOM.render(<App />, document.querySelector('#root'));
```

The `index.html` for a React project often looks like the following:

```html
<html>
<body>
  <div id="root">
  </div>
</body>
```

The call to the *render()* function as part of **ReactDOM** adds the internal elements of each component to this `<div>` in the HTML document, starting with `<App />`. The first argument to the function is the component to render and the second is where to render it.

Moving from the **ReactDOM** outward, the first component encountered is **App**.

The `index.js` file of most React projects, in fact, look like the following, where both **ReactDOM** and **App** are imported.

```javascript
import React from 'react';
import ReactDOM from 'reactDOM';
import App from './App.js';

ReactDOM.render(<App />, document.querySelector('#root'));
```

## Anatomy of a Class Component

*Every React component must import React.*

While this might seem like a silly rule given the usage of React, this establishes two things behind-the-scenes

- It tells WebPack + Babel that the file could contain JSX.

- It allows classes to `extends` the class **Component**.

In other words, nearly every file in a React project will start with the following line:

```javascript
import React from 'react';
```

This will be the first step and allow a developer, as was mentioned above, to use JSX if she wants and also extend the existing **Component** class.

### Extending **React.Component**

The most common form of a component in React is what is known as a *class component*. It is named this way because A) it is a component and B) it uses the `class` keyword.

One of the general patterns in React looks like the following:

```javascript
import React from 'react'

class Example extends React.Component {

}

export default Example;
```

The class **Example** uses the `extends` keyword to "extend" the existing class **React.Component** and thus makes it a class component, as it is both a component and uses the keyword `class`.

### Class Component Functions

As with any other extending of exiting classes in JavaScript, anything that `extends` **React.Component** gains all of its functions. One of the most important of these is the *render()* function.

*Every class component must render.*

If a class `extends` **React.Component**, this means it will contain some HTML elements. The "containing" part comes through the function *render()* it inherits.

```javascript
import React from 'react'

class Example extends React.Component {
  render() {
    // Return something!
  }
}

export default Example;
```

*Every render() must return HTML.*

Along with every class component needing to have a *render()* function, the *render()* must also return HTML. After all, a component is defined, in part, because it is a collection of HTML.

```javascript
import React from 'react'

class Example extends React.Component {
  render() {
    return (
        // Return JSX!
    );
  }
}

export default Example;
```

Most React projects also use a feature available to all functions in JavaScript, the use of parentheses. The JSX returned from a *render()* function is often "wrapped" to help show where the contents begins and ends.

### Returning JSX

Because all class components must have a *render()* function and return JSX, this means that the three rules of JSX are most commonly seen as part components. This also means that only *expressions* are allowed in the returned JSX. The use of the keywords `if`, `else`, `for`, and `while` are not allowed.

*What does this mean?* If conditional statements and traditional loop structures are not allowed, this means that the additional functionality of arrays and objects should be used instead. Functions like **map()**, **filter()**, and **forEach()** begin important for displaying parts or the values of certain arrays and objects.

For example, consider the common example of needing to display every entry in an array.

```javascript
import React from 'react';

class Example extends React.Component {
  render() {

    let arrayExample = [1,2,3,4,5];

    return (
      <div>
        {
          arrayExample.forEach(
            (entry, position) => {
              <p key={position}>{entry}</p>
            }
          );
        }
      </div>
    );

  }
}

export default Example;
```

#### Using `key`

Whenever a large group of elements are added to the document, React stresses the use of the attribute `key` with a unique on value per each element. This helps React know which, if any, of the elements might need to be updated in the future. (Remember, React keeps its own shadow version of the DOM and only updates the real one when needed!)

When working with functions like **map()**, **filter()**, and **forEach()**, it is strongly suggested to use the attribute `key` on each element added and set its value to the optional value *position* all of these functions supply. This will not only help React speed up the application, but it also a good practice helping to identify different individual elements within a larger set of them.

## Render Chaining

This chapter started with discussing the object **ReactDOM** and its function *render()*. In reviewing class components, it was mentioned that all class components must have a *render()* function. In fact, all components, because they are collections of elements, must render something.

Because of this use of rendering, React uses a system of *render()* into *render()* functions, or known as render chaining. One component will have a *render()* that is used by another and another until a class component is found that does not have its own *render()* function.

Consider the following code:

**index.js:**

```javascript
import React from 'react';
import ReactDOM from 'reactDOM';

ReactDOM.render(<App />, document.querySelector('#root'));
```

**App.js:**

```javascript
import React from 'react';

class App extends React.Component {
  render() {
    return (
      <div>
        <p>Hi!</p>
      </div>
    )
  }
}

export default App;
```

In the above two files, the function *ReactDOM.render()* attempts to render the component **App**. Because it has its own *render()* function, this is called and its HTML returned. What started as a nested form like the following example --

```javascript
ReactDOM.render(App.render());
```

-- becomes the following HTML:

```html
<div>
  <p>Hi!</p>
</div>
```

The chain of *render()* functions navigates down and adds the element to the document when it reaches a bottom-most component that has only HTML elements and returns the content back up the chain to the top-most usage of **ReactDOM.render()**.

## Elements and Attributes into Objects and Properties

React translate objects from their HTML form into an object form, skipping over the explicit usage of the `new`. Through including a component as if it was an element, it is converted from an element into an object.

As was reviewed in an earlier chapter, elements have *attributes*. In React, these are also translated. As elements become object, an element's attributes become *properties*. In fact, to help with this common process, React pass all class components an object called **props**. Any attributes an element has are translated into this object.

Consider the following code:

**index.js:**

```javascript
import React from 'react';
import ReactDOM from 'reactDOM';

ReactDOM.render(<App attributeExample={5} />, document.querySelector('#root'));
```

**App.js:**

```javascript
import React from 'react';

class App extends React.Component {
  render() {
    <div>
      The value of attributeExample is: {this.props.attributeExample}.
    </div>
  }
}

export default App;
```

When passed to a class component, the object **props** becomes *this.props* and accessible from anywhere inside the class.

This make it easy to pass values from one component to another. Instead of using the `new` keyword, the use of attributes allows initial values to be "passed" to the new object created based on the element form.

**Node.js:**

```javascript
let value1 = 1;
let value2 = 2;

let element = new Element(value1, value2);
```

**React:**

```html
<Example value1={1} value2={2} />
```

Internally, in the above code, the object **Example** would have access to *this.props.value1* and *this.props.value2*.

## Organizing Components

*Components can contain components.* While this concept seems simple given what has been shown of working with *ReactDOM.render()* and other other *render()* functions, this also extends to *all* components. At the center of a React project will be a usage of *ReactDOM.render()*. However, this is no limit to the number of other components.

To help with organizing them, like with a Node.js project, it is strongly recommended to create separate folders for each new component with their own `index.js` file. These should each be inside an overall folder named `components`.

Consider the following folder structure:

```bash
src
  /components
      /Example
        index.js
  App.js
  index.js
```

The above folder structure shows the base React code of using `App.js` and `index.js`. It also shows that there is a component named **Example** that is based in the folder `src/components/Example.index.js`.

When imported into **App** or another module, the path to the component would be the folder of its name and not the internal `index.js`. The reason for this is the same as it is for using `import` and `export`: WebPack understands Node.js projects use `index.js` and will automatically import it when given a folder.

**src/components/Element/index.js:**

```javascript
import React from 'react';

class Element extends React.Component {
  render () {
    return (
      <p>Hi!</p>
    );
  }
}

export default Element;
```

**src/App.js:**

```javascript
import React from `react`;
import Element from './components/Element';

class App extends React.Component {
  render () {
    return (
      <div>
        <Element />
      </div>
    )
  }
}

export default App;
```
