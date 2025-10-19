---
title: "Modern Asset Management in React"
order: 12
chapter_number: 12
layout: chapter
permalink: /chapters/chapter12-redirect/
redirect_to: "/chapters/chapter12/"
---

## Chapter 12: Modern Asset Management in React

This chapter has been split into multiple sections for better accessibility and navigation.

## Quick Links

- [Modern Asset Import Patterns](/chapters/chapter12/import-patterns/)
- [Build Tool Asset Handling](/chapters/chapter12/build-tools/)
- [Performance-Optimized Image Loading](/chapters/chapter12/performance/)
- [Advanced Asset Strategies](/chapters/chapter12/advanced-strategies/)
- [Asset Security and Optimization](/chapters/chapter12/security-optimization/)

## Objectives

In this chapter, readers will:

- **Master** modern asset importing and optimization strategies in React applications
- **Implement** performance-optimized image loading with lazy loading and responsive images
- **Configure** asset handling in Vite, Next.js, and other modern build tools
- **Optimize** bundle sizes through dynamic imports and code splitting for assets
- **Apply** TypeScript best practices for type-safe asset management
- **Deploy** assets with modern CDN and caching strategies

## Chapter Outline

- [Chapter 12: Modern Asset Management in React](#chapter-12-modern-asset-management-in-react)
- [Quick Links](#quick-links)
- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Modern Asset Import Patterns](#modern-asset-import-patterns)
  - [Static Asset Imports](#static-asset-imports)
    - [Basic Asset Imports](#basic-asset-imports)
    - [Asset Import with Size Variants](#asset-import-with-size-variants)
  - [Dynamic Asset Loading](#dynamic-asset-loading)
    - [Dynamic Imports with Suspense](#dynamic-imports-with-suspense)
    - [Conditional Asset Loading](#conditional-asset-loading)
  - [TypeScript Asset Types](#typescript-asset-types)
    - [Asset Type Declarations](#asset-type-declarations)
    - [Typed Asset Components](#typed-asset-components)
- [Build Tool Asset Handling](#build-tool-asset-handling)
  - [Vite Asset Management](#vite-asset-management)
    - [Vite Asset Processing](#vite-asset-processing)
    - [Vite Asset Import Suffixes](#vite-asset-import-suffixes)
  - [Next.js Asset Optimization](#nextjs-asset-optimization)
    - [Next.js Image Component](#nextjs-image-component)
    - [Next.js Image Configuration](#nextjs-image-configuration)
  - [Create React App (Legacy)](#create-react-app-legacy)
    - [CRA Asset Handling (For Reference)](#cra-asset-handling-for-reference)
- [Performance-Optimized Image Loading](#performance-optimized-image-loading)
  - [Lazy Loading Images](#lazy-loading-images)
    - [Native Lazy Loading](#native-lazy-loading)
    - [Custom Lazy Loading Hook](#custom-lazy-loading-hook)
  - [Responsive Images](#responsive-images)
    - [Modern Picture Element](#modern-picture-element)
    - [Responsive Image Component](#responsive-image-component)
  - [Modern Image Formats](#modern-image-formats)
    - [Progressive Enhancement with Modern Formats](#progressive-enhancement-with-modern-formats)
- [Advanced Asset Strategies](#advanced-asset-strategies)
  - [Code Splitting with Dynamic Imports](#code-splitting-with-dynamic-imports)
    - [Asset Code Splitting](#asset-code-splitting)
    - [Asset Chunking Strategy](#asset-chunking-strategy)
  - [Asset Preloading](#asset-preloading)
    - [Strategic Preloading](#strategic-preloading)
    - [Intersection Observer Preloading](#intersection-observer-preloading)
  - [CDN Integration](#cdn-integration)
    - [Multi-CDN Strategy](#multi-cdn-strategy)
- [Asset Security and Optimization](#asset-security-and-optimization)
  - [Content Security Policy](#content-security-policy)
    - [CSP for Asset Security](#csp-for-asset-security)
    - [Asset Integrity Checking](#asset-integrity-checking)
  - [Bundle Analysis](#bundle-analysis)
    - [Asset Bundle Analyzer](#asset-bundle-analyzer)
  - [Caching Strategies](#caching-strategies)
    - [Service Worker Asset Caching](#service-worker-asset-caching)
    - [Client-Side Asset Caching](#client-side-asset-caching)
- [Summary and Best Practices](#summary-and-best-practices)
  - [2025 Asset Management Guidelines](#2025-asset-management-guidelines)
  - [Migration from Legacy Patterns](#migration-from-legacy-patterns)

## Modern Asset Import Patterns

**2025 Asset Management:** Modern React applications use sophisticated build tools (Vite, Next.js, Turbopack) that provide advanced asset optimization, automatic format conversion, and intelligent caching strategies.

### Static Asset Imports

Modern build tools automatically optimize and process static assets during the build process:

#### Basic Asset Imports

```javascript
// Modern asset imports with automatic optimization
import heroImage from './assets/hero.jpg';
import logoSvg from './assets/logo.svg';
import iconPng from './assets/icon.png';
import dataJson from './assets/data.json';

function HomePage() {
  return (
    <div>
      <img 
        src={heroImage} 
        alt="Hero banner"
        loading="lazy" // Modern browsers support native lazy loading
      />
      <img src={logoSvg} alt="Company logo" />
    </div>
  );
}
```

#### Asset Import with Size Variants

```javascript
// Import multiple sizes (Vite/Next.js automatically generates variants)
import heroSmall from './assets/hero.jpg?w=400';
import heroMedium from './assets/hero.jpg?w=800';
import heroLarge from './assets/hero.jpg?w=1200';

function ResponsiveImage() {
  return (
    <img
      src={heroLarge}
      srcSet={`
        ${heroSmall} 400w,
        ${heroMedium} 800w,
        ${heroLarge} 1200w
      `}
      sizes="(max-width: 400px) 400px, (max-width: 800px) 800px, 1200px"
      alt="Responsive hero image"
      loading="lazy"
    />
  );
}
```

### Dynamic Asset Loading

Modern patterns for loading assets based on runtime conditions:

#### Dynamic Imports with Suspense

```javascript
import React, { Suspense, lazy } from 'react';

// Dynamically import heavy components with their assets
const ImageGallery = lazy(() => import('./components/ImageGallery'));
const VideoPlayer = lazy(() => import('./components/VideoPlayer'));

function MediaSection({ mediaType }) {
  return (
    <Suspense fallback={<div>Loading media...</div>}>
      {mediaType === 'images' && <ImageGallery />}
      {mediaType === 'video' && <VideoPlayer />}
    </Suspense>
  );
}
```

#### Conditional Asset Loading

```javascript
import { useState, useEffect } from 'react';

function AdaptiveImage({ imageName, fallback }) {
  const [imageSrc, setImageSrc] = useState(fallback);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadImage = async () => {
      try {
        // Dynamic import based on runtime conditions
        const { default: image } = await import(`./assets/images/${imageName}.jpg`);
        setImageSrc(image);
      } catch (error) {
        console.warn(`Failed to load ${imageName}, using fallback`);
        setImageSrc(fallback);
      } finally {
        setLoading(false);
      }
    };

    loadImage();
  }, [imageName, fallback]);

  if (loading) {
    return <div className="image-placeholder">Loading...</div>;
  }

  return <img src={imageSrc} alt={imageName} loading="lazy" />;
}
```

### TypeScript Asset Types

**2025 TypeScript Best Practice:** Define proper types for all assets to ensure type safety:

#### Asset Type Declarations

```typescript
// types/assets.d.ts
declare module '*.jpg' {
  const src: string;
  export default src;
}

declare module '*.jpeg' {
  const src: string;
  export default src;
}

declare module '*.png' {
  const src: string;
  export default src;
}

declare module '*.svg' {
  const src: string;
  export default src;
}

declare module '*.webp' {
  const src: string;
  export default src;
}

declare module '*.avif' {
  const src: string;
  export default src;
}

declare module '*.mp4' {
  const src: string;
  export default src;
}

declare module '*.webm' {
  const src: string;
  export default src;
}

// For query parameters (Vite)
declare module '*?url' {
  const src: string;
  export default src;
}

declare module '*?raw' {
  const src: string;
  export default src;
}

declare module '*?inline' {
  const src: string;
  export default src;
}
```

#### Typed Asset Components

```typescript
// components/Image.tsx
interface ImageProps {
  src: string;
  alt: string;
  width?: number;
  height?: number;
  loading?: 'lazy' | 'eager';
  className?: string;
}

export function Image({ 
  src, 
  alt, 
  width, 
  height, 
  loading = 'lazy',
  className 
}: ImageProps) {
  return (
    <img
      src={src}
      alt={alt}
      width={width}
      height={height}
      loading={loading}
      className={className}
    />
  );
}

// Usage with imported assets
import heroImage from './assets/hero.jpg';

function Hero() {
  return (
    <Image
      src={heroImage}
      alt="Hero banner"
      width={1200}
      height={600}
      className="hero-image"
    />
  );
}
```

## Build Tool Asset Handling

Different build tools provide various optimization strategies and configuration options for asset management in 2025.

### Vite Asset Management

**Vite** is the leading build tool for modern React applications, offering superior performance and developer experience:

#### Vite Asset Processing

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  assetsInclude: ['**/*.lottie'], // Include custom asset types
  build: {
    rollupOptions: {
      output: {
        assetFileNames: (assetInfo) => {
          // Custom asset naming strategy
          if (assetInfo.name?.endsWith('.css')) {
            return 'assets/css/[name]-[hash][extname]';
          }
          if (/\.(png|jpe?g|svg|gif|webp|avif)$/.test(assetInfo.name || '')) {
            return 'assets/images/[name]-[hash][extname]';
          }
          return 'assets/[name]-[hash][extname]';
        },
      },
    },
  },
});
```

#### Vite Asset Import Suffixes

```javascript
// Different ways to import assets in Vite
import imageUrl from './image.png'; // Processed URL
import imageRaw from './image.png?raw'; // Raw file content
import imageInline from './image.svg?inline'; // Inlined as data URL
import imageExplicitUrl from './image.png?url'; // Explicit URL

// Dynamic asset resolution
function getAssetUrl(name) {
  return new URL(`./assets/${name}`, import.meta.url).href;
}

// Usage in component
function ViteAssetExample() {
  const dynamicImage = getAssetUrl('dynamic-image.jpg');
  
  return (
    <div>
      <img src={imageUrl} alt="Standard import" />
      <img src={dynamicImage} alt="Dynamic import" />
      {/* SVG can be inlined for better performance */}
      <div dangerouslySetInnerHTML={% raw %}{{ __html: imageInline }}{% endraw %} />
    </div>
  );
}
```

### Next.js Asset Optimization

**Next.js** provides the most advanced built-in image optimization:

#### Next.js Image Component

```javascript
// Next.js 15+ Image component with automatic optimization
import Image from 'next/image';
import heroImage from '../public/images/hero.jpg';

function OptimizedImages() {
  return (
    <div>
      {/* Automatic optimization with imported images */}
      <Image
        src={heroImage}
        alt="Hero banner"
        width={1200}
        height={600}
        priority // Load above-the-fold images immediately
        placeholder="blur" // Automatic blur placeholder
      />
      
      {/* Remote images with optimization */}
      <Image
        src="https://example.com/external-image.jpg"
        alt="External image"
        width={800}
        height={400}
        loading="lazy"
      />
      
      {/* Responsive images */}
      <Image
        src="/images/responsive.jpg"
        alt="Responsive image"
        fill
        style={% raw %}{{ objectFit: 'cover' }}{% endraw %}
        sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
      />
    </div>
  );
}
```

#### Next.js Image Configuration

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    formats: ['image/webp', 'image/avif'], // Modern formats first
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    domains: ['example.com', 'cdn.example.com'], // Allowed external domains
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '**.example.com',
        port: '',
        pathname: '/images/**',
      },
    ],
    minimumCacheTTL: 31536000, // 1 year cache
  },
};

export default nextConfig;
```

### Create React App (Legacy)

**⚠️ Legacy Warning:** Create React App is no longer recommended for new projects. Use Vite or Next.js instead.

#### CRA Asset Handling (For Reference)

```javascript
// Legacy CRA patterns (avoid in new projects)
import logo from './logo.svg'; // Webpack file-loader pattern
import './App.css'; // CSS imports

function LegacyCRAApp() {
  return (
    <div className="App">
      {/* Legacy public folder access */}
      <img src={process.env.PUBLIC_URL + '/logo192.png'} alt="Public logo" />
      
      {/* Imported assets */}
      <img src={logo} alt="Imported logo" />
    </div>
  );
}
```

## Performance-Optimized Image Loading

Modern performance patterns for optimal user experience and Core Web Vitals.

### Lazy Loading Images

#### Native Lazy Loading

```javascript
// Modern browsers support native lazy loading
function LazyImages() {
  return (
    <div>
      {/* Images below the fold should be lazy loaded */}
      <img
        src="/images/hero.jpg"
        alt="Above the fold"
        loading="eager" // Load immediately for LCP
      />
      
      <img
        src="/images/gallery-1.jpg"
        alt="Gallery image 1"
        loading="lazy" // Lazy load below-the-fold images
      />
    </div>
  );
}
```

#### Custom Lazy Loading Hook

```javascript
import { useState, useEffect, useRef } from 'react';

function useLazyLoading(threshold = 0.1) {
  const [isVisible, setIsVisible] = useState(false);
  const ref = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.disconnect();
        }
      },
      { threshold }
    );

    if (ref.current) {
      observer.observe(ref.current);
    }

    return () => observer.disconnect();
  }, [threshold]);

  return { ref, isVisible };
}

// Usage
function LazyImage({ src, alt, ...props }) {
  const { ref, isVisible } = useLazyLoading();
  const [imageLoaded, setImageLoaded] = useState(false);

  return (
    <div ref={ref} className="lazy-image-container">
      {isVisible ? (
        <img
          src={src}
          alt={alt}
          onLoad={() => setImageLoaded(true)}
          className={`lazy-image ${imageLoaded ? 'loaded' : 'loading'}`}
          {...props}
        />
      ) : (
        <div className="image-placeholder" />
      )}
    </div>
  );
}
```

### Responsive Images

#### Modern Picture Element

```javascript
function ResponsiveImage() {
  return (
    <picture>
      {/* Modern formats for supporting browsers */}
      <source
        srcSet="/images/hero.avif"
        type="image/avif"
        media="(min-width: 800px)"
      />
      <source
        srcSet="/images/hero.webp"
        type="image/webp"
        media="(min-width: 800px)"
      />
      
      {/* Mobile versions */}
      <source
        srcSet="/images/hero-mobile.avif"
        type="image/avif"
        media="(max-width: 799px)"
      />
      <source
        srcSet="/images/hero-mobile.webp"
        type="image/webp"
        media="(max-width: 799px)"
      />
      
      {/* Fallback */}
      <img
        src="/images/hero.jpg"
        alt="Hero image"
        loading="lazy"
      />
    </picture>
  );
}
```

#### Responsive Image Component

```javascript
interface ResponsiveImageProps {
  src: string;
  srcSet?: string;
  sizes?: string;
  alt: string;
  className?: string;
  loading?: 'lazy' | 'eager';
}

function ResponsiveImage({
  src,
  srcSet,
  sizes = '100vw',
  alt,
  className,
  loading = 'lazy'
}: ResponsiveImageProps) {
  return (
    <img
      src={src}
      srcSet={srcSet}
      sizes={sizes}
      alt={alt}
      className={className}
      loading={loading}
      decoding="async" // Non-blocking decode
    />
  );
}

// Usage with different breakpoints
function Gallery() {
  const imageSizes = '(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw';
  
  return (
    <div className="gallery">
      <ResponsiveImage
        src="/images/gallery-1-800.jpg"
        srcSet="/images/gallery-1-400.jpg 400w, /images/gallery-1-800.jpg 800w, /images/gallery-1-1200.jpg 1200w"
        sizes={imageSizes}
        alt="Gallery item 1"
      />
    </div>
  );
}
```

### Modern Image Formats

#### Progressive Enhancement with Modern Formats

```javascript
import { useState, useEffect } from 'react';

function useModernImageSupport() {
  const [formats, setFormats] = useState({
    avif: false,
    webp: false,
  });

  useEffect(() => {
    const checkFormat = (format: string): Promise<boolean> => {
      return new Promise((resolve) => {
        const img = new Image();
        img.onload = () => resolve(true);
        img.onerror = () => resolve(false);
        
        const testImages = {
          avif: 'data:image/avif;base64,AAAAIGZ0eXBhdmlmAAAAAGF2aWZtaWYxbWlhZk1BMUIAAADybWV0YQAAAAAAAAAoaGRscgAAAAAAAAAAcGljdAAAAAAAAAAAAAAAAGxpYmF2aWYAAAAADnBpdG0AAAAAAAEAAAAeaWxvYwAAAABEAAABAAEAAAABAAABGgAAAB0AAAAoaWluZgAAAAAAAQAAABppbmZlAgAAAAABAABhdjAxQ29sb3IAAAAAamlwcnAAAABLaXBjbwAAABRpc3BlAAAAAAAAAAEAAAABAAAAEHBpeGkAAAAAAwgICAAAAAxhdjFDgQ0MAAAAABNjb2xybmNseAACAAIAAYAAAAAXaXBtYQAAAAAAAAABAAEEAQKDBAAAACVtZGF0EgAKCBgABogQEAwgMgwf8D///8WfhwB8+ErK42A=',
          webp: 'data:image/webp;base64,UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==',
        };
        
        img.src = testImages[format as keyof typeof testImages];
      });
    };

    Promise.all([
      checkFormat('avif'),
      checkFormat('webp'),
    ]).then(([avif, webp]) => {
      setFormats({ avif, webp });
    });
  }, []);

  return formats;
}

// Smart image component that uses best available format
function SmartImage({ baseSrc, alt, ...props }) {
  const { avif, webp } = useModernImageSupport();
  
  const getSrc = () => {
    const base = baseSrc.replace(/\.[^/.]+$/, ''); // Remove extension
    
    if (avif) return `${base}.avif`;
    if (webp) return `${base}.webp`;
    return baseSrc; // Fallback to original
  };

  return <img src={getSrc()} alt={alt} {...props} />;
}
```

## Advanced Asset Strategies

### Code Splitting with Dynamic Imports

**2025 Best Practice:** Split large assets and components to improve initial page load performance.

#### Asset Code Splitting

```javascript
import { Suspense, lazy, useState } from 'react';

// Lazy load heavy media components
const VideoPlayer = lazy(() => import('./components/VideoPlayer'));
const ImageGallery = lazy(() => import('./components/ImageGallery'));

function MediaPage() {
  const [activeTab, setActiveTab] = useState('images');

  return (
    <div>
      <nav>
        <button 
          onClick={() => setActiveTab('images')}
          className={activeTab === 'images' ? 'active' : ''}
        >
          Images
        </button>
        <button 
          onClick={() => setActiveTab('video')}
          className={activeTab === 'video' ? 'active' : ''}
        >
          Video
        </button>
      </nav>

      <Suspense fallback={<div>Loading media...</div>}>
        {activeTab === 'images' && <ImageGallery />}
        {activeTab === 'video' && <VideoPlayer />}
      </Suspense>
    </div>
  );
}
```

#### Asset Chunking Strategy

```javascript
// utils/assetLoader.js
const assetCache = new Map();

export async function loadAssetBundle(bundleName) {
  if (assetCache.has(bundleName)) {
    return assetCache.get(bundleName);
  }

  try {
    let bundle;
    
    switch (bundleName) {
      case 'hero-images':
        bundle = await import('./assets/bundles/hero-images');
        break;
      case 'gallery-images':
        bundle = await import('./assets/bundles/gallery-images');
        break;
      case 'icons':
        bundle = await import('./assets/bundles/icons');
        break;
      default:
        throw new Error(`Unknown bundle: ${bundleName}`);
    }
    
    assetCache.set(bundleName, bundle);
    return bundle;
  } catch (error) {
    console.error(`Failed to load asset bundle: ${bundleName}`, error);
    throw error;
  }
}

// Usage in components
function HeroSection() {
  const [heroAssets, setHeroAssets] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    loadAssetBundle('hero-images')
      .then(setHeroAssets)
      .catch(console.error)
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <div>Loading hero...</div>;
  if (!heroAssets) return <div>Failed to load hero assets</div>;

  return (
    <section>
      <img src={heroAssets.primaryHero} alt="Main hero" />
      <img src={heroAssets.secondaryHero} alt="Secondary hero" />
    </section>
  );
}
```

### Asset Preloading

#### Strategic Preloading

```javascript
import { useEffect } from 'react';

function useAssetPreloader(assets, priority = 'low') {
  useEffect(() => {
    const preloadAssets = async () => {
      for (const asset of assets) {
        try {
          if (asset.endsWith('.js')) {
            // Preload JavaScript modules
            const link = document.createElement('link');
            link.rel = 'modulepreload';
            link.href = asset;
            document.head.appendChild(link);
          } else if (/\.(jpg|jpeg|png|webp|avif)$/i.test(asset)) {
            // Preload images
            const link = document.createElement('link');
            link.rel = 'preload';
            link.as = 'image';
            link.href = asset;
            link.fetchPriority = priority;
            document.head.appendChild(link);
          }
        } catch (error) {
          console.warn(`Failed to preload asset: ${asset}`, error);
        }
      }
    };

    preloadAssets();
  }, [assets, priority]);
}

// Usage
function App() {
  // Preload critical above-the-fold assets
  useAssetPreloader([
    '/images/hero.webp',
    '/images/logo.svg',
  ], 'high');

  // Preload likely next page assets
  useAssetPreloader([
    '/images/gallery-preview.webp',
    '/js/gallery.chunk.js',
  ], 'low');

  return <div>{/* App content */}</div>;
}
```

#### Intersection Observer Preloading

```javascript
function useIntersectionPreloader(threshold = 0.5) {
  const [shouldPreload, setShouldPreload] = useState(false);
  const ref = useRef(null);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setShouldPreload(true);
        }
      },
      { threshold }
    );

    if (ref.current) {
      observer.observe(ref.current);
    }

    return () => observer.disconnect();
  }, [threshold]);

  return { ref, shouldPreload };
}

function PreloadableSection({ nextPageAssets }) {
  const { ref, shouldPreload } = useIntersectionPreloader();

  useEffect(() => {
    if (shouldPreload) {
      // Preload assets for likely next navigation
      nextPageAssets.forEach(asset => {
        const link = document.createElement('link');
        link.rel = 'prefetch';
        link.href = asset;
        document.head.appendChild(link);
      });
    }
  }, [shouldPreload, nextPageAssets]);

  return (
    <div ref={ref}>
      {/* Content that triggers preloading when visible */}
    </div>
  );
}
```

### CDN Integration

#### Multi-CDN Strategy

```javascript
// config/cdn.js
const CDN_CONFIGS = {
  primary: {
    baseUrl: 'https://cdn.example.com',
    formats: ['avif', 'webp', 'jpg'],
  },
  fallback: {
    baseUrl: 'https://backup-cdn.example.com',
    formats: ['webp', 'jpg'],
  },
  local: {
    baseUrl: '/assets',
    formats: ['jpg'],
  },
};

export function getCDNUrl(imagePath, options = {}) {
  const { 
    width, 
    height, 
    format = 'auto', 
    quality = 80,
    cdn = 'primary' 
  } = options;

  const config = CDN_CONFIGS[cdn];
  const params = new URLSearchParams();

  if (width) params.set('w', width.toString());
  if (height) params.set('h', height.toString());
  if (format !== 'auto') params.set('f', format);
  if (quality !== 80) params.set('q', quality.toString());

  const queryString = params.toString();
  const separator = queryString ? '?' : '';
  
  return `${config.baseUrl}${imagePath}${separator}${queryString}`;
}

// Smart CDN component with fallback
function CDNImage({ src, alt, fallbacks = ['fallback', 'local'], ...props }) {
  const [currentSrc, setCurrentSrc] = useState(
    getCDNUrl(src, { cdn: 'primary', ...props })
  );
  const [fallbackIndex, setFallbackIndex] = useState(-1);

  const handleError = () => {
    if (fallbackIndex < fallbacks.length - 1) {
      const nextIndex = fallbackIndex + 1;
      const nextCdn = fallbacks[nextIndex];
      setCurrentSrc(getCDNUrl(src, { cdn: nextCdn, ...props }));
      setFallbackIndex(nextIndex);
    }
  };

  return (
    <img
      src={currentSrc}
      alt={alt}
      onError={handleError}
      {...props}
    />
  );
}
```

## Asset Security and Optimization

### Content Security Policy

#### CSP for Asset Security

```javascript
// next.config.js or similar
const ContentSecurityPolicy = `
  default-src 'self';
  script-src 'self' 'unsafe-inline' 'unsafe-eval' https://analytics.example.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: blob: https://cdn.example.com https://backup-cdn.example.com;
  media-src 'self' https://videos.example.com;
  connect-src 'self' https://api.example.com;
  font-src 'self' https://fonts.googleapis.com https://fonts.gstatic.com;
`;

export default {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: ContentSecurityPolicy.replace(/\s{2,}/g, ' ').trim(),
          },
        ],
      },
    ];
  },
};
```

#### Asset Integrity Checking

```javascript
function SecureImage({ src, alt, integrity, ...props }) {
  const [imageError, setImageError] = useState(false);

  const handleLoad = (event) => {
    if (integrity) {
      // Verify image integrity if provided
      const img = event.target;
      // Implementation would depend on your security requirements
      console.log('Image loaded and verified:', img.src);
    }
  };

  const handleError = () => {
    setImageError(true);
    console.error('Failed to load secure image:', src);
  };

  if (imageError) {
    return <div className="image-error">Failed to load secure image</div>;
  }

  return (
    <img
      src={src}
      alt={alt}
      onLoad={handleLoad}
      onError={handleError}
      crossOrigin="anonymous" // Enable CORS for integrity checking
      {...props}
    />
  );
}
```

### Bundle Analysis

#### Asset Bundle Analyzer

```javascript
// scripts/analyze-assets.js
import { readdir, stat } from 'fs/promises';
import path from 'path';

async function analyzeAssets(directory) {
  const assets = {
    images: { count: 0, totalSize: 0, formats: {} },
    fonts: { count: 0, totalSize: 0 },
    videos: { count: 0, totalSize: 0 },
    other: { count: 0, totalSize: 0 },
  };

  async function processDirectory(dir) {
    const items = await readdir(dir);
    
    for (const item of items) {
      const fullPath = path.join(dir, item);
      const stats = await stat(fullPath);
      
      if (stats.isDirectory()) {
        await processDirectory(fullPath);
      } else {
        const ext = path.extname(item).toLowerCase();
        const size = stats.size;
        
        if (['.jpg', '.jpeg', '.png', '.webp', '.avif', '.svg'].includes(ext)) {
          assets.images.count++;
          assets.images.totalSize += size;
          assets.images.formats[ext] = (assets.images.formats[ext] || 0) + 1;
        } else if (['.woff', '.woff2', '.ttf', '.otf'].includes(ext)) {
          assets.fonts.count++;
          assets.fonts.totalSize += size;
        } else if (['.mp4', '.webm', '.mov'].includes(ext)) {
          assets.videos.count++;
          assets.videos.totalSize += size;
        } else {
          assets.other.count++;
          assets.other.totalSize += size;
        }
      }
    }
  }

  await processDirectory(directory);
  return assets;
}

// Usage
analyzeAssets('./dist/assets').then(assets => {
  console.log('Asset Analysis:');
  console.log(`Images: ${assets.images.count} files, ${(assets.images.totalSize / 1024 / 1024).toFixed(2)} MB`);
  console.log('Image formats:', assets.images.formats);
  console.log(`Fonts: ${assets.fonts.count} files, ${(assets.fonts.totalSize / 1024).toFixed(2)} KB`);
  console.log(`Videos: ${assets.videos.count} files, ${(assets.videos.totalSize / 1024 / 1024).toFixed(2)} MB`);
});
```

### Caching Strategies

#### Service Worker Asset Caching

```javascript
// public/sw.js (Service Worker)
const CACHE_NAME = 'assets-v1';
const ASSET_CACHE_TIME = 365 * 24 * 60 * 60 * 1000; // 1 year

self.addEventListener('fetch', event => {
  const { request } = event;
  
  // Cache images, fonts, and other static assets
  if (request.destination === 'image' || 
      request.destination === 'font' ||
      request.url.includes('/assets/')) {
    
    event.respondWith(
      caches.open(CACHE_NAME).then(cache => {
        return cache.match(request).then(response => {
          if (response) {
            // Check if cached asset is still fresh
            const cachedTime = new Date(response.headers.get('cached-time') || 0);
            const now = new Date();
            
            if (now - cachedTime < ASSET_CACHE_TIME) {
              return response;
            }
          }
          
          // Fetch and cache new asset
          return fetch(request).then(networkResponse => {
            if (networkResponse.ok) {
              const responseClone = networkResponse.clone();
              responseClone.headers.set('cached-time', new Date().toISOString());
              cache.put(request, responseClone);
            }
            return networkResponse;
          });
        });
      })
    );
  }
});
```

#### Client-Side Asset Caching

```javascript
class AssetCache {
  constructor(maxSize = 50 * 1024 * 1024) { // 50MB default
    this.cache = new Map();
    this.maxSize = maxSize;
    this.currentSize = 0;
  }

  async get(url) {
    const cached = this.cache.get(url);
    if (cached && this.isValid(cached)) {
      return cached.data;
    }
    
    const data = await this.fetch(url);
    this.set(url, data);
    return data;
  }

  set(url, data) {
    const size = this.estimateSize(data);
    
    // Evict old entries if necessary
    while (this.currentSize + size > this.maxSize && this.cache.size > 0) {
      const firstKey = this.cache.keys().next().value;
      const firstEntry = this.cache.get(firstKey);
      this.currentSize -= this.estimateSize(firstEntry.data);
      this.cache.delete(firstKey);
    }
    
    this.cache.set(url, {
      data,
      timestamp: Date.now(),
      size,
    });
    this.currentSize += size;
  }

  isValid(entry, maxAge = 3600000) { // 1 hour default
    return Date.now() - entry.timestamp < maxAge;
  }

  estimateSize(data) {
    if (typeof data === 'string') {
      return data.length * 2; // Rough estimate for Unicode
    }
    if (data instanceof ArrayBuffer) {
      return data.byteLength;
    }
    return 1024; // Default estimate
  }

  async fetch(url) {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Failed to fetch ${url}: ${response.statusText}`);
    }
    
    const contentType = response.headers.get('content-type');
    if (contentType?.startsWith('image/')) {
      return response.arrayBuffer();
    }
    return response.text();
  }
}

// Global asset cache instance
export const assetCache = new AssetCache();

// Hook for cached assets
export function useCachedAsset(url) {
  const [asset, setAsset] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    assetCache.get(url)
      .then(setAsset)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { asset, loading, error };
}
```

## Summary and Best Practices

### 2025 Asset Management Guidelines

**✅ Recommended Approaches:**

1. **Use Modern Build Tools**: Prefer Vite or Next.js over Create React App
2. **Optimize Images**: Use WebP/AVIF formats with fallbacks
3. **Implement Lazy Loading**: Use native loading="lazy" and Intersection Observer
4. **Code Split Assets**: Load heavy assets only when needed  
5. **Leverage CDNs**: Use multi-CDN strategy with fallbacks
6. **Cache Strategically**: Implement service worker and client-side caching
7. **Monitor Performance**: Regularly analyze bundle sizes and optimize

**❌ Avoid These Patterns:**

1. **Large Bundle Sizes**: Don't load all assets upfront
2. **Unoptimized Images**: Avoid serving only JPEG/PNG formats
3. **Blocking Asset Loads**: Don't block initial page render
4. **Missing Fallbacks**: Always provide fallback strategies
5. **Insecure Assets**: Don't serve assets without proper CSP

### Migration from Legacy Patterns

**From Create React App to Modern Tools:**

1. **Assessment**: Analyze current asset usage and bundle sizes
2. **Tool Selection**: Choose Vite for SPAs, Next.js for SSR/SSG
3. **Asset Migration**: Update import patterns and optimization strategies  
4. **Performance Testing**: Measure improvements in Core Web Vitals
5. **Gradual Rollout**: Migrate sections incrementally

**Key Performance Metrics:**

- **Largest Contentful Paint (LCP)**: < 2.5s
- **First Input Delay (FID)**: < 100ms
- **Cumulative Layout Shift (CLS)**: < 0.1
- **Bundle Size**: Keep initial bundles < 100KB gzipped

This comprehensive modernization transforms basic file loading into a complete asset management strategy suitable for production React applications in 2025.
