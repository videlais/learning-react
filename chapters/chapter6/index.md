# Working with CSS in React

- [Working with CSS in React](#working-with-css-in-react)
  - [Reviewing CSS Selectors](#reviewing-css-selectors)
    - [Using `className`](#using-classname)
  - [Importing CSS](#importing-css)
  - [CSS Modules](#css-modules)
  - [Inline CSS](#inline-css)
    - [CamelCase Naming](#camelcase-naming)
    - [Changing Properties During Runtime](#changing-properties-during-runtime)
    - [Automatic `px`](#automatic-px)
    - [Performance Issues](#performance-issues)

## Reviewing CSS Selectors

Cascading StyleSheets (CSS) work through *selectors*. These are either special symbols for named attributes like `class` or `id`, or a name of an element like `body`.

For example, to style an element with the `class` of "redFont", the code might look like the following:

```css
.redFont {
  color: red;
  font-size: 1.2em;
}
```

Running inside web browsers, React continues this same pattern. However, as the keyword `class` is already reserved in JavaScript, JSX elements cannot use it. Instead, React uses what JavaScript does when working with **Elements** and their attributes directly using the DOM: `className`.

### Using `className`

To assign a value to an element, the attribute `className` is used, with a capital, `N`. This allows JSX elements to have a `class` assigned to them and use CSS rules for their styling, cascading down to its child elements, if any.

The attribute `id` is not used with CSS in React for a particular reason that links to what is already known about components: *They take care of themselves.*

The `id` selector, when used in CSS, can "reach" across a document. As it is a unique selector, it can only be given to one element in a document at one time. However, if a particular element has the `id`, how would any one component know? They are "taking care of themselves" and not looking -- nor do they have access! -- to the rest of the document. They can only see what elements they **render()**.

## Importing CSS

The use of WebPack allows for using CSS files in a way that contributes to the existing rule of components taking care of themselves. They can be used with the keyword `import` like with other modules.

Consider the following file examples:

**Example/index.css:**

```css
.redFont {
  color: red;
  font-size: 1.2em;
}
```

**Example/index.js:**

```javascript
import React from 'react';
import 'index.css';

class Example extends React.Component {
    render() {
        return (
          <div>
            <p className="redFont">This will be larger and in red!</p>
          </div>
        );
    }
}

export default Example;
```

In the above example, the CSS file, `index.css`, is directly imported into the existing `index.js` file. The naming of the files is meaningful, too. Previously, it was reviewed how components should be in their own folder with all files related to them. As the CSS file is related to the component, it would also be in the same folder.

```bash
src
  components
    Example
      index.css
      index.js
```

The use of the name `index.css` reflects this naming process, putting the CSS code in the same folder. (The CSS files could be named anything and WebPack would import it, but it makes more sense to connect the CSS file naming with that of the component's to better organize everything.)

While CSS files can be simply imported into components and the classes used, React also supports another way: CSS modules.

## CSS Modules

When used with the `from` keyword, the CSS file is treated as a module like other `.js` files. This means that it can also be imported *into* a variable as well. React supports what is known as *CSS Modules* where all existing class rules in a CSS file become properties of the object created from importing.

**index.css:**

```css
.redFont {
  color: red;
  font-size: 1.2em;
}
.greenFont {
  color: green;
  font-size: 2em;
  border: 1px solid black;
}
```

**index.js:**

```javascript
import React, { Component } from 'react';
import styles from './index.css';

class Example extends Component {
  render() {
    return (
      <div>
        <p className={styles.redFont}>This is red!</p>
        <p className={styles.greenFont}>This is green!</p>
      </div>
    );
  }
}

export default Example;
```

In the above code, the selectors `.redFont` and `.greenFont` became properties *redFont* and *greenFont* of the object **styles**. Any other existing class selectors from the `index.css` file would have undergone the same processing.

When working with CSS Modules, this allows for easily "assigning" of a `className` value (using JSX expressions) when working with generating elements or creating components based on using data and adding multiple elements.

This also allows a parent component to more easily pass the value to its children and for them to be able to use it as well.

## Inline CSS

So far, only class selectors have been discussed. ID selectors were mention and, because they break the *Components take care of themselves* rule, ignored. It is possible to create an ID-like selector in React. However, instead of "selecting," the code would be assigned to the `style` attribute.

Class selectors use the keyword `className`, but that does not means the `style` attribute does not exist. It can also be used and, because React understands CSS Modules, can be given an object literal of properties, all of which are CSS values.

**index.js:**

```javascript
import React, { Component } from 'react';

class Example extends Component {
  render() {
      let styles = {
          color: "red",
          fontSize: "1.2em"
      }

    return (
      <div>
        <p style={styles}>This will be red!</p>
      </div>
    );

  }
}

export default Example;
```

### CamelCase Naming

As the above example shows, CSS rules that would have used a hyphen become camel-case when used in JavScript. This is because the hyphen is not allowed in variable names and thus cannot be in a property name.

When working in a web browser, any hyphened names as part of the DOM follow this rule. Thus, using inline CSS follows the same. Examples that would have been `font-size` in CSS become `fontSize` in JavaScript.

### Changing Properties During Runtime

The advantage of inline CSS, unlike CSS modules or importing CSS, is that it can be calculated during runtime. Because the string values are merely properties of an object, they can be changed through concatenating values.

**index.js:**

```javascript
import React, { Component } from 'react';

class Example extends Component {
  render() {
    let styles = {
      color: 'red',
      fontSize: '1.2em'
    }

    styles.color = 'green';

    return (
      <div>
        <p style={styles}>This will be green!</p>
      </div>
    );

  }
}

export default Example;
```

### Automatic `px`

When using inline CSS, React will default to using the measurement `px`. For example when specifying the `height` of an element, the property value could be 10 and it would be translated into `10px` by React.

```javascript
import React, { Component } from 'react';

class Example extends Component {
  render() {
    return (
      <div>
        <p style={ { height: 10 } }>This will have a height of 10px!</p>
      </div>
    );

  }
}

export default Example;
```

### Performance Issues

While using inline CSS may seem like a great way to use CSS in React, it is also not recommended. Using `className` is the preferred way whenever possible.

The reason for avoiding inline CSS relates to its slower performance. When added to the document, all CSS calculations are done in real-time when they are added to the document instead of being storied and applied to an existing document.

With imported CSS, these calculations are done *before* the elements themselves are loaded. With in-line CSS, this calculation is done *during* loading as the elements are parsed by React and rendered.
