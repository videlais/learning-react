---
title: "Deployment and Production"
order: 17
chapter_number: 17
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Deploy** React applications to modern hosting platforms
- **Optimize** builds for production performance
- **Implement** CI/CD pipelines for automated deployments
- **Monitor** applications in production
- **Handle** environment-specific configurations

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Production Build Optimization](#production-build-optimization)
  - [Build Configuration](#build-configuration)
    - [Vite Production Build](#vite-production-build)
    - [Next.js Production Build](#nextjs-production-build)
  - [Code Splitting](#code-splitting)
    - [Route-Based Code Splitting](#route-based-code-splitting)
    - [Component-Based Code Splitting](#component-based-code-splitting)
  - [Bundle Analysis](#bundle-analysis)
    - [Analyze Bundle Size](#analyze-bundle-size)
  - [Performance Budget](#performance-budget)
- [Deployment Platforms](#deployment-platforms)
  - [Vercel](#vercel)
    - [Deploy with Vercel CLI](#deploy-with-vercel-cli)
    - [Vercel Configuration File](#vercel-configuration-file)
    - [GitHub Integration](#github-integration)
  - [Netlify](#netlify)
    - [Deploy with Netlify CLI](#deploy-with-netlify-cli)
    - [Configuration File](#configuration-file)
  - [AWS Amplify](#aws-amplify)
    - [AWS Amplify Configuration](#aws-amplify-configuration)
  - [Docker Deployment](#docker-deployment)
    - [Dockerfile for Vite](#dockerfile-for-vite)
    - [Docker Compose](#docker-compose)
    - [Build and Run](#build-and-run)
- [Environment Configuration](#environment-configuration)
  - [Environment Variables](#environment-variables)
    - [Vite Environment Variables](#vite-environment-variables)
    - [Next.js Environment Variables](#nextjs-environment-variables)
  - [Build Modes](#build-modes)
  - [Feature Flags](#feature-flags)
- [CI/CD Pipelines](#cicd-pipelines)
  - [GitHub Actions](#github-actions)
    - [Complete CI/CD Pipeline](#complete-cicd-pipeline)
  - [Automated Testing](#automated-testing)
  - [Deployment Automation](#deployment-automation)
- [Production Monitoring](#production-monitoring)
  - [Error Tracking](#error-tracking)
    - [Sentry Integration](#sentry-integration)
    - [Error Boundary with Sentry](#error-boundary-with-sentry)
  - [Performance Monitoring](#performance-monitoring)
    - [Web Vitals](#web-vitals)
  - [Analytics](#analytics)
    - [Google Analytics 4](#google-analytics-4)
- [Security Best Practices](#security-best-practices)
  - [Content Security Policy](#content-security-policy)
  - [HTTPS Configuration](#https-configuration)
  - [Security Headers](#security-headers)
- [Performance Optimization](#performance-optimization)
  - [Caching Strategies](#caching-strategies)
  - [CDN Configuration](#cdn-configuration)
  - [Asset Optimization](#asset-optimization)
- [Summary and Deployment Checklist](#summary-and-deployment-checklist)
  - [Pre-Deployment Checklist](#pre-deployment-checklist)
  - [2025 Deployment Standards](#2025-deployment-standards)

## Production Build Optimization

### Build Configuration

#### Vite Production Build

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { visualizer } from 'rollup-plugin-visualizer';
import compression from 'vite-plugin-compression';

export default defineConfig({
  plugins: [
    react(),
    compression({
      algorithm: 'gzip',
      ext: '.gz',
    }),
    compression({
      algorithm: 'brotliCompress',
      ext: '.br',
    }),
    visualizer({
      filename: './dist/stats.html',
      open: true,
    }),
  ],
  build: {
    target: 'es2020',
    outDir: 'dist',
    assetsDir: 'assets',
    sourcemap: false, // Enable for production debugging
    minify: 'esbuild',
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom', 'react-router-dom'],
          'ui-vendor': ['@headlessui/react', '@heroicons/react'],
        },
      },
    },
    chunkSizeWarningLimit: 1000,
  },
  server: {
    port: 3000,
  },
  preview: {
    port: 4173,
  },
});
```

#### Next.js Production Build

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  
  // Image optimization
  images: {
    domains: ['example.com', 'cdn.example.com'],
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
  
  // Compression
  compress: true,
  
  // Headers for security and caching
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on',
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
          {
            key: 'X-Frame-Options',
            value: 'SAMEORIGIN',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'Referrer-Policy',
            value: 'origin-when-cross-origin',
          },
        ],
      },
      {
        source: '/static/(.*)',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
    ];
  },
  
  // Webpack customization
  webpack: (config, { isServer }) => {
    if (!isServer) {
      config.resolve.fallback = {
        ...config.resolve.fallback,
        fs: false,
        net: false,
        tls: false,
      };
    }
    return config;
  },
};

module.exports = nextConfig;
```

### Code Splitting

#### Route-Based Code Splitting

```typescript
// src/App.tsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

const HomePage = lazy(() => import('./pages/HomePage'));
const AboutPage = lazy(() => import('./pages/AboutPage'));
const ProductsPage = lazy(() => import('./pages/ProductsPage'));
const AdminPage = lazy(() => import('./pages/AdminPage'));

function LoadingFallback() {
  return (
    <div className="loading-container">
      <div className="spinner" />
      <p>Loading...</p>
    </div>
  );
}

function App() {
  return (
    <Suspense fallback={<LoadingFallback />}>
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
        <Route path="/products" element={<ProductsPage />} />
        <Route path="/admin" element={<AdminPage />} />
      </Routes>
    </Suspense>
  );
}
```

#### Component-Based Code Splitting

```typescript
// Lazy load heavy components
import { lazy, Suspense } from 'react';

const HeavyChart = lazy(() => import('./components/HeavyChart'));
const RichTextEditor = lazy(() => import('./components/RichTextEditor'));

function Dashboard() {
  const [showChart, setShowChart] = useState(false);

  return (
    <div>
      <h1>Dashboard</h1>
      
      <button onClick={() => setShowChart(true)}>
        Show Analytics
      </button>
      
      {showChart && (
        <Suspense fallback={<div>Loading chart...</div>}>
          <HeavyChart />
        </Suspense>
      )}
    </div>
  );
}
```

### Bundle Analysis

#### Analyze Bundle Size

```bash
# Vite
npm run build
npx vite-bundle-visualizer

# Next.js
npm install -D @next/bundle-analyzer
```

```javascript
// next.config.js with analyzer
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // ... your Next.js config
});
```

```bash
# Run analysis
ANALYZE=true npm run build
```

### Performance Budget

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks(id) {
          // Vendor chunks
          if (id.includes('node_modules')) {
            if (id.includes('react') || id.includes('react-dom')) {
              return 'react-vendor';
            }
            if (id.includes('lodash') || id.includes('date-fns')) {
              return 'utility-vendor';
            }
            return 'vendor';
          }
        },
      },
    },
    chunkSizeWarningLimit: 500, // Warning at 500KB
  },
});
```

## Deployment Platforms

### Vercel

**Best for:** Next.js applications, frontend projects

#### Deploy with Vercel CLI

```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel

# Deploy to production
vercel --prod
```

#### Vercel Configuration File

```json
{
  "version": 2,
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "vite",
  "outputDirectory": "dist",
  "env": {
    "VITE_API_URL": "@api-url"
  },
  "build": {
    "env": {
      "VITE_API_URL": "@api-url"
    }
  },
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "s-maxage=0"
        }
      ]
    }
  ],
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "https://api.example.com/:path*"
    }
  ]
}
```

#### GitHub Integration

```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install Vercel CLI
        run: npm install -g vercel
      
      - name: Deploy to Vercel
        run: vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

### Netlify

**Best for:** Static sites, Vite apps, JAMstack

#### Deploy with Netlify CLI

```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Initialize site
netlify init

# Deploy
netlify deploy

# Deploy to production
netlify deploy --prod
```

#### Configuration File

```toml
# netlify.toml
[build]
  command = "npm run build"
  publish = "dist"
  functions = "netlify/functions"

[build.environment]
  NODE_VERSION = "20"

[[redirects]]
  from = "/api/*"
  to = "https://api.example.com/:splat"
  status = 200

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"

[[headers]]
  for = "/static/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
```

### AWS Amplify

**Best for:** Full-stack applications, AWS integration

#### AWS Amplify Configuration

```yaml
# amplify.yml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: dist
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

### Docker Deployment

**Best for:** Self-hosted, Kubernetes, cloud platforms

#### Dockerfile for Vite

```dockerfile
# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built files
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
```

```nginx
# nginx.conf
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/json;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Cache static assets
    location /assets {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # SPA fallback
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

#### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "80:80"
    environment:
      - NODE_ENV=production
    restart: unless-stopped
```

#### Build and Run

```bash
# Build image
docker build -t my-react-app .

# Run container
docker run -d -p 80:80 --name react-app my-react-app

# Docker Compose
docker-compose up -d
```

## Environment Configuration

### Environment Variables

#### Vite Environment Variables

```bash
# .env.development
VITE_API_URL=http://localhost:3000/api
VITE_APP_TITLE=My App (Dev)
VITE_ENABLE_ANALYTICS=false

# .env.production
VITE_API_URL=https://api.production.com
VITE_APP_TITLE=My App
VITE_ENABLE_ANALYTICS=true
```

```typescript
// src/config/env.ts
export const config = {
  apiUrl: import.meta.env.VITE_API_URL,
  appTitle: import.meta.env.VITE_APP_TITLE,
  enableAnalytics: import.meta.env.VITE_ENABLE_ANALYTICS === 'true',
  isDevelopment: import.meta.env.DEV,
  isProduction: import.meta.env.PROD,
} as const;

// Type-safe environment variables
interface ImportMetaEnv {
  readonly VITE_API_URL: string;
  readonly VITE_APP_TITLE: string;
  readonly VITE_ENABLE_ANALYTICS: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

#### Next.js Environment Variables

```bash
# .env.local (not committed)
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_ANALYTICS_ID=dev-analytics-id
DATABASE_URL=postgresql://localhost:5432/mydb
SECRET_KEY=dev-secret-key

# .env.production (committed, no secrets)
NEXT_PUBLIC_API_URL=https://api.production.com
```

```typescript
// src/config/env.ts
export const config = {
  // Public (accessible in browser)
  apiUrl: process.env.NEXT_PUBLIC_API_URL!,
  analyticsId: process.env.NEXT_PUBLIC_ANALYTICS_ID!,
  
  // Server-only (not exposed to browser)
  databaseUrl: process.env.DATABASE_URL!,
  secretKey: process.env.SECRET_KEY!,
};
```

### Build Modes

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:staging": "vite build --mode staging",
    "build:production": "vite build --mode production",
    "preview": "vite preview"
  }
}
```

```bash
# .env.staging
VITE_API_URL=https://api.staging.com
VITE_ENV=staging
```

### Feature Flags

```typescript
// src/config/features.ts
export const features = {
  newDashboard: import.meta.env.VITE_FEATURE_NEW_DASHBOARD === 'true',
  darkMode: import.meta.env.VITE_FEATURE_DARK_MODE === 'true',
  betaFeatures: import.meta.env.VITE_FEATURE_BETA === 'true',
} as const;

// Usage
import { features } from './config/features';

function App() {
  return (
    <div>
      {features.newDashboard ? <NewDashboard /> : <OldDashboard />}
      {features.darkMode && <DarkModeToggle />}
    </div>
  );
}
```

## CI/CD Pipelines

### GitHub Actions

#### Complete CI/CD Pipeline

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'

jobs:
  lint:
    name: Lint Code
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Check formatting
        run: npm run format:check

  test:
    name: Run Tests
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json
          fail_ci_if_error: true

  build:
    name: Build Application
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build application
        run: npm run build
        env:
          VITE_API_URL: ${{ secrets.VITE_API_URL }}

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build
          path: dist/
          retention-days: 7

  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build]
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://myapp.com
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build
          path: dist/

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

### Automated Testing

```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 */6 * * *' # Every 6 hours

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

### Deployment Automation

```yaml
# .github/workflows/preview-deploy.yml
name: Preview Deployment

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  deploy-preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Deploy to Netlify
        uses: nwtgck/actions-netlify@v2
        with:
          publish-dir: './dist'
          production-deploy: false
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-message: 'Preview for PR #${{ github.event.number }}'
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

## Production Monitoring

### Error Tracking

#### Sentry Integration

```bash
npm install @sentry/react
```

```typescript
// src/main.tsx
import * as Sentry from '@sentry/react';
import { BrowserTracing } from '@sentry/tracing';

if (import.meta.env.PROD) {
  Sentry.init({
    dsn: import.meta.env.VITE_SENTRY_DSN,
    integrations: [
      new BrowserTracing(),
      new Sentry.Replay({
        maskAllText: false,
        blockAllMedia: false,
      }),
    ],
    tracesSampleRate: 0.1,
    replaysSessionSampleRate: 0.1,
    replaysOnErrorSampleRate: 1.0,
    environment: import.meta.env.MODE,
    beforeSend(event, hint) {
      // Filter out local development errors
      if (event.request?.url?.includes('localhost')) {
        return null;
      }
      return event;
    },
  });
}
```

#### Error Boundary with Sentry

```typescript
// src/components/ErrorBoundary.tsx
import * as Sentry from '@sentry/react';

function FallbackComponent({ error, resetError }: any) {
  return (
    <div className="error-container">
      <h1>Something went wrong</h1>
      <p>{error.message}</p>
      <button onClick={resetError}>Try again</button>
    </div>
  );
}

export const ErrorBoundary = Sentry.withErrorBoundary(FallbackComponent, {
  fallback: FallbackComponent,
  showDialog: true,
});

// Usage in App
function App() {
  return (
    <ErrorBoundary>
      <YourApp />
    </ErrorBoundary>
  );
}
```

### Performance Monitoring

#### Web Vitals

```bash
npm install web-vitals
```

```typescript
// src/utils/reportWebVitals.ts
import { onCLS, onFID, onFCP, onLCP, onTTFB } from 'web-vitals';

function sendToAnalytics(metric: any) {
  // Send to analytics service
  if (window.gtag) {
    window.gtag('event', metric.name, {
      value: Math.round(metric.value),
      event_category: 'Web Vitals',
      event_label: metric.id,
      non_interaction: true,
    });
  }

  // Send to custom analytics
  fetch('/api/analytics', {
    method: 'POST',
    body: JSON.stringify(metric),
    headers: { 'Content-Type': 'application/json' },
  });
}

export function reportWebVitals() {
  onCLS(sendToAnalytics);
  onFID(sendToAnalytics);
  onFCP(sendToAnalytics);
  onLCP(sendToAnalytics);
  onTTFB(sendToAnalytics);
}
```

```typescript
// src/main.tsx
import { reportWebVitals } from './utils/reportWebVitals';

// Report web vitals in production
if (import.meta.env.PROD) {
  reportWebVitals();
}
```

### Analytics

#### Google Analytics 4

```typescript
// src/utils/analytics.ts
export const initGA = (measurementId: string) => {
  if (typeof window === 'undefined') return;

  window.gtag('config', measurementId, {
    page_path: window.location.pathname,
  });
};

export const logPageView = (url: string) => {
  if (typeof window.gtag !== 'undefined') {
    window.gtag('config', import.meta.env.VITE_GA_MEASUREMENT_ID, {
      page_path: url,
    });
  }
};

export const logEvent = (action: string, params?: Record<string, any>) => {
  if (typeof window.gtag !== 'undefined') {
    window.gtag('event', action, params);
  }
};
```

```typescript
// Track page views in React Router
import { useEffect } from 'react';
import { useLocation } from 'react-router-dom';
import { logPageView } from './utils/analytics';

function App() {
  const location = useLocation();

  useEffect(() => {
    logPageView(location.pathname + location.search);
  }, [location]);

  return <YourApp />;
}
```

## Security Best Practices

### Content Security Policy

```typescript
// vite.config.ts with CSP plugin
import { defineConfig } from 'vite';
import { createHtmlPlugin } from 'vite-plugin-html';

export default defineConfig({
  plugins: [
    createHtmlPlugin({
      inject: {
        data: {
          csp: `
            default-src 'self';
            script-src 'self' 'unsafe-inline' https://www.google-analytics.com;
            style-src 'self' 'unsafe-inline';
            img-src 'self' data: https:;
            font-src 'self' data:;
            connect-src 'self' https://api.example.com;
            frame-ancestors 'none';
            base-uri 'self';
            form-action 'self';
          `.replace(/\s+/g, ' ').trim(),
        },
      },
    }),
  ],
});
```

```html
<!-- index.html -->
<meta http-equiv="Content-Security-Policy" content="<%= csp %>">
```

### HTTPS Configuration

```nginx
# nginx SSL configuration
server {
    listen 443 ssl http2;
    server_name example.com;

    # SSL certificates
    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # HSTS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

    # Other security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # ... rest of configuration
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}
```

### Security Headers

```typescript
// Next.js security headers
// next.config.js
const securityHeaders = [
  {
    key: 'X-DNS-Prefetch-Control',
    value: 'on',
  },
  {
    key: 'Strict-Transport-Security',
    value: 'max-age=63072000; includeSubDomains; preload',
  },
  {
    key: 'X-Frame-Options',
    value: 'SAMEORIGIN',
  },
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff',
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block',
  },
  {
    key: 'Referrer-Policy',
    value: 'origin-when-cross-origin',
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()',
  },
];

module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: securityHeaders,
      },
    ];
  },
};
```

## Performance Optimization

### Caching Strategies

```nginx
# nginx caching configuration
server {
    # Cache static assets
    location ~* \.(jpg|jpeg|png|gif|ico|svg|webp|avif)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    location ~* \.(css|js)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    location ~* \.(woff|woff2|ttf|otf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Don't cache HTML
    location / {
        expires -1;
        add_header Cache-Control "no-store, no-cache, must-revalidate, proxy-revalidate";
    }
}
```

### CDN Configuration

```typescript
// vite.config.ts with CDN
export default defineConfig({
  build: {
    rollupOptions: {
      external: ['react', 'react-dom'],
      output: {
        globals: {
          react: 'React',
          'react-dom': 'ReactDOM',
        },
      },
    },
  },
});
```

```html
<!-- index.html with CDN fallback -->
<script crossorigin src="https://cdn.jsdelivr.net/npm/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://cdn.jsdelivr.net/npm/react-dom@18/umd/react-dom.production.min.js"></script>
```

### Asset Optimization

```typescript
// vite.config.ts
import imagemin from 'vite-plugin-imagemin';

export default defineConfig({
  plugins: [
    imagemin({
      gifsicle: { optimizationLevel: 7 },
      optipng: { optimizationLevel: 7 },
      mozjpeg: { quality: 80 },
      pngquant: { quality: [0.8, 0.9], speed: 4 },
      svgo: {
        plugins: [
          { name: 'removeViewBox', active: false },
          { name: 'removeEmptyAttrs', active: true },
        ],
      },
    }),
  ],
});
```

## Summary and Deployment Checklist

### Pre-Deployment Checklist

**✅ Before deploying:**

- [ ] Run production build locally (`npm run build`)
- [ ] Test production build (`npm run preview`)
- [ ] Check bundle size and optimize if needed
- [ ] Run all tests (`npm run test`)
- [ ] Check test coverage meets requirements
- [ ] Run E2E tests (`npm run test:e2e`)
- [ ] Verify environment variables are set
- [ ] Review and update security headers
- [ ] Enable error tracking (Sentry)
- [ ] Configure analytics
- [ ] Set up monitoring
- [ ] Test on multiple browsers
- [ ] Test on mobile devices
- [ ] Check accessibility (Lighthouse)
- [ ] Verify SEO meta tags
- [ ] Update documentation
- [ ] Tag release in version control

**🚀 Post-Deployment:**

- [ ] Verify application loads correctly
- [ ] Check error tracking dashboard
- [ ] Monitor performance metrics
- [ ] Test critical user flows
- [ ] Verify API integrations
- [ ] Check analytics data
- [ ] Review server logs
- [ ] Monitor uptime
- [ ] Test rollback procedure

### 2025 Deployment Standards

**Platform Recommendations:**

- **Vercel**: Best for Next.js, automatic preview deployments
- **Netlify**: Best for Vite/static sites, great free tier
- **AWS Amplify**: Best for AWS integration, full-stack apps
- **Docker**: Best for self-hosting, maximum control

**CI/CD Best Practices:**

1. Automate testing in pull requests
2. Deploy previews for every PR
3. Require passing tests before merge
4. Automate production deployments from main branch
5. Enable automatic rollbacks on errors
6. Monitor deployment metrics

**Performance Goals:**

- **First Contentful Paint (FCP)**: < 1.8s
- **Largest Contentful Paint (LCP)**: < 2.5s
- **Time to Interactive (TTI)**: < 3.8s
- **Total Blocking Time (TBT)**: < 200ms
- **Cumulative Layout Shift (CLS)**: < 0.1
