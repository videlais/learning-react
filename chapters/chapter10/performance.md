---
title: "Performance Optimization"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/performance/
---

## Performance Optimization

Optimize data fetching for production applications with caching, deduplication, and intelligent loading strategies.

## Overview

Performance techniques covered:

- Request deduplication
- Caching strategies
- Debouncing and throttling
- Lazy loading data
- Bundle optimization

## Request Deduplication

Prevent duplicate simultaneous requests:

```tsx
import { useEffect, useState, useRef } from 'react'

// Global request cache
const pendingRequests = new Map<string, Promise<any>>()

function useDedupedFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true)

        // Check if request is already pending
        if (!pendingRequests.has(url)) {
          const promise = fetch(url)
            .then(r => r.json())
            .finally(() => {
              // Clean up after request completes
              pendingRequests.delete(url)
            })
          
          pendingRequests.set(url, promise)
        }

        const result = await pendingRequests.get(url)!
        setData(result)
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Fetch failed'))
      } finally {
        setLoading(false)
      }
    }

    fetchData()
  }, [url])

  return { data, loading, error }
}

// Multiple components can use this without duplicate requests
function Component1() {
  const { data } = useDedupedFetch('/api/shared-data')
  return <div>{data?.value}</div>
}

function Component2() {
  const { data } = useDedupedFetch('/api/shared-data') // Same request, no duplication
  return <div>{data?.value}</div>
}
```

## Caching Strategies

Implement smart caching:

```tsx
interface CacheEntry<T> {
  data: T
  timestamp: number
  expiresAt: number
}

class DataCache {
  private cache = new Map<string, CacheEntry<any>>()
  private defaultTTL = 5 * 60 * 1000 // 5 minutes

  set<T>(key: string, data: T, ttl?: number): void {
    const now = Date.now()
    this.cache.set(key, {
      data,
      timestamp: now,
      expiresAt: now + (ttl || this.defaultTTL)
    })
  }

  get<T>(key: string): T | null {
    const entry = this.cache.get(key)
    
    if (!entry) return null
    
    // Check if expired
    if (Date.now() > entry.expiresAt) {
      this.cache.delete(key)
      return null
    }

    return entry.data
  }

  invalidate(pattern?: string): void {
    if (!pattern) {
      this.cache.clear()
      return
    }

    // Invalidate matching keys
    const regex = new RegExp(pattern)
    for (const key of this.cache.keys()) {
      if (regex.test(key)) {
        this.cache.delete(key)
      }
    }
  }

  has(key: string): boolean {
    return this.get(key) !== null
  }
}

const cache = new DataCache()

function useCachedFetch<T>(url: string, ttl?: number) {
  const [data, setData] = useState<T | null>(() => cache.get<T>(url))
  const [loading, setLoading] = useState(!cache.has(url))
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    if (cache.has(url)) {
      setData(cache.get<T>(url))
      setLoading(false)
      return
    }

    async function fetchData() {
      try {
        setLoading(true)
        const response = await fetch(url)
        if (!response.ok) throw new Error('Fetch failed')
        
        const json = await response.json()
        cache.set(url, json, ttl)
        setData(json)
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Fetch failed'))
      } finally {
        setLoading(false)
      }
    }

    fetchData()
  }, [url, ttl])

  const invalidate = () => {
    cache.invalidate(url)
  }

  return { data, loading, error, invalidate }
}
```

## Stale-While-Revalidate

Show cached data immediately while fetching fresh data:

```tsx
import { useState, useEffect } from 'react'

function useSWR<T>(url: string) {
  const [data, setData] = useState<T | null>(() => cache.get<T>(url))
  const [isValidating, setIsValidating] = useState(false)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    async function revalidate() {
      try {
        setIsValidating(true)
        const response = await fetch(url)
        if (!response.ok) throw new Error('Fetch failed')
        
        const json = await response.json()
        cache.set(url, json)
        setData(json)
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Fetch failed'))
      } finally {
        setIsValidating(false)
      }
    }

    revalidate()
  }, [url])

  return { 
    data, 
    isValidating, 
    error,
    isStale: data !== null && isValidating
  }
}

// Usage
function DataDisplay() {
  const { data, isValidating, isStale } = useSWR('/api/data')

  return (
    <div>
      {isStale && <div className="stale-indicator">Refreshing...</div>}
      {data && <div>{data.value}</div>}
      {!data && isValidating && <div>Loading...</div>}
    </div>
  )
}
```

## Debouncing

Optimize rapid successive calls:

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

// Search with debouncing
function SearchComponent() {
  const [query, setQuery] = useState('')
  const [results, setResults] = useState([])
  const [searching, setSearching] = useState(false)
  const debouncedQuery = useDebounce(query, 300)

  useEffect(() => {
    if (!debouncedQuery) {
      setResults([])
      return
    }

    const controller = new AbortController()

    async function search() {
      setSearching(true)
      try {
        const response = await fetch(
          `/api/search?q=${encodeURIComponent(debouncedQuery)}`,
          { signal: controller.signal }
        )
        const data = await response.json()
        setResults(data.results)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          console.error('Search failed:', err)
        }
      } finally {
        setSearching(false)
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
      {searching && <div>Searching...</div>}
      <ul>
        {results.map((result, i) => (
          <li key={i}>{result}</li>
        ))}
      </ul>
    </div>
  )
}
```

## Throttling

Limit request frequency:

```tsx
import { useRef, useCallback } from 'react'

function useThrottle<T extends (...args: any[]) => any>(
  callback: T,
  delay: number
): T {
  const lastRun = useRef(Date.now())

  return useCallback((...args: Parameters<T>) => {
    const now = Date.now()

    if (now - lastRun.current >= delay) {
      callback(...args)
      lastRun.current = now
    }
  }, [callback, delay]) as T
}

// Usage for scroll-triggered fetching
function InfiniteScroll() {
  const [items, setItems] = useState([])

  const loadMore = useThrottle(async () => {
    const response = await fetch('/api/items?page=next')
    const data = await response.json()
    setItems(prev => [...prev, ...data.items])
  }, 1000) // Max once per second

  useEffect(() => {
    const handleScroll = () => {
      if (window.innerHeight + window.scrollY >= document.body.offsetHeight - 500) {
        loadMore()
      }
    }

    window.addEventListener('scroll', handleScroll)
    return () => window.removeEventListener('scroll', handleScroll)
  }, [loadMore])

  return (
    <div>
      {items.map(item => (
        <div key={item.id}>{item.name}</div>
      ))}
    </div>
  )
}
```

## Lazy Data Loading

Load data only when needed:

```tsx
import { useState, useEffect } from 'react'

function useLazyFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState<Error | null>(null)
  const [shouldFetch, setShouldFetch] = useState(false)

  useEffect(() => {
    if (!shouldFetch) return

    const controller = new AbortController()

    async function fetchData() {
      try {
        setLoading(true)
        const response = await fetch(url, { signal: controller.signal })
        if (!response.ok) throw new Error('Fetch failed')
        
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
  }, [url, shouldFetch])

  const trigger = () => setShouldFetch(true)

  return { data, loading, error, trigger }
}

// Usage - load data on user action
function ExpandableSection() {
  const [isOpen, setIsOpen] = useState(false)
  const { data, loading, trigger } = useLazyFetch('/api/details')

  const handleToggle = () => {
    if (!isOpen) {
      trigger()
    }
    setIsOpen(!isOpen)
  }

  return (
    <div>
      <button onClick={handleToggle}>
        {isOpen ? 'Hide' : 'Show'} Details
      </button>
      {isOpen && (
        <div>
          {loading && <div>Loading...</div>}
          {data && <div>{JSON.stringify(data)}</div>}
        </div>
      )}
    </div>
  )
}
```

## Prefetching

Load data before it's needed:

```tsx
import { useEffect } from 'react'

function usePrefetch(url: string, condition: boolean = true) {
  useEffect(() => {
    if (!condition) return

    const link = document.createElement('link')
    link.rel = 'prefetch'
    link.href = url
    document.head.appendChild(link)

    return () => {
      document.head.removeChild(link)
    }
  }, [url, condition])
}

// Prefetch on hover
function NavigationLink({ href, label }: { href: string; label: string }) {
  const [shouldPrefetch, setShouldPrefetch] = useState(false)
  
  usePrefetch(href, shouldPrefetch)

  return (
    <a
      href={href}
      onMouseEnter={() => setShouldPrefetch(true)}
      onFocus={() => setShouldPrefetch(true)}
    >
      {label}
    </a>
  )
}
```

## Bundle Size Optimization

Lazy load data fetching libraries:

```tsx
import { lazy, Suspense } from 'react'

// Lazy load React Query only when needed
const ReactQueryProvider = lazy(() => import('./ReactQueryProvider'))

function App() {
  const [showDashboard, setShowDashboard] = useState(false)

  return (
    <div>
      <button onClick={() => setShowDashboard(true)}>
        Open Dashboard
      </button>
      
      {showDashboard && (
        <Suspense fallback={<div>Loading...</div>}>
          <ReactQueryProvider>
            <Dashboard />
          </ReactQueryProvider>
        </Suspense>
      )}
    </div>
  )
}
```

## Monitoring Performance

Track data fetching performance:

```tsx
import { useEffect } from 'react'

function usePerformanceMonitoring(url: string) {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.name.includes(url)) {
          console.log('Fetch performance:', {
            url: entry.name,
            duration: entry.duration,
            transferSize: (entry as PerformanceResourceTiming).transferSize,
            cached: (entry as PerformanceResourceTiming).transferSize === 0
          })

          // Send to analytics
          // analytics.track('data_fetch', { ... })
        }
      }
    })

    observer.observe({ entryTypes: ['resource'] })

    return () => observer.disconnect()
  }, [url])
}
```

## Best Practices Checklist

- [ ] Implement request deduplication
- [ ] Use appropriate caching strategies
- [ ] Debounce user input (300ms typical)
- [ ] Throttle scroll/resize events
- [ ] Lazy load data not immediately needed
- [ ] Prefetch anticipated data
- [ ] Monitor and optimize bundle size
- [ ] Track performance metrics
- [ ] Use stale-while-revalidate pattern
- [ ] Implement proper cache invalidation

## Summary

Performance optimization is critical for production data fetching. By implementing caching, deduplication, and intelligent loading strategies, you can dramatically improve application performance.

**Next Steps:**

- See [Real-World Examples](./examples/) of complete implementations
- Review earlier sections for comprehensive understanding

**Key Takeaways:**

1. Request deduplication prevents wasteful duplicate calls
2. Caching reduces server load and improves UX
3. Debouncing optimizes rapid input changes
4. Throttling limits request frequency
5. Lazy loading reduces initial bundle size
6. Prefetching improves perceived performance
7. Monitoring helps identify bottlenecks
