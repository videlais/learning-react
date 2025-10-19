---
title: "Real-World Examples"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/examples/
---

## Real-World Examples

Complete, production-ready implementations demonstrating modern data fetching patterns in realistic scenarios.

## Overview

Examples covered:

- E-commerce product catalog
- Social media feed
- Real-time dashboard
- Multi-step form with API
- Search with filters

## E-Commerce Product Catalog

Complete product listing with cart functionality:

```tsx
import { useState } from 'react'
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

interface Product {
  id: number
  name: string
  price: number
  image: string
  inStock: boolean
}

interface CartItem {
  productId: number
  quantity: number
}

// API functions
async function fetchProducts(category?: string, sort?: string) {
  const params = new URLSearchParams()
  if (category) params.append('category', category)
  if (sort) params.append('sort', sort)
  
  const response = await fetch(`/api/products?${params}`)
  if (!response.ok) throw new Error('Failed to fetch products')
  return response.json()
}

async function addToCart(productId: number, quantity: number) {
  const response = await fetch('/api/cart', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ productId, quantity })
  })
  return response.json()
}

// Product catalog component
function ProductCatalog() {
  const [category, setCategory] = useState<string>('')
  const [sort, setSort] = useState<string>('name')
  const queryClient = useQueryClient()

  // Fetch products
  const { data: products, isLoading, error } = useQuery({
    queryKey: ['products', category, sort],
    queryFn: () => fetchProducts(category, sort),
    staleTime: 2 * 60 * 1000 // 2 minutes
  })

  // Add to cart mutation
  const addToCartMutation = useMutation({
    mutationFn: ({ productId, quantity }: { productId: number; quantity: number }) =>
      addToCart(productId, quantity),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['cart'] })
    }
  })

  if (isLoading) {
    return (
      <div className="product-grid">
        {Array.from({ length: 8 }).map((_, i) => (
          <ProductSkeleton key={i} />
        ))}
      </div>
    )
  }

  if (error) {
    return (
      <div className="error">
        <h3>Failed to load products</h3>
        <p>{error.message}</p>
        <button onClick={() => queryClient.invalidateQueries({ queryKey: ['products'] })}>
          Try Again
        </button>
      </div>
    )
  }

  return (
    <div>
      <div className="filters">
        <select value={category} onChange={(e) => setCategory(e.target.value)}>
          <option value="">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
          <option value="books">Books</option>
        </select>

        <select value={sort} onChange={(e) => setSort(e.target.value)}>
          <option value="name">Name</option>
          <option value="price-asc">Price: Low to High</option>
          <option value="price-desc">Price: High to Low</option>
        </select>
      </div>

      <div className="product-grid">
        {products?.map((product: Product) => (
          <div key={product.id} className="product-card">
            <img src={product.image} alt={product.name} />
            <h3>{product.name}</h3>
            <p className="price">${product.price.toFixed(2)}</p>
            <button
              onClick={() => addToCartMutation.mutate({ productId: product.id, quantity: 1 })}
              disabled={!product.inStock || addToCartMutation.isPending}
            >
              {!product.inStock ? 'Out of Stock' : 
               addToCartMutation.isPending ? 'Adding...' : 
               'Add to Cart'}
            </button>
          </div>
        ))}
      </div>
    </div>
  )
}

function ProductSkeleton() {
  return (
    <div className="product-card skeleton">
      <div className="skeleton-image" />
      <div className="skeleton-title" />
      <div className="skeleton-price" />
      <div className="skeleton-button" />
    </div>
  )
}
```

## Social Media Feed

Infinite scrolling feed with like/comment functionality:

```tsx
import { useInfiniteQuery, useMutation, useQueryClient } from '@tanstack/react-query'
import { useInView } from 'react-intersection-observer'

interface Post {
  id: number
  author: {
    name: string
    avatar: string
  }
  content: string
  likes: number
  comments: number
  timestamp: string
  liked: boolean
}

interface FeedResponse {
  posts: Post[]
  nextCursor: string | null
}

async function fetchFeed({ pageParam = '' }: { pageParam?: string }) {
  const response = await fetch(`/api/feed?cursor=${pageParam}`)
  if (!response.ok) throw new Error('Failed to fetch feed')
  return response.json() as Promise<FeedResponse>
}

async function likePost(postId: number) {
  const response = await fetch(`/api/posts/${postId}/like`, { method: 'POST' })
  return response.json()
}

function SocialFeed() {
  const queryClient = useQueryClient()
  const { ref, inView } = useInView()

  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    status
  } = useInfiniteQuery({
    queryKey: ['feed'],
    queryFn: fetchFeed,
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    initialPageParam: ''
  })

  const likeMutation = useMutation({
    mutationFn: likePost,
    onMutate: async (postId) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: ['feed'] })

      // Optimistically update
      queryClient.setQueryData(['feed'], (old: any) => {
        if (!old) return old

        return {
          ...old,
          pages: old.pages.map((page: FeedResponse) => ({
            ...page,
            posts: page.posts.map((post: Post) =>
              post.id === postId
                ? {
                    ...post,
                    liked: !post.liked,
                    likes: post.liked ? post.likes - 1 : post.likes + 1
                  }
                : post
            )
          }))
        }
      })
    },
    onError: () => {
      // Revert on error
      queryClient.invalidateQueries({ queryKey: ['feed'] })
    }
  })

  // Fetch more when scrolling to bottom
  useEffect(() => {
    if (inView && hasNextPage && !isFetchingNextPage) {
      fetchNextPage()
    }
  }, [inView, hasNextPage, isFetchingNextPage, fetchNextPage])

  if (status === 'pending') {
    return (
      <div className="feed">
        {Array.from({ length: 3 }).map((_, i) => (
          <PostSkeleton key={i} />
        ))}
      </div>
    )
  }

  if (status === 'error') {
    return <div>Error loading feed</div>
  }

  return (
    <div className="feed">
      {data.pages.map((page, pageIndex) => (
        <div key={pageIndex}>
          {page.posts.map((post) => (
            <article key={post.id} className="post">
              <div className="post-header">
                <img src={post.author.avatar} alt={post.author.name} />
                <div>
                  <h4>{post.author.name}</h4>
                  <time>{new Date(post.timestamp).toLocaleDateString()}</time>
                </div>
              </div>

              <p className="post-content">{post.content}</p>

              <div className="post-actions">
                <button
                  onClick={() => likeMutation.mutate(post.id)}
                  className={post.liked ? 'liked' : ''}
                  disabled={likeMutation.isPending}
                >
                  ❤️ {post.likes}
                </button>
                <button>💬 {post.comments}</button>
              </div>
            </article>
          ))}
        </div>
      ))}

      {hasNextPage && (
        <div ref={ref} className="loading-trigger">
          {isFetchingNextPage ? <div>Loading more...</div> : null}
        </div>
      )}

      {!hasNextPage && <div className="end-message">You're all caught up!</div>}
    </div>
  )
}

function PostSkeleton() {
  return (
    <article className="post skeleton">
      <div className="skeleton-avatar" />
      <div className="skeleton-content" />
      <div className="skeleton-actions" />
    </article>
  )
}
```

## Real-Time Dashboard

Live updating metrics dashboard with WebSocket:

```tsx
import { useState, useEffect } from 'react'
import { useQuery } from '@tanstack/react-query'

interface Metrics {
  activeUsers: number
  revenue: number
  orders: number
  conversionRate: number
}

interface HistoricalData {
  timestamp: string
  value: number
}

function Dashboard() {
  const [liveMetrics, setLiveMetrics] = useState<Metrics | null>(null)
  const [isConnected, setIsConnected] = useState(false)

  // Fetch historical data
  const { data: historical } = useQuery({
    queryKey: ['metrics-history'],
    queryFn: async () => {
      const response = await fetch('/api/metrics/history')
      return response.json() as Promise<HistoricalData[]>
    },
    refetchInterval: 60000 // Refresh every minute
  })

  // WebSocket for real-time updates
  useEffect(() => {
    const ws = new WebSocket('wss://api.example.com/metrics')

    ws.onopen = () => {
      setIsConnected(true)
      console.log('Connected to metrics stream')
    }

    ws.onmessage = (event) => {
      const metrics = JSON.parse(event.data)
      setLiveMetrics(metrics)
    }

    ws.onerror = (error) => {
      console.error('WebSocket error:', error)
    }

    ws.onclose = () => {
      setIsConnected(false)
      console.log('Disconnected from metrics stream')
    }

    return () => ws.close()
  }, [])

  return (
    <div className="dashboard">
      <div className="status">
        Status: {isConnected ? '🟢 Live' : '🔴 Offline'}
      </div>

      <div className="metrics-grid">
        <MetricCard
          title="Active Users"
          value={liveMetrics?.activeUsers || 0}
          format="number"
        />
        <MetricCard
          title="Revenue"
          value={liveMetrics?.revenue || 0}
          format="currency"
        />
        <MetricCard
          title="Orders"
          value={liveMetrics?.orders || 0}
          format="number"
        />
        <MetricCard
          title="Conversion Rate"
          value={liveMetrics?.conversionRate || 0}
          format="percent"
        />
      </div>

      {historical && (
        <div className="chart">
          <h3>Revenue Trend</h3>
          <LineChart data={historical} />
        </div>
      )}
    </div>
  )
}

function MetricCard({ title, value, format }: {
  title: string
  value: number
  format: 'number' | 'currency' | 'percent'
}) {
  const formatted = format === 'currency'
    ? `$${value.toLocaleString()}`
    : format === 'percent'
    ? `${(value * 100).toFixed(1)}%`
    : value.toLocaleString()

  return (
    <div className="metric-card">
      <h4>{title}</h4>
      <div className="value">{formatted}</div>
    </div>
  )
}
```

## Multi-Step Form with API Validation

Complex form with server-side validation:

```tsx
import { useState } from 'react'
import { useMutation } from '@tanstack/react-query'

interface FormData {
  step1: { email: string; password: string }
  step2: { firstName: string; lastName: string; phone: string }
  step3: { address: string; city: string; zip: string }
}

async function validateEmail(email: string) {
  const response = await fetch('/api/validate/email', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email })
  })
  const data = await response.json()
  if (!data.valid) throw new Error(data.message)
  return data
}

async function submitRegistration(data: FormData) {
  const response = await fetch('/api/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data)
  })
  if (!response.ok) throw new Error('Registration failed')
  return response.json()
}

function MultiStepForm() {
  const [step, setStep] = useState(1)
  const [formData, setFormData] = useState<Partial<FormData>>({})

  const emailValidation = useMutation({
    mutationFn: validateEmail
  })

  const registration = useMutation({
    mutationFn: submitRegistration
  })

  const handleStep1Submit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const form = e.currentTarget
    const email = (form.elements.namedItem('email') as HTMLInputElement).value
    const password = (form.elements.namedItem('password') as HTMLInputElement).value

    try {
      await emailValidation.mutateAsync(email)
      setFormData({ ...formData, step1: { email, password } })
      setStep(2)
    } catch (err) {
      console.error('Email validation failed:', err)
    }
  }

  const handleStep2Submit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const form = e.currentTarget
    setFormData({
      ...formData,
      step2: {
        firstName: (form.elements.namedItem('firstName') as HTMLInputElement).value,
        lastName: (form.elements.namedItem('lastName') as HTMLInputElement).value,
        phone: (form.elements.namedItem('phone') as HTMLInputElement).value
      }
    })
    setStep(3)
  }

  const handleStep3Submit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const form = e.currentTarget
    
    const finalData = {
      ...formData,
      step3: {
        address: (form.elements.namedItem('address') as HTMLInputElement).value,
        city: (form.elements.namedItem('city') as HTMLInputElement).value,
        zip: (form.elements.namedItem('zip') as HTMLInputElement).value
      }
    } as FormData

    try {
      await registration.mutateAsync(finalData)
      // Success! Redirect or show success message
    } catch (err) {
      console.error('Registration failed:', err)
    }
  }

  return (
    <div className="multi-step-form">
      <div className="progress">
        <div className={`step ${step >= 1 ? 'active' : ''}`}>1. Account</div>
        <div className={`step ${step >= 2 ? 'active' : ''}`}>2. Profile</div>
        <div className={`step ${step >= 3 ? 'active' : ''}`}>3. Address</div>
      </div>

      {step === 1 && (
        <form onSubmit={handleStep1Submit}>
          <h2>Create Account</h2>
          <input
            name="email"
            type="email"
            placeholder="Email"
            required
          />
          <input
            name="password"
            type="password"
            placeholder="Password"
            minLength={8}
            required
          />
          {emailValidation.isError && (
            <div className="error">{emailValidation.error.message}</div>
          )}
          <button type="submit" disabled={emailValidation.isPending}>
            {emailValidation.isPending ? 'Checking...' : 'Next'}
          </button>
        </form>
      )}

      {step === 2 && (
        <form onSubmit={handleStep2Submit}>
          <h2>Personal Information</h2>
          <input name="firstName" placeholder="First Name" required />
          <input name="lastName" placeholder="Last Name" required />
          <input name="phone" type="tel" placeholder="Phone" required />
          <div className="buttons">
            <button type="button" onClick={() => setStep(1)}>Back</button>
            <button type="submit">Next</button>
          </div>
        </form>
      )}

      {step === 3 && (
        <form onSubmit={handleStep3Submit}>
          <h2>Address</h2>
          <input name="address" placeholder="Street Address" required />
          <input name="city" placeholder="City" required />
          <input name="zip" placeholder="ZIP Code" required />
          {registration.isError && (
            <div className="error">{registration.error.message}</div>
          )}
          <div className="buttons">
            <button type="button" onClick={() => setStep(2)}>Back</button>
            <button type="submit" disabled={registration.isPending}>
              {registration.isPending ? 'Submitting...' : 'Complete Registration'}
            </button>
          </div>
          {registration.isSuccess && (
            <div className="success">Registration successful!</div>
          )}
        </form>
      )}
    </div>
  )
}
```

## Best Practices Demonstrated

These examples showcase:

1. **Proper error handling** with user-friendly messages
2. **Loading states** with skeleton screens
3. **Optimistic updates** for better UX
4. **Real-time data** with WebSockets
5. **Infinite scrolling** with intersection observer
6. **Form validation** with server-side checks
7. **Cache invalidation** after mutations
8. **TypeScript** for type safety
9. **Accessibility** considerations
10. **Performance optimization** with proper caching

## Summary

These real-world examples demonstrate how to combine the patterns and techniques from earlier sections into complete, production-ready implementations.

**Key Takeaways:**

1. Combine multiple patterns for complex features
2. Always handle loading and error states
3. Use optimistic updates for better UX
4. Implement proper caching strategies
5. Consider real-time needs with WebSockets
6. Validate data on both client and server
7. Use TypeScript for better developer experience
8. Test all user flows thoroughly

## Conclusion

You now have a comprehensive understanding of modern data fetching in React, from basic patterns to production-ready implementations. Apply these techniques to build fast, reliable, and user-friendly applications.

**Continue Learning:**

- Experiment with these examples in your projects
- Explore the official documentation for React Query, SWR, or Apollo
- Build your own custom solutions for unique requirements
- Stay updated with evolving best practices in the React ecosystem
