---
title: "React Hooks and Side Effects"
order: 7
chapter_number: 7
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Master** the `useEffect` hook for handling side effects in function components.
- **Implement** data fetching, subscriptions, and cleanup operations using modern React patterns.
- **Create** custom hooks to encapsulate and reuse stateful logic across components.
- **Optimize** performance using dependency arrays and hook best practices.
- **Apply** modern React patterns that replace legacy class component lifecycle methods.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Introduction to React Hooks](#introduction-to-react-hooks)
- [The useEffect Hook](#the-useeffect-hook)
  - [Basic Side Effects](#basic-side-effects)
  - [Dependency Arrays](#dependency-arrays)
  - [Cleanup Functions](#cleanup-functions)
- [Data Fetching Patterns](#data-fetching-patterns)
  - [Async Data Loading](#async-data-loading)
  - [Error Handling](#error-handling)
  - [Loading States](#loading-states)
- [Advanced useEffect Patterns](#advanced-useeffect-patterns)
  - [Multiple Effects](#multiple-effects)
  - [Conditional Effects](#conditional-effects)
  - [Effect Dependencies](#effect-dependencies)
- [Custom Hooks](#custom-hooks)
  - [Creating Custom Hooks](#creating-custom-hooks)
  - [Advanced Custom Hook Example](#advanced-custom-hook-example)
  - [Memoizing Effect Dependencies](#memoizing-effect-dependencies)
- [Best Practices for useEffect](#best-practices-for-useeffect)
  - [1. Keep Effects Simple and Focused](#1-keep-effects-simple-and-focused)
  - [2. Always Include Dependencies](#2-always-include-dependencies)
  - [3. Optimize with useCallback and useMemo](#3-optimize-with-usecallback-and-usememo)
- [Common useEffect Patterns](#common-useeffect-patterns)
  - [1. Data Fetching on Mount](#1-data-fetching-on-mount)
  - [2. Subscription and Event Listeners](#2-subscription-and-event-listeners)
  - [3. Timer and Intervals](#3-timer-and-intervals)
  - [4. WebSocket Connections](#4-websocket-connections)
- [Advanced Hooks Patterns](#advanced-hooks-patterns)
  - [useReducer for Complex State](#usereducer-for-complex-state)
- [Summary](#summary)
  - [Benefits of Modern Hooks Approach](#benefits-of-modern-hooks-approach)
  - [useEffect vs. Class Lifecycle Methods](#useeffect-vs-class-lifecycle-methods)
  - [Best Practices Summary](#best-practices-summary)
  - [Common Patterns](#common-patterns)

## Introduction to React Hooks

React Hooks revolutionized React development by allowing function components to have state and lifecycle features that were previously only available in class components. Introduced in React 16.8 (2019), hooks have become the standard way to build React applications.

**Why Hooks Matter in 2025:**

- **Simpler Code**: Less boilerplate than class components
- **Better Performance**: Function components optimize better
- **Easier Testing**: Logic can be extracted and tested independently
- **Code Reuse**: Custom hooks allow sharing stateful logic
- **Developer Experience**: More intuitive and functional programming patterns

**Core React Hooks:**

- `useState` - Manage component state
- `useEffect` - Handle side effects and lifecycle events
- `useContext` - Access React context
- `useReducer` - Manage complex state logic
- `useMemo` - Memoize expensive calculations
- `useCallback` - Memoize functions
- `useRef` - Access DOM elements and persist values

## The useEffect Hook

`useEffect` is the most powerful and commonly used hook for handling side effects in React components. It replaces multiple class component lifecycle methods with a single, flexible API.

**What are Side Effects?**

Side effects are operations that affect something outside the component:

- API calls and data fetching
- Setting up subscriptions or event listeners
- Manually changing the DOM
- Timers and intervals
- Cleanup operations

### Basic Side Effects

**Simple Effect Example:**

```javascript
import React, { useState, useEffect } from 'react';

function DocumentTitle() {
  const [count, setCount] = useState(0);

  // Effect runs after every render
  useEffect(() => {
    document.title = `Count: ${count}`;
  });

  return (
    <div>
      <p>Current count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default DocumentTitle;
```

**Effect with Cleanup:**

```javascript
import React, { useState, useEffect } from 'react';

function WindowWidth() {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);

  useEffect(() => {
    function handleResize() {
      setWindowWidth(window.innerWidth);
    }

    // Set up the event listener
    window.addEventListener('resize', handleResize);

    // Cleanup function (runs when component unmounts or effect re-runs)
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  });

  return <div>Window width: {windowWidth}px</div>;
}

export default WindowWidth;
```

### Dependency Arrays

The dependency array is the second argument to `useEffect` and controls when the effect runs:

**No Dependency Array (runs after every render):**

```javascript
useEffect(() => {
  console.log('Runs after every render');
});
```

**Empty Dependency Array (runs once after mount):**

```javascript
useEffect(() => {
  console.log('Runs once after component mounts');
}, []); // Empty array
```

**With Dependencies (runs when dependencies change):**

```javascript
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    async function fetchUser() {
      const response = await fetch(`/api/users/${userId}`);
      const userData = await response.json();
      setUser(userData);
    }

    fetchUser();
  }, [userId]); // Runs when userId changes

  return user ? <div>{user.name}</div> : <div>Loading...</div>;
}
```

**Multiple Dependencies:**

```javascript
function SearchResults({ query, filters, sortBy }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    async function search() {
      const response = await fetch(`/api/search`, {
        method: 'POST',
        body: JSON.stringify({ query, filters, sortBy })
      });
      const data = await response.json();
      setResults(data.results);
    }

    if (query) {
      search();
    }
  }, [query, filters, sortBy]); // Runs when any dependency changes

  return (
    <ul>
      {results.map(result => (
        <li key={result.id}>{result.title}</li>
      ))}
    </ul>
  );
}
```

### Cleanup Functions

Cleanup functions prevent memory leaks and cancel ongoing operations:

**Timer Cleanup:**

```javascript
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const intervalId = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Cleanup function
    return () => {
      clearInterval(intervalId);
    };
  }, []); // Empty dependency array = mount/unmount only

  return <div>Timer: {seconds} seconds</div>;
}
```

**Subscription Cleanup:**

```javascript
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const subscription = chatAPI.subscribe(roomId, (message) => {
      setMessages(prev => [...prev, message]);
    });

    // Cleanup subscription
    return () => {
      subscription.unsubscribe();
    };
  }, [roomId]); // Re-subscribe when roomId changes

  return (
    <ul>
      {messages.map(msg => (
        <li key={msg.id}>{msg.text}</li>
      ))}
    </ul>
  );
}
```

## Data Fetching Patterns

Data fetching is one of the most common use cases for `useEffect`. Modern React applications handle async operations with hooks in clean, predictable ways.

### Async Data Loading

**Basic Data Fetching:**

```javascript
import React, { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUsers() {
      try {
        const response = await fetch('/api/users');
        if (!response.ok) throw new Error('Failed to fetch');
        
        const userData = await response.json();
        setUsers(userData);
      } catch (error) {
        console.error('Error fetching users:', error);
      } finally {
        setLoading(false);
      }
    }

    fetchUsers();
  }, []); // Fetch once on mount

  if (loading) return <div>Loading users...</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name} ({user.email})</li>
      ))}
    </ul>
  );
}

export default UserList;
```

**Data Fetching with Dependencies:**

```javascript
function ProductDetails({ productId }) {
  const [product, setProduct] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let cancelled = false; // Flag to prevent state updates if component unmounts

    async function fetchProduct() {
      setLoading(true);
      try {
        const response = await fetch(`/api/products/${productId}`);
        const productData = await response.json();
        
        // Only update state if effect hasn't been cancelled
        if (!cancelled) {
          setProduct(productData);
          setLoading(false);
        }
      } catch (error) {
        if (!cancelled) {
          console.error('Error:', error);
          setLoading(false);
        }
      }
    }

    fetchProduct();

    // Cleanup function to cancel the effect
    return () => {
      cancelled = true;
    };
  }, [productId]); // Re-fetch when productId changes

  if (loading) return <div>Loading product...</div>;
  if (!product) return <div>Product not found</div>;

  return (
    <div>
      <h2>{product.name}</h2>
      <p>{product.description}</p>
      <p>Price: ${product.price}</p>
    </div>
  );
}
```

### Error Handling

Proper error handling is crucial for production applications:

```javascript
function DataComponent({ endpoint }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let abortController = new AbortController();

    async function fetchData() {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch(endpoint, {
          signal: abortController.signal
        });

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const result = await response.json();
        setData(result);
      } catch (err) {
        // Don't set error if request was aborted
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchData();

    // Cleanup function to abort request
    return () => {
      abortController.abort();
    };
  }, [endpoint]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!data) return <div>No data available</div>;

  return <div>{JSON.stringify(data, null, 2)}</div>;
}
```

### Loading States

Advanced loading state management:

```javascript
function AdvancedUserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [retryCount, setRetryCount] = useState(0);

  const fetchUsers = async () => {
    setLoading(true);
    setError(null);

    try {
      // Simulate network delay
      await new Promise(resolve => setTimeout(resolve, 1000));
      
      const response = await fetch('/api/users');
      if (!response.ok) throw new Error('Failed to fetch users');
      
      const userData = await response.json();
      setUsers(userData);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchUsers();
  }, [retryCount]); // Re-fetch when retry count changes

  const handleRetry = () => {
    setRetryCount(prev => prev + 1);
  };

  return (
    <div>
      <h2>Users</h2>
      
      {loading && <div>Loading users...</div>}
      
      {error && (
        <div>
          <p>Error: {error}</p>
          <button onClick={handleRetry}>
            Retry ({retryCount > 0 ? `Attempt ${retryCount + 1}` : 'First try'})
          </button>
        </div>
      )}
      
      {!loading && !error && (
        <ul>
          {users.map(user => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

## Advanced useEffect Patterns

As applications grow more complex, you'll need advanced patterns for managing effects effectively.

### Multiple Effects

Separate concerns by using multiple `useEffect` hooks:

```javascript
function UserDashboard({ userId }) {
  const [user, setUser] = useState(null);
  const [notifications, setNotifications] = useState([]);
  const [analytics, setAnalytics] = useState(null);

  // Effect for user data
  useEffect(() => {
    async function fetchUser() {
      const response = await fetch(`/api/users/${userId}`);
      const userData = await response.json();
      setUser(userData);
    }
    
    fetchUser();
  }, [userId]);

  // Effect for notifications
  useEffect(() => {
    async function fetchNotifications() {
      const response = await fetch(`/api/users/${userId}/notifications`);
      const notificationData = await response.json();
      setNotifications(notificationData);
    }
    
    fetchNotifications();
  }, [userId]);

  // Effect for analytics tracking
  useEffect(() => {
    // Track page view
    analytics.track('dashboard_viewed', { userId });
    
    // Set up real-time analytics
    const unsubscribe = analytics.subscribe(`user_${userId}`, (data) => {
      setAnalytics(data);
    });

    return () => unsubscribe();
  }, [userId]);

  return (
    <div>
      {user && <h1>Welcome, {user.name}!</h1>}
      {notifications.length > 0 && (
        <div>You have {notifications.length} notifications</div>
      )}
    </div>
  );
}
```

### Conditional Effects

Skip effects based on conditions:

```javascript
function ConditionalEffect({ shouldFetch, endpoint }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    // Early return prevents effect from running
    if (!shouldFetch || !endpoint) return;

    async function fetchData() {
      const response = await fetch(endpoint);
      const result = await response.json();
      setData(result);
    }

    fetchData();
  }, [shouldFetch, endpoint]);

  return data ? <div>{JSON.stringify(data)}</div> : null;
}
```

### Effect Dependencies

Understanding when effects run is crucial for performance:

```javascript
function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [filters, setFilters] = useState({ category: 'all' });

  // ❌ Bad: Missing dependencies
  useEffect(() => {
    if (query) {
      fetch(`/api/search?q=${query}&category=${filters.category}`)
        .then(r => r.json())
        .then(setResults);
    }
  }, [query]); // Missing 'filters' dependency!

  // ✅ Good: All dependencies included
  useEffect(() => {
    if (query) {
      fetch(`/api/search?q=${query}&category=${filters.category}`)
        .then(r => r.json())
        .then(setResults);
    }
  }, [query, filters.category]); // Include all dependencies

  // ✅ Better: Use useCallback for complex dependencies
  const searchFunction = useCallback(async () => {
    if (query) {
      const response = await fetch(`/api/search?q=${query}&category=${filters.category}`);
      const data = await response.json();
      setResults(data);
    }
  }, [query, filters.category]);

  useEffect(() => {
    searchFunction();
  }, [searchFunction]);

  return (
    <div>
      <input 
        value={query} 
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      <select 
        value={filters.category}
        onChange={(e) => setFilters({ ...filters, category: e.target.value })}
      >
        <option value="all">All Categories</option>
        <option value="books">Books</option>
        <option value="movies">Movies</option>
      </select>
      
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Custom Hooks

Custom hooks are functions that use other hooks and allow you to extract and reuse stateful logic between components.

### Creating Custom Hooks

Custom hooks must start with "use" and can call other hooks:

```javascript
// useLocalStorage custom hook
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error('Error reading from localStorage:', error);
      return initialValue;
    }
  });

  const setValue = useCallback((value) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error('Error writing to localStorage:', error);
    }
  }, [key]);

  return [storedValue, setValue];
}

// Usage in components
function UserPreferences() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [language, setLanguage] = useLocalStorage('language', 'en');

  return (
    <div>
      <label>
        Theme:
        <select value={theme} onChange={(e) => setTheme(e.target.value)}>
          <option value="light">Light</option>
          <option value="dark">Dark</option>
        </select>
      </label>
      
      <label>
        Language:
        <select value={language} onChange={(e) => setLanguage(e.target.value)}>
          <option value="en">English</option>
          <option value="es">Spanish</option>
          <option value="fr">French</option>
        </select>
      </label>
    </div>
  );
}
```

### Advanced Custom Hook Example

```javascript
// useFetch custom hook with loading states and error handling
function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          ...options,
          signal: abortController.signal
        });
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const result = await response.json();
        setData(result);
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchData();

    return () => abortController.abort();
  }, [url, JSON.stringify(options)]);

  return { data, loading, error };
}

// Using the custom hook
function ProductList({ category }) {
  const { data: products, loading, error } = useFetch(`/api/products?category=${category}`);

  if (loading) return <div>Loading products...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h2>{category} Products</h2>
      <ul>
        {products?.map(product => (
          <li key={product.id}>
            {product.name} - ${product.price}
          </li>
        ))}
      </ul>
    </div>
  );
}

## Performance Optimization with useEffect

Proper use of `useEffect` is crucial for application performance. Here are key optimization techniques:

### Debouncing Effects

For effects that respond to user input, debouncing prevents excessive API calls:

```javascript
import React, { useState, useEffect, useMemo } from 'react';

function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  // Debounce the search query
  const debouncedQuery = useMemo(() => {
    const handler = setTimeout(() => query, 500);
    return () => clearTimeout(handler);
  }, [query]);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      return;
    }

    setLoading(true);
    
    const searchTimeout = setTimeout(async () => {
      try {
        const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`);
        const data = await response.json();
        setResults(data);
      } catch (error) {
        console.error('Search failed:', error);
        setResults([]);
      } finally {
        setLoading(false);
      }
    }, 500); // 500ms debounce

    return () => clearTimeout(searchTimeout);
  }, [query]);

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      
      {loading && <p>Searching...</p>}
      
      <ul>
        {results.map(result => (
          <li key={result.id}>{result.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Memoizing Effect Dependencies

Use `useCallback` and `useMemo` to prevent unnecessary effect re-runs:

```javascript
import React, { useState, useEffect, useCallback, useMemo } from 'react';

function DataProcessor({ apiEndpoint, filters }) {
  const [data, setData] = useState([]);
  const [processedData, setProcessedData] = useState([]);

  // Memoize the fetch function to prevent unnecessary re-creation
  const fetchData = useCallback(async () => {
    const response = await fetch(apiEndpoint);
    const result = await response.json();
    setData(result);
  }, [apiEndpoint]);

  // Memoize expensive calculations
  const filterConfig = useMemo(() => ({
    category: filters.category,
    priceRange: filters.priceRange,
    sortBy: filters.sortBy
  }), [filters.category, filters.priceRange, filters.sortBy]);

  // Effect for fetching data
  useEffect(() => {
    fetchData();
  }, [fetchData]);

  // Effect for processing data
  useEffect(() => {
    const processed = data
      .filter(item => !filterConfig.category || item.category === filterConfig.category)
      .filter(item => item.price >= filterConfig.priceRange.min && item.price <= filterConfig.priceRange.max)
      .sort((a, b) => {
        if (filterConfig.sortBy === 'price') return a.price - b.price;
        if (filterConfig.sortBy === 'name') return a.name.localeCompare(b.name);
        return 0;
      });
    
    setProcessedData(processed);
  }, [data, filterConfig]);

      <h2>Processed Data ({processedData.length} items)</h2>
      <ul>
        {processedData.map(item => (
          <li key={item.id}>
            {item.name} - ${item.price} ({item.category})
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Best Practices for useEffect

### 1. Keep Effects Simple and Focused

Each effect should have a single responsibility:

```javascript
// ❌ Bad: Multiple responsibilities in one effect
useEffect(() => {
  fetchUserData();
  subscribeToNotifications();
  trackPageView();
  setupKeyboardListeners();
}, []);

// ✅ Good: Separate effects for separate concerns
useEffect(() => {
  fetchUserData();
}, []);

useEffect(() => {
  const unsubscribe = subscribeToNotifications();
  return unsubscribe;
}, []);

useEffect(() => {
  trackPageView();
}, []);

useEffect(() => {
  const handleKeyPress = (e) => { /* ... */ };
  document.addEventListener('keydown', handleKeyPress);
  return () => document.removeEventListener('keydown', handleKeyPress);
}, []);
```

### 2. Always Include Dependencies

Use the ESLint rule `react-hooks/exhaustive-deps` to catch missing dependencies:

```javascript
// ❌ Bad: Missing dependencies
function SearchComponent({ query, filters }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    searchAPI(query, filters).then(setResults);
  }, [query]); // Missing 'filters' dependency!

  return <SearchResults results={results} />;
}

// ✅ Good: All dependencies included
function SearchComponent({ query, filters }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    searchAPI(query, filters).then(setResults);
  }, [query, filters]); // All dependencies included

  return <SearchResults results={results} />;
}
```

### 3. Optimize with useCallback and useMemo

Prevent unnecessary re-renders by memoizing functions and values:

```javascript
function ExpensiveComponent({ data, onItemClick }) {
  // Memoize expensive calculations
  const processedData = useMemo(() => {
    return data
      .filter(item => item.active)
      .sort((a, b) => a.priority - b.priority)
      .map(item => ({
        ...item,
        displayName: `${item.name} (${item.category})`
      }));
  }, [data]);

  // Memoize event handlers
  const handleItemClick = useCallback((itemId) => {
    onItemClick(itemId);
  }, [onItemClick]);

  return (
    <ul>
      {processedData.map(item => (
        <li key={item.id} onClick={() => handleItemClick(item.id)}>
          {item.displayName}
        </li>
      ))}
    </ul>
  );
}
```

## Common useEffect Patterns

### 1. Data Fetching on Mount

```javascript
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUser() {
      try {
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return <div>Welcome, {user?.name}!</div>;
}
```

### 2. Subscription and Event Listeners

```javascript
function WindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    function handleResize() {
      setWidth(window.innerWidth);
    }

    window.addEventListener('resize', handleResize);
    
    // Cleanup function removes event listener
    return () => window.removeEventListener('resize', handleResize);
  }, []); // Empty array means this effect runs once on mount

  return <div>Window width: {width}px</div>;
}
```

### 3. Timer and Intervals

```javascript
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Cleanup function clears interval
    return () => clearInterval(interval);
  }, []);

  return <div>Timer: {seconds} seconds</div>;
}
```

### 4. WebSocket Connections

```javascript
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  const [connectionStatus, setConnectionStatus] = useState('Connecting...');

  useEffect(() => {
    const ws = new WebSocket(`ws://localhost:8080/room/${roomId}`);
    
    ws.onopen = () => setConnectionStatus('Connected');
    ws.onclose = () => setConnectionStatus('Disconnected');
    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages(prev => [...prev, message]);
    };

    // Cleanup function closes WebSocket
    return () => {
      if (ws.readyState === WebSocket.OPEN) {
        ws.close();
      }
    };
  }, [roomId]);

  return (
    <div>
      <p>Status: {connectionStatus}</p>
      <ul>
        {messages.map(msg => (
          <li key={msg.id}>{msg.user}: {msg.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Advanced Hooks Patterns

### useReducer for Complex State

When state logic becomes complex, `useReducer` can be more appropriate than `useState`:

```javascript
import React, { useReducer, useEffect } from 'react';

const todoReducer = (state, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, {
          id: Date.now(),
          text: action.payload,
          completed: false
        }]
      };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    case 'SET_FILTER':
      return { ...state, filter: action.payload };
    default:
      return state;
  }
};

function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, {
    todos: [],
    filter: 'all'
  });

  const addTodo = (text) => {
    dispatch({ type: 'ADD_TODO', payload: text });
  };

  const toggleTodo = (id) => {
    dispatch({ type: 'TOGGLE_TODO', payload: id });
  };

    <div>
      <h1>Todo App</h1>
      {/* Implementation details... */}
    </div>
  );
}
```

## Summary

React hooks, particularly `useEffect`, provide a powerful and flexible way to handle side effects in modern React applications. Key takeaways:

### Benefits of Modern Hooks Approach

1. **Simpler API**: No need to learn multiple lifecycle methods
2. **Better Code Organization**: Group related logic together
3. **Easier Testing**: Function components are easier to test
4. **Better Performance**: React can optimize function components more effectively
5. **Reusable Logic**: Custom hooks enable easy sharing of stateful logic

### useEffect vs. Class Lifecycle Methods

| Class Lifecycle | useEffect Equivalent |
|----------------|---------------------|
| `componentDidMount()` | `useEffect(() => {}, [])` |
| `componentDidUpdate()` | `useEffect(() => {})` |
| `componentWillUnmount()` | `useEffect(() => { return cleanup; }, [])` |

### Best Practices Summary

1. **Use multiple effects** to separate concerns
2. **Always include dependencies** in the dependency array
3. **Clean up resources** to prevent memory leaks
4. **Optimize with useMemo and useCallback** when needed
5. **Create custom hooks** for reusable logic
6. **Keep effects simple** and focused on single responsibilities

### Common Patterns

- **Data fetching** on component mount
- **Subscribing and unsubscribing** from external data sources
- **Setting up and tearing down** timers and intervals
- **Managing event listeners** and WebSocket connections
- **Optimizing performance** with proper dependency management

Modern React development prioritizes hooks over class components. While class components remain supported, new projects should use function components with hooks for better developer experience, performance, and maintainability.

In the next chapter, we'll explore class components and lifecycle methods in detail - primarily for understanding legacy codebases and migration scenarios.

Modern React development prioritizes hooks over class components. While class components remain supported, new projects should use function components with hooks for better developer experience, performance, and maintainability.

In the next chapter, we'll explore class components and lifecycle methods in detail - primarily for understanding legacy codebases and migration scenarios.
