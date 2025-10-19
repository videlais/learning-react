---
title: "Modern Data Fetching Libraries"
parent: "Modern Data Fetching in React"
layout: chapter
permalink: /chapters/chapter10/libraries/
---

## Modern Data Fetching Libraries

Modern data fetching libraries provide powerful abstractions that handle caching, synchronization, and optimization automatically. Learn when and how to use them effectively.

## Overview

Popular libraries covered:

- React Query (TanStack Query)
- SWR (stale-while-revalidate)
- Apollo Client (GraphQL)
- RTK Query (Redux Toolkit)

## React Query (TanStack Query)

The most popular data fetching library for React.

### Basic Setup

```tsx
import { QueryClient, QueryClientProvider, useQuery } from '@tanstack/react-query'

// Create a client
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      cacheTime: 10 * 60 * 1000, // 10 minutes
      refetchOnWindowFocus: false
    }
  }
})

// Wrap your app
function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourApp />
    </QueryClientProvider>
  )
}
```

### Basic Query

```tsx
import { useQuery } from '@tanstack/react-query'

interface User {
  id: number
  name: string
  email: string
}

function UserProfile({ userId }: { userId: number }) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['user', userId],
    queryFn: async () => {
      const response = await fetch(`/api/users/${userId}`)
      if (!response.ok) throw new Error('Failed to fetch user')
      return response.json() as Promise<User>
    }
  })

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>
  if (!data) return null

  return (
    <div>
      <h2>{data.name}</h2>
      <p>{data.email}</p>
    </div>
  )
}
```

### Mutations

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

interface CreatePostData {
  title: string
  body: string
}

function CreatePost() {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: async (newPost: CreatePostData) => {
      const response = await fetch('/api/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newPost)
      })
      return response.json()
    },
    onSuccess: () => {
      // Invalidate and refetch
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    }
  })

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    mutation.mutate({
      title: formData.get('title') as string,
      body: formData.get('body') as string
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="title" placeholder="Title" required />
      <textarea name="body" placeholder="Body" required />
      <button type="submit" disabled={mutation.isPending}>
        {mutation.isPending ? 'Creating...' : 'Create Post'}
      </button>
      {mutation.isError && <div>Error: {mutation.error.message}</div>}
      {mutation.isSuccess && <div>Post created!</div>}
    </form>
  )
}
```

### Optimistic Updates

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

interface Todo {
  id: number
  text: string
  completed: boolean
}

function useTodoMutations() {
  const queryClient = useQueryClient()

  const toggleMutation = useMutation({
    mutationFn: async (id: number) => {
      const response = await fetch(`/api/todos/${id}/toggle`, {
        method: 'POST'
      })
      return response.json()
    },
    onMutate: async (id) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: ['todos'] })

      // Snapshot previous value
      const previousTodos = queryClient.getQueryData<Todo[]>(['todos'])

      // Optimistically update
      queryClient.setQueryData<Todo[]>(['todos'], (old) =>
        old?.map(todo =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
      )

      return { previousTodos }
    },
    onError: (err, id, context) => {
      // Rollback on error
      queryClient.setQueryData(['todos'], context?.previousTodos)
    },
    onSettled: () => {
      // Always refetch after error or success
      queryClient.invalidateQueries({ queryKey: ['todos'] })
    }
  })

  return { toggleMutation }
}
```

### Infinite Queries

```tsx
import { useInfiniteQuery } from '@tanstack/react-query'

interface PostsResponse {
  posts: Post[]
  nextCursor: number | null
}

function InfinitePosts() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    status
  } = useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: async ({ pageParam = 0 }) => {
      const response = await fetch(`/api/posts?cursor=${pageParam}`)
      return response.json() as Promise<PostsResponse>
    },
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    initialPageParam: 0
  })

  if (status === 'pending') return <div>Loading...</div>
  if (status === 'error') return <div>Error loading posts</div>

  return (
    <div>
      {data.pages.map((page, i) => (
        <div key={i}>
          {page.posts.map(post => (
            <article key={post.id}>
              <h3>{post.title}</h3>
            </article>
          ))}
        </div>
      ))}
      
      {hasNextPage && (
        <button
          onClick={() => fetchNextPage()}
          disabled={isFetchingNextPage}
        >
          {isFetchingNextPage ? 'Loading more...' : 'Load More'}
        </button>
      )}
    </div>
  )
}
```

## SWR

Lightweight alternative focused on simplicity.

### Basic SWR Setup

```tsx
import { SWRConfig } from 'swr'

function App() {
  return (
    <SWRConfig
      value={% raw %}{{
        refreshInterval: 3000,
        fetcher: (url: string) => fetch(url).then(res => res.json())
      }}{% endraw %}
    >
      <YourApp />
    </SWRConfig>
  )
}
```

### Basic Usage

```tsx
import useSWR from 'swr'

interface User {
  id: number
  name: string
}

function UserProfile({ userId }: { userId: number }) {
  const { data, error, isLoading } = useSWR<User>(
    `/api/users/${userId}`
  )

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>
  if (!data) return null

  return (
    <div>
      <h2>{data.name}</h2>
    </div>
  )
}
```

### Mutations with SWR

```tsx
import useSWR, { useSWRConfig } from 'swr'

function TodoList() {
  const { data: todos } = useSWR<Todo[]>('/api/todos')
  const { mutate } = useSWRConfig()

  const addTodo = async (text: string) => {
    // Optimistic update
    mutate('/api/todos', [...(todos || []), { id: Date.now(), text, completed: false }], false)

    // Send to server
    await fetch('/api/todos', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ text })
    })

    // Revalidate
    mutate('/api/todos')
  }

  return (
    <div>
      {todos?.map(todo => (
        <div key={todo.id}>{todo.text}</div>
      ))}
    </div>
  )
}
```

### Dependent Fetching

```tsx
import useSWR from 'swr'

function PostWithAuthor({ postId }: { postId: number }) {
  const { data: post } = useSWR(`/api/posts/${postId}`)
  
  // Only fetch author when post is loaded
  const { data: author } = useSWR(
    post ? `/api/users/${post.authorId}` : null
  )

  if (!post) return <div>Loading post...</div>
  if (!author) return <div>Loading author...</div>

  return (
    <article>
      <h2>{post.title}</h2>
      <p>By {author.name}</p>
    </article>
  )
}
```

## Apollo Client (GraphQL)

For GraphQL APIs.

### Setup

```tsx
import { ApolloClient, InMemoryCache, ApolloProvider, gql } from '@apollo/client'

const client = new ApolloClient({
  uri: 'https://api.example.com/graphql',
  cache: new InMemoryCache()
})

function App() {
  return (
    <ApolloProvider client={client}>
      <YourApp />
    </ApolloProvider>
  )
}
```

### Query

```tsx
import { useQuery, gql } from '@apollo/client'

const GET_USERS = gql`
  query GetUsers {
    users {
      id
      name
      email
    }
  }
`

function UserList() {
  const { loading, error, data } = useQuery(GET_USERS)

  if (loading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <ul>
      {data.users.map((user: User) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

### Mutation

```tsx
import { useMutation, gql } from '@apollo/client'

const CREATE_USER = gql`
  mutation CreateUser($name: String!, $email: String!) {
    createUser(name: $name, email: $email) {
      id
      name
      email
    }
  }
`

function CreateUser() {
  const [createUser, { data, loading, error }] = useMutation(CREATE_USER)

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault()
    const formData = new FormData(e.currentTarget)
    
    createUser({
      variables: {
        name: formData.get('name'),
        email: formData.get('email')
      }
    })
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" placeholder="Name" required />
      <input name="email" type="email" placeholder="Email" required />
      <button type="submit" disabled={loading}>
        {loading ? 'Creating...' : 'Create User'}
      </button>
      {error && <div>Error: {error.message}</div>}
      {data && <div>User created: {data.createUser.name}</div>}
    </form>
  )
}
```

## RTK Query (Redux Toolkit)

Integrated with Redux.

### Redux Setup

```tsx
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'

export const api = createApi({
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
  endpoints: (builder) => ({
    getUsers: builder.query<User[], void>({
      query: () => 'users'
    }),
    getUserById: builder.query<User, number>({
      query: (id) => `users/${id}`
    }),
    createUser: builder.mutation<User, Partial<User>>({
      query: (body) => ({
        url: 'users',
        method: 'POST',
        body
      })
    })
  })
})

export const { useGetUsersQuery, useGetUserByIdQuery, useCreateUserMutation } = api
```

### Usage

```tsx
import { useGetUsersQuery, useCreateUserMutation } from './api'

function UserManager() {
  const { data: users, isLoading, error } = useGetUsersQuery()
  const [createUser, { isLoading: isCreating }] = useCreateUserMutation()

  const handleCreate = async (name: string, email: string) => {
    await createUser({ name, email })
  }

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error loading users</div>

  return (
    <div>
      <ul>
        {users?.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  )
}
```

## Comparison

| Feature | React Query | SWR | Apollo | RTK Query |
|---------|-------------|-----|--------|-----------|
| Learning Curve | Medium | Easy | Hard | Medium |
| Bundle Size | Medium | Small | Large | Medium |
| GraphQL Support | Manual | Manual | Native | Manual |
| Redux Integration | No | No | No | Native |
| Devtools | ✅ | ✅ | ✅ | ✅ |
| TypeScript | Excellent | Good | Excellent | Excellent |
| Caching | Advanced | Basic | Advanced | Advanced |
| Best For | REST APIs | Simple apps | GraphQL | Redux apps |

## When to Use Libraries

**Use a library when:**

- Building production applications
- Need automatic caching and revalidation
- Require optimistic updates
- Want devtools for debugging
- Need automatic request deduplication

**Stick with custom hooks when:**

- Building simple applications
- Learning React fundamentals
- Have very specific requirements
- Want minimal bundle size
- Don't need advanced caching

## Best Practices

1. **Choose based on your stack** - GraphQL? Use Apollo. Redux? Use RTK Query.
2. **Configure appropriately** - Set stale times and cache policies
3. **Use TypeScript** - All libraries have excellent type support
4. **Leverage devtools** - Debug queries and mutations easily
5. **Test properly** - Mock library hooks in tests
6. **Monitor bundle size** - Some libraries are large
7. **Read the docs** - Each library has specific patterns

## Summary

Modern data fetching libraries eliminate boilerplate and provide powerful features out of the box. Choose the right library for your use case and leverage its full capabilities.

**Next Steps:**

- Learn about [Performance Optimization](./performance/) techniques
- See [Real-World Examples](./examples/) of complete implementations

**Key Takeaways:**

1. Libraries handle caching and synchronization automatically
2. React Query is the most popular general-purpose solution
3. SWR offers simplicity and small bundle size
4. Apollo Client is best for GraphQL
5. RTK Query integrates seamlessly with Redux
6. Choose based on your specific needs and existing stack
