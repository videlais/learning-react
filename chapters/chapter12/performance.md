---
title: "Performance-Optimized Image Loading"
parent: "Modern Asset Management in React"
layout: chapter
permalink: /chapters/chapter12/performance/
---

## Performance-Optimized Image Loading

Performance is critical for modern web applications. Images are often the largest assets, making optimization essential for fast load times and excellent user experience.

## Overview

This section covers:

- Lazy loading strategies for images
- Responsive image implementation  
- Modern image format optimization
- Progressive loading techniques

## Lazy Loading Images

Lazy loading defers image loading until they're needed, dramatically improving initial page load time.

### Native Lazy Loading

Modern browsers support native lazy loading:

```tsx
interface ImageProps {
  src: string
  alt: string
  loading?: 'lazy' | 'eager'
}

function OptimizedImage({ src, alt, loading = 'lazy' }: ImageProps) {
  return (
    <img 
      src={src} 
      alt={alt}
      loading={loading}
      decoding="async"
    />
  )
}

// Usage
function Gallery() {
  return (
    <div>
      <OptimizedImage src="/hero.jpg" alt="Hero" loading="eager" />
      <OptimizedImage src="/photo1.jpg" alt="Photo 1" />
      <OptimizedImage src="/photo2.jpg" alt="Photo 2" />
    </div>
  )
}
```

### Intersection Observer API

For more control over lazy loading behavior:

```tsx
import { useEffect, useRef, useState } from 'react'

interface LazyImageProps {
  src: string
  alt: string
  placeholder?: string
  threshold?: number
}

function LazyImage({ 
  src, 
  alt, 
  placeholder = '/placeholder.jpg',
  threshold = 0.1 
}: LazyImageProps) {
  const [imageSrc, setImageSrc] = useState(placeholder)
  const [isLoaded, setIsLoaded] = useState(false)
  const imageRef = useRef<HTMLImageElement>(null)

  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            setImageSrc(src)
            observer.disconnect()
          }
        })
      },
      { threshold }
    )

    if (imageRef.current) {
      observer.observe(imageRef.current)
    }

    return () => observer.disconnect()
  }, [src, threshold])

  return (
    <img
      ref={imageRef}
      src={imageSrc}
      alt={alt}
      className={isLoaded ? 'loaded' : 'loading'}
      onLoad={() => setIsLoaded(true)}
      style={% raw %}{{
        transition: 'opacity 0.3s ease-in-out',
        opacity: isLoaded ? 1 : 0.5
      }}{% endraw %}
    />
  )
}
```

### React Suspense for Images

Using React 18+ Suspense for declarative loading states:

```tsx
import { Suspense, lazy } from 'react'

// Image preloader utility
function preloadImage(src: string): Promise<string> {
  return new Promise((resolve, reject) => {
    const img = new Image()
    img.onload = () => resolve(src)
    img.onerror = reject
    img.src = src
  })
}

// Suspense-compatible image resource
function createImageResource(src: string) {
  let status = 'pending'
  let result: string
  
  const suspender = preloadImage(src).then(
    (url) => {
      status = 'success'
      result = url
    },
    (error) => {
      status = 'error'
      result = error
    }
  )

  return {
    read() {
      if (status === 'pending') throw suspender
      if (status === 'error') throw result
      return result
    }
  }
}

// Suspense image component
interface SuspenseImageProps {
  src: string
  alt: string
}

const imageCache = new Map<string, ReturnType<typeof createImageResource>>()

function SuspenseImage({ src, alt }: SuspenseImageProps) {
  if (!imageCache.has(src)) {
    imageCache.set(src, createImageResource(src))
  }
  
  const resource = imageCache.get(src)!
  const imageSrc = resource.read()
  
  return <img src={imageSrc} alt={alt} />
}

// Usage with fallback
function Gallery() {
  return (
    <Suspense fallback={<div>Loading image...</div>}>
      <SuspenseImage src="/photo.jpg" alt="Photo" />
    </Suspense>
  )
}
```

## Responsive Images

Serve appropriately sized images for different screen sizes and resolutions.

### srcset and sizes

```tsx
interface ResponsiveImageProps {
  src: string
  alt: string
  sizes?: string
}

function ResponsiveImage({ src, alt, sizes = '100vw' }: ResponsiveImageProps) {
  // Generate srcset for different widths
  const widths = [320, 640, 960, 1280, 1920]
  const srcset = widths
    .map(width => `${src}?w=${width} ${width}w`)
    .join(', ')

  return (
    <img
      src={src}
      srcSet={srcset}
      sizes={sizes}
      alt={alt}
      loading="lazy"
    />
  )
}

// Usage with specific sizes
function ProductImage() {
  return (
    <ResponsiveImage
      src="/product.jpg"
      alt="Product"
      sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
    />
  )
}
```

### Picture Element for Art Direction

```tsx
interface ArtDirectedImageProps {
  mobileImage: string
  tabletImage: string
  desktopImage: string
  alt: string
}

function ArtDirectedImage({
  mobileImage,
  tabletImage,
  desktopImage,
  alt
}: ArtDirectedImageProps) {
  return (
    <picture>
      <source 
        media="(max-width: 767px)" 
        srcSet={mobileImage}
      />
      <source 
        media="(max-width: 1023px)" 
        srcSet={tabletImage}
      />
      <img 
        src={desktopImage} 
        alt={alt}
        loading="lazy"
      />
    </picture>
  )
}
```

## Modern Image Formats

Leverage modern formats like WebP and AVIF for better compression.

### Format Detection and Fallbacks

```tsx
interface ModernImageProps {
  src: string // Base image path without extension
  alt: string
  fallback?: string
}

function ModernImage({ src, alt, fallback }: ModernImageProps) {
  return (
    <picture>
      {/* Try AVIF first (best compression) */}
      <source 
        type="image/avif" 
        srcSet={`${src}.avif`}
      />
      
      {/* Fallback to WebP */}
      <source 
        type="image/webp" 
        srcSet={`${src}.webp`}
      />
      
      {/* Final fallback to JPEG/PNG */}
      <img 
        src={fallback || `${src}.jpg`}
        alt={alt}
        loading="lazy"
      />
    </picture>
  )
}

// Usage
function Hero() {
  return (
    <ModernImage
      src="/images/hero"
      alt="Hero image"
      fallback="/images/hero.jpg"
    />
  )
}
```

### Dynamic Format Selection

```tsx
import { useState, useEffect } from 'react'

function useImageFormatSupport() {
  const [formats, setFormats] = useState({
    avif: false,
    webp: false
  })

  useEffect(() => {
    const checkFormat = async (format: 'avif' | 'webp') => {
      const testImages = {
        avif: 'data:image/avif;base64,AAAAIGZ0eXBhdmlmAAAAAGF2aWZtaWYxbWlhZk1BMUIAAADybWV0YQAAAAAAAAAoaGRscgAAAAAAAAAAcGljdAAAAAAAAAAAAAAAAGxpYmF2aWYAAAAADnBpdG0AAAAAAAEAAAAeaWxvYwAAAABEAAABAAEAAAABAAABGgAAAB0AAAAoaWluZgAAAAAAAQAAABppbmZlAgAAAAABAABhdjAxQ29sb3IAAAAAamlwcnAAAABLaXBjbwAAABRpc3BlAAAAAAAAAAIAAAACAAAAEHBpeGkAAAAAAwgICAAAAAxhdjFDgQ0MAAAAABNjb2xybmNseAACAAIAAYAAAAAXaXBtYQAAAAAAAAABAAEEAQKDBAAAACVtZGF0EgAKCBgANogQEAwgMg8f8D///8WfhwB8+ErK42A=',
        webp: 'data:image/webp;base64,UklGRiQAAABXRUJQVlA4IBgAAAAwAQCdASoBAAEAAwA0JaQAA3AA/vuUAAA='
      }

      try {
        const response = await fetch(testImages[format])
        const blob = await response.blob()
        return await createImageBitmap(blob)
          .then(() => true)
          .catch(() => false)
      } catch {
        return false
      }
    }

    Promise.all([
      checkFormat('avif'),
      checkFormat('webp')
    ]).then(([avif, webp]) => {
      setFormats({ avif, webp })
    })
  }, [])

  return formats
}

// Use format support
function SmartImage({ src, alt }: { src: string; alt: string }) {
  const { avif, webp } = useImageFormatSupport()
  
  const imageSrc = avif 
    ? `${src}.avif`
    : webp 
    ? `${src}.webp` 
    : `${src}.jpg`

  return <img src={imageSrc} alt={alt} loading="lazy" />
}
```

## Progressive Loading

Show low-quality placeholders while full images load.

### Blur-up Technique

```tsx
import { useState, useEffect } from 'react'

interface ProgressiveImageProps {
  src: string
  placeholder: string // Low-quality image
  alt: string
}

function ProgressiveImage({ src, placeholder, alt }: ProgressiveImageProps) {
  const [imageSrc, setImageSrc] = useState(placeholder)
  const [isLoading, setIsLoading] = useState(true)

  useEffect(() => {
    const img = new Image()
    img.src = src
    img.onload = () => {
      setImageSrc(src)
      setIsLoading(false)
    }
  }, [src])

  return (
    <div style={% raw %}{{ position: 'relative', overflow: 'hidden' }}{% endraw %}>
      <img
        src={imageSrc}
        alt={alt}
        style={% raw %}={{
          filter: isLoading ? 'blur(10px)' : 'none',
          transition: 'filter 0.3s ease-out',
          transform: isLoading ? 'scale(1.1)' : 'scale(1)'
        }}{% endraw %}
      />
    </div>
  )
}
```

### Low Quality Image Placeholder (LQIP)

```tsx
import { useState } from 'react'

interface LQIPImageProps {
  src: string
  lqip: string // Base64-encoded tiny image
  alt: string
  aspectRatio?: string
}

function LQIPImage({ 
  src, 
  lqip, 
  alt,
  aspectRatio = '16 / 9'
}: LQIPImageProps) {
  const [loaded, setLoaded] = useState(false)

  return (
    <div 
      style={% raw %}{{ 
        position: 'relative',
        aspectRatio,
        overflow: 'hidden',
        backgroundColor: '#f0f0f0'
      }}{% endraw %}
    >
      {/* LQIP background */}
      <img
        src={lqip}
        alt=""
        aria-hidden="true"
        style={% raw %}={{
          position: 'absolute',
          inset: 0,
          width: '100%',
          height: '100%',
          filter: 'blur(20px)',
          transform: 'scale(1.2)',
          opacity: loaded ? 0 : 1,
          transition: 'opacity 0.3s'
        }}{% endraw %}
      />
      
      {/* Full quality image */}
      <img
        src={src}
        alt={alt}
        loading="lazy"
        onLoad={() => setLoaded(true)}
        style={% raw %}={{
          position: 'absolute',
          inset: 0,
          width: '100%',
          height: '100%',
          objectFit: 'cover',
          opacity: loaded ? 1 : 0,
          transition: 'opacity 0.3s'
        }}{% endraw %}
      />
    </div>
  )
}
```

## Performance Monitoring

Track image loading performance to identify optimization opportunities.

### Performance Observer

```tsx
import { useEffect } from 'react'

function useImagePerformance() {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.initiatorType === 'img') {
          console.log({
            url: entry.name,
            duration: entry.duration,
            size: entry.transferSize,
            cached: entry.transferSize === 0
          })
        }
      }
    })

    observer.observe({ entryTypes: ['resource'] })

    return () => observer.disconnect()
  }, [])
}
```

### Core Web Vitals for Images

```tsx
import { useEffect } from 'react'

function useLargestContentfulPaint() {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      const entries = list.getEntries()
      const lastEntry = entries[entries.length - 1]
      
      console.log('LCP:', {
        element: lastEntry.element,
        renderTime: lastEntry.renderTime,
        loadTime: lastEntry.loadTime
      })
    })

    observer.observe({ entryTypes: ['largest-contentful-paint'] })

    return () => observer.disconnect()
  }, [])
}
```

## Best Practices Summary

1. **Always use lazy loading** for below-the-fold images
2. **Implement responsive images** with srcset and sizes
3. **Provide modern formats** (AVIF, WebP) with fallbacks
4. **Use progressive loading** for better perceived performance
5. **Monitor performance** to identify bottlenecks
6. **Set explicit dimensions** to prevent layout shift
7. **Use appropriate quality settings** (80-85% often sufficient)
8. **Consider CDN** for serving optimized images

## Summary

Performance-optimized image loading is essential for modern web applications. By combining lazy loading, responsive images, modern formats, and progressive techniques, you can dramatically improve load times and user experience.

**Next Steps:**

- Explore [Advanced Asset Strategies](./advanced-strategies/) for production optimization
- Learn about [Asset Security and Optimization](./security-optimization/) for comprehensive best practices

**Key Takeaways:**

1. Native lazy loading is widely supported and easy to implement
2. Responsive images reduce bandwidth usage significantly
3. Modern formats (AVIF, WebP) offer superior compression
4. Progressive loading improves perceived performance
5. Performance monitoring helps identify optimization opportunities
