---
title: "Custom Data Fetching Hooks"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/custom-hooks/
---

## Custom Data Fetching Hooks

Custom hooks let you extract data fetching logic into reusable functions, making your components cleaner and more maintainable.

## Overview

Learn to build:

- Basic custom data fetching hooks
- Hooks with caching capabilities
- Typed hooks with TypeScript
- Advanced hooks with retry logic

## Basic useFetch Hook

Start with a simple reusable fetch hook:

```tsx
import { useState, useEffect } from 'react'

interface UseFetchResult<T> {
  data: T | null
  loading: boolean
  error: Error | null
}

function useFetch<T>(url: string): UseFetchResult<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchData() {
      try {
        setLoading(true)
        setError(null)

        const response = await fetch(url, { signal: controller.signal })
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }

        const json = await response.json()
        setData(json)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchData()

    return () => controller.abort()
  }, [url])

  return { data, loading, error }
}

// Usage
interface User {
  id: number
  name: string
  email: string
}

function UserProfile({ userId }: { userId: number }) {
  const { data: user, loading, error } = useFetch<User>(
    `https://api.example.com/users/${userId}`
  )

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>
  if (!user) return <div>No user found</div>

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  )
}
```

## Hook with Refetch Capability

Add the ability to manually refetch data:

```tsx
import { useState, useEffect, useCallback } from 'react'

interface UseFetchOptions {
  autoFetch?: boolean
}

interface UseFetchReturn<T> {
  data: T | null
  loading: boolean
  error: Error | null
  refetch: () => void
}

function useFetch<T>(
  url: string,
  options: UseFetchOptions = {}
): UseFetchReturn<T> {
  const { autoFetch = true } = options
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(autoFetch)
  const [error, setError] = useState<Error | null>(null)
  const [trigger, setTrigger] = useState(0)

  useEffect(() => {
    if (!autoFetch && trigger === 0) return

    const controller = new AbortController()

    async function fetchData() {
      try {
        setLoading(true)
        setError(null)

        const response = await fetch(url, { signal: controller.signal })
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }

        const json = await response.json()
        setData(json)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchData()

    return () => controller.abort()
  }, [url, trigger, autoFetch])

  const refetch = useCallback(() => {
    setTrigger(prev => prev + 1)
  }, [])

  return { data, loading, error, refetch }
}

// Usage
function PostList() {
  const { data: posts, loading, error, refetch } = useFetch<Post[]>(
    'https://api.example.com/posts'
  )

  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      {loading && <div>Loading...</div>}
      {error && <div>Error: {error.message}</div>}
      {posts?.map(post => (
        <article key={post.id}>
          <h3>{post.title}</h3>
        </article>
      ))}
    </div>
  )
}
```

## Hook with Caching

Implement basic caching to avoid redundant requests:

```tsx
import { useState, useEffect, useRef } from 'react'

// Simple in-memory cache
const cache = new Map<string, any>()

interface UseCachedFetchOptions {
  cacheTime?: number // milliseconds
}

interface CacheEntry<T> {
  data: T
  timestamp: number
}

function useCachedFetch<T>(
  url: string,
  options: UseCachedFetchOptions = {}
): UseFetchReturn<T> {
  const { cacheTime = 5 * 60 * 1000 } = options // 5 minutes default
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchData() {
      // Check cache first
      const cached = cache.get(url) as CacheEntry<T> | undefined
      const now = Date.now()

      if (cached && now - cached.timestamp < cacheTime) {
        setData(cached.data)
        setLoading(false)
        return
      }

      try {
        setLoading(true)
        setError(null)

        const response = await fetch(url, { signal: controller.signal })
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }

        const json = await response.json()
        
        // Store in cache
        cache.set(url, {
          data: json,
          timestamp: now
        })

        setData(json)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          setError(err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchData()

    return () => controller.abort()
  }, [url, cacheTime])

  const refetch = useCallback(() => {
    // Clear cache for this URL
    cache.delete(url)
    setTrigger(prev => prev + 1)
  }, [url])

  return { data, loading, error, refetch }
}
```

## Hook with POST Support

Extend the hook to support mutations:

```tsx
import { useState, useCallback } from 'react'

interface UseApiOptions {
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE'
  headers?: HeadersInit
}

interface UseApiReturn<T, TBody = any> {
  data: T | null
  loading: boolean
  error: Error | null
  execute: (body?: TBody) => Promise<void>
}

function useApi<T, TBody = any>(
  url: string,
  options: UseApiOptions = {}
): UseApiReturn<T, TBody> {
  const { method = 'GET', headers = {} } = options
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<Error | null>(null)

  const execute = useCallback(async (body?: TBody) => {
    const controller = new AbortController()

    try {
      setLoading(true)
      setError(null)

      const requestOptions: RequestInit = {
        method,
        headers: {
          'Content-Type': 'application/json',
          ...headers
        },
        signal: controller.signal
      }

      if (body) {
        requestOptions.body = JSON.stringify(body)
      }

      const response = await fetch(url, requestOptions)
      
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`)
      }

      const json = await response.json()
      setData(json)
    } catch (err) {
      if (err instanceof Error && err.name !== 'AbortError') {
        setError(err)
      }
    } finally {
      setLoading(false)
    }
  }, [url, method, headers])

  return { data, loading, error, execute }
}

// Usage
interface CreatePostBody {
  title: string
  body: string
}

interface Post {
  id: number
  title: string
  body: string
}

function CreatePost() {
  const { data, loading, error, execute } = useApi<Post, CreatePostBody>(
    'https://api.example.com/posts',
    { method: 'POST' }
  )

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    
    await execute({
      title: formData.get('title') as string,
      body: formData.get('body') as string
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" placeholder="Title" required />
      <textarea name="body" placeholder="Body" required />
      <button type="submit" disabled={loading}>
        {loading ? 'Creating...' : 'Create Post'}
      </button>
      {error && <div>Error: {error.message}</div>}
      {data && <div>Created post with ID: {data.id}</div>}
    </form>
  )
}
```

## Hook with Retry Logic

Add automatic retry capability for failed requests:

```tsx
import { useState, useEffect, useRef } from 'react'

interface UseRetryFetchOptions {
  retries?: number
  retryDelay?: number // milliseconds
}

function useRetryFetch<T>(
  url: string,
  options: UseRetryFetchOptions = {}
): UseFetchReturn<T> {
  const { retries = 3, retryDelay = 1000 } = options
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)
  const attemptRef = useRef(0)

  useEffect(() => {
    const controller = new AbortController()
    attemptRef.current = 0

    async function fetchWithRetry() {
      while (attemptRef.current <= retries) {
        try {
          setLoading(true)
          setError(null)

          const response = await fetch(url, { signal: controller.signal })
          
          if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`)
          }

          const json = await response.json()
          setData(json)
          setLoading(false)
          return
        } catch (err) {
          if (err instanceof Error && err.name === 'AbortError') {
            return
          }

          attemptRef.current++

          if (attemptRef.current > retries) {
            setError(err instanceof Error ? err : new Error('Fetch failed'))
            setLoading(false)
            return
          }

          // Wait before retrying
          await new Promise(resolve => setTimeout(resolve, retryDelay))
        }
      }
    }

    fetchWithRetry()

    return () => controller.abort()
  }, [url, retries, retryDelay])

  const refetch = useCallback(() => {
    attemptRef.current = 0
    setTrigger(prev => prev + 1)
  }, [])

  return { data, loading, error, refetch }
}
```

## Pagination Hook

Create a hook specifically for paginated data:

```tsx
import { useState, useEffect, useCallback } from 'react'

interface PaginatedResponse<T> {
  data: T[]
  page: number
  totalPages: number
  total: number
}

interface UsePaginationReturn<T> {
  data: T[]
  loading: boolean
  error: Error | null
  page: number
  totalPages: number
  nextPage: () => void
  prevPage: () => void
  goToPage: (page: number) => void
}

function usePagination<T>(
  baseUrl: string,
  pageSize: number = 10
): UsePaginationReturn<T> {
  const [data, setData] = useState<T[]>([])
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)
  const [page, setPage] = useState(1)
  const [totalPages, setTotalPages] = useState(1)

  useEffect(() => {
    const controller = new AbortController()

    async function fetchPage() {
      try {
        setLoading(true)
        setError(null)

        const url = `${baseUrl}?page=${page}&limit=${pageSize}`
        const response = await fetch(url, { signal: controller.signal })
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }

        const result: PaginatedResponse<T> = await response.json()
        setData(result.data)
        setTotalPages(result.totalPages)
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

  const nextPage = useCallback(() => {
    setPage(prev => Math.min(prev + 1, totalPages))
  }, [totalPages])

  const prevPage = useCallback(() => {
    setPage(prev => Math.max(prev - 1, 1))
  }, [])

  const goToPage = useCallback((newPage: number) => {
    setPage(Math.max(1, Math.min(newPage, totalPages)))
  }, [totalPages])

  return {
    data,
    loading,
    error,
    page,
    totalPages,
    nextPage,
    prevPage,
    goToPage
  }
}

// Usage
function ProductList() {
  const {
    data: products,
    loading,
    error,
    page,
    totalPages,
    nextPage,
    prevPage
  } = usePagination<Product>('https://api.example.com/products', 20)

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <div>
      <ul>
        {products.map(product => (
          <li key={product.id}>{product.name}</li>
        ))}
      </ul>
      <div>
        <button onClick={prevPage} disabled={page === 1}>
          Previous
        </button>
        <span>Page {page} of {totalPages}</span>
        <button onClick={nextPage} disabled={page === totalPages}>
          Next
        </button>
      </div>
    </div>
  )
}
```

## Best Practices

1. **Type your hooks properly** with TypeScript generics
2. **Always implement cleanup** with AbortController
3. **Consider caching** for frequently accessed data
4. **Add retry logic** for unreliable endpoints
5. **Make hooks flexible** with options parameters
6. **Document your hooks** with clear examples
7. **Test edge cases** like rapid changes and unmounting
8. **Keep hooks focused** - one responsibility per hook

## Summary

Custom hooks are powerful tools for encapsulating data fetching logic. They improve code reusability and make components cleaner and easier to test.

**Next Steps:**

- Learn about [Error Handling and Loading States](./error-loading/) for better UX
- Explore [Advanced Data Fetching Patterns](./advanced-patterns/) for complex scenarios

**Key Takeaways:**

1. Custom hooks encapsulate reusable data fetching logic
2. TypeScript generics make hooks type-safe and flexible
3. Caching reduces unnecessary network requests
4. Retry logic improves reliability
5. Specialized hooks (pagination, mutations) solve specific problems
