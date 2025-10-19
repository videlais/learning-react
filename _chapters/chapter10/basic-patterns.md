---
title: "Modern Data Fetching with useEffect"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/basic-patterns/
---

## Modern Data Fetching with useEffect

The `useEffect` hook is the foundation of data fetching in modern React applications. Understanding how to use it correctly is essential for building reliable applications.

## Overview

In this section, you'll learn:

- Basic data fetching with useEffect
- Proper cleanup and abort patterns
- Dependency array management
- Common pitfalls and solutions

## Basic Data Fetching Pattern

The simplest data fetching pattern uses `useEffect` with the Fetch API:

```tsx
import { useState, useEffect } from 'react'

interface User {
  id: number
  name: string
  email: string
}

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => {
        if (!response.ok) {
          throw new Error('Failed to fetch user')
        }
        return response.json()
      })
      .then(data => {
        setUser(data)
        setLoading(false)
      })
      .catch(err => {
        setError(err)
        setLoading(false)
      })
  }, [userId])

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

## Using Async/Await

Modern JavaScript async/await syntax provides cleaner code:

```tsx
import { useState, useEffect } from 'react'

interface Post {
  id: number
  title: string
  body: string
}

function BlogPost({ postId }: { postId: number }) {
  const [post, setPost] = useState<Post | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    async function fetchPost() {
      try {
        const response = await fetch(`https://api.example.com/posts/${postId}`)
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`)
        }
        
        const data = await response.json()
        setPost(data)
      } catch (err) {
        setError(err instanceof Error ? err.message : 'An error occurred')
      } finally {
        setLoading(false)
      }
    }

    fetchPost()
  }, [postId])

  if (loading) return <div>Loading post...</div>
  if (error) return <div>Error: {error}</div>
  if (!post) return <div>Post not found</div>

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </article>
  )
}
```

## Proper Cleanup with AbortController

Always implement cleanup to prevent memory leaks and race conditions:

```tsx
import { useState, useEffect } from 'react'

interface Product {
  id: number
  name: string
  price: number
}

function ProductDetails({ productId }: { productId: number }) {
  const [product, setProduct] = useState<Product | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<string | null>(null)

  useEffect(() => {
    const abortController = new AbortController()
    
    async function fetchProduct() {
      try {
        setLoading(true)
        setError(null)
        
        const response = await fetch(
          `https://api.example.com/products/${productId}`,
          { signal: abortController.signal }
        )
        
        if (!response.ok) {
          throw new Error('Failed to fetch product')
        }
        
        const data = await response.json()
        setProduct(data)
      } catch (err) {
        // Ignore abort errors
        if (err instanceof Error && err.name === 'AbortError') {
          return
        }
        setError(err instanceof Error ? err.message : 'Unknown error')
      } finally {
        setLoading(false)
      }
    }

    fetchProduct()

    // Cleanup function
    return () => {
      abortController.abort()
    }
  }, [productId])

  if (loading) return <div>Loading product...</div>
  if (error) return <div>Error: {error}</div>
  if (!product) return <div>Product not found</div>

  return (
    <div>
      <h2>{product.name}</h2>
      <p>${product.price}</p>
    </div>
  )
}
```

## Preventing Race Conditions

Use cleanup flags to prevent state updates after unmount:

```tsx
import { useState, useEffect } from 'react'

function SearchResults({ query }: { query: string }) {
  const [results, setResults] = useState<string[]>([])
  const [loading, setLoading] = useState(false)

  useEffect(() => {
    let isCancelled = false
    
    async function search() {
      if (!query) {
        setResults([])
        return
      }

      setLoading(true)
      
      try {
        const response = await fetch(
          `https://api.example.com/search?q=${encodeURIComponent(query)}`
        )
        const data = await response.json()
        
        // Only update state if component is still mounted
        if (!isCancelled) {
          setResults(data.results)
          setLoading(false)
        }
      } catch (err) {
        if (!isCancelled) {
          console.error('Search failed:', err)
          setLoading(false)
        }
      }
    }

    search()

    return () => {
      isCancelled = true
    }
  }, [query])

  return (
    <div>
      {loading && <div>Searching...</div>}
      <ul>
        {results.map((result, index) => (
          <li key={index}>{result}</li>
        ))}
      </ul>
    </div>
  )
}
```

## Dependency Array Best Practices

Understanding the dependency array is crucial:

```tsx
import { useState, useEffect } from 'react'

// ❌ Missing dependencies
function BadExample({ userId }: { userId: number }) {
  const [user, setUser] = useState(null)
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser)
  }, []) // Missing userId dependency!
  
  return <div>{user?.name}</div>
}

// ✅ Correct dependencies
function GoodExample({ userId }: { userId: number }) {
  const [user, setUser] = useState(null)
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser)
  }, [userId]) // Correctly includes userId
  
  return <div>{user?.name}</div>
}

// ✅ Using objects as dependencies
function ConfigExample({ config }: { config: { apiUrl: string; timeout: number } }) {
  const [data, setData] = useState(null)
  
  // Extract primitives to avoid unnecessary re-fetches
  const { apiUrl, timeout } = config
  
  useEffect(() => {
    const controller = new AbortController()
    
    fetch(apiUrl, { 
      signal: controller.signal,
      // Use timeout in request
    })
      .then(r => r.json())
      .then(setData)
    
    return () => controller.abort()
  }, [apiUrl, timeout]) // Depend on primitives, not the object
  
  return <div>{data?.value}</div>
}
```

## Conditional Fetching

Sometimes you need to fetch data conditionally:

```tsx
import { useState, useEffect } from 'react'

interface UserData {
  id: number
  name: string
  premium: boolean
}

function UserDashboard({ userId, isPremium }: { userId: number; isPremium: boolean }) {
  const [userData, setUserData] = useState<UserData | null>(null)
  const [premiumData, setPremiumData] = useState(null)

  // Always fetch user data
  useEffect(() => {
    const controller = new AbortController()
    
    fetch(`/api/users/${userId}`, { signal: controller.signal })
      .then(r => r.json())
      .then(setUserData)
    
    return () => controller.abort()
  }, [userId])

  // Only fetch premium data if user is premium
  useEffect(() => {
    if (!isPremium) return
    
    const controller = new AbortController()
    
    fetch(`/api/premium/${userId}`, { signal: controller.signal })
      .then(r => r.json())
      .then(setPremiumData)
    
    return () => controller.abort()
  }, [userId, isPremium])

  return (
    <div>
      <h2>{userData?.name}</h2>
      {isPremium && premiumData && (
        <div>Premium Features: {JSON.stringify(premiumData)}</div>
      )}
    </div>
  )
}
```

## Refetching Data

Implement data refetching with a state trigger:

```tsx
import { useState, useEffect, useCallback } from 'react'

interface Comment {
  id: number
  text: string
  author: string
}

function CommentList({ postId }: { postId: number }) {
  const [comments, setComments] = useState<Comment[]>([])
  const [loading, setLoading] = useState(true)
  const [refreshTrigger, setRefreshTrigger] = useState(0)

  useEffect(() => {
    const controller = new AbortController()
    
    async function fetchComments() {
      setLoading(true)
      try {
        const response = await fetch(
          `/api/posts/${postId}/comments`,
          { signal: controller.signal }
        )
        const data = await response.json()
        setComments(data)
      } catch (err) {
        if (err instanceof Error && err.name !== 'AbortError') {
          console.error('Failed to fetch comments:', err)
        }
      } finally {
        setLoading(false)
      }
    }

    fetchComments()

    return () => controller.abort()
  }, [postId, refreshTrigger])

  const refresh = useCallback(() => {
    setRefreshTrigger(prev => prev + 1)
  }, [])

  return (
    <div>
      <button onClick={refresh} disabled={loading}>
        Refresh Comments
      </button>
      {loading && <div>Loading...</div>}
      <ul>
        {comments.map(comment => (
          <li key={comment.id}>
            <strong>{comment.author}:</strong> {comment.text}
          </li>
        ))}
      </ul>
    </div>
  )
}
```

## Common Pitfalls

### Pitfall 1: Infinite Loops

```tsx
// ❌ Creates infinite loop
function BadInfiniteLoop() {
  const [data, setData] = useState([])
  
  useEffect(() => {
    fetch('/api/data')
      .then(r => r.json())
      .then(setData)
  }, [data]) // data changes every render!
}

// ✅ Correct approach
function GoodApproach() {
  const [data, setData] = useState([])
  
  useEffect(() => {
    fetch('/api/data')
      .then(r => r.json())
      .then(setData)
  }, []) // Empty array - fetch once
}
```

### Pitfall 2: Not Handling Errors

```tsx
// ❌ Errors not handled
function BadErrorHandling() {
  const [data, setData] = useState(null)
  
  useEffect(() => {
    fetch('/api/data')
      .then(r => r.json())
      .then(setData)
    // No error handling!
  }, [])
}

// ✅ Proper error handling
function GoodErrorHandling() {
  const [data, setData] = useState(null)
  const [error, setError] = useState(null)
  
  useEffect(() => {
    fetch('/api/data')
      .then(r => {
        if (!r.ok) throw new Error('Failed to fetch')
        return r.json()
      })
      .then(setData)
      .catch(setError)
  }, [])
  
  if (error) return <div>Error: {error.message}</div>
}
```

### Pitfall 3: Not Cleaning Up

```tsx
// ❌ No cleanup
function BadCleanup({ id }: { id: number }) {
  const [data, setData] = useState(null)
  
  useEffect(() => {
    fetch(`/api/items/${id}`)
      .then(r => r.json())
      .then(setData)
    // If id changes quickly, old requests might complete after new ones
  }, [id])
}

// ✅ Proper cleanup
function GoodCleanup({ id }: { id: number }) {
  const [data, setData] = useState(null)
  
  useEffect(() => {
    const controller = new AbortController()
    
    fetch(`/api/items/${id}`, { signal: controller.signal })
      .then(r => r.json())
      .then(setData)
      .catch(err => {
        if (err.name !== 'AbortError') {
          console.error(err)
        }
      })
    
    return () => controller.abort()
  }, [id])
}
```

## Best Practices Summary

1. **Always handle loading and error states**
2. **Use AbortController for cleanup**
3. **Include all dependencies in the dependency array**
4. **Handle component unmount gracefully**
5. **Use async/await for cleaner code**
6. **Validate responses before setting state**
7. **Consider race conditions with rapid changes**
8. **Don't fetch on every render - use dependencies wisely**

## Summary

The `useEffect` hook provides a solid foundation for data fetching in React. By following best practices for cleanup, error handling, and dependency management, you can build reliable data fetching logic.

**Next Steps:**

- Learn about [Custom Data Fetching Hooks](./custom-hooks/) to make patterns reusable
- Explore [Error Handling and Loading States](./error-loading/) for better UX

**Key Takeaways:**

1. useEffect is the fundamental hook for side effects like data fetching
2. Always implement cleanup with AbortController
3. Properly manage the dependency array to avoid bugs
4. Handle loading and error states explicitly
5. Prevent race conditions with cleanup flags
