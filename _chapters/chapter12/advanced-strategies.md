---
title: "Advanced Asset Strategies"
parent: "Modern Asset Management in React"
layout: chapter
permalink: /chapters/chapter12/advanced-strategies/
---

## Advanced Asset Strategies

Production React applications require sophisticated asset management strategies to deliver optimal performance at scale. This section covers advanced techniques used by high-traffic applications.

## Overview

Learn about:

- Code splitting for assets
- Preloading and prefetching strategies
- CDN integration and optimization
- Dynamic asset loading patterns

## Code Splitting for Assets

Split assets into separate bundles to reduce initial load time.

### Dynamic Import for Large Assets

```tsx
import { lazy, Suspense, useState } from 'react'

// Lazy load heavy image gallery component
const ImageGallery = lazy(() => import('./ImageGallery'))

function App() {
  const [showGallery, setShowGallery] = useState(false)

  return (
    <div>
      <button onClick={() => setShowGallery(true)}>
        View Gallery
      </button>
      
      {showGallery && (
        <Suspense fallback={<div>Loading gallery...</div>}>
          <ImageGallery />
        </Suspense>
      )}
    </div>
  )
}
```

### Route-Based Asset Loading

```tsx
import { lazy, Suspense } from 'react'
import { BrowserRouter, Routes, Route } from 'react-router-dom'

// Each route loads its own assets
const Home = lazy(() => import('./pages/Home'))
const Gallery = lazy(() => import('./pages/Gallery'))
const About = lazy(() => import('./pages/About'))

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/gallery" element={<Gallery />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  )
}
```

### Component-Level Asset Splitting

```tsx
import { useState, useEffect } from 'react'

interface VideoPlayerProps {
  src: string
}

function VideoPlayer({ src }: VideoPlayerProps) {
  const [VideoComponent, setVideoComponent] = useState<React.ComponentType | null>(null)

  useEffect(() => {
    // Load video player library only when needed
    import('./HeavyVideoPlayer').then(module => {
      setVideoComponent(() => module.default)
    })
  }, [])

  if (!VideoComponent) {
    return <div>Initializing player...</div>
  }

  return <VideoComponent src={src} />
}
```

## Preloading Strategies

Intelligently preload assets to improve perceived performance.

### Link Preload

```tsx
import { useEffect } from 'react'

function usePreloadImage(src: string) {
  useEffect(() => {
    const link = document.createElement('link')
    link.rel = 'preload'
    link.as = 'image'
    link.href = src
    document.head.appendChild(link)

    return () => {
      document.head.removeChild(link)
    }
  }, [src])
}

function HeroSection({ imageSrc }: { imageSrc: string }) {
  usePreloadImage(imageSrc)
  
  return (
    <div>
      <img src={imageSrc} alt="Hero" />
    </div>
  )
}
```

### Intelligent Prefetching

```tsx
import { useEffect, useRef } from 'react'

interface PrefetchOnHoverProps {
  href: string
  prefetchImages?: string[]
  children: React.ReactNode
}

function PrefetchOnHover({ href, prefetchImages = [], children }: PrefetchOnHoverProps) {
  const prefetched = useRef(false)

  const handleMouseEnter = () => {
    if (prefetched.current) return
    prefetched.current = true

    // Prefetch route
    const link = document.createElement('link')
    link.rel = 'prefetch'
    link.href = href
    document.head.appendChild(link)

    // Prefetch images
    prefetchImages.forEach(src => {
      const img = new Image()
      img.src = src
    })
  }

  return (
    <a href={href} onMouseEnter={handleMouseEnter}>
      {children}
    </a>
  )
}

// Usage
function Navigation() {
  return (
    <nav>
      <PrefetchOnHover 
        href="/gallery" 
        prefetchImages={['/gallery-hero.jpg']}
      >
        Gallery
      </PrefetchOnHover>
    </nav>
  )
}
```

### Predictive Prefetching

```tsx
import { useEffect } from 'react'

interface PrefetchConfig {
  routes: string[]
  images: string[]
  priority: 'low' | 'high'
}

function usePredictivePrefetch(config: PrefetchConfig) {
  useEffect(() => {
    // Wait for idle time
    if ('requestIdleCallback' in window) {
      requestIdleCallback(() => {
        prefetchAssets(config)
      })
    } else {
      setTimeout(() => prefetchAssets(config), 1000)
    }
  }, [config])
}

function prefetchAssets({ routes, images, priority }: PrefetchConfig) {
  // Prefetch routes
  routes.forEach(route => {
    const link = document.createElement('link')
    link.rel = 'prefetch'
    link.href = route
    if (priority === 'high') {
      link.setAttribute('importance', 'high')
    }
    document.head.appendChild(link)
  })

  // Prefetch images
  images.forEach(src => {
    const link = document.createElement('link')
    link.rel = 'prefetch'
    link.as = 'image'
    link.href = src
    document.head.appendChild(link)
  })
}
```

## CDN Integration

Leverage Content Delivery Networks for global asset delivery.

### CDN Configuration

```tsx
// config/cdn.ts
export const CDN_CONFIG = {
  baseUrl: process.env.REACT_APP_CDN_URL || '',
  regions: {
    'us-east': 'https://cdn-us-east.example.com',
    'eu-west': 'https://cdn-eu-west.example.com',
    'ap-south': 'https://cdn-ap-south.example.com'
  }
}

export function getCdnUrl(path: string, region?: string): string {
  if (region && CDN_CONFIG.regions[region]) {
    return `${CDN_CONFIG.regions[region]}${path}`
  }
  return `${CDN_CONFIG.baseUrl}${path}`
}

// Detect user region
export function detectRegion(): string {
  // Use geolocation API or IP-based detection
  const timezone = Intl.DateTimeFormat().resolvedOptions().timeZone
  
  if (timezone.startsWith('America/')) return 'us-east'
  if (timezone.startsWith('Europe/')) return 'eu-west'
  if (timezone.startsWith('Asia/')) return 'ap-south'
  
  return 'us-east' // Default
}
```

### CDN Image Component

```tsx
import { getCdnUrl, detectRegion } from './config/cdn'

interface CdnImageProps {
  src: string
  alt: string
  transforms?: {
    width?: number
    height?: number
    quality?: number
    format?: 'webp' | 'avif' | 'jpg'
  }
}

function CdnImage({ src, alt, transforms }: CdnImageProps) {
  const region = detectRegion()
  
  // Build CDN URL with transforms
  const buildUrl = () => {
    let url = getCdnUrl(src, region)
    
    if (transforms) {
      const params = new URLSearchParams()
      if (transforms.width) params.append('w', transforms.width.toString())
      if (transforms.height) params.append('h', transforms.height.toString())
      if (transforms.quality) params.append('q', transforms.quality.toString())
      if (transforms.format) params.append('f', transforms.format)
      
      url += `?${params.toString()}`
    }
    
    return url
  }

  return (
    <img 
      src={buildUrl()} 
      alt={alt}
      loading="lazy"
    />
  )
}

// Usage
function ProductImage() {
  return (
    <CdnImage
      src="/products/widget.jpg"
      alt="Widget"
      transforms={% raw %}{{
        width: 800,
        quality: 85,
        format: 'webp'
      }}{% endraw %}
    />
  )
}
```

### CDN Fallback Strategy

```tsx
import { useState, useEffect } from 'react'

interface CdnImageWithFallbackProps {
  src: string
  alt: string
  fallbackSrc?: string
}

function CdnImageWithFallback({ 
  src, 
  alt, 
  fallbackSrc 
}: CdnImageWithFallbackProps) {
  const [imageSrc, setImageSrc] = useState(getCdnUrl(src))
  const [error, setError] = useState(false)

  const handleError = () => {
    if (!error) {
      setError(true)
      // Try fallback or origin server
      setImageSrc(fallbackSrc || src)
    }
  }

  return (
    <img 
      src={imageSrc} 
      alt={alt}
      onError={handleError}
      loading="lazy"
    />
  )
}
```

## Dynamic Asset Loading

Load assets based on runtime conditions.

### Conditional Asset Loading

```tsx
import { useEffect, useState } from 'react'

interface ConditionalAssetProps {
  lowQualitySrc: string
  highQualitySrc: string
  alt: string
}

function ConditionalAsset({ 
  lowQualitySrc, 
  highQualitySrc, 
  alt 
}: ConditionalAssetProps) {
  const [src, setSrc] = useState(lowQualitySrc)

  useEffect(() => {
    // Check connection speed
    const connection = (navigator as any).connection
    
    if (connection) {
      const effectiveType = connection.effectiveType
      
      // Use high quality on fast connections
      if (effectiveType === '4g') {
        setSrc(highQualitySrc)
      }
      
      // Monitor connection changes
      connection.addEventListener('change', () => {
        if (connection.effectiveType === '4g') {
          setSrc(highQualitySrc)
        } else {
          setSrc(lowQualitySrc)
        }
      })
    } else {
      // Default to high quality if API not available
      setSrc(highQualitySrc)
    }
  }, [lowQualitySrc, highQualitySrc])

  return <img src={src} alt={alt} loading="lazy" />
}
```

### Data Saver Mode

```tsx
import { createContext, useContext, useEffect, useState } from 'react'

interface DataSaverContextType {
  dataSaverMode: boolean
  setDataSaverMode: (enabled: boolean) => void
}

const DataSaverContext = createContext<DataSaverContextType>({
  dataSaverMode: false,
  setDataSaverMode: () => {}
})

export function DataSaverProvider({ children }: { children: React.ReactNode }) {
  const [dataSaverMode, setDataSaverMode] = useState(false)

  useEffect(() => {
    // Check for browser data saver
    const connection = (navigator as any).connection
    if (connection?.saveData) {
      setDataSaverMode(true)
    }

    // Load user preference
    const saved = localStorage.getItem('dataSaverMode')
    if (saved) {
      setDataSaverMode(JSON.parse(saved))
    }
  }, [])

  useEffect(() => {
    localStorage.setItem('dataSaverMode', JSON.stringify(dataSaverMode))
  }, [dataSaverMode])

  return (
    <DataSaverContext.Provider value={% raw %}{{ dataSaverMode, setDataSaverMode }}{% endraw %}>
      {children}
    </DataSaverContext.Provider>
  )
}

export function useDataSaver() {
  return useContext(DataSaverContext)
}

// Usage
function OptimizedImage({ src, alt }: { src: string; alt: string }) {
  const { dataSaverMode } = useDataSaver()
  
  const imageSrc = dataSaverMode
    ? `${src}?q=50&w=600` // Lower quality/size
    : src

  return <img src={imageSrc} alt={alt} loading="lazy" />
}
```

## Asset Versioning and Cache Busting

Ensure users get fresh assets after deployments.

### Hash-Based Versioning

```tsx
// Build time asset manifest
interface AssetManifest {
  [key: string]: string
}

// Generated during build
const manifest: AssetManifest = {
  'hero.jpg': 'hero.abc123.jpg',
  'logo.svg': 'logo.def456.svg'
}

function getAssetUrl(assetName: string): string {
  return `/assets/${manifest[assetName] || assetName}`
}

// Usage
function Logo() {
  return <img src={getAssetUrl('logo.svg')} alt="Logo" />
}
```

### Service Worker Cache Strategy

```tsx
// sw.ts - Service Worker
const CACHE_VERSION = 'v1'
const ASSET_CACHE = `assets-${CACHE_VERSION}`

self.addEventListener('install', (event: any) => {
  event.waitUntil(
    caches.open(ASSET_CACHE).then(cache => {
      return cache.addAll([
        '/assets/critical.css',
        '/assets/logo.svg',
        '/assets/hero.jpg'
      ])
    })
  )
})

self.addEventListener('fetch', (event: any) => {
  if (event.request.destination === 'image') {
    event.respondWith(
      caches.match(event.request).then(response => {
        if (response) {
          // Serve from cache
          return response
        }
        
        // Fetch and cache
        return fetch(event.request).then(response => {
          return caches.open(ASSET_CACHE).then(cache => {
            cache.put(event.request, response.clone())
            return response
          })
        })
      })
    )
  }
})
```

## Performance Budgets

Set and enforce performance limits for assets.

### Bundle Size Monitoring

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          images: ['./src/assets/images']
        }
      }
    }
  },
  plugins: [
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true
    })
  ]
})
```

### Runtime Performance Monitoring

```tsx
import { useEffect } from 'react'

interface PerformanceBudget {
  images: number // Max MB
  scripts: number
  total: number
}

const BUDGET: PerformanceBudget = {
  images: 2,
  scripts: 1,
  total: 3
}

function usePerformanceBudget() {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      let totalImages = 0
      let totalScripts = 0

      for (const entry of list.getEntries()) {
        const size = (entry as any).transferSize / 1024 / 1024 // MB
        
        if (entry.initiatorType === 'img') {
          totalImages += size
        } else if (entry.initiatorType === 'script') {
          totalScripts += size
        }
      }

      if (totalImages > BUDGET.images) {
        console.warn(`Image budget exceeded: ${totalImages.toFixed(2)}MB`)
      }
      
      if (totalScripts > BUDGET.scripts) {
        console.warn(`Script budget exceeded: ${totalScripts.toFixed(2)}MB`)
      }
    })

    observer.observe({ entryTypes: ['resource'] })

    return () => observer.disconnect()
  }, [])
}
```

## Best Practices Summary

1. **Implement code splitting** to reduce initial bundle size
2. **Use intelligent prefetching** based on user behavior
3. **Leverage CDNs** for global asset delivery
4. **Adapt to network conditions** with conditional loading
5. **Implement proper caching strategies** with service workers
6. **Monitor performance budgets** to prevent regression
7. **Version assets** for effective cache busting
8. **Consider data saver modes** for mobile users

## Summary

Advanced asset strategies are essential for production applications at scale. By combining code splitting, intelligent preloading, CDN integration, and adaptive loading, you can deliver exceptional performance to users worldwide.

**Next Steps:**

- Learn about [Asset Security and Optimization](./security-optimization/) for comprehensive production best practices

**Key Takeaways:**

1. Code splitting reduces initial load time significantly
2. Intelligent prefetching improves perceived performance
3. CDN integration provides global performance optimization
4. Adaptive loading respects user preferences and conditions
5. Performance monitoring prevents budget violations
