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

Because all class components must have a *render()* function and return JSX, this means that the three rules of JSX are most commonly seen as part components.

TODO

## Render Chaining

## Elements and Attributes into Objects and Properties
