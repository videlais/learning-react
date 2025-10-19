---
title: "Modern Data Fetching in React"
order: 10
chapter_number: 10
layout: chapter
redirect_to: "/chapters/chapter10/"
---

## Chapter 10: Modern Data Fetching in React

This chapter has been split into multiple sections for better accessibility and navigation.

**[Go to Chapter 10 →](/chapters/chapter10/)**

## Quick Links

- [Modern Data Fetching with useEffect](/chapters/chapter10/basic-patterns/)
- [Custom Data Fetching Hooks](/chapters/chapter10/custom-hooks/)
- [Error Handling and Loading States](/chapters/chapter10/error-loading/)
- [Advanced Data Fetching Patterns](/chapters/chapter10/advanced-patterns/)
- [Modern Data Fetching Libraries](/chapters/chapter10/libraries/)
- [Performance Optimization](/chapters/chapter10/performance/)
- [Real-World Examples](/chapters/chapter10/examples/)

## Objectives

In this chapter, readers will:

- **Implement** modern data fetching patterns using React hooks and the Fetch API
- **Handle** loading states, errors, and data updates effectively in React applications
- **Optimize** data fetching with caching, deduplication, and performance best practices
- **Integrate** modern data fetching libraries and understand their benefits over manual approaches
- **Apply** advanced patterns like parallel fetching, pagination, and real-time data updates

## Chapter Outline

- [Chapter 10: Modern Data Fetching in React](#chapter-10-modern-data-fetching-in-react)
- [Quick Links](#quick-links)
- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Modern Data Fetching with useEffect](#modern-data-fetching-with-useeffect)
  - [Basic Data Fetching Pattern](#basic-data-fetching-pattern)
  - [Key Principles](#key-principles)
  - [Advanced Pattern with Cleanup](#advanced-pattern-with-cleanup)
- [Building a Complete Data Fetching Hook](#building-a-complete-data-fetching-hook)
  - [Custom useFetch Hook](#custom-usefetch-hook)
  - [Enhanced Hook with Caching](#enhanced-hook-with-caching)
- [Error Handling and Loading States](#error-handling-and-loading-states)
  - [Comprehensive Error Handling](#comprehensive-error-handling)
  - [Loading States with Skeletons](#loading-states-with-skeletons)
- [Advanced Data Fetching Patterns](#advanced-data-fetching-patterns)
  - [Parallel Data Fetching](#parallel-data-fetching)
  - [Pagination with Data Fetching](#pagination-with-data-fetching)
  - [Real-time Data with WebSockets](#real-time-data-with-websockets)
- [Modern Data Fetching Libraries](#modern-data-fetching-libraries)
  - [React Query (TanStack Query)](#react-query-tanstack-query)
  - [SWR (Stale-While-Revalidate)](#swr-stale-while-revalidate)
  - [Apollo Client (for GraphQL)](#apollo-client-for-graphql)
- [Performance Optimization](#performance-optimization)
  - [Debounced Search](#debounced-search)
  - [Request Deduplication](#request-deduplication)
- [Real-World Examples](#real-world-examples)
  - [E-commerce Product Catalog](#e-commerce-product-catalog)
  - [Social Media Feed with Optimistic Updates](#social-media-feed-with-optimistic-updates)
- [Best Practices and Common Pitfalls](#best-practices-and-common-pitfalls)
  - [Do's and Don'ts](#dos-and-donts)
  - [Common Mistakes and Solutions](#common-mistakes-and-solutions)
  - [Summary](#summary)

## Modern Data Fetching with useEffect

**2025 Best Practice:** Use function components with hooks for all data fetching operations. Class components and lifecycle methods are legacy patterns.

### Basic Data Fetching Pattern

The modern approach uses `useEffect` with proper dependency management and state handling:

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Reset states when userId changes
    setLoading(true);
    setError(null);

    async function fetchUser() {
      try {
        const response = await fetch(`/api/users/${userId}`);
        
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();
  }, [userId]); // Re-fetch when userId changes

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
```

### Key Principles

1. **Proper Cleanup**: Use AbortController to cancel requests when component unmounts
2. **Dependency Arrays**: Include all dependencies in useEffect dependency array
3. **Error Boundaries**: Handle both network and application errors
4. **Loading States**: Provide clear feedback to users during data fetching

### Advanced Pattern with Cleanup

```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher({ endpoint }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(endpoint, {
          signal: abortController.signal,
          headers: {
            'Content-Type': 'application/json',
          },
        });

        if (!response.ok) {
          throw new Error(`Failed to fetch: ${response.status} ${response.statusText}`);
        }

        const result = await response.json();
        
        // Only update state if request wasn't cancelled
        if (!abortController.signal.aborted) {
          setData(result);
        }
      } catch (err) {
        // Don't set error if request was cancelled
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        if (!abortController.signal.aborted) {
          setLoading(false);
        }
      }
    }

    fetchData();

    // Cleanup function cancels the request
    return () => {
      abortController.abort();
    };
  }, [endpoint]);

  return { data, loading, error };
}
```

## Building a Complete Data Fetching Hook

### Custom useFetch Hook

Create reusable data fetching logic with a custom hook:

```javascript
import { useState, useEffect, useRef } from 'react';

function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  // Use ref to store the latest options to avoid recreating effect
  const optionsRef = useRef(options);
  optionsRef.current = options;

  useEffect(() => {
    if (!url) return;

    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          ...optionsRef.current,
          signal: abortController.signal,
        });

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }

        const contentType = response.headers.get('content-type');
        let result;
        
        if (contentType && contentType.includes('application/json')) {
          result = await response.json();
        } else {
          result = await response.text();
        }

        if (!abortController.signal.aborted) {
          setData(result);
        }
      } catch (err) {
        if (err.name !== 'AbortError' && !abortController.signal.aborted) {
          setError(err.message);
        }
      } finally {
        if (!abortController.signal.aborted) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => {
      abortController.abort();
    };
  }, [url]);

  const refetch = () => {
    setLoading(true);
    setError(null);
    // Trigger re-fetch by updating a dependency
  };

  return { data, loading, error, refetch };
}

// Usage
function ProductList() {
  const { data: products, loading, error, refetch } = useFetch('/api/products');

  if (loading) return <div>Loading products...</div>;
  if (error) return <div>Error: {error} <button onClick={refetch}>Retry</button></div>;

  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      <ul>
        {products?.map(product => (
          <li key={product.id}>{product.name} - ${product.price}</li>
        ))}
      </ul>
    </div>
  );
}
```

### Enhanced Hook with Caching

```javascript
import { useState, useEffect, useRef } from 'react';

// Simple in-memory cache
const cache = new Map();

function useFetchWithCache(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  const cacheKey = `${url}${JSON.stringify(options)}`;

  useEffect(() => {
    if (!url) return;

    // Check cache first
    if (cache.has(cacheKey)) {
      setData(cache.get(cacheKey));
      setLoading(false);
      return;
    }

    const abortController = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        
        const response = await fetch(url, {
          ...options,
          signal: abortController.signal,
        });

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }

        const result = await response.json();

        if (!abortController.signal.aborted) {
          // Cache the result
          cache.set(cacheKey, result);
          setData(result);
        }
      } catch (err) {
        if (err.name !== 'AbortError' && !abortController.signal.aborted) {
          setError(err.message);
        }
      } finally {
        if (!abortController.signal.aborted) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => {
      abortController.abort();
    };
  }, [url, cacheKey]);

  return { data, loading, error };
}
```

## Error Handling and Loading States

### Comprehensive Error Handling

```javascript
import React, { useState, useEffect } from 'react';

function DataComponent({ endpoint }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [retryCount, setRetryCount] = useState(0);

  const maxRetries = 3;
  const retryDelay = 1000; // 1 second

  useEffect(() => {
    async function fetchWithRetry() {
      const abortController = new AbortController();
      
      for (let attempt = 0; attempt <= maxRetries; attempt++) {
        try {
          setLoading(true);
          setError(null);
          
          const response = await fetch(endpoint, {
            signal: abortController.signal,
          });

          if (!response.ok) {
            // Handle different HTTP status codes
            if (response.status === 404) {
              throw new Error('Data not found');
            } else if (response.status === 401) {
              throw new Error('Unauthorized access');
            } else if (response.status >= 500) {
              throw new Error('Server error - please try again later');
            } else {
              throw new Error(`Request failed: ${response.statusText}`);
            }
          }

          const result = await response.json();
          setData(result);
          setRetryCount(0); // Reset retry count on success
          return;
          
        } catch (err) {
          if (err.name === 'AbortError') return;
          
          if (attempt === maxRetries) {
            setError(`Failed after ${maxRetries + 1} attempts: ${err.message}`);
          } else {
            // Wait before retrying
            await new Promise(resolve => setTimeout(resolve, retryDelay * (attempt + 1)));
            setRetryCount(attempt + 1);
          }
        } finally {
          setLoading(false);
        }
      }
    }

    fetchWithRetry();
  }, [endpoint]);

  if (loading) {
    return (
      <div>
        Loading...
        {retryCount > 0 && <p>Retry attempt {retryCount}/{maxRetries}</p>}
      </div>
    );
  }

  if (error) {
    return (
      <div>
        <p>Error: {error}</p>
        <button onClick={() => window.location.reload()}>Retry</button>
      </div>
    );
  }

  return <div>{/* Render data */}</div>;
}
```

### Loading States with Skeletons

```javascript
import React from 'react';

function LoadingSkeleton() {
  return (
    <div className="animate-pulse">
      <div className="h-4 bg-gray-300 rounded w-3/4 mb-2"></div>
      <div className="h-4 bg-gray-300 rounded w-1/2 mb-2"></div>
      <div className="h-4 bg-gray-300 rounded w-5/6"></div>
    </div>
  );
}

function UserList() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) {
    return (
      <div>
        {Array.from({ length: 5 }).map((_, i) => (
          <LoadingSkeleton key={i} />
        ))}
      </div>
    );
  }

  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

## Advanced Data Fetching Patterns

### Parallel Data Fetching

Fetch multiple resources simultaneously for better performance:

```javascript
import React, { useState, useEffect } from 'react';

function Dashboard() {
  const [data, setData] = useState({
    user: null,
    posts: null,
    notifications: null
  });
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchDashboardData() {
      try {
        setLoading(true);
        
        // Fetch all data in parallel
        const [userResponse, postsResponse, notificationsResponse] = await Promise.all([
          fetch('/api/user'),
          fetch('/api/posts'),
          fetch('/api/notifications')
        ]);

        // Check all responses
        if (!userResponse.ok || !postsResponse.ok || !notificationsResponse.ok) {
          throw new Error('One or more requests failed');
        }

        // Parse all responses in parallel
        const [userData, postsData, notificationsData] = await Promise.all([
          userResponse.json(),
          postsResponse.json(),
          notificationsResponse.json()
        ]);

        setData({
          user: userData,
          posts: postsData,
          notifications: notificationsData
        });
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchDashboardData();
  }, []);

  if (loading) return <div>Loading dashboard...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>Welcome, {data.user?.name}!</h1>
      <p>You have {data.posts?.length} posts</p>
      <p>You have {data.notifications?.length} notifications</p>
    </div>
  );
}
```

### Pagination with Data Fetching

```javascript
import React, { useState, useEffect } from 'react';

function usePagination(baseUrl, pageSize = 10) {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [page, setPage] = useState(1);
  const [hasMore, setHasMore] = useState(true);
  const [totalCount, setTotalCount] = useState(0);

  useEffect(() => {
    async function fetchPage() {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch(
          `${baseUrl}?page=${page}&limit=${pageSize}`
        );

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }

        const result = await response.json();
        
        if (page === 1) {
          setData(result.data);
        } else {
          setData(prev => [...prev, ...result.data]);
        }
        
        setTotalCount(result.total);
        setHasMore(result.data.length === pageSize && data.length + result.data.length < result.total);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchPage();
  }, [baseUrl, page, pageSize]);

  const loadMore = () => {
    if (!loading && hasMore) {
      setPage(prev => prev + 1);
    }
  };

  const reset = () => {
    setPage(1);
    setData([]);
    setHasMore(true);
    setError(null);
  };

  return {
    data,
    loading,
    error,
    hasMore,
    totalCount,
    loadMore,
    reset,
    currentPage: page
  };
}

function InfiniteScrollList() {
  const {
    data: products,
    loading,
    error,
    hasMore,
    loadMore
  } = usePagination('/api/products', 20);

  return (
    <div>
      <ul>
        {products.map(product => (
          <li key={product.id}>{product.name}</li>
        ))}
      </ul>
      
      {hasMore && (
        <button onClick={loadMore} disabled={loading}>
          {loading ? 'Loading...' : 'Load More'}
        </button>
      )}
      
      {error && <div>Error: {error}</div>}
    </div>
  );
}
```

### Real-time Data with WebSockets

```javascript
import React, { useState, useEffect, useRef } from 'react';

function useWebSocket(url) {
  const [data, setData] = useState(null);
  const [connectionStatus, setConnectionStatus] = useState('Connecting');
  const ws = useRef(null);

  useEffect(() => {
    ws.current = new WebSocket(url);
    
    ws.current.onopen = () => {
      setConnectionStatus('Connected');
    };
    
    ws.current.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setData(message);
    };
    
    ws.current.onclose = () => {
      setConnectionStatus('Disconnected');
    };
    
    ws.current.onerror = () => {
      setConnectionStatus('Error');
    };

    return () => {
      ws.current.close();
    };
  }, [url]);

  const sendMessage = (message) => {
    if (ws.current.readyState === WebSocket.OPEN) {
      ws.current.send(JSON.stringify(message));
    }
  };

  return { data, connectionStatus, sendMessage };
}

function LiveChat() {
  const [messages, setMessages] = useState([]);
  const { data, connectionStatus, sendMessage } = useWebSocket('ws://localhost:8080/chat');

  useEffect(() => {
    if (data) {
      setMessages(prev => [...prev, data]);
    }
  }, [data]);

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

## Modern Data Fetching Libraries

**2025 Recommendation:** For production applications, consider using specialized data fetching libraries instead of manual `fetch` implementations.

### React Query (TanStack Query)

React Query provides powerful data synchronization and caching:

```javascript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Basic query
function UserProfile({ userId }) {
  const { data: user, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetch(`/api/users/${userId}`).then(res => res.json()),
    staleTime: 5 * 60 * 1000, // 5 minutes
    cacheTime: 10 * 60 * 1000, // 10 minutes
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return <div>Welcome, {user.name}!</div>;
}

// Mutation example
function CreatePost() {
  const queryClient = useQueryClient();
  
  const mutation = useMutation({
    mutationFn: (newPost) => 
      fetch('/api/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newPost)
      }).then(res => res.json()),
    onSuccess: () => {
      // Invalidate and refetch posts
      queryClient.invalidateQueries(['posts']);
    },
  });

  return (
    <button
      onClick={() => mutation.mutate({ title: 'New Post', content: 'Hello World' })}
      disabled={mutation.isLoading}
    >
      {mutation.isLoading ? 'Creating...' : 'Create Post'}
    </button>
  );
}
```

### SWR (Stale-While-Revalidate)

SWR provides a simpler API for data fetching:

```javascript
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

function UserList() {
  const { data: users, error, isLoading } = useSWR('/api/users', fetcher, {
    refreshInterval: 30000, // Refresh every 30 seconds
    revalidateOnFocus: true,
    revalidateOnReconnect: true,
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Apollo Client (for GraphQL)

For GraphQL APIs, Apollo Client is the gold standard:

```javascript
import { useQuery, gql } from '@apollo/client';

const GET_USERS = gql`
  query GetUsers($limit: Int) {
    users(limit: $limit) {
      id
      name
      email
      posts {
        id
        title
      }
    }
  }
`;

function UserList() {
  const { loading, error, data, refetch } = useQuery(GET_USERS, {
    variables: { limit: 10 },
    errorPolicy: 'all',
    fetchPolicy: 'cache-and-network',
  });

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <button onClick={() => refetch()}>Refresh</button>
      <ul>
        {data?.users?.map(user => (
          <li key={user.id}>
            {user.name} - {user.posts.length} posts
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Performance Optimization

### Debounced Search

Optimize search queries to avoid excessive API calls:

```javascript
import React, { useState, useEffect, useMemo } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  
  const debouncedQuery = useDebounce(query, 500);

  useEffect(() => {
    if (!debouncedQuery.trim()) {
      setResults([]);
      return;
    }

    async function search() {
      setLoading(true);
      try {
        const response = await fetch(`/api/search?q=${encodeURIComponent(debouncedQuery)}`);
        const data = await response.json();
        setResults(data);
      } catch (error) {
        console.error('Search failed:', error);
      } finally {
        setLoading(false);
      }
    }

    search();
  }, [debouncedQuery]);

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

### Request Deduplication

Prevent duplicate requests for the same resource:

```javascript
const requestCache = new Map();

function useDedupedFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;

    // Check if request is already in progress
    if (requestCache.has(url)) {
      requestCache.get(url).then(result => {
        setData(result);
        setLoading(false);
      }).catch(err => {
        setError(err.message);
        setLoading(false);
      });
      return;
    }

    // Create new request promise
    const requestPromise = fetch(url).then(res => {
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return res.json();
    });

    // Cache the promise
    requestCache.set(url, requestPromise);

    requestPromise
      .then(result => {
        setData(result);
        setLoading(false);
        // Keep in cache for 5 minutes
        setTimeout(() => requestCache.delete(url), 5 * 60 * 1000);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
        requestCache.delete(url); // Remove failed requests from cache
      });

    return () => {
      // Cleanup would be more complex in real implementation
    };
  }, [url]);

  return { data, loading, error };
}
```

## Real-World Examples

### E-commerce Product Catalog

A complete example showing modern data fetching patterns:

```javascript
import React, { useState, useEffect } from 'react';

function useProducts(filters = {}) {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [totalCount, setTotalCount] = useState(0);

  useEffect(() => {
    const abortController = new AbortController();

    async function fetchProducts() {
      try {
        setLoading(true);
        setError(null);

        const params = new URLSearchParams({
          category: filters.category || '',
          minPrice: filters.minPrice || '',
          maxPrice: filters.maxPrice || '',
          sort: filters.sort || 'name',
          page: filters.page || 1,
          limit: filters.limit || 20
        });

        const response = await fetch(`/api/products?${params}`, {
          signal: abortController.signal,
        });

        if (!response.ok) {
          throw new Error(`Failed to fetch products: ${response.statusText}`);
        }

        const data = await response.json();
        setProducts(data.products);
        setTotalCount(data.total);
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchProducts();

    return () => abortController.abort();
  }, [
    filters.category,
    filters.minPrice,
    filters.maxPrice,
    filters.sort,
    filters.page,
    filters.limit
  ]);

  return { products, loading, error, totalCount };
}

function ProductCatalog() {
  const [filters, setFilters] = useState({
    category: '',
    minPrice: '',
    maxPrice: '',
    sort: 'name',
    page: 1
  });

  const { products, loading, error, totalCount } = useProducts(filters);

  const updateFilter = (key, value) => {
    setFilters(prev => ({
      ...prev,
      [key]: value,
      page: key !== 'page' ? 1 : value // Reset to page 1 when changing filters
    }));
  };

  if (error) {
    return <div className="error">Error: {error}</div>;
  }

  return (
    <div className="product-catalog">
      <div className="filters">
        <select
          value={filters.category}
          onChange={(e) => updateFilter('category', e.target.value)}
        >
          <option value="">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
          <option value="books">Books</option>
        </select>

        <input
          type="number"
          placeholder="Min Price"
          value={filters.minPrice}
          onChange={(e) => updateFilter('minPrice', e.target.value)}
        />

        <input
          type="number"
          placeholder="Max Price"
          value={filters.maxPrice}
          onChange={(e) => updateFilter('maxPrice', e.target.value)}
        />

        <select
          value={filters.sort}
          onChange={(e) => updateFilter('sort', e.target.value)}
        >
          <option value="name">Sort by Name</option>
          <option value="price-asc">Price: Low to High</option>
          <option value="price-desc">Price: High to Low</option>
        </select>
      </div>

      {loading ? (
        <div className="loading">Loading products...</div>
      ) : (
        <div>
          <p>{totalCount} products found</p>
          <div className="product-grid">
            {products.map(product => (
              <div key={product.id} className="product-card">
                <img src={product.image} alt={product.name} />
                <h3>{product.name}</h3>
                <p>${product.price}</p>
                <button>Add to Cart</button>
              </div>
            ))}
          </div>

          <div className="pagination">
            <button
              onClick={() => updateFilter('page', filters.page - 1)}
              disabled={filters.page === 1}
            >
              Previous
            </button>
            <span>Page {filters.page}</span>
            <button
              onClick={() => updateFilter('page', filters.page + 1)}
              disabled={products.length < 20}
            >
              Next
            </button>
          </div>
        </div>
      )}
    </div>
  );
}
```

### Social Media Feed with Optimistic Updates

```javascript
import React, { useState, useEffect } from 'react';

function usePosts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetchPosts();
  }, []);

  async function fetchPosts() {
    try {
      setLoading(true);
      const response = await fetch('/api/posts');
      if (!response.ok) throw new Error('Failed to fetch posts');
      const data = await response.json();
      setPosts(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }

  const addPost = async (content) => {
    const tempId = Date.now();
    const tempPost = {
      id: tempId,
      content,
      author: 'You',
      createdAt: new Date().toISOString(),
      likes: 0,
      isOptimistic: true
    };

    // Optimistic update
    setPosts(prev => [tempPost, ...prev]);

    try {
      const response = await fetch('/api/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ content })
      });

      if (!response.ok) throw new Error('Failed to create post');
      
      const newPost = await response.json();
      
      // Replace optimistic post with real post
      setPosts(prev => 
        prev.map(post => 
          post.id === tempId ? { ...newPost, isOptimistic: false } : post
        )
      );
    } catch (err) {
      // Remove failed optimistic post
      setPosts(prev => prev.filter(post => post.id !== tempId));
      throw err;
    }
  };

  const likePost = async (postId) => {
    // Optimistic update
    setPosts(prev =>
      prev.map(post =>
        post.id === postId ? { ...post, likes: post.likes + 1 } : post
      )
    );

    try {
      await fetch(`/api/posts/${postId}/like`, { method: 'POST' });
    } catch (err) {
      // Revert optimistic update
      setPosts(prev =>
        prev.map(post =>
          post.id === postId ? { ...post, likes: post.likes - 1 } : post
        )
      );
    }
  };

  return { posts, loading, error, addPost, likePost, refetch: fetchPosts };
}

function SocialFeed() {
  const [newPostContent, setNewPostContent] = useState('');
  const [submitting, setSubmitting] = useState(false);
  const { posts, loading, error, addPost, likePost } = usePosts();

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!newPostContent.trim()) return;

    try {
      setSubmitting(true);
      await addPost(newPostContent);
      setNewPostContent('');
    } catch (err) {
      alert('Failed to create post: ' + err.message);
    } finally {
      setSubmitting(false);
    }
  };

  if (loading) return <div>Loading feed...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="social-feed">
      <form onSubmit={handleSubmit}>
        <textarea
          value={newPostContent}
          onChange={(e) => setNewPostContent(e.target.value)}
          placeholder="What's on your mind?"
          disabled={submitting}
        />
        <button type="submit" disabled={submitting || !newPostContent.trim()}>
          {submitting ? 'Posting...' : 'Post'}
        </button>
      </form>

      <div className="posts">
        {posts.map(post => (
          <div
            key={post.id}
            className={`post ${post.isOptimistic ? 'optimistic' : ''}`}
          >
            <h4>{post.author}</h4>
            <p>{post.content}</p>
            <div className="post-actions">
              <button onClick={() => likePost(post.id)}>
                👍 {post.likes}
              </button>
              <span>{new Date(post.createdAt).toLocaleDateString()}</span>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

## Best Practices and Common Pitfalls

### Do's and Don'ts

**✅ Best Practices:**

1. **Always handle loading and error states**
2. **Use AbortController for cleanup**
3. **Implement proper dependency arrays in useEffect**
4. **Consider using data fetching libraries for complex apps**
5. **Cache requests when appropriate**
6. **Provide meaningful error messages**
7. **Use optimistic updates for better UX**
8. **Implement retry logic for failed requests**

**❌ Common Pitfalls:**

1. **Missing dependency arrays (causes infinite loops)**
2. **Not handling cleanup (memory leaks)**
3. **Ignoring error states**
4. **Making requests in render functions**
5. **Not considering race conditions**
6. **Fetching data that's already available**
7. **Poor error handling**
8. **Not showing loading states**

### Common Mistakes and Solutions

**Mistake 1:** Infinite Loops

```javascript
// ❌ Wrong: Missing dependency array
useEffect(() => {
  fetchData();
}); // Runs on every render!

// ❌ Wrong: Missing dependencies
useEffect(() => {
  fetchData(userId);
}, []); // userId changes won't trigger refetch

// ✅ Correct: Proper dependencies
useEffect(() => {
  fetchData(userId);
}, [userId]);
```

**Mistake 2:** Race Conditions

```javascript
// ❌ Wrong: Race condition possible
useEffect(() => {
  async function fetchUser() {
    const user = await fetch(`/api/users/${userId}`).then(r => r.json());
    setUser(user); // May set stale data if userId changed
  }
  fetchUser();
}, [userId]);

// ✅ Correct: Handle race conditions
useEffect(() => {
  const abortController = new AbortController();
  
  async function fetchUser() {
    try {
      const response = await fetch(`/api/users/${userId}`, {
        signal: abortController.signal
      });
      const user = await response.json();
      
      if (!abortController.signal.aborted) {
        setUser(user);
      }
    } catch (err) {
      if (err.name !== 'AbortError') {
        setError(err.message);
      }
    }
  }
  
  fetchUser();
  return () => abortController.abort();
}, [userId]);
```

**Mistake 3:** Poor Error Handling

```javascript
// ❌ Wrong: Silent failures
useEffect(() => {
  fetch('/api/data')
    .then(res => res.json())
    .then(setData);
}, []);

// ✅ Correct: Proper error handling
useEffect(() => {
  async function fetchData() {
    try {
      const response = await fetch('/api/data');
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      const data = await response.json();
      setData(data);
    } catch (err) {
      setError(err.message);
      console.error('Failed to fetch data:', err);
    }
  }
  
  fetchData();
}, []);
```

### Summary

Modern React data fetching in 2025 emphasizes:

1. **Function components with hooks** over class components
2. **Proper error handling and loading states** for better UX
3. **Performance optimization** through caching and deduplication
4. **Modern libraries** like React Query or SWR for complex applications
5. **Clean patterns** that are maintainable and testable

The key is to choose the right tool for your use case: simple `fetch` with custom hooks for basic needs, or specialized libraries for more complex data synchronization requirements.
