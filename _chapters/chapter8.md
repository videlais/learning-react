---
title: "Class Components (Legacy Pattern)"
order: 8
chapter_number: 8
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Understand** class components as a legacy React pattern still found in existing codebases.
- **Build** class components that extend React.Component with proper syntax and structure.
- **Implement** lifecycle methods and state management in class components.
- **Convert** between class and function components when maintaining legacy code.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Understanding Class Components](#understanding-class-components)
- [Legacy ReactDOM.render() API](#legacy-reactdomrender-api)
- [Anatomy of a Class Component](#anatomy-of-a-class-component)
  - [Every React component must import React](#every-react-component-must-import-react)
  - [Extending React.Component](#extending-reactcomponent)
  - [Class Component Functions](#class-component-functions)
  - [Every render() must return JSX](#every-render-must-return-jsx)
- [Class Component Props and State](#class-component-props-and-state)
  - [Props in Class Components](#props-in-class-components)
  - [State in Class Components](#state-in-class-components)
  - [Lifecycle Methods (Legacy)](#lifecycle-methods-legacy)
- [Class Component Lifecycle Methods (Legacy)](#class-component-lifecycle-methods-legacy)
  - [Understanding Component Phases](#understanding-component-phases)
  - [Legacy Lifecycle Functions](#legacy-lifecycle-functions)
  - [Mounting Phase](#mounting-phase)
  - [Updating Phase](#updating-phase)
  - [Unmounting Phase](#unmounting-phase)
  - [Lifecycle Migration Patterns](#lifecycle-migration-patterns)
- [Legacy Patterns and Migration](#legacy-patterns-and-migration)
  - [Common Class Component Patterns](#common-class-component-patterns)
  - [Migration from Class to Function Components](#migration-from-class-to-function-components)
  - [When to Use Class Components Today](#when-to-use-class-components-today)

## Understanding Class Components

**Note:** Class components are a legacy pattern in React. While still supported and found in many existing codebases, function components with hooks are now the recommended approach for new development. This chapter covers class components for understanding and maintaining existing code.

Class components were the primary way to build React applications before React 16.8 introduced hooks. They use JavaScript ES6 class syntax and extend `React.Component` to gain access to React's features like state and lifecycle methods.

**When You'll Encounter Class Components:**

- **Legacy Codebases:** Older React applications built before 2019
- **Third-Party Libraries:** Some libraries still use class components
- **Migration Projects:** When updating older applications to modern React
- **Team Preferences:** Some teams may still prefer class components for complex state logic

## Legacy ReactDOM.render() API

Before React 18, applications used `ReactDOM.render()` to mount React applications. While this API still works, it's deprecated in favor of `createRoot()`.

**Legacy Pattern (React 17 and earlier):**

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.js';

ReactDOM.render(<App />, document.querySelector('#root'));
```

**Modern Pattern (React 18+):**

```javascript
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.js';

const container = document.getElementById('root');
const root = createRoot(container);
root.render(<App />);
```

## Anatomy of a Class Component

### Every React component must import React

In class components, you must import React because:

- It tells build tools that the file contains JSX
- It allows classes to extend `React.Component`
- JSX transforms reference React internally

```javascript
import React from 'react';
```

**Note:** React 17+ with modern build tools can automatically inject React imports, but explicit imports are still common in class components.

### Extending React.Component

Class components extend the `React.Component` base class to gain access to React features:

```javascript
import React from 'react';

class Example extends React.Component {
  // Class component methods go here
}

export default Example;
```

**Alternative Import Style:**

```javascript
import React, { Component } from 'react';

class Example extends Component {
  // Class component methods go here
}

export default Example;
```

### Class Component Functions

Class components inherit several methods from `React.Component`. The most important is the `render()` method:

**Every class component must have a render() method:**

```javascript
import React from 'react';

class Example extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello from Class Component</h1>
      </div>
    );
  }
}

export default Example;
```

### Every render() must return JSX

The `render()` method must return JSX that describes what should be displayed:

```javascript
import React from 'react';

class Welcome extends React.Component {
  render() {
    return (
      <div>
        <h1>Welcome, {this.props.name}!</h1>
        <p>Today is {new Date().toLocaleDateString()}</p>
      </div>
    );
  }
}

export default Welcome;
```

**JSX Rules in Class Components:**

- Only expressions are allowed in JSX (no `if`, `else`, `for`, `while`)
- Use `map()`, `filter()`, and ternary operators for dynamic content
- Always include `key` props when rendering lists

## Class Component Props and State

### Props in Class Components

Class components receive props through `this.props`:

```javascript
import React from 'react';

class Welcome extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, {this.props.name}!</h1>
        <p>Age: {this.props.age}</p>
        <p>Location: {this.props.location}</p>
      </div>
    );
  }
}

// Usage
<Welcome name="Alice" age={30} location="New York" />
```

### State in Class Components

Class components can have internal state using `this.state` and `this.setState()`:

```javascript
import React from 'react';

class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

export default Counter;
```

**Modern Class Field Syntax:**

```javascript
import React from 'react';

class Counter extends React.Component {
  state = {
    count: 0
  };

  increment = () => {
    this.setState(prevState => ({
      count: prevState.count + 1
    }));
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

export default Counter;
```

### Lifecycle Methods (Legacy)

Class components have lifecycle methods for different phases:

```javascript
import React from 'react';

class UserProfile extends React.Component {
  state = {
    user: null,
    loading: true
  };

  componentDidMount() {
    // Called after component mounts
    this.fetchUser();
  }

  componentDidUpdate(prevProps) {
    // Called after component updates
    if (prevProps.userId !== this.props.userId) {
      this.fetchUser();
    }
  }

  componentWillUnmount() {
    // Called before component unmounts
    // Cleanup subscriptions, timers, etc.
  }

  fetchUser = async () => {
    this.setState({ loading: true });
    try {
      const response = await fetch(`/api/users/${this.props.userId}`);
      const user = await response.json();
      this.setState({ user, loading: false });
    } catch (error) {
      this.setState({ loading: false });
    }
  }

  render() {
    if (this.state.loading) return <div>Loading...</div>;
    if (!this.state.user) return <div>User not found</div>;

    return (
      <div>
        <h2>{this.state.user.name}</h2>
        <p>{this.state.user.email}</p>
      </div>
    );
  }
}

export default UserProfile;
```

## Legacy Patterns and Migration

### Common Class Component Patterns

**Error Boundaries (Class-Only Feature):**

```javascript
import React from 'react';

class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

**Note:** Error boundaries can only be implemented as class components. Function components cannot catch errors in their children.

### Migration from Class to Function Components

**Class Component:**

```javascript
import React from 'react';

class Timer extends React.Component {
  state = { seconds: 0 };

  componentDidMount() {
    this.interval = setInterval(() => {
      this.setState(prevState => ({
        seconds: prevState.seconds + 1
      }));
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  render() {
    return <div>Seconds: {this.state.seconds}</div>;
  }
}
```

**Equivalent Function Component:**

```javascript
import React, { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prevSeconds => prevSeconds + 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);

  return <div>Seconds: {seconds}</div>;
}
```

### When to Use Class Components Today

**Still Appropriate For:**

- **Error Boundaries:** Only class components can catch JavaScript errors
- **Legacy Codebases:** When working with existing class-based code
- **Team Familiarity:** When the team is more comfortable with class syntax
- **Library Requirements:** Some third-party libraries expect class components

**Migration Strategy:**

1. **New Components:** Write all new components as function components
2. **Bug Fixes:** Convert class components to function components when fixing bugs
3. **Feature Additions:** Convert when adding new features to class components
4. **Gradual Migration:** Don't feel pressured to convert everything at once

**Modern React Recommendation:** Use function components with hooks for all new development. Class components remain supported but are no longer the recommended pattern.

## Class Component Lifecycle Methods (Legacy)

**⚠️ Legacy Pattern Warning:** Class component lifecycle methods are a pre-2019 React pattern. While still supported in 2025, they are not recommended for new development. Function components with hooks provide better performance, easier testing, and cleaner code.

### Understanding Component Phases

Class components follow a predictable lifecycle with three distinct phases. Understanding these phases is crucial for maintaining legacy React applications built before 2019.

**The Three Lifecycle Phases:**

1. **Mounting**: When a component is created and added to the DOM
2. **Updating**: When a component re-renders due to state or prop changes  
3. **Unmounting**: When a component is removed from the DOM

**Legacy vs Modern Comparison:**

| Legacy Class Pattern | Modern Function Component Pattern |
|---------------------|-----------------------------------|
| Lifecycle methods | useEffect hook |
| Multiple methods for different phases | Single hook with dependency array |
| `this.state` and `this.setState()` | `useState` hook |
| More boilerplate code | Concise and declarative |

### Legacy Lifecycle Functions

**Mounting Phase Methods:**

- `constructor()` - Initialize state and bind methods
- `render()` - Return JSX to be rendered
- `componentDidMount()` - Side effects after mount

**Updating Phase Methods:**

- `setState()` - Trigger re-render with state update
- `render()` - Re-render with new state/props
- `componentDidUpdate()` - Side effects after update

**Unmounting Phase Methods:**

- `componentWillUnmount()` - Cleanup before removal

**Modern Alternative:** All of these are replaced by `useEffect` hook in function components.

### Mounting Phase

The mounting phase in class components follows a predictable sequence of method calls. Understanding this flow is essential for maintaining legacy applications.

**Mounting Sequence:**

1. `constructor()` - Component instantiation and initial state
2. `render()` - First render to create virtual DOM
3. DOM insertion - React adds elements to actual DOM
4. `componentDidMount()` - Post-mount side effects

#### componentDidMount()

`componentDidMount()` is called immediately after a component mounts. This is where you perform side effects like API calls, DOM manipulation, or setting up subscriptions.

**Legacy Class Component Pattern:**

```javascript
import React, { Component } from 'react';

class UserProfile extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      loading: true,
      error: null
    };
  }

  async componentDidMount() {
    try {
      const response = await fetch(`/api/users/${this.props.userId}`);
      if (!response.ok) throw new Error('Failed to fetch');
      
      const user = await response.json();
      this.setState({ user, loading: false });
    } catch (error) {
      this.setState({ error: error.message, loading: false });
    }
  }

  render() {
    const { user, loading, error } = this.state;
    
    if (loading) return <div>Loading user...</div>;
    if (error) return <div>Error: {error}</div>;
    if (!user) return <div>No user found</div>;

    return (
      <div>
        <h2>{user.name}</h2>
        <p>Email: {user.email}</p>
        <p>Joined: {new Date(user.createdAt).toLocaleDateString()}</p>
      </div>
    );
  }
}

export default UserProfile;
```

#### Modern Equivalent: useEffect

The modern equivalent uses `useEffect` with an empty dependency array:

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) throw new Error('Failed to fetch');
        
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, []); // Empty dependency array = componentDidMount equivalent

  if (loading) return <div>Loading user...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Joined: {new Date(user.createdAt).toLocaleDateString()}</p>
    </div>
  );
}

export default UserProfile;
```

**Key Differences:**

- **Less boilerplate**: No constructor or class syntax needed
- **Better error handling**: Easier to isolate async logic
- **More readable**: Linear flow instead of scattered methods
- **Better performance**: Function components optimize better

### Updating Phase

The updating phase begins after mounting and continues throughout the component's lifecycle. This phase handles state changes, prop updates, and subsequent re-renders.

**Update Triggers:**

- `setState()` calls change component state
- Parent component passes new props
- `forceUpdate()` manually triggers update (rarely used)

#### setState()

`setState()` is the legacy method for updating component state and triggering re-renders.

**Legacy Class Component Pattern:**

```javascript
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      history: []
    };
  }

  // Object-based setState
  increment = () => {
    this.setState({ count: this.state.count + 1 });
  }

  // Function-based setState (recommended for state dependencies)
  incrementWithHistory = () => {
    this.setState((prevState) => {
      const newCount = prevState.count + 1;
      return {
        count: newCount,
        history: [...prevState.history, newCount]
      };
    });
  }

  render() {
    return (
      <div>
        <h2>Count: {this.state.count}</h2>
        <button onClick={this.increment}>Simple Increment</button>
        <button onClick={this.incrementWithHistory}>Increment with History</button>
        <p>History: {this.state.history.join(', ')}</p>
      </div>
    );
  }
}

export default Counter;
```

#### componentDidUpdate()

`componentDidUpdate()` is called after every update except the initial render. It receives previous props and state as parameters.

**Legacy Pattern:**

```javascript
import React, { Component } from 'react';

class SearchResults extends Component {
  state = {
    results: [],
    loading: false
  };

  async componentDidUpdate(prevProps) {
    // Only search if the query actually changed
    if (prevProps.query !== this.props.query) {
      this.setState({ loading: true });
      
      try {
        const response = await fetch(`/api/search?q=${this.props.query}`);
        const results = await response.json();
        this.setState({ results, loading: false });
      } catch (error) {
        console.error('Search failed:', error);
        this.setState({ loading: false });
      }
    }
  }

  render() {
    return (
      <div>
        <h3>Search Results for "{this.props.query}"</h3>
        {this.state.loading ? (
          <p>Searching...</p>
        ) : (
          <ul>
            {this.state.results.map(result => (
              <li key={result.id}>{result.title}</li>
            ))}
          </ul>
        )}
      </div>
    );
  }
}

export default SearchResults;
```

#### Modern Equivalent: useEffect with Dependencies

The modern approach uses `useEffect` with a dependency array:

```javascript
import React, { useState, useEffect } from 'react';

// Modern Counter
function Counter() {
  const [count, setCount] = useState(0);
  const [history, setHistory] = useState([]);

  const increment = () => setCount(prev => prev + 1);
  
  const incrementWithHistory = () => {
    setCount(prev => {
      const newCount = prev + 1;
      setHistory(prevHistory => [...prevHistory, newCount]);
      return newCount;
    });
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={increment}>Simple Increment</button>
      <button onClick={incrementWithHistory}>Increment with History</button>
      <p>History: {history.join(', ')}</p>
    </div>
  );
}

// Modern Search Results
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query) return;

    async function search() {
      setLoading(true);
      try {
        const response = await fetch(`/api/search?q=${query}`);
        const searchResults = await response.json();
        setResults(searchResults);
      } catch (error) {
        console.error('Search failed:', error);
      } finally {
        setLoading(false);
      }
    }

    search();
  }, [query]); // Re-run when query changes

  return (
    <div>
      <h3>Search Results for "{query}"</h3>
      {loading ? (
        <p>Searching...</p>
      ) : (
        <ul>
          {results.map(result => (
            <li key={result.id}>{result.title}</li>
          ))}
        </ul>
      )}
    </div>
  );
}

export { Counter, SearchResults };
```

### Unmounting Phase

The unmounting phase occurs when a component is removed from the DOM. This is your last chance to clean up resources, cancel subscriptions, and prevent memory leaks.

#### componentWillUnmount()

`componentWillUnmount()` is called immediately before a component is destroyed. This is essential for cleanup to prevent memory leaks.

**Legacy Class Component Pattern:**

```javascript
import React, { Component } from 'react';

class Timer extends Component {
  state = { seconds: 0 };

  componentDidMount() {
    // Set up interval when component mounts
    this.intervalId = setInterval(() => {
      this.setState(prevState => ({
        seconds: prevState.seconds + 1
      }));
    }, 1000);
  }

  componentWillUnmount() {
    // Critical: Clean up interval to prevent memory leak
    if (this.intervalId) {
      clearInterval(this.intervalId);
    }
  }

  render() {
    return (
      <div>
        <h3>Timer: {this.state.seconds} seconds</h3>
      </div>
    );
  }
}

class EventListener extends Component {
  handleResize = () => {
    console.log('Window resized:', window.innerWidth);
  }

  componentDidMount() {
    window.addEventListener('resize', this.handleResize);
  }

  componentWillUnmount() {
    // Clean up event listener
    window.removeEventListener('resize', this.handleResize);
  }

  render() {
    return <div>Listening for window resize...</div>;
  }
}

class WebSocketConnection extends Component {
  state = { messages: [] };

  componentDidMount() {
    this.ws = new WebSocket('ws://localhost:8080');
    
    this.ws.onmessage = (event) => {
      this.setState(prevState => ({
        messages: [...prevState.messages, event.data]
      }));
    };
  }

  componentWillUnmount() {
    // Close WebSocket connection
    if (this.ws) {
      this.ws.close();
    }
  }

  render() {
    return (
      <div>
        <h3>Messages: {this.state.messages.length}</h3>
      </div>
    );
  }
}

export { Timer, EventListener, WebSocketConnection };
```

#### Modern Equivalent: useEffect Cleanup

The modern approach uses `useEffect` cleanup functions:

```javascript
import React, { useState, useEffect } from 'react';

// Modern Timer with cleanup
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Return cleanup function (equivalent to componentWillUnmount)
    return () => clearInterval(intervalId);
  }, []); // Empty deps = mount/unmount only

  return (
    <div>
      <h3>Timer: {seconds} seconds</h3>
    </div>
  );
}

// Modern Event Listener with cleanup
function WindowSizeTracker() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }

    window.addEventListener('resize', handleResize);
    
    // Cleanup function
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return (
    <div>
      Window size: {windowSize.width} x {windowSize.height}
    </div>
  );
}

// Modern WebSocket with cleanup
function WebSocketConnection() {
  const [messages, setMessages] = useState([]);
  const [connectionStatus, setConnectionStatus] = useState('Connecting...');

  useEffect(() => {
    const ws = new WebSocket('ws://localhost:8080');
    
    ws.onopen = () => setConnectionStatus('Connected');
    ws.onclose = () => setConnectionStatus('Disconnected');
    ws.onerror = () => setConnectionStatus('Error');
    
    ws.onmessage = (event) => {
      setMessages(prev => [...prev, event.data]);
    };

    // Cleanup function
    return () => {
      ws.close();
    };
  }, []);

  return (
    <div>
      <p>Status: {connectionStatus}</p>
      <p>Messages: {messages.length}</p>
    </div>
  );
}

export { Timer, WindowSizeTracker, WebSocketConnection };
```

### Lifecycle Migration Patterns

**Common Migration Patterns:**

| Legacy Class Pattern | Modern Hook Pattern |
|---------------------|-------------------|
| `componentDidMount()` | `useEffect(() => {}, [])` |
| `componentDidUpdate()` | `useEffect(() => {}, [dependency])` |
| `componentWillUnmount()` | `useEffect(() => { return cleanup; }, [])` |
| `this.state` | `useState()` |
| `this.setState()` | State setter from `useState()` |

**Migration Benefits:**

- **Simpler Code**: Less boilerplate and easier to understand
- **Better Performance**: Function components optimize better
- **Easier Testing**: Hooks can be tested in isolation
- **Better Reusability**: Custom hooks can be shared between components
- **Smaller Bundle Size**: Less code overall

**When to Migrate:**

- **New Features**: Always use function components for new development
- **Bug Fixes**: Consider migrating when fixing bugs in class components
- **Performance Issues**: Function components often perform better
- **Team Onboarding**: New developers learn hooks faster than class components

This legacy lifecycle knowledge is essential for maintaining existing React applications, but all new development should use function components with hooks.
