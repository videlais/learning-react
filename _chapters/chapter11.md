---
title: "Modern React Context and State Management"
order: 11
chapter_number: 11
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Understand** React Context as the modern solution for prop drilling and global state management
- **Implement** context providers and consumers using modern hooks-based patterns
- **Optimize** context usage to prevent unnecessary re-renders and improve performance
- **Design** scalable context architectures for complex applications
- **Integrate** context with modern state management patterns and TypeScript

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Understanding Prop Drilling and Context Solutions](#understanding-prop-drilling-and-context-solutions)
  - [The Prop Drilling Problem](#the-prop-drilling-problem)
  - [Modern Context Solution](#modern-context-solution)
- [Modern Context with useContext Hook](#modern-context-with-usecontext-hook)
  - [Creating and Using Context](#creating-and-using-context)
    - [Step 1: Create the Context](#step-1-create-the-context)
    - [Step 2: Create a Provider Component](#step-2-create-a-provider-component)
    - [Step 3: Use the Context](#step-3-use-the-context)
    - [Step 4: Wrap Your App](#step-4-wrap-your-app)
  - [Legacy Patterns (For Reference Only)](#legacy-patterns-for-reference-only)
- [Building Practical Context Providers](#building-practical-context-providers)
  - [Stateful Context with Reducers](#stateful-context-with-reducers)
  - [Context with Local Storage Persistence](#context-with-local-storage-persistence)
  - [Context with API Integration](#context-with-api-integration)
- [Performance Optimization with Context](#performance-optimization-with-context)
  - [Context Performance Patterns](#context-performance-patterns)
    - [Optimizing Context Value Creation](#optimizing-context-value-creation)
    - [Splitting Contexts for Performance](#splitting-contexts-for-performance)
    - [Context with React.memo](#context-with-reactmemo)
  - [Context Composition Patterns](#context-composition-patterns)
    - [Multiple Provider Pattern](#multiple-provider-pattern)
    - [Context Factory Pattern](#context-factory-pattern)
- [TypeScript Integration](#typescript-integration)
  - [Typed Context Pattern](#typed-context-pattern)
  - [Generic Context Pattern](#generic-context-pattern)
- [Advanced Context Patterns](#advanced-context-patterns)
  - [Context with Middleware Pattern](#context-with-middleware-pattern)
  - [Context with Selectors](#context-with-selectors)
- [Testing Context Components](#testing-context-components)
  - [Testing Context Providers](#testing-context-providers)
  - [Mock Context for Testing](#mock-context-for-testing)
- [Best Practices and Common Pitfalls](#best-practices-and-common-pitfalls)
  - [Context Best Practices](#context-best-practices)
  - [Common Pitfalls to Avoid](#common-pitfalls-to-avoid)
  - [Migration from Legacy Patterns](#migration-from-legacy-patterns)

## Understanding Prop Drilling and Context Solutions

**Prop drilling** occurs when you pass data through multiple component layers, even when intermediate components don't need the data. This creates maintenance issues and tight coupling between components.

### The Prop Drilling Problem

**❌ Problematic Pattern:**

```javascript
function App() {
  const user = { name: 'Alice', role: 'admin' };
  const theme = { primaryColor: '#007bff', mode: 'dark' };
  
  return <Dashboard user={user} theme={theme} />;
}

function Dashboard({ user, theme }) {
  // Dashboard doesn't use user or theme directly
  return (
    <div>
      <Header user={user} theme={theme} />
      <MainContent user={user} theme={theme} />
      <Sidebar user={user} theme={theme} />
    </div>
  );
}

function Header({ user, theme }) {
  // Header doesn't use user or theme directly  
  return (
    <div>
      <Navigation user={user} theme={theme} />
      <UserMenu user={user} theme={theme} />
    </div>
  );
}

function Navigation({ user, theme }) {
  // Finally uses the props!
  return (
    <nav style={{ backgroundColor: theme.primaryColor }}>
      <span>Welcome, {user.name}</span>
    </nav>
  );
}
```

**Problems with prop drilling:**

- Intermediate components become tightly coupled to data they don't use
- Refactoring becomes difficult when component structure changes
- Props have to be passed through many layers
- Components become less reusable

### Modern Context Solution

**✅ Context-Based Solution:**

```javascript
import React, { createContext, useContext } from 'react';

// Create contexts
const UserContext = createContext(null);
const ThemeContext = createContext(null);

function App() {
  const user = { name: 'Alice', role: 'admin' };
  const theme = { primaryColor: '#007bff', mode: 'dark' };
  
  return (
    <UserContext.Provider value={user}>
      <ThemeContext.Provider value={theme}>
        <Dashboard />
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}

function Dashboard() {
  // Clean component - no unnecessary props
  return (
    <div>
      <Header />
      <MainContent />
      <Sidebar />
    </div>
  );
}

function Header() {
  // Clean component - no unnecessary props
  return (
    <div>
      <Navigation />
      <UserMenu />
    </div>
  );
}

function Navigation() {
  // Direct access to needed data
  const user = useContext(UserContext);
  const theme = useContext(ThemeContext);
  
  return (
    <nav style={{ backgroundColor: theme.primaryColor }}>
      <span>Welcome, {user.name}</span>
    </nav>
  );
}
```

**Benefits of React Context:**

- Clean separation of concerns
- Direct communication between distant components
- Eliminates unnecessary prop drilling
- Provides a centralized way to manage shared state

## Modern Context with useContext Hook

**2025 Best Practice:** Use the `useContext` hook for consuming context in function components. Class components and Consumer render props are legacy patterns.

### Creating and Using Context

#### Step 1: Create the Context

```javascript
// contexts/ThemeContext.js
import { createContext } from 'react';

export const ThemeContext = createContext({
  theme: 'light',
  toggleTheme: () => {},
});

// Export a custom hook for easier usage
export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};
```

#### Step 2: Create a Provider Component

```javascript
// contexts/ThemeProvider.js
import React, { useState, useCallback } from 'react';
import { ThemeContext } from './ThemeContext';

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = useCallback(() => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  }, []);
  
  const value = {
    theme,
    toggleTheme,
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}
```

#### Step 3: Use the Context

```javascript
// components/Header.js
import React from 'react';
import { useTheme } from '../contexts/ThemeContext';

function Header() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <header style={{
      backgroundColor: theme === 'light' ? '#ffffff' : '#1a1a1a',
      color: theme === 'light' ? '#000000' : '#ffffff',
      padding: '1rem'
    }}>
      <h1>My Application</h1>
      <button onClick={toggleTheme}>
        Switch to {theme === 'light' ? 'dark' : 'light'} mode
      </button>
    </header>
  );
}

export default Header;
```

#### Step 4: Wrap Your App

```javascript
// App.js
import React from 'react';
import { ThemeProvider } from './contexts/ThemeProvider';
import Header from './components/Header';
import MainContent from './components/MainContent';

function App() {
  return (
    <ThemeProvider>
      <div className="app">
        <Header />
        <MainContent />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

### Legacy Patterns (For Reference Only)

**⚠️ Not Recommended for New Code:** These patterns are included for understanding existing codebases.

**Class Component with contextType (Legacy):**

```javascript
import React, { Component } from 'react';
import { ThemeContext } from '../contexts/ThemeContext';

class LegacyHeader extends Component {
  static contextType = ThemeContext;
  
  render() {
    const { theme, toggleTheme } = this.context;
    return (
      <header>
        <h1>Theme: {theme}</h1>
        <button onClick={toggleTheme}>Toggle</button>
      </header>
    );
  }
}
```

**Consumer Render Props (Legacy):**

```javascript
import React from 'react';
import { ThemeContext } from '../contexts/ThemeContext';

function LegacyHeader() {
  return (
    <ThemeContext.Consumer>
      {({ theme, toggleTheme }) => (
        <header>
          <h1>Theme: {theme}</h1>
          <button onClick={toggleTheme}>Toggle</button>
        </header>
      )}
    </ThemeContext.Consumer>
  );
}
```

## Building Practical Context Providers

### Stateful Context with Reducers

For complex state management, combine context with `useReducer`:

```javascript
// contexts/AppStateContext.js
import React, { createContext, useContext, useReducer } from 'react';

const AppStateContext = createContext(null);

// Action types
const ACTIONS = {
  SET_USER: 'SET_USER',
  SET_LOADING: 'SET_LOADING',
  SET_ERROR: 'SET_ERROR',
  CLEAR_ERROR: 'CLEAR_ERROR',
};

// Reducer function
function appStateReducer(state, action) {
  switch (action.type) {
    case ACTIONS.SET_USER:
      return {
        ...state,
        user: action.payload,
        loading: false,
        error: null,
      };
    case ACTIONS.SET_LOADING:
      return {
        ...state,
        loading: action.payload,
      };
    case ACTIONS.SET_ERROR:
      return {
        ...state,
        error: action.payload,
        loading: false,
      };
    case ACTIONS.CLEAR_ERROR:
      return {
        ...state,
        error: null,
      };
    default:
      return state;
  }
}

// Initial state
const initialState = {
  user: null,
  loading: false,
  error: null,
};

// Provider component
export function AppStateProvider({ children }) {
  const [state, dispatch] = useReducer(appStateReducer, initialState);
  
  // Action creators
  const setUser = (user) => {
    dispatch({ type: ACTIONS.SET_USER, payload: user });
  };
  
  const setLoading = (loading) => {
    dispatch({ type: ACTIONS.SET_LOADING, payload: loading });
  };
  
  const setError = (error) => {
    dispatch({ type: ACTIONS.SET_ERROR, payload: error });
  };
  
  const clearError = () => {
    dispatch({ type: ACTIONS.CLEAR_ERROR });
  };
  
  const value = {
    ...state,
    setUser,
    setLoading,
    setError,
    clearError,
  };
  
  return (
    <AppStateContext.Provider value={value}>
      {children}
    </AppStateContext.Provider>
  );
}

// Custom hook
export const useAppState = () => {
  const context = useContext(AppStateContext);
  if (!context) {
    throw new Error('useAppState must be used within AppStateProvider');
  }
  return context;
};
```

### Context with Local Storage Persistence

```javascript
// contexts/PreferencesContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';

const PreferencesContext = createContext(null);

export function PreferencesProvider({ children }) {
  const [preferences, setPreferences] = useState(() => {
    // Load from localStorage on initialization
    try {
      const saved = localStorage.getItem('userPreferences');
      return saved ? JSON.parse(saved) : {
        theme: 'light',
        language: 'en',
        notifications: true,
      };
    } catch {
      return {
        theme: 'light',
        language: 'en',
        notifications: true,
      };
    }
  });
  
  // Save to localStorage whenever preferences change
  useEffect(() => {
    try {
      localStorage.setItem('userPreferences', JSON.stringify(preferences));
    } catch (error) {
      console.error('Failed to save preferences:', error);
    }
  }, [preferences]);
  
  const updatePreference = (key, value) => {
    setPreferences(prev => ({
      ...prev,
      [key]: value,
    }));
  };
  
  const resetPreferences = () => {
    setPreferences({
      theme: 'light',
      language: 'en',
      notifications: true,
    });
  };
  
  const value = {
    preferences,
    updatePreference,
    resetPreferences,
  };
  
  return (
    <PreferencesContext.Provider value={value}>
      {children}
    </PreferencesContext.Provider>
  );
}

export const usePreferences = () => {
  const context = useContext(PreferencesContext);
  if (!context) {
    throw new Error('usePreferences must be used within PreferencesProvider');
  }
  return context;
};
```

### Context with API Integration

```javascript
// contexts/AuthContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  // Check authentication status on mount
  useEffect(() => {
    checkAuthStatus();
  }, []);
  
  const checkAuthStatus = async () => {
    try {
      setLoading(true);
      const token = localStorage.getItem('authToken');
      if (!token) {
        setLoading(false);
        return;
      }
      
      const response = await fetch('/api/auth/me', {
        headers: {
          'Authorization': `Bearer ${token}`,
        },
      });
      
      if (response.ok) {
        const userData = await response.json();
        setUser(userData);
      } else {
        localStorage.removeItem('authToken');
      }
    } catch (err) {
      setError('Failed to check authentication status');
    } finally {
      setLoading(false);
    }
  };
  
  const login = async (email, password) => {
    try {
      setLoading(true);
      setError(null);
      
      const response = await fetch('/api/auth/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ email, password }),
      });
      
      if (response.ok) {
        const { user: userData, token } = await response.json();
        localStorage.setItem('authToken', token);
        setUser(userData);
        return { success: true };
      } else {
        const errorData = await response.json();
        setError(errorData.message || 'Login failed');
        return { success: false, error: errorData.message };
      }
    } catch (err) {
      const errorMessage = 'Network error occurred';
      setError(errorMessage);
      return { success: false, error: errorMessage };
    } finally {
      setLoading(false);
    }
  };
  
  const logout = () => {
    localStorage.removeItem('authToken');
    setUser(null);
    setError(null);
  };
  
  const value = {
    user,
    loading,
    error,
    login,
    logout,
    isAuthenticated: !!user,
  };
  
  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};
```

## Performance Optimization with Context

### Context Performance Patterns

**Problem:** Context re-renders all consuming components when the value changes, even if they only use part of the context.

**Solution:** Split contexts by concern and optimize value creation.

#### Optimizing Context Value Creation

```javascript
// ❌ Bad: Creates new object on every render
function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  
  return (
    <AppContext.Provider value={{
      user,
      setUser,
      theme,
      setTheme,
    }}>
      {children}
    </AppContext.Provider>
  );
}

// ✅ Good: Memoize the context value
function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState('light');
  
  const value = useMemo(() => ({
    user,
    setUser,
    theme,
    setTheme,
  }), [user, theme]);
  
  return (
    <AppContext.Provider value={value}>
      {children}
    </AppContext.Provider>
  );
}
```

#### Splitting Contexts for Performance

```javascript
// Split related but independent concerns
// contexts/UserContext.js
export const UserContext = createContext(null);

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  
  const value = useMemo(() => ({
    user,
    setUser,
    isLoading,
    setIsLoading,
  }), [user, isLoading]);
  
  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}

// contexts/ThemeContext.js  
export const ThemeContext = createContext(null);

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = useCallback(() => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  }, []);
  
  const value = useMemo(() => ({
    theme,
    toggleTheme,
  }), [theme, toggleTheme]);
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}
```

#### Context with React.memo

```javascript
// Prevent unnecessary re-renders with React.memo
const ExpensiveComponent = React.memo(function ExpensiveComponent() {
  const { theme } = useTheme();
  
  // This only re-renders when theme changes
  return (
    <div className={`expensive-component ${theme}`}>
      <ComplexCalculation />
    </div>
  );
});
```

### Context Composition Patterns

#### Multiple Provider Pattern

```javascript
// Compose multiple providers cleanly
function AppProviders({ children }) {
  return (
    <ErrorBoundary>
      <AuthProvider>
        <ThemeProvider>
          <UserPreferencesProvider>
            <NotificationProvider>
              {children}
            </NotificationProvider>
          </UserPreferencesProvider>
        </ThemeProvider>
      </AuthProvider>
    </ErrorBoundary>
  );
}

// Usage in App
function App() {
  return (
    <AppProviders>
      <Router>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/profile" element={<ProfilePage />} />
        </Routes>
      </Router>
    </AppProviders>
  );
}
```

#### Context Factory Pattern

```javascript
// contexts/createResourceContext.js
export function createResourceContext(resourceName) {
  const Context = createContext(null);
  
  function Provider({ children }) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    const fetchData = useCallback(async () => {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(`/api/${resourceName}`);
        if (!response.ok) throw new Error('Failed to fetch');
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }, []);
    
    const value = useMemo(() => ({
      data,
      loading,
      error,
      fetchData,
      setData,
    }), [data, loading, error, fetchData]);
    
    return (
      <Context.Provider value={value}>
        {children}
      </Context.Provider>
    );
  }
  
  function useResource() {
    const context = useContext(Context);
    if (!context) {
      throw new Error(`useResource must be used within ${resourceName} Provider`);
    }
    return context;
  }
  
  return { Provider, useResource };
}

// Usage
const { Provider: PostsProvider, useResource: usePosts } = createResourceContext('posts');
const { Provider: UsersProvider, useResource: useUsers } = createResourceContext('users');
```

## TypeScript Integration

### Typed Context Pattern

```typescript
// contexts/AuthContext.tsx
import React, { createContext, useContext, useState, useEffect, ReactNode } from 'react';

interface User {
  id: string;
  email: string;
  name: string;
  role: 'admin' | 'user';
}

interface AuthContextType {
  user: User | null;
  loading: boolean;
  error: string | null;
  login: (email: string, password: string) => Promise<{ success: boolean; error?: string }>;
  logout: () => void;
  isAuthenticated: boolean;
}

const AuthContext = createContext<AuthContextType | null>(null);

interface AuthProviderProps {
  children: ReactNode;
}

export function AuthProvider({ children }: AuthProviderProps) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  
  // Implementation...
  const login = async (email: string, password: string) => {
    // Login logic
    return { success: true };
  };
  
  const logout = () => {
    setUser(null);
    localStorage.removeItem('authToken');
  };
  
  const value: AuthContextType = {
    user,
    loading,
    error,
    login,
    logout,
    isAuthenticated: !!user,
  };
  
  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = (): AuthContextType => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};
```

### Generic Context Pattern

```typescript
// contexts/createTypedContext.ts
import { createContext, useContext } from 'react';

export function createTypedContext<T>(name: string) {
  const Context = createContext<T | null>(null);
  
  function useTypedContext(): T {
    const context = useContext(Context);
    if (!context) {
      throw new Error(`use${name} must be used within ${name}Provider`);
    }
    return context;
  }
  
  return [Context, useTypedContext] as const;
}

// Usage
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const [ThemeContext, useTheme] = createTypedContext<ThemeContextType>('Theme');
```

## Advanced Context Patterns

### Context with Middleware Pattern

```javascript
// contexts/StateContext.js
import React, { createContext, useContext, useReducer } from 'react';

// Middleware function
const loggingMiddleware = (action, state) => {
  console.log('Action:', action.type, action.payload);
  console.log('Previous state:', state);
};

const analyticsMiddleware = (action, state) => {
  // Track user actions
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('event', action.type, {
      custom_parameter: action.payload,
    });
  }
};

function StateProvider({ children, middleware = [] }) {
  const [state, dispatch] = useReducer((state, action) => {
    // Run middleware
    middleware.forEach(mw => mw(action, state));
    
    // Execute reducer
    return stateReducer(state, action);
  }, initialState);
  
  return (
    <StateContext.Provider value={{ state, dispatch }}>
      {children}
    </StateContext.Provider>
  );
}

// Usage with middleware
function App() {
  return (
    <StateProvider middleware={[loggingMiddleware, analyticsMiddleware]}>
      <AppContent />
    </StateProvider>
  );
}
```

### Context with Selectors

```javascript
// contexts/AppStateContext.js
import React, { createContext, useContext, useMemo } from 'react';

const AppStateContext = createContext(null);

export function AppStateProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, initialState);
  
  // Selector functions
  const selectors = useMemo(() => ({
    getUser: () => state.user,
    getTheme: () => state.theme,
    getNotifications: () => state.notifications,
    getUnreadCount: () => state.notifications.filter(n => !n.read).length,
    getUserPreferences: () => state.user?.preferences || {},
  }), [state]);
  
  const value = {
    state,
    dispatch,
    ...selectors,
  };
  
  return (
    <AppStateContext.Provider value={value}>
      {children}
    </AppStateContext.Provider>
  );
}

// Custom hooks for specific data
export const useUser = () => {
  const { getUser } = useContext(AppStateContext);
  return getUser();
};

export const useNotifications = () => {
  const { getNotifications, getUnreadCount } = useContext(AppStateContext);
  return {
    notifications: getNotifications(),
    unreadCount: getUnreadCount(),
  };
};
```

## Testing Context Components

### Testing Context Providers

```javascript
// __tests__/ThemeContext.test.js
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { ThemeProvider, useTheme } from '../contexts/ThemeContext';

// Test component that uses the context
function TestComponent() {
  const { theme, toggleTheme } = useTheme();
  return (
    <div>
      <div data-testid="theme">{theme}</div>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

describe('ThemeContext', () => {
  it('provides default theme value', () => {
    render(
      <ThemeProvider>
        <TestComponent />
      </ThemeProvider>
    );
    
    expect(screen.getByTestId('theme')).toHaveTextContent('light');
  });
  
  it('toggles theme when button is clicked', () => {
    render(
      <ThemeProvider>
        <TestComponent />
      </ThemeProvider>
    );
    
    const toggleButton = screen.getByText('Toggle Theme');
    fireEvent.click(toggleButton);
    
    expect(screen.getByTestId('theme')).toHaveTextContent('dark');
  });
  
  it('throws error when used outside provider', () => {
    // Suppress console.error for this test
    const originalError = console.error;
    console.error = jest.fn();
    
    expect(() => {
      render(<TestComponent />);
    }).toThrow('useTheme must be used within a ThemeProvider');
    
    console.error = originalError;
  });
});
```

### Mock Context for Testing

```javascript
// __tests__/utils/mockContext.js
export function createMockContextProvider(Context, mockValue) {
  return function MockProvider({ children }) {
    return (
      <Context.Provider value={mockValue}>
        {children}
      </Context.Provider>
    );
  };
}

// Usage in tests
import { AuthContext } from '../contexts/AuthContext';
import { createMockContextProvider } from './utils/mockContext';

const MockAuthProvider = createMockContextProvider(AuthContext, {
  user: { id: '1', email: 'test@example.com', name: 'Test User' },
  loading: false,
  error: null,
  login: jest.fn(),
  logout: jest.fn(),
  isAuthenticated: true,
});

it('renders user profile when authenticated', () => {
  render(
    <MockAuthProvider>
      <UserProfile />
    </MockAuthProvider>
  );
  
  expect(screen.getByText('Test User')).toBeInTheDocument();
});
```

## Best Practices and Common Pitfalls

### Context Best Practices

1. **Keep Context Values Stable**: Use `useMemo` and `useCallback` to prevent unnecessary re-renders
2. **Split Large Contexts**: Separate concerns into different contexts
3. **Provide Default Values**: Always provide meaningful default values
4. **Error Boundaries**: Wrap context providers with error boundaries
5. **Custom Hooks**: Create custom hooks for easier consumption

### Common Pitfalls to Avoid

1. **Creating New Objects in Render**: Causes all consumers to re-render
2. **Putting Everything in One Context**: Creates performance issues
3. **Not Memoizing Context Values**: Leads to excessive re-renders  
4. **Using Context for Everything**: Not all state needs to be global
5. **Forgetting Error Handling**: Always handle cases where context is used outside provider

### Migration from Legacy Patterns

When migrating from class components and Consumer patterns:

1. Replace `static contextType` with `useContext` hook
2. Replace `<Context.Consumer>` render props with `useContext`
3. Convert class components to function components where appropriate
4. Add TypeScript types for better type safety
5. Implement performance optimizations with `useMemo`

This comprehensive guide covers modern React Context patterns for 2025, focusing on performance, TypeScript integration, and best practices while maintaining understanding of legacy patterns for code maintenance.
