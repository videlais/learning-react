---
title: "Asset Security and Optimization"
parent: "Modern Asset Management in React"
layout: chapter
permalink: /chapters/chapter12/security-optimization/
---

## Asset Security and Optimization

Security and optimization are critical concerns for production React applications. This section covers best practices for securing assets and maintaining optimal performance.

## Overview

Learn about:

- Content Security Policy (CSP) implementation
- Asset integrity verification
- Bundle analysis and optimization
- Caching strategies

## Content Security Policy

Protect your application from XSS and injection attacks.

### Basic CSP Configuration

```tsx
// Set CSP headers in your server or meta tag
export function SecurityHeaders() {
  return (
    <head>
      <meta
        httpEquiv="Content-Security-Policy"
        content="
          default-src 'self';
          script-src 'self' 'unsafe-inline' 'unsafe-eval' https://trusted-cdn.com;
          style-src 'self' 'unsafe-inline' https://trusted-cdn.com;
          img-src 'self' data: https: blob:;
          font-src 'self' https://trusted-cdn.com;
          connect-src 'self' https://api.example.com;
          media-src 'self' https://media.example.com;
          object-src 'none';
          base-uri 'self';
          form-action 'self';
          frame-ancestors 'none';
          upgrade-insecure-requests;
        "
      />
    </head>
  )
}
```

### CSP for Dynamic Assets

```tsx
// utils/csp.ts
interface CspConfig {
  allowedImageDomains: string[]
  allowedScriptDomains: string[]
  allowedStyleDomains: string[]
}

export function generateCsp(config: CspConfig): string {
  return [
    "default-src 'self'",
    `img-src 'self' ${config.allowedImageDomains.join(' ')}`,
    `script-src 'self' ${config.allowedScriptDomains.join(' ')}`,
    `style-src 'self' ${config.allowedStyleDomains.join(' ')}`,
    "font-src 'self' data:",
    "connect-src 'self'",
    "object-src 'none'",
    "base-uri 'self'"
  ].join('; ')
}

// Usage in Next.js
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: generateCsp({
              allowedImageDomains: ['https://cdn.example.com', 'https://images.example.com'],
              allowedScriptDomains: ['https://cdn.example.com'],
              allowedStyleDomains: ['https://cdn.example.com']
            })
          }
        ]
      }
    ]
  }
}
```

### CSP Violation Reporting

```tsx
import { useEffect } from 'react'

function useCspReporting() {
  useEffect(() => {
    document.addEventListener('securitypolicyviolation', (event) => {
      console.error('CSP Violation:', {
        blockedURI: event.blockedURI,
        violatedDirective: event.violatedDirective,
        originalPolicy: event.originalPolicy
      })

      // Send to monitoring service
      fetch('/api/csp-report', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          blockedURI: event.blockedURI,
          violatedDirective: event.violatedDirective,
          timestamp: new Date().toISOString()
        })
      })
    })
  }, [])
}
```

## Subresource Integrity

Verify that fetched resources haven't been tampered with.

### SRI for CDN Assets

```tsx
interface ExternalScriptProps {
  src: string
  integrity: string
  crossOrigin?: 'anonymous' | 'use-credentials'
}

function ExternalScript({ src, integrity, crossOrigin = 'anonymous' }: ExternalScriptProps) {
  return (
    <script
      src={src}
      integrity={integrity}
      crossOrigin={crossOrigin}
    />
  )
}

// Usage
function App() {
  return (
    <>
      <ExternalScript
        src="https://cdn.example.com/library.js"
        integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
      />
    </>
  )
}
```

### Generate SRI Hashes

```typescript
import crypto from 'crypto'
import fs from 'fs'

export function generateSriHash(filePath: string): string {
  const fileContent = fs.readFileSync(filePath)
  const hash = crypto.createHash('sha384')
    .update(fileContent)
    .digest('base64')
  
  return `sha384-${hash}`
}

// Build script usage
const hash = generateSriHash('./dist/main.js')
console.log(`<script src="main.js" integrity="${hash}"></script>`)
```

## Bundle Analysis

Understand and optimize your bundle composition.

### Webpack Bundle Analyzer

```typescript
// webpack.config.js
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
      reportFilename: 'bundle-report.html'
    })
  ]
}
```

### Vite Bundle Analysis

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    visualizer({
      filename: './dist/stats.html',
      open: true,
      gzipSize: true,
      brotliSize: true
    })
  ]
})
```

### Runtime Bundle Monitoring

```tsx
import { useEffect } from 'react'

interface BundleMetrics {
  totalSize: number
  chunkSizes: Record<string, number>
  loadTimes: Record<string, number>
}

function useBundleMetrics() {
  useEffect(() => {
    const metrics: BundleMetrics = {
      totalSize: 0,
      chunkSizes: {},
      loadTimes: {}
    }

    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.initiatorType === 'script') {
          const resource = entry as PerformanceResourceTiming
          metrics.chunkSizes[entry.name] = resource.transferSize
          metrics.loadTimes[entry.name] = resource.duration
          metrics.totalSize += resource.transferSize
        }
      }

      console.log('Bundle Metrics:', metrics)
    })

    observer.observe({ entryTypes: ['resource'] })

    return () => observer.disconnect()
  }, [])
}
```

## Caching Strategies

Implement effective caching for optimal performance.

### HTTP Caching Headers

```typescript
// server/middleware/cache.ts
import { Request, Response, NextFunction } from 'express'

interface CacheOptions {
  maxAge: number // seconds
  immutable?: boolean
  public?: boolean
}

export function setCacheHeaders(options: CacheOptions) {
  return (req: Request, res: Response, next: NextFunction) => {
    const directives = [
      options.public ? 'public' : 'private',
      `max-age=${options.maxAge}`
    ]

    if (options.immutable) {
      directives.push('immutable')
    }

    res.setHeader('Cache-Control', directives.join(', '))
    next()
  }
}

// Usage
app.use('/static/images', setCacheHeaders({
  maxAge: 31536000, // 1 year
  immutable: true,
  public: true
}))

app.use('/api', setCacheHeaders({
  maxAge: 0,
  public: false
}))
```

### Service Worker Caching

```typescript
// sw.ts
const CACHE_VERSION = 'v1'
const STATIC_CACHE = `static-${CACHE_VERSION}`
const DYNAMIC_CACHE = `dynamic-${CACHE_VERSION}`

// Static assets to precache
const STATIC_ASSETS = [
  '/',
  '/static/css/main.css',
  '/static/js/main.js',
  '/logo.svg'
]

// Install event - precache static assets
self.addEventListener('install', (event: any) => {
  event.waitUntil(
    caches.open(STATIC_CACHE).then(cache => {
      return cache.addAll(STATIC_ASSETS)
    })
  )
})

// Activate event - clean up old caches
self.addEventListener('activate', (event: any) => {
  event.waitUntil(
    caches.keys().then(keys => {
      return Promise.all(
        keys
          .filter(key => key !== STATIC_CACHE && key !== DYNAMIC_CACHE)
          .map(key => caches.delete(key))
      )
    })
  )
})

// Fetch event - cache strategies
self.addEventListener('fetch', (event: any) => {
  const { request } = event

  // Cache-first for images
  if (request.destination === 'image') {
    event.respondWith(
      caches.match(request).then(response => {
        return response || fetch(request).then(fetchResponse => {
          return caches.open(DYNAMIC_CACHE).then(cache => {
            cache.put(request, fetchResponse.clone())
            return fetchResponse
          })
        })
      })
    )
  }

  // Network-first for API calls
  else if (request.url.includes('/api/')) {
    event.respondWith(
      fetch(request)
        .then(response => {
          return caches.open(DYNAMIC_CACHE).then(cache => {
            cache.put(request, response.clone())
            return response
          })
        })
        .catch(() => caches.match(request))
    )
  }

  // Stale-while-revalidate for other assets
  else {
    event.respondWith(
      caches.match(request).then(cachedResponse => {
        const fetchPromise = fetch(request).then(networkResponse => {
          caches.open(DYNAMIC_CACHE).then(cache => {
            cache.put(request, networkResponse.clone())
          })
          return networkResponse
        })

        return cachedResponse || fetchPromise
      })
    )
  }
})
```

### React Cache API

```tsx
import { cache } from 'react'

// Deduplicate asset fetches
export const fetchAsset = cache(async (url: string): Promise<Blob> => {
  const response = await fetch(url)
  if (!response.ok) {
    throw new Error(`Failed to fetch ${url}`)
  }
  return response.blob()
})

// Usage in component
async function ImageComponent({ src }: { src: string }) {
  const imageBlob = await fetchAsset(src)
  const imageUrl = URL.createObjectURL(imageBlob)
  
  return <img src={imageUrl} alt="Cached" />
}
```

## Asset Optimization Checklist

### Image Optimization

```typescript
interface ImageOptimizationConfig {
  maxWidth: number
  maxHeight: number
  quality: number
  format: 'webp' | 'avif' | 'jpg'
}

const IMAGE_OPTIMIZATION: Record<string, ImageOptimizationConfig> = {
  thumbnail: {
    maxWidth: 200,
    maxHeight: 200,
    quality: 75,
    format: 'webp'
  },
  preview: {
    maxWidth: 800,
    maxHeight: 600,
    quality: 85,
    format: 'webp'
  },
  fullsize: {
    maxWidth: 1920,
    maxHeight: 1080,
    quality: 90,
    format: 'webp'
  }
}

export function getOptimizedImageUrl(
  src: string,
  type: keyof typeof IMAGE_OPTIMIZATION
): string {
  const config = IMAGE_OPTIMIZATION[type]
  const params = new URLSearchParams({
    w: config.maxWidth.toString(),
    h: config.maxHeight.toString(),
    q: config.quality.toString(),
    f: config.format
  })
  
  return `${src}?${params.toString()}`
}
```

### Compression Configuration

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import viteCompression from 'vite-plugin-compression'

export default defineConfig({
  plugins: [
    // Brotli compression
    viteCompression({
      algorithm: 'brotliCompress',
      ext: '.br',
      threshold: 1024 // Only compress files > 1KB
    }),
    // Gzip compression
    viteCompression({
      algorithm: 'gzip',
      ext: '.gz',
      threshold: 1024
    })
  ]
})
```

## Security Best Practices

### 1. Validate Asset Sources

```tsx
const ALLOWED_DOMAINS = [
  'cdn.example.com',
  'images.example.com'
]

function validateImageUrl(url: string): boolean {
  try {
    const parsed = new URL(url)
    return ALLOWED_DOMAINS.includes(parsed.hostname)
  } catch {
    return false
  }
}

interface SecureImageProps {
  src: string
  alt: string
}

function SecureImage({ src, alt }: SecureImageProps) {
  if (!validateImageUrl(src)) {
    console.error('Invalid image source:', src)
    return <div>Invalid image source</div>
  }

  return <img src={src} alt={alt} />
}
```

### 2. Sanitize User-Uploaded Assets

```typescript
import DOMPurify from 'dompurify'

export function sanitizeSvg(svgContent: string): string {
  return DOMPurify.sanitize(svgContent, {
    USE_PROFILES: { svg: true, svgFilters: true }
  })
}

// Usage
function UserUploadedSvg({ content }: { content: string }) {
  const sanitized = sanitizeSvg(content)
  
  return (
    <div dangerouslySetInnerHTML={% raw %}{{ __html: sanitized }}{% endraw %} />
  )
}
```

### 3. Implement Rate Limiting

```typescript
// server/middleware/rateLimit.ts
import rateLimit from 'express-rate-limit'

export const assetRateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many asset requests, please try again later.',
  standardHeaders: true,
  legacyHeaders: false
})

// Usage
app.use('/api/upload', assetRateLimiter)
```

### 4. Prevent Hotlinking

```typescript
// server/middleware/referer.ts
export function preventHotlinking(req: Request, res: Response, next: NextFunction) {
  const referer = req.get('Referer')
  const allowedDomains = ['example.com', 'www.example.com']
  
  if (referer) {
    const refererDomain = new URL(referer).hostname
    if (!allowedDomains.includes(refererDomain)) {
      return res.status(403).send('Hotlinking not allowed')
    }
  }
  
  next()
}

// Usage
app.use('/images', preventHotlinking)
```

## Performance Optimization Checklist

- [ ] Enable HTTP/2 or HTTP/3 for multiplexing
- [ ] Implement Brotli compression for assets
- [ ] Use responsive images with srcset
- [ ] Lazy load below-the-fold images
- [ ] Implement progressive image loading
- [ ] Use modern image formats (WebP, AVIF)
- [ ] Set appropriate cache headers
- [ ] Implement service worker caching
- [ ] Minify CSS and JavaScript
- [ ] Remove unused code with tree shaking
- [ ] Split code by route
- [ ] Preload critical assets
- [ ] Use CDN for static assets
- [ ] Monitor bundle size
- [ ] Implement performance budgets

## Monitoring and Reporting

### Performance Monitoring

```tsx
import { useEffect } from 'react'

interface PerformanceMetrics {
  fcp: number // First Contentful Paint
  lcp: number // Largest Contentful Paint
  fid: number // First Input Delay
  cls: number // Cumulative Layout Shift
  ttfb: number // Time to First Byte
}

function usePerformanceMonitoring() {
  useEffect(() => {
    const metrics: Partial<PerformanceMetrics> = {}

    // FCP
    new PerformanceObserver((list) => {
      const entries = list.getEntries()
      const fcp = entries.find(e => e.name === 'first-contentful-paint')
      if (fcp) metrics.fcp = fcp.startTime
    }).observe({ entryTypes: ['paint'] })

    // LCP
    new PerformanceObserver((list) => {
      const entries = list.getEntries()
      const lcp = entries[entries.length - 1]
      if (lcp) metrics.lcp = lcp.startTime
    }).observe({ entryTypes: ['largest-contentful-paint'] })

    // CLS
    new PerformanceObserver((list) => {
      let cls = 0
      for (const entry of list.getEntries()) {
        if (!(entry as any).hadRecentInput) {
          cls += (entry as any).value
        }
      }
      metrics.cls = cls
    }).observe({ entryTypes: ['layout-shift'] })

    // Send to analytics
    window.addEventListener('beforeunload', () => {
      if (Object.keys(metrics).length > 0) {
        navigator.sendBeacon('/api/metrics', JSON.stringify(metrics))
      }
    })
  }, [])
}
```

## Summary

Security and optimization are ongoing processes that require constant attention. By implementing CSP, SRI, effective caching, and monitoring, you can ensure your assets are both secure and performant.

**Key Takeaways:**

1. Content Security Policy prevents injection attacks
2. Subresource Integrity verifies asset authenticity
3. Bundle analysis identifies optimization opportunities
4. Effective caching strategies improve performance
5. Security measures protect user data and application integrity
6. Continuous monitoring ensures sustained performance

## Conclusion

Modern React asset management combines performance, security, and user experience. By mastering the techniques in this chapter, you can build production-ready applications that deliver exceptional performance while maintaining robust security.

**Remember:**

- Performance is a feature, not an afterthought
- Security should be built in from the start
- Monitor and iterate continuously
- Stay updated with evolving best practices

**Further Reading:**

- [Web.dev Performance Guide](https://web.dev/performance/)
- [MDN Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [React Performance Optimization](https://react.dev/learn/render-and-commit)
