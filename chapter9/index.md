# Higher-Order Components

- [Higher-Order Components](#higher-order-components)
  - [Augmenting Components](#augmenting-components)
    - [Reducing Code](#reducing-code)

## Augmenting Components

When using function and class components, another general pattern emerges: higher-order. When working with functions in JavaScript, a *higher-order function* is one which changes or updates a function and returns a new one. In React, a *higher-order component* does the same: it accepts a component, augments it somehow, and returns a new component.

### Reducing Code

Generally, higher-order components come into play in patterns where code could be reduced through creating a new component that has or does common tasks. In these case, the higher-order component could add things like common attributes or working with event listeners.

Consider the following code:

**src/components/withHover/index.js:**

```javascript
import React from 'react';

function withHover(Component) {

  return class extends React.Component {

    state = { hovering: false };

    mouseOver = () => this.setState({ hovering: true });

    mouseOut = () => this.setState({ hovering: false });

    render() {
      return <Component onMouseOver={this.mouseOver} onMouseOut={this.mouseOut} />
    }
  }

}

export default withHover;
```

In the above example, the function *withHover()* accepts a component and returns a class that is augmented with three new public class fields: **state**, *mouseOver()*, and *mouseOut()*. Two events have also been added to the component, `onMouseOver` and `onMouseOut`. For these, the internal functions matching the event will be called as they occur.

The name of the function also starts with a lowercase letter. This is a common pattern for higher-order components. As they are functions, they follow camel case naming. They also, because they are not objects or strictly components themselves, would not follow the style pattern of using a capital letter for it.
