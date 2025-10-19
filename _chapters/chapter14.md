---
title: "Routing and Navigation in React"
order: 14
chapter_number: 14
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Implement** client-side routing using React Router v6
- **Build** Next.js applications with App Router and file-based routing
- **Create** dynamic routes and handle route parameters
- **Protect** routes with authentication and authorization
- **Optimize** navigation with prefetching and loading states

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Introduction to React Routing](#introduction-to-react-routing)
- [React Router v6](#react-router-v6)
  - [Installation and Setup](#installation-and-setup)
    - [Step 1: Install React Router](#step-1-install-react-router)
    - [Step 2: Basic App Structure](#step-2-basic-app-structure)
  - [Basic Routing](#basic-routing)
    - [Simple Route Configuration](#simple-route-configuration)
    - [Navigation with NavLink](#navigation-with-navlink)
  - [Route Parameters](#route-parameters)
    - [Dynamic Routes](#dynamic-routes)
    - [Accessing Parameters](#accessing-parameters)
  - [Nested Routes](#nested-routes)
    - [Layout with Nested Routes](#layout-with-nested-routes)
    - [Layout Component with Outlet](#layout-component-with-outlet)
  - [Navigation](#navigation)
    - [Programmatic Navigation in Next.js](#programmatic-navigation-in-nextjs)
    - [Accessing Navigation State](#accessing-navigation-state)
  - [Protected Routes](#protected-routes)
    - [Protected Route Component](#protected-route-component)
    - [Usage](#usage)
- [Next.js App Router](#nextjs-app-router)
  - [File-Based Routing](#file-based-routing)
    - [Basic File Structure](#basic-file-structure)
    - [Root Layout (Required)](#root-layout-required)
    - [Page Component](#page-component)
  - [Dynamic Routes in Next.js](#dynamic-routes-in-nextjs)
    - [Single Dynamic Segment](#single-dynamic-segment)
    - [Multiple Dynamic Segments](#multiple-dynamic-segments)
    - [Catch-All Routes](#catch-all-routes)
  - [Route Groups](#route-groups)
  - [Parallel Routes](#parallel-routes)
  - [Intercepting Routes](#intercepting-routes)
  - [Navigation in Next.js](#navigation-in-nextjs)
    - [Link Component](#link-component)
    - [Programmatic Navigation](#programmatic-navigation)
- [Advanced Routing Patterns](#advanced-routing-patterns)
  - [Query Parameters](#query-parameters)
    - [React Router Query Parameters](#react-router-query-parameters)
    - [Next.js Query Parameters](#nextjs-query-parameters)
  - [404 and Error Pages](#404-and-error-pages)
    - [React Router](#react-router)
    - [Next.js](#nextjs)
  - [Redirects](#redirects)
    - [React Router Redirect Example](#react-router-redirect-example)
    - [Next.js Redirect Example](#nextjs-redirect-example)
  - [Loading States](#loading-states)
    - [React Router with Suspense](#react-router-with-suspense)
    - [Next.js Loading UI](#nextjs-loading-ui)
- [Authentication and Route Protection](#authentication-and-route-protection)
  - [Protected Route Pattern](#protected-route-pattern)
    - [Comprehensive Protected Route](#comprehensive-protected-route)
  - [Role-Based Access](#role-based-access)
  - [Redirect After Login](#redirect-after-login)
- [Performance Optimization](#performance-optimization)
  - [Code Splitting Routes](#code-splitting-routes)
    - [React Router Code Splitting Routes](#react-router-code-splitting-routes)
  - [Prefetching](#prefetching)
    - [Next.js Automatic Prefetching](#nextjs-automatic-prefetching)
  - [Scroll Restoration](#scroll-restoration)
    - [React Router Scroll Restoration](#react-router-scroll-restoration)
    - [Next.js (Automatic)](#nextjs-automatic)
- [Summary and Best Practices](#summary-and-best-practices)
  - [2025 Routing Guidelines](#2025-routing-guidelines)
  - [Quick Decision Guide](#quick-decision-guide)
  - [Next Steps](#next-steps)

## Introduction to React Routing

**Single Page Application (SPA) Routing:** Unlike traditional multi-page websites where each URL loads a new HTML document from the server, React applications use client-side routing. This means:

- URLs change without full page reloads
- Faster navigation between pages
- State persists during navigation
- Better user experience

**2025 Routing Solutions:**

1. **React Router**: Most popular routing library for React SPAs
2. **Next.js App Router**: Modern file-based routing with advanced features
3. **TanStack Router**: Type-safe routing (emerging option)

This chapter focuses on React Router (for Vite apps) and Next.js App Router.

## React Router v6

React Router v6 is the standard routing solution for React single-page applications.

### Installation and Setup

#### Step 1: Install React Router

```bash
npm install react-router-dom
```

#### Step 2: Basic App Structure

```typescript
// src/main.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

### Basic Routing

#### Simple Route Configuration

```typescript
// src/App.tsx
import { Routes, Route, Link } from 'react-router-dom';
import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';
import ContactPage from './pages/ContactPage';
import NotFoundPage from './pages/NotFoundPage';

function App() {
  return (
    <div className="app">
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
        <Route path="/contact" element={<ContactPage />} />
        <Route path="*" element={<NotFoundPage />} />
      </Routes>
    </div>
  );
}

export default App;
```

#### Navigation with NavLink

```typescript
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <NavLink
        to="/"
        className={({ isActive }) => isActive ? 'nav-link active' : 'nav-link'}
      >
        Home
      </NavLink>
      
      <NavLink
        to="/about"
        style={({ isActive }) => ({
          fontWeight: isActive ? 'bold' : 'normal',
          color: isActive ? '#0066cc' : '#666',
        })}
      >
        About
      </NavLink>
    </nav>
  );
}
```

### Route Parameters

#### Dynamic Routes

```typescript
// src/App.tsx
<Routes>
  <Route path="/blog/:slug" element={<BlogPost />} />
  <Route path="/user/:userId" element={<UserProfile />} />
  <Route path="/products/:category/:id" element={<ProductDetail />} />
</Routes>
```

#### Accessing Parameters

```typescript
// src/pages/BlogPost.tsx
import { useParams, useNavigate } from 'react-router-dom';
import { useEffect, useState } from 'react';

interface BlogPost {
  slug: string;
  title: string;
  content: string;
}

function BlogPost() {
  const { slug } = useParams<{ slug: string }>();
  const navigate = useNavigate();
  const [post, setPost] = useState<BlogPost | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchPost() {
      try {
        const response = await fetch(`/api/posts/${slug}`);
        if (!response.ok) {
          navigate('/404');
          return;
        }
        const data = await response.json();
        setPost(data);
      } catch (error) {
        console.error('Failed to fetch post:', error);
      } finally {
        setLoading(false);
      }
    }

    fetchPost();
  }, [slug, navigate]);

  if (loading) return <div>Loading...</div>;
  if (!post) return null;

  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

### Nested Routes

#### Layout with Nested Routes

```typescript
// src/App.tsx
import { Routes, Route } from 'react-router-dom';
import DashboardLayout from './layouts/DashboardLayout';
import DashboardHome from './pages/Dashboard/Home';
import DashboardSettings from './pages/Dashboard/Settings';
import DashboardProfile from './pages/Dashboard/Profile';

function App() {
  return (
    <Routes>
      <Route path="/" element={<HomePage />} />
      
      <Route path="/dashboard" element={<DashboardLayout />}>
        <Route index element={<DashboardHome />} />
        <Route path="settings" element={<DashboardSettings />} />
        <Route path="profile" element={<DashboardProfile />} />
      </Route>
    </Routes>
  );
}
```

#### Layout Component with Outlet

```typescript
// src/layouts/DashboardLayout.tsx
import { Outlet, NavLink } from 'react-router-dom';

function DashboardLayout() {
  return (
    <div className="dashboard-layout">
      <aside className="sidebar">
        <nav>
          <NavLink to="/dashboard">Home</NavLink>
          <NavLink to="/dashboard/settings">Settings</NavLink>
          <NavLink to="/dashboard/profile">Profile</NavLink>
        </nav>
      </aside>
      
      <main className="dashboard-content">
        <Outlet /> {/* Child routes render here */}
      </main>
    </div>
  );
}

export default DashboardLayout;
```

### Navigation

#### Programmatic Navigation in Next.js

```typescript
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    // Perform login
    const success = await login();
    
    if (success) {
      // Navigate to dashboard after successful login
      navigate('/dashboard');
      
      // Or navigate with state
      navigate('/dashboard', {
        state: { message: 'Login successful!' },
        replace: true, // Replace history entry
      });
    }
  };

  return <form onSubmit={handleSubmit}>{/* form fields */}</form>;
}
```

#### Accessing Navigation State

```typescript
import { useLocation } from 'react-router-dom';

function Dashboard() {
  const location = useLocation();
  const message = location.state?.message;

  return (
    <div>
      {message && <div className="alert">{message}</div>}
      <h1>Dashboard</h1>
    </div>
  );
}
```

### Protected Routes

#### Protected Route Component

```typescript
// src/components/ProtectedRoute.tsx
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

interface ProtectedRouteProps {
  children: React.ReactNode;
}

function ProtectedRoute({ children }: ProtectedRouteProps) {
  const { user, loading } = useAuth();
  const location = useLocation();

  if (loading) {
    return <div>Loading...</div>;
  }

  if (!user) {
    // Redirect to login, saving the attempted location
    return <Navigate to="/login" state={{ from: location }} replace />;
  }

  return <>{children}</>;
}

export default ProtectedRoute;
```

#### Usage

```typescript
// src/App.tsx
<Routes>
  <Route path="/login" element={<LoginPage />} />
  
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <DashboardPage />
      </ProtectedRoute>
    }
  />
  
  <Route
    path="/profile"
    element={
      <ProtectedRoute>
        <ProfilePage />
      </ProtectedRoute>
    }
  />
</Routes>
```

## Next.js App Router

Next.js App Router (introduced in Next.js 13, stable in 15) provides a modern, file-based routing system.

### File-Based Routing

#### Basic File Structure

```tree
src/app/
├── layout.tsx          # Root layout (required)
├── page.tsx            # Home page (/)
├── about/
│   └── page.tsx        # /about
├── blog/
│   ├── page.tsx        # /blog
│   └── [slug]/
│       └── page.tsx    # /blog/:slug
└── dashboard/
    ├── layout.tsx      # Dashboard layout
    ├── page.tsx        # /dashboard
    └── settings/
        └── page.tsx    # /dashboard/settings
```

#### Root Layout (Required)

```typescript
// src/app/layout.tsx
export const metadata = {
  title: 'My App',
  description: 'App description',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <header>
          <nav>{/* Navigation */}</nav>
        </header>
        <main>{children}</main>
        <footer>{/* Footer */}</footer>
      </body>
    </html>
  );
}
```

#### Page Component

```typescript
// src/app/page.tsx
export default function HomePage() {
  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is the home page</p>
    </div>
  );
}
```

### Dynamic Routes in Next.js

#### Single Dynamic Segment

```typescript
// src/app/blog/[slug]/page.tsx
interface PageProps {
  params: { slug: string };
}

export default async function BlogPost({ params }: PageProps) {
  const { slug } = params;
  
  const post = await fetchPost(slug);
  
  return (
    <article>
      <h1>{post.title}</h1>
      <div>{post.content}</div>
    </article>
  );
}

// Generate static params for SSG
export async function generateStaticParams() {
  const posts = await fetchAllPosts();
  
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

// Generate metadata
export async function generateMetadata({ params }: PageProps) {
  const post = await fetchPost(params.slug);
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

#### Multiple Dynamic Segments

```typescript
// src/app/shop/[category]/[productId]/page.tsx
interface PageProps {
  params: { category: string; productId: string };
}

export default async function ProductPage({ params }: PageProps) {
  const { category, productId } = params;
  
  const product = await fetchProduct(category, productId);
  
  return (
    <div>
      <h1>{product.name}</h1>
      <p>Category: {category}</p>
      <p>Price: ${product.price}</p>
    </div>
  );
}
```

#### Catch-All Routes

```typescript
// src/app/docs/[...slug]/page.tsx - Matches /docs/a, /docs/a/b, /docs/a/b/c
interface PageProps {
  params: { slug: string[] };
}

export default async function DocsPage({ params }: PageProps) {
  const { slug } = params;
  const path = slug.join('/');
  
  const content = await fetchDocs(path);
  
  return <div>{content}</div>;
}

// Optional catch-all: [[...slug]]/page.tsx - Also matches /docs
```

### Route Groups

Route groups allow you to organize routes without affecting the URL structure:

```tree
src/app/
├── (marketing)/
│   ├── layout.tsx      # Marketing layout
│   ├── page.tsx        # / (home)
│   ├── about/
│   │   └── page.tsx    # /about
│   └── contact/
│       └── page.tsx    # /contact
├── (shop)/
│   ├── layout.tsx      # Shop layout
│   ├── products/
│   │   └── page.tsx    # /products
│   └── cart/
│       └── page.tsx    # /cart
└── (auth)/
    ├── login/
    │   └── page.tsx    # /login
    └── register/
        └── page.tsx    # /register
```

```typescript
// src/app/(marketing)/layout.tsx
export default function MarketingLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="marketing-layout">
      <nav>{/* Marketing nav */}</nav>
      {children}
      <footer>{/* Marketing footer */}</footer>
    </div>
  );
}
```

### Parallel Routes

Parallel routes allow you to render multiple pages simultaneously:

```tree
src/app/
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    ├── @analytics/      # Named slot
    │   └── page.tsx
    └── @team/           # Named slot
        └── page.tsx
```

```typescript
// src/app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <div className="dashboard">
      <div className="main">{children}</div>
      <div className="sidebar">
        <div className="analytics">{analytics}</div>
        <div className="team">{team}</div>
      </div>
    </div>
  );
}
```

### Intercepting Routes

Intercepting routes allow you to show a route within the current layout:

```tree
src/app/
├── feed/
│   └── page.tsx
└── photo/
    ├── [id]/
    │   └── page.tsx
    └── (..)feed/        # Intercepts /feed from /photo
        └── page.tsx
```

```typescript
// src/app/photo/(..)feed/page.tsx
import Modal from '@/components/Modal';

export default function InterceptedFeed() {
  return (
    <Modal>
      <h1>Feed (in modal)</h1>
      {/* Feed content */}
    </Modal>
  );
}
```

### Navigation in Next.js

#### Link Component

```typescript
import Link from 'next/link';

function Navigation() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
      <Link href="/blog">Blog</Link>
      
      {/* With prefetching disabled */}
      <Link href="/large-page" prefetch={false}>
        Large Page
      </Link>
      
      {/* Dynamic route */}
      <Link href={`/blog/${slug}`}>
        View Post
      </Link>
    </nav>
  );
}
```

#### Programmatic Navigation

```typescript
'use client';

import { useRouter } from 'next/navigation';

function LoginForm() {
  const router = useRouter();

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    const success = await login();
    
    if (success) {
      router.push('/dashboard');
      // or router.replace('/dashboard') to replace history
    }
  };

  return <form onSubmit={handleSubmit}>{/* form */}</form>;
}
```

## Advanced Routing Patterns

### Query Parameters

#### React Router Query Parameters

```typescript
import { useSearchParams } from 'react-router-dom';

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  
  const query = searchParams.get('q') || '';
  const page = Number(searchParams.get('page')) || 1;

  const handleSearch = (newQuery: string) => {
    setSearchParams({ q: newQuery, page: '1' });
  };

  return (
    <div>
      <input
        value={query}
        onChange={(e) => handleSearch(e.target.value)}
      />
      <p>Page {page}</p>
    </div>
  );
}
```

#### Next.js Query Parameters

```typescript
'use client';

import { useSearchParams, useRouter } from 'next/navigation';

function SearchPage() {
  const router = useRouter();
  const searchParams = useSearchParams();
  
  const query = searchParams.get('q') || '';

  const handleSearch = (newQuery: string) => {
    const params = new URLSearchParams(searchParams);
    params.set('q', newQuery);
    params.set('page', '1');
    router.push(`/search?${params.toString()}`);
  };

  return <div>{/* search UI */}</div>;
}
```

### 404 and Error Pages

#### React Router

```typescript
<Routes>
  <Route path="/" element={<HomePage />} />
  <Route path="/about" element={<AboutPage />} />
  <Route path="*" element={<NotFoundPage />} />
</Routes>
```

#### Next.js

```typescript
// src/app/not-found.tsx
export default function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>
      <p>The page you're looking for doesn't exist.</p>
    </div>
  );
}

// src/app/error.tsx
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

### Redirects

#### React Router Redirect Example

```typescript
import { Navigate } from 'react-router-dom';

<Routes>
  <Route path="/old-page" element={<Navigate to="/new-page" replace />} />
</Routes>
```

#### Next.js Redirect Example

```typescript
// next.config.js
module.exports = {
  async redirects() {
    return [
      {
        source: '/old-blog/:slug',
        destination: '/blog/:slug',
        permanent: true,
      },
    ];
  },
};

// Or in a page
import { redirect } from 'next/navigation';

export default async function Page() {
  const session = await getSession();
  
  if (!session) {
    redirect('/login');
  }
  
  return <div>Protected content</div>;
}
```

### Loading States

#### React Router with Suspense

```typescript
import { Suspense, lazy } from 'react';

const BlogPost = lazy(() => import('./pages/BlogPost'));

<Routes>
  <Route
    path="/blog/:slug"
    element={
      <Suspense fallback={<div>Loading...</div>}>
        <BlogPost />
      </Suspense>
    }
  />
</Routes>
```

#### Next.js Loading UI

```typescript
// src/app/blog/loading.tsx
export default function Loading() {
  return (
    <div className="loading-skeleton">
      <div className="skeleton-title" />
      <div className="skeleton-content" />
    </div>
  );
}
```

## Authentication and Route Protection

### Protected Route Pattern

#### Comprehensive Protected Route

```typescript
// src/components/ProtectedRoute.tsx
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

interface ProtectedRouteProps {
  children: React.ReactNode;
  requiredRole?: string[];
  redirectTo?: string;
}

function ProtectedRoute({
  children,
  requiredRole,
  redirectTo = '/login',
}: ProtectedRouteProps) {
  const { user, loading } = useAuth();
  const location = useLocation();

  if (loading) {
    return <div>Checking authentication...</div>;
  }

  if (!user) {
    return <Navigate to={redirectTo} state={{ from: location }} replace />;
  }

  if (requiredRole && !requiredRole.includes(user.role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <>{children}</>;
}

export default ProtectedRoute;
```

### Role-Based Access

```typescript
<Routes>
  <Route path="/login" element={<LoginPage />} />
  
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <DashboardPage />
      </ProtectedRoute>
    }
  />
  
  <Route
    path="/admin"
    element={
      <ProtectedRoute requiredRole={['admin']}>
        <AdminPage />
      </ProtectedRoute>
    }
  />
</Routes>
```

### Redirect After Login

```typescript
function LoginPage() {
  const navigate = useNavigate();
  const location = useLocation();
  const { login } = useAuth();

  const from = location.state?.from?.pathname || '/dashboard';

  const handleSubmit = async (credentials: Credentials) => {
    try {
      await login(credentials);
      navigate(from, { replace: true });
    } catch (error) {
      console.error('Login failed:', error);
    }
  };

  return <form onSubmit={handleSubmit}>{/* form */}</form>;
}
```

## Performance Optimization

### Code Splitting Routes

#### React Router Code Splitting Routes

```typescript
import { lazy, Suspense } from 'react';

const HomePage = lazy(() => import('./pages/HomePage'));
const AboutPage = lazy(() => import('./pages/AboutPage'));
const BlogPage = lazy(() => import('./pages/BlogPage'));

function App() {
  return (
    <Routes>
      <Route
        path="/"
        element={
          <Suspense fallback={<div>Loading...</div>}>
            <HomePage />
          </Suspense>
        }
      />
      <Route
        path="/about"
        element={
          <Suspense fallback={<div>Loading...</div>}>
            <AboutPage />
          </Suspense>
        }
      />
    </Routes>
  );
}
```

### Prefetching

#### Next.js Automatic Prefetching

```typescript
// Links are automatically prefetched on hover
<Link href="/blog">Blog</Link>

// Disable prefetching
<Link href="/large-page" prefetch={false}>
  Large Page
</Link>

// Programmatic prefetch
'use client';

import { useRouter } from 'next/navigation';
import { useEffect } from 'react';

function Component() {
  const router = useRouter();
  
  useEffect(() => {
    // Prefetch on component mount
    router.prefetch('/likely-next-page');
  }, [router]);
}
```

### Scroll Restoration

#### React Router Scroll Restoration

```typescript
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';

function ScrollToTop() {
  const { pathname } = useLocation();

  useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);

  return null;
}

// Use in App
function App() {
  return (
    <>
      <ScrollToTop />
      <Routes>{/* routes */}</Routes>
    </>
  );
}
```

#### Next.js (Automatic)

Next.js automatically handles scroll restoration. To customize:

```typescript
'use client';

import { usePathname } from 'next/navigation';
import { useEffect } from 'react';

export function ScrollToTop() {
  const pathname = usePathname();

  useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);

  return null;
}
```

## Summary and Best Practices

### 2025 Routing Guidelines

**✅ Recommended Practices:**

1. **Use React Router v6 for SPAs** - Modern, type-safe, performant
2. **Use Next.js App Router for full apps** - Built-in optimization, SEO
3. **Code split routes** - Lazy load for better performance
4. **Protect sensitive routes** - Implement authentication guards
5. **Handle loading states** - Provide feedback during navigation
6. **Use TypeScript** - Type-safe route parameters
7. **Prefetch strategically** - Improve perceived performance

**❌ Avoid These Patterns:**

1. **Don't use class components for routing** - Use functional components with hooks
2. **Don't skip loading states** - Always provide user feedback
3. **Don't hardcode URLs** - Use route constants or type-safe routing
4. **Don't ignore 404 pages** - Handle not found routes gracefully
5. **Don't skip authentication checks** - Always protect sensitive routes

### Quick Decision Guide

**Use React Router when:**

- Building a single-page application
- Don't need server-side rendering
- Want maximum flexibility
- Using Vite or other non-Next.js tools

**Use Next.js App Router when:**

- Need SEO optimization (SSR/SSG)
- Want file-based routing
- Building full-featured applications
- Need advanced features (parallel routes, intercepting)

### Next Steps

Now that you understand routing, continue with:

- **Chapter 15**: Forms and Input Handling
- **Chapter 16**: Testing React Applications
- **Chapter 17**: Deployment and Production

With proper routing in place, you can build complex, navigable React applications!
