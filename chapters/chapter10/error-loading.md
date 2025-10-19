---
title: "Error Handling and Loading States"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/error-loading/
---

## Error Handling and Loading States

Proper error handling and loading states are essential for creating professional user experiences. This section covers comprehensive patterns for managing these states.

## Overview

Learn to implement:

- Sophisticated loading indicators
- Comprehensive error handling
- Retry mechanisms
- Skeleton screens
- Error boundaries

## Loading States

### Basic Loading Indicator

```tsx
import { useState, useEffect } from 'react'

function DataDisplay() {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetch('/api/data')
      .then(r => r.json())
      .then(setData)
      .finally(() => setLoading(false))
  }, [])

  if (loading) {
    return (
      <div className="loading-container">
        <div className="spinner" />
        <p>Loading...</p>
      </div>
    )
  }

  return <div>{/* render data */}</div>
}
```

### Skeleton Screens

Provide better perceived performance with skeleton loading:

```tsx
function SkeletonCard() {
  return (
    <div className="skeleton-card">
      <div className="skeleton skeleton-image" />
      <div className="skeleton skeleton-title" />
      <div className="skeleton skeleton-text" />
      <div className="skeleton skeleton-text" />
    </div>
  )
}

function ProductList() {
  const { data: products, loading } = useFetch<Product[]>('/api/products')

  if (loading) {
    return (
      <div className="product-grid">
        {Array.from({ length: 6 }).map((_, i) => (
          <SkeletonCard key={i} />
        ))}
      </div>
    )
  }

  return (
    <div className="product-grid">
      {products?.map(product => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  )
}
```

### Progressive Loading

Show partial data while loading more:

```tsx
import { useState, useEffect } from 'react'

interface Comment {
  id: number
  text: string
  author: string
}

function CommentThread({ postId }: { postId: number }) {
  const [comments, setComments] = useState<Comment[]>([])
  const [loadingMore, setLoadingMore] = useState(false)
  const [hasMore, setHasMore] = useState(true)
  const [page, setPage] = useState(1)

  useEffect(() => {
    const controller = new AbortController()

    async function loadComments() {
      setLoadingMore(true)
      try {
        const response = await fetch(
          `/api/posts/${postId}/comments?page=${page}`,
          { signal: controller.signal }
        )
        const data = await response.json()
        
        setComments(prev => [...prev, ...data.comments])
        setHasMore(data.hasMore)
      } catch (err) {
        console.error('Failed to load comments:', err)
      } finally {
        setLoadingMore(false)
      }
    }

    loadComments()

    return () => controller.abort()
  }, [postId, page])

  return (
    <div>
      {comments.map(comment => (
        <div key={comment.id}>
          <strong>{comment.author}:</strong> {comment.text}
        </div>
      ))}
      
      {loadingMore && <div>Loading more comments...</div>}
      
      {hasMore && !loadingMore && (
        <button onClick={() => setPage(p => p + 1)}>
          Load More
        </button>
      )}
    </div>
  )
}
```

## Error Handling

### Comprehensive Error States

```tsx
interface ErrorDisplayProps {
  error: Error
  onRetry?: () => void
}

function ErrorDisplay({ error, onRetry }: ErrorDisplayProps) {
  const isNetworkError = error.message.includes('Failed to fetch')
  const is404 = error.message.includes('404')
  const is500 = error.message.includes('500')

  return (
    <div className="error-container">
      <h3>
        {isNetworkError && '🌐 Network Error'}
        {is404 && '🔍 Not Found'}
        {is500 && '⚠️ Server Error'}
        {!isNetworkError && !is404 && !is500 && '❌ Error'}
      </h3>
      
      <p>{error.message}</p>
      
      {onRetry && (
        <button onClick={onRetry}>
          Try Again
        </button>
      )}
      
      {isNetworkError && (
        <p className="help-text">
          Please check your internet connection
        </p>
      )}
    </div>
  )
}

// Usage
function DataComponent() {
  const { data, loading, error, refetch } = useFetch('/api/data')

  if (loading) return <div>Loading...</div>
  if (error) return <ErrorDisplay error={error} onRetry={refetch} />
  
  return <div>{/* render data */}</div>
}
```

### Error Boundaries

Catch errors in component tree:

```tsx
import { Component, ReactNode, ErrorInfo } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
  error: Error | null
}

class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props)
    this.state = { hasError: false, error: null }
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Error boundary caught:', error, errorInfo)
    // Log to error reporting service
  }

  render() {
    if (this.state.hasError) {
      if (this.props.fallback) {
        return this.props.fallback
      }

      return (
        <div className="error-boundary">
          <h2>Something went wrong</h2>
          <details>
            <summary>Error details</summary>
            <pre>{this.state.error?.message}</pre>
          </details>
          <button onClick={() => this.setState({ hasError: false, error: null })}>
            Try again
          </button>
        </div>
      )
    }

    return this.props.children
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <DataFetchingComponent />
    </ErrorBoundary>
  )
}
```

### Retry Logic

Implement automatic and manual retry:

```tsx
import { useState, useEffect, useCallback } from 'react'

interface UseRetryFetchReturn<T> {
  data: T | null
  loading: boolean
  error: Error | null
  retryCount: number
  retry: () => void
}

function useRetryFetch<T>(
  url: string,
  maxRetries: number = 3
): UseRetryFetchReturn<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)
  const [retryCount, setRetryCount] = useState(0)
  const [shouldRetry, setShouldRetry] = useState(false)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchWithRetry() {
      let attempt = 0

      while (attempt <= maxRetries) {
        try {
          setLoading(true)
          setError(null)

          const response = await fetch(url, { signal: controller.signal })
          
          if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`)
          }

          const json = await response.json()
          setData(json)
          setRetryCount(attempt)
          setLoading(false)
          return
        } catch (err) {
          if (err instanceof Error && err.name === 'AbortError') {
            return
          }

          attempt++
          setRetryCount(attempt)

          if (attempt > maxRetries) {
            setError(err instanceof Error ? err : new Error('Fetch failed'))
            setLoading(false)
            return
          }

          // Exponential backoff
          await new Promise(resolve => 
            setTimeout(resolve, Math.min(1000 * Math.pow(2, attempt), 10000))
          )
        }
      }
    }

    fetchWithRetry()

    return () => controller.abort()
  }, [url, maxRetries, shouldRetry])

  const retry = useCallback(() => {
    setShouldRetry(prev => !prev)
  }, [])

  return { data, loading, error, retryCount, retry }
}

// Usage
function ResilientDataFetch() {
  const { data, loading, error, retryCount, retry } = useRetryFetch('/api/data')

  if (loading) {
    return (
      <div>
        Loading...
        {retryCount > 0 && <p>Retry attempt {retryCount}</p>}
      </div>
    )
  }

  if (error) {
    return (
      <div>
        <p>Failed after {retryCount} attempts</p>
        <p>Error: {error.message}</p>
        <button onClick={retry}>Retry Now</button>
      </div>
    )
  }

  return <div>{/* render data */}</div>
}
```

## Toast Notifications

Show non-blocking error messages:

```tsx
import { createContext, useContext, useState, useCallback, ReactNode } from 'react'

interface Toast {
  id: number
  message: string
  type: 'success' | 'error' | 'info'
}

interface ToastContextType {
  addToast: (message: string, type: Toast['type']) => void
}

const ToastContext = createContext<ToastContextType | undefined>(undefined)

export function ToastProvider({ children }: { children: ReactNode }) {
  const [toasts, setToasts] = useState<Toast[]>([])

  const addToast = useCallback((message: string, type: Toast['type']) => {
    const id = Date.now()
    setToasts(prev => [...prev, { id, message, type }])

    setTimeout(() => {
      setToasts(prev => prev.filter(t => t.id !== id))
    }, 5000)
  }, [])

  return (
    <ToastContext.Provider value={% raw %}{{ addToast }}{% endraw %}>
      {children}
      <div className="toast-container">
        {toasts.map(toast => (
          <div key={toast.id} className={`toast toast-${toast.type}`}>
            {toast.message}
          </div>
        ))}
      </div>
    </ToastContext.Provider>
  )
}

export function useToast() {
  const context = useContext(ToastContext)
  if (!context) {
    throw new Error('useToast must be used within ToastProvider')
  }
  return context
}

// Usage
function DataComponent() {
  const { addToast } = useToast()
  const { data, loading, error } = useFetch('/api/data')

  useEffect(() => {
    if (error) {
      addToast(error.message, 'error')
    }
  }, [error, addToast])

  if (loading) return <div>Loading...</div>
  return <div>{/* render data */}</div>
}
```

## Empty States

Handle cases when data is successfully loaded but empty:

```tsx
interface EmptyStateProps {
  title: string
  message: string
  action?: {
    label: string
    onClick: () => void
  }
}

function EmptyState({ title, message, action }: EmptyStateProps) {
  return (
    <div className="empty-state">
      <h3>{title}</h3>
      <p>{message}</p>
      {action && (
        <button onClick={action.onClick}>
          {action.label}
        </button>
      )}
    </div>
  )
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch<User[]>('/api/users')

  if (loading) return <div>Loading users...</div>
  if (error) return <ErrorDisplay error={error} />
  
  if (!users || users.length === 0) {
    return (
      <EmptyState
        title="No users found"
        message="There are no users in the system yet."
        action={% raw %}{{
          label: 'Add User',
          onClick: () => console.log('Navigate to add user')
        }}{% endraw %}
      />
    )
  }

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

## Best Practices

1. **Always show loading states** - Never leave users wondering
2. **Provide helpful error messages** - Explain what went wrong and how to fix it
3. **Implement retry mechanisms** - Allow users to recover from transient errors
4. **Use skeleton screens** - Better perceived performance than spinners
5. **Handle empty states** - Don't just show nothing when data is empty
6. **Log errors appropriately** - Send to monitoring services
7. **Use error boundaries** - Catch unexpected errors
8. **Test error scenarios** - Network failures, timeouts, malformed responses

## Summary

Professional error handling and loading states significantly improve user experience. By implementing comprehensive patterns, you create applications that feel polished and reliable.

**Next Steps:**

- Explore [Advanced Data Fetching Patterns](./advanced-patterns/) for complex scenarios
- Learn about [Modern Data Fetching Libraries](./libraries/) for built-in solutions

**Key Takeaways:**

1. Loading states should be informative and visually appropriate
2. Skeleton screens improve perceived performance
3. Error messages should be helpful and actionable
4. Retry logic handles transient failures automatically
5. Error boundaries catch unexpected errors
6. Empty states guide users when no data exists
