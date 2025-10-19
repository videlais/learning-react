---
title: "Modern React Project Setup"
order: 13
chapter_number: 13
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Initialize** modern React projects using Vite and Next.js
- **Configure** development environments with optimal tooling and settings
- **Organize** project structure following industry best practices
- **Implement** environment variables and configuration management
- **Optimize** build processes for development and production

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Modern React Build Tools in 2025](#modern-react-build-tools-in-2025)
- [Setting Up with Vite](#setting-up-with-vite)
  - [Why Vite?](#why-vite)
  - [Creating a Vite Project](#creating-a-vite-project)
  - [Vite Project Structure](#vite-project-structure)
  - [Vite Configuration](#vite-configuration)
- [Setting Up with Next.js](#setting-up-with-nextjs)
  - [Why Next.js?](#why-nextjs)
  - [Creating a Next.js Project](#creating-a-nextjs-project)
  - [Next.js Project Structure](#nextjs-project-structure)
  - [Next.js Configuration](#nextjs-configuration)
- [Project Structure Best Practices](#project-structure-best-practices)
  - [Component Organization](#component-organization)
  - [Feature-Based Structure](#feature-based-structure)
  - [Shared Resources](#shared-resources)
- [Development Environment Setup](#development-environment-setup)
  - [ESLint Configuration](#eslint-configuration)
  - [Prettier Setup](#prettier-setup)
  - [TypeScript Integration](#typescript-integration)
  - [Git Configuration](#git-configuration)
- [Environment Variables](#environment-variables)
  - [Vite Environment Variables](#vite-environment-variables)
  - [Next.js Environment Variables](#nextjs-environment-variables)
  - [Security Best Practices](#security-best-practices)
- [Build and Deployment Preparation](#build-and-deployment-preparation)
  - [Development vs Production Builds](#development-vs-production-builds)
  - [Build Optimization](#build-optimization)
  - [Analyzing Bundle Size](#analyzing-bundle-size)
- [Legacy: Create React App](#legacy-create-react-app)
  - [⚠️ Not Recommended for New Projects](#️-not-recommended-for-new-projects)
- [Summary and Best Practices](#summary-and-best-practices)
  - [2025 Project Setup Guidelines](#2025-project-setup-guidelines)
  - [Quick Decision Guide](#quick-decision-guide)
  - [Next Steps](#next-steps)

## Modern React Build Tools in 2025

**⚠️ Important Context:** The React ecosystem has evolved significantly since 2020. Create React App, once the recommended starting point, is no longer actively maintained and should not be used for new projects.

**2025 Recommended Tools:**

1. **Vite**: Lightning-fast development server and optimized production builds
2. **Next.js**: Full-featured React framework with SSR, SSG, and API routes
3. **Remix**: Modern full-stack framework (alternative option)

This chapter focuses on Vite (for client-side applications) and Next.js (for full-featured applications).

## Setting Up with Vite

### Why Vite?

Vite (French for "fast") is the modern choice for React single-page applications:

**Advantages:**

- **Instant server start**: No bundling during development
- **Lightning-fast HMR**: Hot Module Replacement updates in milliseconds
- **Optimized builds**: Uses Rollup for production builds
- **Modern by default**: ES modules, native ESM, no legacy browser support needed
- **Framework agnostic**: Works with React, Vue, Svelte, etc.

**When to use Vite:**

- Client-side React applications (SPAs)
- Projects that don't need server-side rendering
- Applications prioritizing development speed
- Modern browser-only targets

### Creating a Vite Project

**Prerequisites:**

- Node.js 18 or higher
- npm 9+ or pnpm/yarn

**Step 1:** Create the project

```bash
# Using npm (creates directory and initializes project)
npm create vite@latest my-react-app -- --template react

# Using pnpm (faster alternative)
pnpm create vite my-react-app --template react

# Using yarn
yarn create vite my-react-app --template react

# For TypeScript
npm create vite@latest my-react-app -- --template react-ts
```

**Step 2:** Navigate and install dependencies

```bash
cd my-react-app
npm install
```

**Step 3:** Start development server

```bash
npm run dev
```

The development server starts at `http://localhost:5173` by default.

### Vite Project Structure

```tree
my-react-app/
├── node_modules/          # Dependencies
├── public/                # Static assets (not processed by Vite)
│   └── vite.svg          # Favicon and public assets
├── src/                   # Source code
│   ├── assets/           # Assets processed by Vite (images, etc.)
│   ├── components/       # React components
│   ├── App.jsx           # Root component
│   ├── App.css           # Component styles
│   ├── index.css         # Global styles
│   └── main.jsx          # Application entry point
├── .gitignore            # Git ignore rules
├── index.html            # HTML entry point
├── package.json          # Project dependencies and scripts
├── vite.config.js        # Vite configuration
└── README.md             # Project documentation
```

**Key differences from Create React App:**

- `index.html` is in the root directory (not in `public/`)
- Entry point is `src/main.jsx` (not `src/index.js`)
- Environment variables use `VITE_` prefix (not `REACT_APP_`)

### Vite Configuration

**vite.config.js** - Basic configuration:

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000, // Change default port
    open: true, // Auto-open browser
  },
});
```

**Advanced configuration with path aliases:**

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
    },
  },
  server: {
    port: 3000,
    open: true,
    host: true, // Expose to network
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
        },
      },
    },
  },
});
```

**Usage of path aliases:**

```javascript
// Instead of: import Button from '../../../components/Button'
import Button from '@components/Button';

// Instead of: import { formatDate } from '../../utils/date'
import { formatDate } from '@utils/date';
```

## Setting Up with Next.js

### Why Next.js?

Next.js is the leading React framework for production applications:

**Advantages:**

- **Full-stack framework**: API routes, middleware, server actions
- **Multiple rendering modes**: SSR, SSG, ISR, client-side
- **Built-in optimization**: Image optimization, font optimization, code splitting
- **File-based routing**: Intuitive routing based on file structure
- **SEO-friendly**: Server-side rendering for better SEO
- **Production-ready**: Built-in performance optimization

**When to use Next.js:**

- Full-featured web applications
- Projects requiring SEO optimization
- Applications with server-side rendering needs
- Projects needing API routes
- E-commerce, blogs, marketing sites

### Creating a Next.js Project

**Step 1:** Create the project

```bash
# Using npx (recommended)
npx create-next-app@latest my-next-app

# Interactive prompts will ask:
# - TypeScript? (Yes recommended)
# - ESLint? (Yes)
# - Tailwind CSS? (Optional)
# - `src/` directory? (Yes recommended)
# - App Router? (Yes - modern approach)
# - Import alias? (Yes - @/*)
```

**Step 2:** Navigate and start

```bash
cd my-next-app
npm run dev
```

The development server starts at `http://localhost:3000`.

### Next.js Project Structure

**Next.js 15 with App Router (2025 standard):**

```tree
my-next-app/
├── node_modules/
├── public/                    # Static files
│   ├── images/
│   └── favicon.ico
├── src/                       # Source code (recommended)
│   ├── app/                   # App Router (Next.js 13+)
│   │   ├── layout.tsx        # Root layout
│   │   ├── page.tsx          # Home page
│   │   ├── globals.css       # Global styles
│   │   ├── api/              # API routes
│   │   │   └── hello/
│   │   │       └── route.ts
│   │   ├── about/            # About page
│   │   │   └── page.tsx
│   │   └── blog/             # Blog section
│   │       ├── page.tsx      # Blog list
│   │       └── [slug]/       # Dynamic route
│   │           └── page.tsx
│   ├── components/            # React components
│   │   ├── ui/               # UI components
│   │   └── features/         # Feature components
│   ├── lib/                  # Utility functions
│   ├── hooks/                # Custom hooks
│   └── types/                # TypeScript types
├── .env.local                # Environment variables
├── .eslintrc.json            # ESLint config
├── .gitignore
├── next.config.js            # Next.js configuration
├── package.json
├── tsconfig.json             # TypeScript config
└── README.md
```

**Key Next.js concepts:**

- **App Router**: Modern routing system (Next.js 13+)
- **Server Components**: Default in App Router
- **Client Components**: Use `'use client'` directive
- **Layouts**: Shared UI for multiple pages
- **Loading/Error**: Special files for loading and error states

### Next.js Configuration

**next.config.js** - Basic configuration:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['example.com'], // External image domains
    formats: ['image/avif', 'image/webp'],
  },
};

export default nextConfig;
```

**Advanced configuration:**

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  
  // Image optimization
  images: {
    domains: ['cdn.example.com', 'images.unsplash.com'],
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
  
  // Environment variables available to browser
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
  },
  
  // Headers
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on',
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
        ],
      },
    ];
  },
  
  // Redirects
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

export default nextConfig;
```

## Project Structure Best Practices

### Component Organization

**Recommended component structure:**

```tree
src/components/
├── ui/                        # Reusable UI components
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.module.css
│   │   └── index.ts
│   ├── Input/
│   └── Card/
├── features/                  # Feature-specific components
│   ├── Auth/
│   │   ├── LoginForm.tsx
│   │   ├── RegisterForm.tsx
│   │   └── index.ts
│   ├── Blog/
│   └── Dashboard/
├── layout/                    # Layout components
│   ├── Header.tsx
│   ├── Footer.tsx
│   └── Sidebar.tsx
└── shared/                    # Shared components
    ├── ErrorBoundary.tsx
    └── Loading.tsx
```

**Component file structure example:**

```typescript
// components/ui/Button/Button.tsx
import styles from './Button.module.css';

interface ButtonProps {
  variant?: 'primary' | 'secondary';
  children: React.ReactNode;
  onClick?: () => void;
}

export function Button({ variant = 'primary', children, onClick }: ButtonProps) {
  return (
    <button className={styles[variant]} onClick={onClick}>
      {children}
    </button>
  );
}

// components/ui/Button/index.ts
export { Button } from './Button';
export type { ButtonProps } from './Button';
```

### Feature-Based Structure

**For larger applications, organize by feature:**

```tree
src/
├── features/
│   ├── authentication/
│   │   ├── components/
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── hooks/
│   │   │   └── useAuth.ts
│   │   ├── api/
│   │   │   └── authApi.ts
│   │   ├── types/
│   │   │   └── auth.types.ts
│   │   └── index.ts
│   ├── blog/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── types/
│   └── dashboard/
├── shared/
│   ├── components/
│   ├── hooks/
│   ├── utils/
│   └── types/
└── app/ or pages/
```

### Shared Resources

**Utility organization:**

```tree
src/
├── lib/                       # Third-party library configs
│   ├── axios.ts
│   └── queryClient.ts
├── utils/                     # Utility functions
│   ├── format.ts
│   ├── validation.ts
│   └── date.ts
├── hooks/                     # Custom hooks
│   ├── useDebounce.ts
│   ├── useLocalStorage.ts
│   └── useMediaQuery.ts
├── types/                     # TypeScript types
│   ├── api.types.ts
│   ├── common.types.ts
│   └── index.ts
└── constants/                 # Constants and configs
    ├── api.ts
    └── routes.ts
```

## Development Environment Setup

### ESLint Configuration

**Install ESLint dependencies:**

```bash
npm install -D eslint eslint-plugin-react eslint-plugin-react-hooks @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

**.eslintrc.json:**

```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2024,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "rules": {
    "react/react-in-jsx-scope": "off",
    "react/prop-types": "off",
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }],
    "no-console": ["warn", { "allow": ["warn", "error"] }]
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

### Prettier Setup

**Install Prettier:**

```bash
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
```

**.prettierrc:**

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

**.prettierignore:**

```tree
node_modules
.next
dist
build
coverage
*.min.js
```

### TypeScript Integration

**tsconfig.json** for Vite + React:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2023", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@utils/*": ["./src/utils/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**Next.js tsconfig.json** (auto-generated, with customizations):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### Git Configuration

**.gitignore:**

```text
# Dependencies
node_modules/
.pnp
.pnp.js

# Testing
coverage/

# Next.js
.next/
out/

# Production
build/
dist/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDEs
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# TypeScript
*.tsbuildinfo
```

## Environment Variables

### Vite Environment Variables

**Creating environment files:**

```bash
# .env - Shared across all environments
VITE_API_URL=https://api.example.com

# .env.local - Local overrides (not committed)
VITE_API_URL=http://localhost:3001

# .env.development - Development only
VITE_DEBUG=true

# .env.production - Production only
VITE_API_URL=https://api.production.com
```

**Usage in code:**

```typescript
// Vite exposes env vars on import.meta.env
const apiUrl = import.meta.env.VITE_API_URL;
const isDev = import.meta.env.DEV;
const isProd = import.meta.env.PROD;

// Type-safe environment variables
interface ImportMetaEnv {
  readonly VITE_API_URL: string;
  readonly VITE_APP_TITLE: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

**vite-env.d.ts:**

```typescript
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_API_URL: string;
  readonly VITE_APP_TITLE: string;
  readonly VITE_ANALYTICS_ID?: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

### Next.js Environment Variables

**Environment file structure:**

```bash
# .env.local - Local development (not committed)
DATABASE_URL=postgresql://localhost:5432/mydb
NEXTAUTH_SECRET=your-secret-key

# .env.production - Production values
DATABASE_URL=postgresql://prod-server:5432/mydb

# For browser access, use NEXT_PUBLIC_ prefix
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_SITE_URL=https://example.com
```

**Usage in code:**

```typescript
// Server-side only (API routes, server components)
const dbUrl = process.env.DATABASE_URL;

// Available in browser (NEXT_PUBLIC_ prefix required)
const apiUrl = process.env.NEXT_PUBLIC_API_URL;

// Type-safe environment variables
declare global {
  namespace NodeJS {
    interface ProcessEnv {
      DATABASE_URL: string;
      NEXTAUTH_SECRET: string;
      NEXT_PUBLIC_API_URL: string;
      NEXT_PUBLIC_SITE_URL: string;
    }
  }
}
```

### Security Best Practices

**DO:**

- ✅ Use `.env.local` for sensitive keys (never commit)
- ✅ Use `NEXT_PUBLIC_` or `VITE_` prefix for browser-exposed variables
- ✅ Validate environment variables on startup
- ✅ Use different keys for development and production
- ✅ Store production secrets in deployment platform (Vercel, Netlify, etc.)

**DON'T:**

- ❌ Commit `.env.local` or `.env.production` to Git
- ❌ Expose sensitive API keys to the browser
- ❌ Use the same secrets across environments
- ❌ Hardcode secrets in source code

**Environment variable validation:**

```typescript
// lib/env.ts
function validateEnv() {
  const required = [
    'VITE_API_URL',
    'VITE_APP_TITLE',
  ];
  
  const missing = required.filter(
    (key) => !import.meta.env[key]
  );
  
  if (missing.length > 0) {
    throw new Error(
      `Missing required environment variables: ${missing.join(', ')}`
    );
  }
}

validateEnv();
```

## Build and Deployment Preparation

### Development vs Production Builds

**Vite build commands:**

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0"
  }
}
```

**Next.js build commands:**

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

### Build Optimization

**Vite build optimization:**

```javascript
// vite.config.js
export default defineConfig({
  build: {
    target: 'es2020',
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'router': ['react-router-dom'],
        },
      },
    },
    chunkSizeWarningLimit: 1000,
  },
});
```

**Next.js optimization (next.config.js):**

```javascript
export default {
  compiler: {
    removeConsole: process.env.NODE_ENV === 'production',
  },
  experimental: {
    optimizePackageImports: ['lodash', 'date-fns'],
  },
  productionBrowserSourceMaps: false,
};
```

### Analyzing Bundle Size

**Vite bundle analysis:**

```bash
npm install -D rollup-plugin-visualizer
```

```javascript
// vite.config.js
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
});
```

**Next.js bundle analysis:**

```bash
npm install -D @next/bundle-analyzer
```

```javascript
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({
  // Your Next.js config
});
```

Run with: `ANALYZE=true npm run build`

## Legacy: Create React App

### ⚠️ Not Recommended for New Projects

Create React App is no longer maintained and should not be used for new projects. However, you may encounter existing projects using it.

**Migration path from CRA:**

1. **To Vite**: Use automated migration tools or manual migration
2. **To Next.js**: For projects needing SSR or enhanced features

**Quick migration to Vite:**

```bash
# Install migration tool
npx cra-to-vite

# Or manual migration:
# 1. Install Vite and plugins
npm install -D vite @vitejs/plugin-react

# 2. Create vite.config.js
# 3. Move public/index.html to root
# 4. Update scripts in package.json
# 5. Replace REACT_APP_ with VITE_
```

## Summary and Best Practices

### 2025 Project Setup Guidelines

**✅ Recommended Practices:**

1. **Use Vite for SPAs** - Modern, fast, optimized
2. **Use Next.js for full-featured apps** - SSR, API routes, optimization
3. **Enable TypeScript** - Type safety prevents bugs
4. **Configure ESLint & Prettier** - Consistent code quality
5. **Organize by feature** - Scalable project structure
6. **Use environment variables** - Secure configuration management
7. **Set up Git properly** - Proper .gitignore and branch strategy

**❌ Avoid These Patterns:**

1. **Don't use Create React App** - Unmaintained, outdated
2. **Don't skip TypeScript** - Missing out on type safety
3. **Don't commit secrets** - Use .env.local properly
4. **Don't skip linting** - Catches bugs early
5. **Don't ignore bundle size** - Monitor and optimize

### Quick Decision Guide

**Choose Vite when:**

- Building a single-page application (SPA)
- Don't need server-side rendering
- Want the fastest development experience
- Targeting modern browsers only

**Choose Next.js when:**

- Need SEO optimization (SSR/SSG)
- Building full-featured web application
- Need API routes
- Want built-in optimizations
- Planning for production deployment

### Next Steps

Now that your project is set up, continue with:

- **Chapter 14**: Routing and Navigation
- **Chapter 15**: Forms and Input Handling
- **Chapter 16**: Testing React Applications
