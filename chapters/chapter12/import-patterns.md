---
title: "Modern Asset Import Patterns"
parent: "Modern Asset Management in React"
nav_order: 1
layout: chapter
---

## Modern Asset Import Patterns

**2025 Asset Management:** Modern React applications use sophisticated build tools (Vite, Next.js, Turbopack) that provide advanced asset optimization, automatic format conversion, and intelligent caching strategies.

## Overview

Asset importing has evolved significantly from basic Webpack file-loader patterns. Modern build tools provide:

- **Automatic optimization**: Images are compressed and converted to optimal formats
- **Intelligent caching**: Assets are hashed for long-term browser caching  
- **Format conversion**: Automatic WebP/AVIF generation with fallbacks
- **Size variants**: Multiple resolution versions for responsive images

## Static Asset Imports

Modern build tools automatically optimize and process static assets during the build process:

### Basic Asset Imports

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

### Asset Import with Size Variants

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

## Dynamic Asset Loading

Modern patterns for loading assets based on runtime conditions:

### Dynamic Imports with Suspense

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

### Conditional Asset Loading

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

## TypeScript Asset Types

**2025 TypeScript Best Practice:** Define proper types for all assets to ensure type safety:

### Asset Type Declarations

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

### Typed Asset Components

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

## Best Practices

### Asset Organization

```tree
src/
├── assets/
│   ├── images/
│   │   ├── heroes/
│   │   ├── icons/
│   │   └── gallery/
│   ├── videos/
│   └── fonts/
├── components/
└── types/
    └── assets.d.ts
```

### Performance Considerations

1. **Use appropriate formats**: AVIF > WebP > JPEG for photos, SVG for icons
2. **Size variants**: Generate multiple sizes for responsive images
3. **Lazy loading**: Use `loading="lazy"` for below-the-fold images
4. **Preload critical assets**: Use `<link rel="preload">` for above-the-fold images

### Common Pitfalls

- **Don't import large assets synchronously** - Use dynamic imports for heavy media
- **Avoid inline images in components** - Import assets at module level
- **Don't skip alt text** - Always provide meaningful alt attributes
- **Don't ignore TypeScript types** - Proper typing prevents runtime errors

## Navigation

- **Previous**: [Chapter 12 Overview](../)
- **Next**: [Build Tool Asset Handling](../build-tools/)

## Related Topics

- [Performance-Optimized Image Loading](../performance/)
- [TypeScript Integration](../typescript/)
- [Build Tool Configuration](../build-tools/)
