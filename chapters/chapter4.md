---
title: "Introducing React with Function Components"
order: 4
chapter_number: 4
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Create** React applications using modern function components and the React 18+ createRoot API.
- **Build** function components that accept props and return JSX elements.
- **Implement** React Hooks including useState() and useEffect() for state management and side effects.
- **Apply** modern React patterns for rendering lists and handling user interactions.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Introducing React with Function Components](#introducing-react-with-function-components)
- [Modern React Applications with createRoot](#modern-react-applications-with-createroot)
- [Anatomy of a Function Component](#anatomy-of-a-function-component)
- [Components Get Props](#components-get-props)
- [React Hooks and State Management](#react-hooks-and-state-management)
  - [useState() Hook](#usestate-hook)
  - [useEffect() Hook](#useeffect-hook)
- [Rendering Lists and Using Keys](#rendering-lists-and-using-keys)
- [Modern Component Organization](#modern-component-organization)

## Introducing React with Function Components

React is known as one of the faster front-end frameworks. One of the ways it achieves this is through its Virtual DOM - an in-memory representation of the real DOM. React keeps track of this virtual document object model and only updates the real document when absolutely necessary. This means that updates to the document only occur when they need to and no unnecessary changes happen.

This makes it "react" faster because it will batch updates and make them all at once instead of updating at different times. React's reconciliation process compares the current virtual DOM with the previous version and efficiently updates only what has changed.

## Modern React Applications with createRoot

Starting with React 18, the recommended way to render React applications uses the `createRoot` API instead of the legacy `ReactDOM.render()` method.

**Modern React 18+ approach:**

```javascript
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.js';

const container = document.getElementById('root');
const root = createRoot(container);
root.render(<App />);
```

**Legacy approach (React 17 and earlier):**

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.js';

ReactDOM.render(<App />, document.querySelector('#root'));
```

The `index.html` for a React project typically looks like the following:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>React App</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

The call to `root.render()` adds the internal elements of each component to this `<div id="root">` in the HTML document, starting with `<App />`.

## Anatomy of a Function Component

Function components are the modern, preferred way to write React components. They are simpler, more concise, and leverage React Hooks for state management and side effects.

**Modern Function Component Pattern:**

```javascript
import React from 'react';

function Welcome() {
  return (
    <div>
      <h1>Hello, World!</h1>
      <p>Welcome to React!</p>
    </div>
  );
}

export default Welcome;
```

**Alternative Arrow Function Syntax:**

```javascript
import React from 'react';

const Welcome = () => {
  return (
    <div>
      <h1>Hello, World!</h1>
      <p>Welcome to React!</p>
    </div>
  );
};

export default Welcome;
```

**Key Characteristics of Function Components:**

- **Simpler Syntax:** No need for `class` keyword or `extends`
- **Direct Return:** Function directly returns JSX
- **Hooks Integration:** Use React Hooks for state and lifecycle management
- **Better Performance:** Generally more optimized by React's reconciliation process

## Components Get Props

Function components receive props as their first parameter. Props (short for "properties") allow you to pass data from parent components to child components.

**Function Component with Props:**

```javascript
import React from 'react';

function Welcome(props) {
  return (
    <div>
      <h1>Hello, {props.name}!</h1>
      <p>Welcome to {props.appName}!</p>
    </div>
  );
}

export default Welcome;
```

**Using Destructuring for Cleaner Code:**

```javascript
import React from 'react';

function Welcome({ name, appName }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Welcome to {appName}!</p>
    </div>
  );
}

export default Welcome;
```

**Using the Component:**

```javascript
import React from 'react';
import Welcome from './Welcome';

function App() {
  return (
    <div>
      <Welcome name="Sarah" appName="React Learning" />
      <Welcome name="Mike" appName="My App" />
    </div>
  );
}

export default App;
```

**Props Characteristics:**

- **Read-Only:** Props cannot be modified by the receiving component
- **Any Data Type:** Props can be strings, numbers, objects, arrays, or functions
- **Optional:** Components can have default values for props
- **Type Checking:** Tools like PropTypes or TypeScript can validate prop types

## React Hooks and State Management

React Hooks allow function components to have state and side effects, making them as powerful as class components. Hooks are functions that start with "use" and can only be called at the top level of function components.

### useState() Hook

The `useState` hook allows function components to have state. It returns an array with two elements: the current state value and a function to update it.

**Basic useState Example:**

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

export default Counter;
```

**Multiple State Variables:**

```javascript
import React, { useState } from 'react';

function UserProfile() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);

  return (
    <div>
      <input 
        value={name} 
        onChange={(e) => setName(e.target.value)}
        placeholder="Name" 
      />
      <input 
        value={email} 
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email" 
      />
      <input 
        type="number"
        value={age} 
        onChange={(e) => setAge(parseInt(e.target.value))}
        placeholder="Age" 
      />
      <div>
        <h3>Profile:</h3>
        <p>Name: {name}</p>
        <p>Email: {email}</p>
        <p>Age: {age}</p>
      </div>
    </div>
  );
}

export default UserProfile;
```

**useState Best Practices:**

- **Descriptive Names:** Use clear names like `[user, setUser]` instead of `[u, setU]`
- **Separate Concerns:** Use multiple useState calls for unrelated state variables
- **Functional Updates:** Use `setCount(prev => prev + 1)` when new value depends on previous value

### useEffect() Hook

The `useEffect` hook handles side effects in function components, such as data fetching, subscriptions, or manually changing the DOM. It combines the functionality of `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` from class components.

**Basic useEffect Example:**

```javascript
import React, { useState, useEffect } from 'react';

function DocumentTitle() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // Update document title after every render
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

export default DocumentTitle;
```

**useEffect with Dependency Array:**

```javascript
import React, { useState, useEffect } from 'react';

function UserData({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // This effect runs only when userId changes
    async function fetchUser() {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } catch (error) {
        console.error('Error fetching user:', error);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, [userId]); // Dependency array - effect runs when userId changes

  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}

export default UserData;
```

**useEffect Cleanup:**

```javascript
import React, { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prevSeconds => prevSeconds + 1);
    }, 1000);

    // Cleanup function - runs when component unmounts
    return () => clearInterval(interval);
  }, []); // Empty dependency array - runs once on mount

  return (
    <div>
      <h2>Timer: {seconds} seconds</h2>
    </div>
  );
}

export default Timer;
```

## Rendering Lists and Using Keys

One of the most common patterns in React is rendering lists of data. The `map()` method is the preferred way to transform arrays into JSX elements.

**Basic List Rendering:**

```javascript
import React from 'react';

function ShoppingList() {
  const items = ['apples', 'bananas', 'oranges', 'grapes'];

  return (
    <div>
      <h2>Shopping List</h2>
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

export default ShoppingList;
```

**Rendering Complex Objects:**

```javascript
import React from 'react';

function UserList() {
  const users = [
    { id: 1, name: 'Alice', email: 'alice@example.com' },
    { id: 2, name: 'Bob', email: 'bob@example.com' },
    { id: 3, name: 'Charlie', email: 'charlie@example.com' }
  ];

  return (
    <div>
      <h2>User List</h2>
      {users.map(user => (
        <div key={user.id} style={% raw %}{{ border: '1px solid #ccc', margin: '10px', padding: '10px' }}{% endraw %}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}

export default UserList;
```

**The Importance of Keys:**

Keys help React identify which items have changed, been added, or removed. React uses keys to efficiently update the DOM when lists change.

**Best Practices for Keys:**

- **Use Unique IDs:** Prefer unique identifiers over array indices when possible
- **Stable Keys:** Keys should be consistent across renders
- **Avoid Index as Key:** Only use array index as key for static lists that don't change

```javascript
// Good - using unique ID
{users.map(user => (
  <UserCard key={user.id} user={user} />
))}

// Acceptable for static lists
{staticItems.map((item, index) => (
  <li key={index}>{item}</li>
))}

// Avoid for dynamic lists
{dynamicItems.map((item, index) => (
  <li key={index}>{item}</li> // Can cause rendering issues
))}
```

## Modern Component Organization

Modern React applications organize components in a clean, scalable way that promotes reusability and maintainability.

**Recommended Folder Structure:**

```bash
src/
  components/
    Button/
      Button.js
      Button.css
      index.js
    UserCard/
      UserCard.js
      UserCard.css
      index.js
  pages/
    Home/
      Home.js
      Home.css
      index.js
  hooks/
    useLocalStorage.js
    useApi.js
  utils/
    helpers.js
  App.js
  index.js
```

**Component Organization Examples:**

**src/components/Button/Button.js:**

```javascript
import React from 'react';
import './Button.css';

function Button({ children, onClick, variant = 'primary', disabled = false }) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

export default Button;
```

**src/components/Button/index.js:**

```javascript
export { default } from './Button';
```

**Using the Component:**

```javascript
import React from 'react';
import Button from './components/Button';

function App() {
  const handleClick = () => {
    alert('Button clicked!');
  };

  return (
    <div>
      <Button onClick={handleClick}>Primary Button</Button>
      <Button variant="secondary" onClick={handleClick}>
        Secondary Button
      </Button>
      <Button disabled>Disabled Button</Button>
    </div>
  );
}

export default App;
```

**Modern Component Features:**

- **Default Props:** Use default parameters in function components
- **PropTypes:** Add runtime type checking (optional)
- **Custom Hooks:** Extract reusable logic into custom hooks
- **Component Composition:** Build complex UIs by combining simple components

**Complete Application Example:**

```javascript
import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [inputValue, setInputValue] = useState('');

  const addTodo = () => {
    if (inputValue.trim()) {
      setTodos([...todos, { 
        id: Date.now(), 
        text: inputValue, 
        completed: false 
      }]);
      setInputValue('');
    }
  };

  return (
    <div>
      <h1>Todo App</h1>
      <div>
        <input
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          placeholder="Add a todo..."
        />
        <button onClick={addTodo}>Add</button>
      </div>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            {todo.text}
          </li>
        ))}
      </ul>
    </div>
  );
}

// Modern React 18+ rendering
const container = document.getElementById('root');
const root = createRoot(container);
root.render(<TodoApp />);
```
