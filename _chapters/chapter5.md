---
title: "Event Handling and State in Function Components"
order: 5
chapter_number: 5
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Implement** event handling in function components using React Synthetic Events.
- **Manage** component state using the useState() hook for interactive user interfaces.
- **Create** dynamic user interactions with forms, buttons, and input elements.
- **Apply** event handling patterns including controlled components and event delegation.

**Chapter Outline:**

- [Objectives](#objectives)
- [Event Handling in Modern React](#event-handling-in-modern-react)
- [Understanding React Synthetic Events](#understanding-react-synthetic-events)
- [Basic Event Handling in Function Components](#basic-event-handling-in-function-components)
- [Working with Event Objects](#working-with-event-objects)
- [State Management with Events](#state-management-with-events)
- [Controlled Components and Form Handling](#controlled-components-and-form-handling)
- [Custom Hooks for Event Handling](#custom-hooks-for-event-handling)

## Event Handling in Modern React

Modern React applications use function components with hooks for event handling, providing a cleaner and more intuitive approach than the legacy class component patterns. Event handling in function components leverages React's Synthetic Event system while being simpler to understand and implement.

## Understanding React Synthetic Events

React creates *synthetic events* that wrap native browser events, providing a consistent interface across different browsers. These synthetic events have the same interface as native events, but work identically across all browsers.

**Key Benefits of Synthetic Events:**

- **Cross-browser compatibility:** Same behavior in all browsers
- **Performance optimization:** Event delegation and pooling for better performance
- **React integration:** Seamless integration with React's rendering system
- **Consistent API:** Uniform event handling regardless of browser differences

**Common Event Types:**

- **Mouse Events:** `onClick`, `onDoubleClick`, `onMouseEnter`, `onMouseLeave`, `onMouseMove`
- **Keyboard Events:** `onKeyDown`, `onKeyUp`, `onKeyPress`
- **Form Events:** `onChange`, `onSubmit`, `onFocus`, `onBlur`
- **Touch Events:** `onTouchStart`, `onTouchMove`, `onTouchEnd`

## Basic Event Handling in Function Components

Function components handle events using simple callback functions. There's no need to worry about `this` binding or context issues that existed with class components.

**Simple Click Handler:**

```javascript
import React from 'react';

function Button() {
  const handleClick = () => {
    alert('Button clicked!');
  };

  return (
    <button onClick={handleClick}>
      Click Me
    </button>
  );
}

export default Button;
```

**Inline Event Handlers:**

```javascript
import React from 'react';

function SimpleButton() {
  return (
    <button onClick={() => console.log('Button clicked!')}>
      Click Me
    </button>
  );
}

export default SimpleButton;
```

**Event Handler with Parameters:**

```javascript
import React from 'react';

function ButtonList() {
  const handleButtonClick = (buttonName) => {
    alert(`${buttonName} button was clicked!`);
  };

  return (
    <div>
      <button onClick={() => handleButtonClick('Save')}>
        Save
      </button>
      <button onClick={() => handleButtonClick('Cancel')}>
        Cancel
      </button>
      <button onClick={() => handleButtonClick('Delete')}>
        Delete
      </button>
    </div>
  );
}

export default ButtonList;
```

## Working with Event Objects

React synthetic events provide access to all the properties and methods you'd expect from native DOM events. Here's how to work with them in function components:

**Accessing Event Properties:**

```javascript
import React from 'react';

function EventDemo() {
  const handleClick = (event) => {
    console.log('Event type:', event.type);
    console.log('Target element:', event.target);
    console.log('Current target:', event.currentTarget);
    console.log('Mouse position:', event.clientX, event.clientY);
  };

  const handleKeyPress = (event) => {
    console.log('Key pressed:', event.key);
    console.log('Key code:', event.keyCode);
    
    if (event.key === 'Enter') {
      console.log('Enter key was pressed!');
    }
  };

  return (
    <div>
      <button onClick={handleClick}>
        Click for event info
      </button>
      <input 
        type="text" 
        onKeyPress={handleKeyPress}
        placeholder="Type here and press Enter"
      />
    </div>
  );
}

export default EventDemo;
```

**Preventing Default Behavior:**

```javascript
import React from 'react';

function LinkDemo() {
  const handleLinkClick = (event) => {
    event.preventDefault(); // Prevents the default link navigation
    console.log('Link clicked, but navigation prevented');
  };

  const handleFormSubmit = (event) => {
    event.preventDefault(); // Prevents the default form submission
    console.log('Form submission prevented');
    // Handle form data here instead
  };

  return (
    <div>
      <a href="https://example.com" onClick={handleLinkClick}>
        Click this link (navigation prevented)
      </a>
      <form onSubmit={handleFormSubmit}>
        <input type="text" placeholder="Enter something" />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}

export default LinkDemo;
```

## State Management with Events

Function components use the `useState` hook to manage state, making event handling much simpler and more intuitive than class components:

**Basic State Updates:**

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const handleIncrement = () => {
    setCount(count + 1);
  };

  const handleDecrement = () => {
    setCount(count - 1);
  };

  const handleReset = () => {
    setCount(0);
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={handleIncrement}>+</button>
      <button onClick={handleDecrement}>-</button>
      <button onClick={handleReset}>Reset</button>
    </div>
  );
}

export default Counter;
```

**Functional State Updates:**

When the new state depends on the previous state, use a function to ensure you always work with the latest value:

```javascript
import React, { useState } from 'react';

function FunctionalCounter() {
  const [count, setCount] = useState(0);

  const handleIncrement = () => {
    setCount(prevCount => prevCount + 1);
  };

  const handleIncrementTwice = () => {
    // This ensures both increments happen
    setCount(prevCount => prevCount + 1);
    setCount(prevCount => prevCount + 1);
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={handleIncrement}>+1</button>
      <button onClick={handleIncrementTwice}>+2</button>
    </div>
  );
}

export default FunctionalCounter;
```

**Managing Multiple State Values:**

```javascript
import React, { useState } from 'react';

function UserProfile() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });

  const handleNameChange = (event) => {
    setUser(prevUser => ({
      ...prevUser,
      name: event.target.value
    }));
  };

  const handleEmailChange = (event) => {
    setUser(prevUser => ({
      ...prevUser,
      email: event.target.value
    }));
  };

  const handleAgeChange = (event) => {
    setUser(prevUser => ({
      ...prevUser,
      age: parseInt(event.target.value, 10)
    }));
  };

  return (
    <div>
      <h2>User Profile</h2>
      <div>
        <label>
          Name:
          <input 
            type="text" 
            value={user.name}
            onChange={handleNameChange}
          />
        </label>
      </div>
      <div>
        <label>
          Email:
          <input 
            type="email" 
            value={user.email}
            onChange={handleEmailChange}
          />
        </label>
      </div>
      <div>
        <label>
          Age:
          <input 
            type="number" 
            value={user.age}
            onChange={handleAgeChange}
          />
        </label>
      </div>
      <div>
        <h3>Current Values:</h3>
        <p>Name: {user.name}</p>
        <p>Email: {user.email}</p>
        <p>Age: {user.age}</p>
      </div>
    </div>
  );
}

export default App;
```



## Controlled Components and Form Handling

Controlled components are the recommended way to handle form inputs in React. The component controls the input's value through state, creating a "single source of truth":

**Simple Controlled Input:**

```javascript
import React, { useState } from 'react';

function ControlledInput() {
  const [inputValue, setInputValue] = useState('');

  const handleChange = (event) => {
    setInputValue(event.target.value);
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Submitted value:', inputValue);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Enter text:
        <input
          type="text"
          value={inputValue}
          onChange={handleChange}
        />
      </label>
      <button type="submit">Submit</button>
      <p>Current value: {inputValue}</p>
    </form>
  );
}

export default ControlledInput;
```

**Complete Form with Multiple Inputs:**

```javascript
import React, { useState } from 'react';

function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: '',
    category: 'general',
    newsletter: false
  });

  const handleInputChange = (event) => {
    const { name, value, type, checked } = event.target;
    
    setFormData(prevData => ({
      ...prevData,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Form submitted:', formData);
    
    // Reset form after submission
    setFormData({
      name: '',
      email: '',
      message: '',
      category: 'general',
      newsletter: false
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>
          Name:
          <input
            type="text"
            name="name"
            value={formData.name}
            onChange={handleInputChange}
            required
          />
        </label>
      </div>

      <div>
        <label>
          Email:
          <input
            type="email"
            name="email"
            value={formData.email}
            onChange={handleInputChange}
            required
          />
        </label>
      </div>

      <div>
        <label>
          Category:
          <select
            name="category"
            value={formData.category}
            onChange={handleInputChange}
          >
            <option value="general">General</option>
            <option value="support">Support</option>
            <option value="sales">Sales</option>
            <option value="feedback">Feedback</option>
          </select>
        </label>
      </div>

      <div>
        <label>
          Message:
          <textarea
            name="message"
            value={formData.message}
            onChange={handleInputChange}
            rows="4"
            required
          />
        </label>
      </div>

      <div>
        <label>
          <input
            type="checkbox"
            name="newsletter"
            checked={formData.newsletter}
            onChange={handleInputChange}
          />
          Subscribe to newsletter
        </label>
      </div>

      <button type="submit">Send Message</button>
    </form>
  );
}

export default ContactForm;
```

## Custom Hooks for Event Handling

You can create custom hooks to encapsulate common event handling patterns:

**useCounter Hook:**

```javascript
import React, { useState } from 'react';

// Custom hook for counter functionality
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(prev => prev + 1);
  const decrement = () => setCount(prev => prev - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// Component using the custom hook
function CounterApp() {
  const { count, increment, decrement, reset } = useCounter(10);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

export default CounterApp;
```

**useForm Hook:**

```javascript
import React, { useState } from 'react';

// Custom hook for form handling
function useForm(initialValues, onSubmit) {
  const [values, setValues] = useState(initialValues);

  const handleChange = (event) => {
    const { name, value, type, checked } = event.target;
    setValues(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    onSubmit(values);
  };

  const reset = () => setValues(initialValues);

  return { values, handleChange, handleSubmit, reset };
}

// Component using the custom hook
function LoginForm() {
  const { values, handleChange, handleSubmit, reset } = useForm(
    { username: '', password: '', rememberMe: false },
    (formData) => {
      console.log('Login attempt:', formData);
    }
  );

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>
          Username:
          <input
            type="text"
            name="username"
            value={values.username}
            onChange={handleChange}
          />
        </label>
      </div>
      <div>
        <label>
          Password:
          <input
            type="password"
            name="password"
            value={values.password}
            onChange={handleChange}
          />
        </label>
      </div>
      <div>
        <label>
          <input
            type="checkbox"
            name="rememberMe"
            checked={values.rememberMe}
            onChange={handleChange}
          />
          Remember me
        </label>
      </div>
      <button type="submit">Login</button>
      <button type="button" onClick={reset}>Clear</button>
    </form>
  );
}

export default LoginForm;
```

This modern approach to event handling with function components and hooks provides a much cleaner and more intuitive way to manage user interactions and state updates in React applications.
