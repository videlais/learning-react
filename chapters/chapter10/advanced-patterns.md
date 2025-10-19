---
title: "Advanced Data Fetching Patterns"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/advanced-patterns/
---

## Advanced Data Fetching Patterns

Advanced patterns handle complex scenarios like parallel fetching, infinite scrolling, and real-time updates. These techniques are essential for production applications.

## Overview

Master these advanced patterns:

- Parallel and dependent fetching
- Infinite scroll and pagination
- Real-time data with WebSockets
- Optimistic updates
- Data prefetching

## Parallel Data Fetching

Fetch multiple resources simultaneously:

```tsx
import { useState, useEffect } from 'react'

interface User {
  id: number
  name: string
}

interface Post {
  id: number
  title: string
}

function UserDashboard({ userId }: { userId: number }) {
  const [data, setData] = useState<{
    user: User | null
    posts: Post[] | null
    comments: any[] | null
  }>({
    user: null,
    posts: null,
    comments: null
  })
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchAll() {
      try {
        setLoading(true)
        
        // Fetch all resources in parallel
        const [userRes, postsRes, commentsRes] = await Promise.all([
          fetch(`/api/users/${userId}`, { signal: controller.signal }),
          fetch(`/api/users/${userId}/posts`, { signal: controller.signal }),
          fetch(`/api/users/${userId}/comments`, { signal: controller.signal })
        ])

        // Parse all responses
        const [user, posts, comments] = await Promise.all([
          userRes.json(),
          postsRes.json(),
          commentsRes.json()
        ])

        setData({ user, posts, comments })
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchAll()

    return () => controller.abort()
  }, [userId])

  if (loading) return <div>Loading dashboard...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <div>
      <h1>{data.user?.name}'s Dashboard</h1>
      <section>
        <h2>Posts ({data.posts?.length})</h2>
        {/* Render posts */}
      </section>
      <section>
        <h2>Comments ({data.comments?.length})</h2>
        {/* Render comments */}
      </section>
    </div>
  )
}
```

## Dependent Data Fetching

Fetch data that depends on previous results:

```tsx
import { useState, useEffect } from 'react'

function PostWithComments({ postId }: { postId: number }) {
  const [post, setPost] = useState(null)
  const [author, setAuthor] = useState(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchDependent() {
      try {
        // First fetch the post
        const postRes = await fetch(`/api/posts/${postId}`, {
          signal: controller.signal
        })
        const postData = await postRes.json()
        setPost(postData)

        // Then fetch the author based on post data
        const authorRes = await fetch(`/api/users/${postData.authorId}`, {
          signal: controller.signal
        })
        const authorData = await authorRes.json()
        setAuthor(authorData)
      } catch (err) {
        console.error('Fetch failed:', err)
      } finally {
        setLoading(false)
      }
    }

    fetchDependent()

    return () => controller.abort()
  }, [postId])

  if (loading) return <div>Loading...</div>

  return (
    <article>
      <h2>{post?.title}</h2>
      <p>By {author?.name}</p>
      <div>{post?.content}</div>
    </article>
  )
}
```

## Infinite Scroll

Implement infinite scrolling with intersection observer:

```tsx
import { useState, useEffect, useRef, useCallback } from 'react'

interface UseInfiniteScrollReturn<T> {
  items: T[]
  loading: boolean
  error: Error | null
  hasMore: boolean
  observerTarget: (node: HTMLDivElement | null) => void
}

function useInfiniteScroll<T>(
  baseUrl: string,
  pageSize: number = 20
): UseInfiniteScrollReturn<T> {
  const [items, setItems] = useState<T[]>([])
  const [page, setPage] = useState(1)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<Error | null>(null)
  const [hasMore, setHasMore] = useState(true)
  const observer = useRef<IntersectionObserver>()

  const observerTarget = useCallback((node: HTMLDivElement | null) => {
    if (loading) return
    if (observer.current) observer.current.disconnect()

    observer.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting && hasMore) {
        setPage(prevPage => prevPage + 1)
      }
    })

    if (node) observer.current.observe(node)
  }, [loading, hasMore])

  useEffect(() => {
    const controller = new AbortController()

    async function fetchPage() {
      try {
        setLoading(true)
        const response = await fetch(
          `${baseUrl}?page=${page}&limit=${pageSize}`,
          { signal: controller.signal }
        )
        const data = await response.json()

        setItems(prev => [...prev, ...data.items])
        setHasMore(data.hasMore)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchPage()

    return () => controller.abort()
  }, [baseUrl, page, pageSize])

  return { items, loading, error, hasMore, observerTarget }
}

// Usage
function InfiniteProductList() {
  const { items, loading, hasMore, observerTarget } = 
    useInfiniteScroll<Product>('/api/products', 20)

  return (
    <div>
      {items.map((product, index) => (
        <div key={`${product.id}-${index}`}>
          <h3>{product.name}</h3>
          <p>${product.price}</p>
        </div>
      ))}
      
      {hasMore && (
        <div ref={observerTarget} style={% raw %}{{ height: '20px' }}{% endraw %}>
          {loading && <div>Loading more...</div>}
        </div>
      )}
      
      {!hasMore && <div>No more items</div>}
    </div>
  )
}
```

## WebSocket Real-Time Updates

Implement real-time data synchronization:

```tsx
import { useState, useEffect, useRef } from 'react'

interface UseWebSocketOptions {
  onMessage?: (data: any) => void
  onError?: (error: Event) => void
  reconnect?: boolean
  reconnectDelay?: number
}

function useWebSocket<T>(
  url: string,
  options: UseWebSocketOptions = {}
) {
  const [data, setData] = useState<T | null>(null)
  const [isConnected, setIsConnected] = useState(false)
  const ws = useRef<WebSocket | null>(null)
  const reconnectTimeout = useRef<NodeJS.Timeout>()

  useEffect(() => {
    const connect = () => {
      ws.current = new WebSocket(url)

      ws.current.onopen = () => {
        setIsConnected(true)
        console.log('WebSocket connected')
      }

      ws.current.onmessage = (event) => {
        const parsed = JSON.parse(event.data)
        setData(parsed)
        options.onMessage?.(parsed)
      }

      ws.current.onerror = (error) => {
        console.error('WebSocket error:', error)
        options.onError?.(error)
      }

      ws.current.onclose = () => {
        setIsConnected(false)
        console.log('WebSocket disconnected')

        // Reconnect if enabled
        if (options.reconnect !== false) {
          reconnectTimeout.current = setTimeout(() => {
            console.log('Reconnecting...')
            connect()
          }, options.reconnectDelay || 3000)
        }
      }
    }

    connect()

    return () => {
      if (reconnectTimeout.current) {
        clearTimeout(reconnectTimeout.current)
      }
      ws.current?.close()
    }
  }, [url, options])

  const send = useCallback((message: any) => {
    if (ws.current?.readyState === WebSocket.OPEN) {
      ws.current.send(JSON.stringify(message))
    }
  }, [])

  return { data, isConnected, send }
}

// Usage
interface StockPrice {
  symbol: string
  price: number
  timestamp: number
}

function LiveStockPrice({ symbol }: { symbol: string }) {
  const { data, isConnected } = useWebSocket<StockPrice>(
    `wss://api.example.com/stocks/${symbol}`,
    { reconnect: true }
  )

  return (
    <div>
      <div>Status: {isConnected ? '🟢 Connected' : '🔴 Disconnected'}</div>
      {data && (
        <div>
          <h2>{data.symbol}</h2>
          <p>${data.price.toFixed(2)}</p>
          <small>{new Date(data.timestamp).toLocaleTimeString()}</small>
        </div>
      )}
    </div>
  )
}
```

## Optimistic Updates

Update UI immediately before server confirmation:

```tsx
import { useState } from 'react'

interface Todo {
  id: number
  text: string
  completed: boolean
}

function useTodos() {
  const [todos, setTodos] = useState<Todo[]>([])

  const toggleTodo = async (id: number) => {
    // Optimistic update
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    )

    try {
      const response = await fetch(`/api/todos/${id}/toggle`, {
        method: 'POST'
      })

      if (!response.ok) {
        throw new Error('Failed to toggle todo')
      }

      // Server confirmed - no need to update again
    } catch (err) {
      // Revert on error
      setTodos(prev =>
        prev.map(todo =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
      )
      console.error('Failed to toggle todo:', err)
    }
  }

  const addTodo = async (text: string) => {
    // Create optimistic todo with temporary ID
    const optimisticTodo: Todo = {
      id: Date.now(),
      text,
      completed: false
    }

    setTodos(prev => [...prev, optimisticTodo])

    try {
      const response = await fetch('/api/todos', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text })
      })

      const serverTodo = await response.json()

      // Replace optimistic todo with server response
      setTodos(prev =>
        prev.map(todo =>
          todo.id === optimisticTodo.id ? serverTodo : todo
        )
      )
    } catch (err) {
      // Remove optimistic todo on error
      setTodos(prev => prev.filter(todo => todo.id !== optimisticTodo.id))
      console.error('Failed to add todo:', err)
    }
  }

  return { todos, toggleTodo, addTodo }
}
```

## Data Prefetching

Prefetch data for better perceived performance:

```tsx
import { useState, useEffect } from 'react'

// Global prefetch cache
const prefetchCache = new Map<string, Promise<any>>()

function prefetch(url: string): Promise<any> {
  if (!prefetchCache.has(url)) {
    prefetchCache.set(
      url,
      fetch(url).then(r => r.json())
    )
  }
  return prefetchCache.get(url)!
}

function usePrefetchedData<T>(url: string) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    prefetch(url).then(data => {
      setData(data)
      setLoading(false)
    })
  }, [url])

  return { data, loading }
}

// Prefetch on hover
function NavigationLink({ href, children }: { href: string; children: React.ReactNode }) {
  const handleMouseEnter = () => {
    prefetch(href)
  }

  return (
    <a href={href} onMouseEnter={handleMouseEnter}>
      {children}
    </a>
  )
}
```

## Debouncing Search

Optimize search with debouncing:

```tsx
import { useState, useEffect, useRef } from 'react'

function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value)

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}

function SearchResults() {
  const [query, setQuery] = useState('')
  const [results, setResults] = useState([])
  const [loading, setLoading] = useState(false)
  const debouncedQuery = useDebounce(query, 300)

  useEffect(() => {
    if (!debouncedQuery) {
      setResults([])
      return
    }

    const controller = new AbortController()

    async function search() {
      setLoading(true)
      try {
        const response = await fetch(
          `/api/search?q=${encodeURIComponent(debouncedQuery)}`,
          { signal: controller.signal }
        )
        const data = await response.json()
        setResults(data.results)
      } catch (err) {
        console.error('Search failed:', err)
      } finally {
        setLoading(false)
      }
    }

    search()

    return () => controller.abort()
  }, [debouncedQuery])

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      {loading && <div>Searching...</div>}
      <ul>
        {results.map((result, i) => (
          <li key={i}>{result}</li>
        ))}
      </ul>
    </div>
  )
}
```

## Best Practices

1. **Use Promise.all** for independent parallel fetches
2. **Implement intersection observer** for infinite scroll
3. **Add optimistic updates** for better UX
4. **Prefetch data** on user intent (hover, etc.)
5. **Debounce search inputs** to reduce API calls
6. **Handle WebSocket reconnection** gracefully
7. **Validate data** from real-time sources
8. **Test edge cases** thoroughly

## Summary

Advanced data fetching patterns enable sophisticated user experiences in production applications. Master these techniques to build responsive, real-time applications.

**Next Steps:**

- Explore [Modern Data Fetching Libraries](./libraries/) for built-in solutions
- Learn about [Performance Optimization](./performance/) techniques

**Key Takeaways:**

1. Parallel fetching improves load times
2. Infinite scroll enhances mobile experiences
3. WebSockets enable real-time features
4. Optimistic updates improve perceived performance
5. Prefetching reduces wait times
6. Debouncing optimizes search and input handling
