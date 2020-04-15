# Working with Data in React

## Using *fetch()*

The function [*fetch()*](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) is a lightweight way to access remote data sources in a web browser. It accepts a URL and HTTP request options such as which method, mode, and any addition headers to send with the request. It returns a promise that will resolve into a [**Response** object](https://developer.mozilla.org/en-US/docs/Web/API/Response).

```javascript
fetch(URL, options);
```

The two processing functions of the **Response** object, [*text()*](https://developer.mozilla.org/en-US/docs/Web/API/Body/text) and [*json()*](https://developer.mozilla.org/en-US/docs/Web/API/Body/json), each also return promises.

When used together, the promise returned by *fetch()* and the processing functions of **Response** can be chained together.

```javascript
fetch(URL, options)
.then((Response) => Response.json())
.then((ProcessesedResponse) => {
    // Do something with the processed response
})
```

### Using `await` and `async`

As both also return promises, the keyword `await` and `async` can be used with them to wait for the promises to resolve and do something with their data.

```javascript
async fetchExample = () => {
  let response = await fetch(URL, options);
  let processed = await response.json();
}
```

## Date Requests and Component Lifecycles

Promises can take time to resolve. With this in mind, when to use *fetch()* and wait for it to resolve becomes an important issue. In considering React class component lifecycle, only one function makes sense.

### Fetching During *componentDidMount()*

Within the class component lifecycle, the mounting phase ends with the function *componentDidMount()*. By the time it is called, both the *constructor()* and *render()* functions have also been called. Any elements or other components have been added to the document.

Using the function *componentDidMonunt()*, then, makes sense. Once the initial elements and components have been added, data can be fetched that may result in things been changed or adjust within the class component.

```javascript
import React, { Component } from 'react';

class Example extends Component {

  state = {
      data: ""
  };

  async componentDidMount() {
    let response = fetch(URL);
    let processed = response.json();
    this.setState({data: processed})
  }

  render() {
      return (
        <div>
          <p>{this.state.data}</p>
        </div>
      );
  }

}

export default Example;
```

### Fetching with Function Components

At initial glance, the function *useEffect()* would seem to be a good candidate for use with *fetch()*. After all, it is called after the function component is rendered and would thus, like with *componentDidMount()* for class components, run after the initial elements had been added to the document.

However, these is a issue. If *useEffect()* is paired with *useState()*, one would trigger the other, which would then trigger the other. This would create an infinite loop!

The solution is to check values. The second argument of *useEffect()* is an array of values. If they have not changed, the returned elements will not be updated. Thus, through checking the whatever is processed, the initial state variables of the function component can be checked. If they have updated, the elements will be re-rendered. If not, nothing will happen.

```javascript
import React, { Component } from 'react';

class Example extends Component {

  state = {
      data: ""
  };

  async componentDidMount() {
    let response = fetch(URL);
    let processed = response.json();
    this.setState({data: processed})
  }

  render() {
      return (
        <div>
          <p>{this.state.data}</p>
        </div>
      );
  }

}

export default Example;
```

TODO
