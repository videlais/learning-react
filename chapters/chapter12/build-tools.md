---
title: "Build Tool Asset Handling"
parent: "Modern Asset Management in React"
layout: chapter
permalink: /chapters/chapter12/build-tools/
---

## Build Tool Asset Handling

Modern React development relies on sophisticated build tools that handle asset processing automatically. Understanding how these tools work is essential for optimizing your application's performance and troubleshooting build issues.

## Overview

In this section, you'll learn:

- How Vite processes assets with native ESM
- Next.js automatic image optimization
- Understanding legacy Create React App patterns
- Configuring custom asset loaders

## Vite Asset Handling

Vite has become the default choice for modern React projects due to its speed and simplicity.

### Static Asset Imports

```tsx
import logoUrl from './logo.svg'
import imageUrl from './photo.jpg'

function App() {
  return (
    <div>
      <img src={logoUrl} alt="Logo" />
      <img src={imageUrl} alt="Photo" />
    </div>
  )
}
```

Vite automatically:

- Hashes filenames for cache busting
- Optimizes image sizes
- Inlines small assets as base64
- Copies larger assets to output directory

### Explicit URL Imports

For more control over asset loading:

```tsx
// Import as URL string
import imageUrl from './image.jpg?url'

// Import as raw string content
import svgContent from './icon.svg?raw'

// Import with worker
import Worker from './worker.js?worker'
```

### Public Directory Assets

Place static assets in the `public/` directory for direct URL access:

```tsx
function App() {
  // References public/favicon.ico
  return <img src="/favicon.ico" alt="Icon" />
}
```

### Vite Configuration

Customize asset handling in `vite.config.ts`:

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  build: {
    assetsInlineLimit: 4096, // 4kb - inline smaller files
    rollupOptions: {
      output: {
        assetFileNames: 'assets/[name]-[hash][extname]'
      }
    }
  },
  assetsInclude: ['**/*.glb', '**/*.hdr'] // Custom asset types
})
```

## Next.js Asset Handling

Next.js provides powerful built-in asset optimization, especially for images.

### Next.js Image Component

The `next/image` component automatically optimizes images:

```tsx
import Image from 'next/image'
import profilePic from '../public/profile.jpg'

export default function Profile() {
  return (
    <div>
      {/* Local image - dimensions inferred */}
      <Image
        src={profilePic}
        alt="Profile picture"
        placeholder="blur" // Automatic blur placeholder
      />
      
      {/* Remote image - dimensions required */}
      <Image
        src="https://example.com/photo.jpg"
        alt="Remote photo"
        width={800}
        height={600}
        loading="lazy"
      />
    </div>
  )
}
```

### Image Optimization Features

Next.js automatically:

- Generates responsive image sizes
- Converts to modern formats (WebP, AVIF)
- Lazy loads images by default
- Provides blur placeholders
- Serves images via CDN

### Static Image Asset Imports

```tsx
import type { StaticImageData } from 'next/image'
import logo from '../public/logo.svg'

interface Props {
  imageSrc: StaticImageData
}

export default function Logo({ imageSrc }: Props) {
  return <img src={imageSrc.src} alt="Logo" />
}
```

### Next.js Configuration

Configure asset handling in `next.config.js`:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    domains: ['example.com', 'cdn.example.com'],
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 60,
  },
  webpack: (config) => {
    config.module.rules.push({
      test: /\.svg$/,
      use: ['@svgr/webpack']
    })
    return config
  }
}

module.exports = nextConfig
```

## Legacy: Create React App

While Create React App is no longer recommended for new projects, you may encounter it in existing codebases.

### CRA Asset Imports

```tsx
import logo from './logo.svg'
import './App.css'

function App() {
  return (
    <div className="App">
      <img src={logo} alt="Logo" />
    </div>
  )
}
```

### Public Folder Usage

```tsx
function App() {
  // References public/images/photo.jpg
  return <img src={process.env.PUBLIC_URL + '/images/photo.jpg'} alt="Photo" />
}
```

### Module Path Resolution

```tsx
// jsconfig.json or tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "src"
  }
}

// Now you can import:
import Button from 'components/Button'
// Instead of:
import Button from '../../../components/Button'
```

### CRA Limitations

- No native TypeScript path aliases
- Limited asset optimization
- Slower build times
- No native SVG component support
- Ejection required for advanced config

**Migration Path**: For existing CRA projects, consider migrating to Vite or Next.js for better performance and developer experience.

## Custom Asset Loaders

### SVG as React Components

Using SVGR in Vite:

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import svgr from 'vite-plugin-svgr'

export default defineConfig({
  plugins: [
    react(),
    svgr({
      svgrOptions: {
        icon: true, // Add icon props
      }
    })
  ]
})
```

```tsx
// Use SVG as component
import { ReactComponent as Logo } from './logo.svg'

function App() {
  return <Logo className="app-logo" />
}
```

### Font Loading

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        assetFileNames: (assetInfo) => {
          if (assetInfo.name?.endsWith('.woff2')) {
            return 'fonts/[name]-[hash][extname]'
          }
          return 'assets/[name]-[hash][extname]'
        }
      }
    }
  }
})
```

### Custom File Types

```typescript
// Handle .glb 3D model files
export default defineConfig({
  assetsInclude: ['**/*.glb', '**/*.gltf'],
  plugins: [
    {
      name: 'glb-loader',
      transform(code, id) {
        if (id.endsWith('.glb')) {
          // Custom transformation logic
          return {
            code: `export default ${JSON.stringify(id)}`,
            map: null
          }
        }
      }
    }
  ]
})
```

## Build Tool Comparison

| Feature | Vite | Next.js | CRA (Legacy) |
|---------|------|---------|--------------|
| Build Speed | ⚡ Fastest | Fast | Slow |
| Image Optimization | Manual | Automatic | Manual |
| Code Splitting | Automatic | Automatic | Manual |
| Asset Hashing | ✅ | ✅ | ✅ |
| SVG Components | Plugin | Built-in | Eject required |
| TypeScript | Native | Native | Supported |
| HMR Speed | Instant | Fast | Slow |
| Bundle Size | Optimal | Optimal | Larger |

## Best Practices

### 1. Choose the Right Tool

```typescript
// Vite: Great for SPAs and fast development
// Perfect for: Component libraries, admin panels, dashboards

// Next.js: Great for full-stack apps with SSR/SSG
// Perfect for: Marketing sites, e-commerce, blogs

// Avoid: Create React App for new projects
```

### 2. Configure Asset Optimization

```typescript
// Optimize for production
export default defineConfig({
  build: {
    target: 'es2020',
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true, // Remove console.logs
        drop_debugger: true
      }
    }
  }
})
```

### 3. Use TypeScript for Assets

```typescript
// src/types/assets.d.ts
declare module '*.svg' {
  const content: string
  export default content
  export const ReactComponent: React.FC<React.SVGProps<SVGSVGElement>>
}

declare module '*.jpg' {
  const src: string
  export default src
}

declare module '*.png' {
  const src: string
  export default src
}

declare module '*.webp' {
  const src: string
  export default src
}
```

### 4. Environment-Specific Assets

```typescript
// Use environment variables
const API_URL = import.meta.env.VITE_API_URL
const CDN_URL = import.meta.env.VITE_CDN_URL

function loadImage(path: string) {
  return `${CDN_URL}${path}`
}
```

### 5. Monitor Bundle Size

```bash
# Vite bundle analysis
npm run build -- --report

# Next.js bundle analysis
npm install @next/bundle-analyzer
```

## Troubleshooting

### Common Issues

**Asset not found:**

```typescript
// ❌ Don't use absolute paths
import image from '/src/assets/image.jpg'

// ✅ Use relative paths
import image from './assets/image.jpg'

// ✅ Or use aliases
import image from '@/assets/image.jpg'
```

**Large bundle size:**

```typescript
// ❌ Importing entire libraries
import _ from 'lodash'

// ✅ Import only what you need
import debounce from 'lodash/debounce'
```

**Images not optimizing:**

```typescript
// Next.js - ensure domains are configured
module.exports = {
  images: {
    domains: ['your-cdn.com'],
  }
}
```

## Summary

Modern build tools handle much of the complexity of asset management automatically, but understanding their behavior is crucial for:

- **Optimizing performance**: Choosing the right tool and configuration
- **Debugging issues**: Understanding build output and error messages  
- **Custom requirements**: Extending default behavior for specific needs
- **Migration**: Moving from legacy tools to modern alternatives

**Next Steps:**

- Explore [Performance-Optimized Image Loading](./performance/) for advanced loading strategies
- Learn about [Advanced Asset Strategies](./advanced-strategies/) for production optimization

**Key Takeaways:**

1. Vite offers the fastest development experience for React SPAs
2. Next.js provides automatic image optimization and SSR/SSG capabilities
3. Create React App is legacy - migrate to modern tools
4. TypeScript integration improves asset handling safety
5. Build tool configuration enables custom optimization strategies
