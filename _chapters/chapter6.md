---
title: "Working with CSS in React"
order: 6
chapter_number: 6
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Apply** CSS selectors and the `className` attribute in modern React function components.
- **Implement** various CSS approaches including CSS Modules, CSS-in-JS solutions, and utility frameworks.
- **Utilize** modern CSS features like custom properties, container queries, and logical properties in React.
- **Create** responsive, accessible designs using contemporary CSS methodologies.
- **Manage** dynamic styling with hooks and modern state management patterns.
- **Choose** appropriate styling solutions for different project requirements and team preferences.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Modern CSS in React](#modern-css-in-react)
- [Traditional CSS Approaches](#traditional-css-approaches)
  - [CSS Classes and `className`](#css-classes-and-classname)
  - [Importing CSS Files](#importing-css-files)
  - [CSS Modules](#css-modules)
- [CSS-in-JS Solutions](#css-in-js-solutions)
  - [Styled Components](#styled-components)
  - [Emotion](#emotion)
- [Utility-First CSS](#utility-first-css)
  - [Tailwind CSS](#tailwind-css)
- [Modern CSS Features](#modern-css-features)
  - [CSS Custom Properties](#css-custom-properties)
  - [Container Queries](#container-queries)
  - [Logical Properties](#logical-properties)
- [Dynamic Styling with Hooks](#dynamic-styling-with-hooks)
- [Performance and Best Practices](#performance-and-best-practices)

## Modern CSS in React

The React ecosystem has evolved dramatically since 2020, offering developers multiple sophisticated approaches to styling components. From traditional CSS files to CSS-in-JS solutions and utility frameworks, the choices can seem overwhelming. This chapter will guide you through the modern landscape of CSS in React, helping you understand when and why to use each approach.

**Key Styling Approaches in 2025:**

- **Traditional CSS**: CSS files, CSS Modules, and global stylesheets
- **CSS-in-JS**: Styled Components, Emotion, and other runtime solutions  
- **Utility-First**: Tailwind CSS and atomic CSS approaches
- **Zero-Runtime**: Compiled CSS-in-JS solutions like Compiled CSS
- **Component Libraries**: Pre-built design systems with built-in styling

## Traditional CSS Approaches

Despite newer alternatives, traditional CSS approaches remain popular and effective for many React applications.

### CSS Classes and `className`

React uses `className` instead of the HTML `class` attribute because `class` is a reserved keyword in JavaScript. This allows JSX elements to apply CSS classes just like regular HTML elements.

**Basic CSS Usage:**

```css
/* styles.css */
.primary-button {
  background-color: #007bff;
  color: white;
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 0.375rem;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.primary-button:hover {
  background-color: #0056b3;
}

.primary-button:disabled {
  background-color: #6c757d;
  cursor: not-allowed;
}
```

**Function Component Example:**

```javascript
import React from 'react';
import './styles.css';

function Button({ children, disabled = false, onClick }) {
  return (
    <button 
      className="primary-button" 
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

export default Button;
```

**Conditional Classes:**

```javascript
import React, { useState } from 'react';
import './styles.css';

function ToggleButton({ children }) {
  const [isActive, setIsActive] = useState(false);

  const buttonClass = `primary-button ${isActive ? 'active' : ''}`;
  
  return (
    <button 
      className={buttonClass}
      onClick={() => setIsActive(!isActive)}
    >
      {children}
    </button>
  );
}

export default ToggleButton;
```

### Importing CSS Files

Modern build tools (Webpack, Vite, Rollup) allow importing CSS files directly into JavaScript modules. This approach keeps styles co-located with components while maintaining traditional CSS syntax.

**Component File Structure:**

```bash
src/
  components/
    Card/
      Card.js
      Card.css
      index.js
```

**Card.css:**

```css
.card {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;
  margin-bottom: 1rem;
}

.card-title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
  color: #1f2937;
}

.card-content {
  color: #6b7280;
  line-height: 1.6;
}

.card-actions {
  margin-top: 1rem;
  display: flex;
  gap: 0.5rem;
}
```

**Card.js:**

```javascript
import React from 'react';
import './Card.css';

function Card({ title, children, actions }) {
  return (
    <div className="card">
      <h3 className="card-title">{title}</h3>
      <div className="card-content">
        {children}
      </div>
      {actions && (
        <div className="card-actions">
          {actions}
        </div>
      )}
    </div>
  );
}

export default Card;
```

**Usage Example:**

```javascript
import React from 'react';
import Card from './components/Card';

function App() {
  return (
    <div>
      <Card 
        title="Welcome" 
        actions={[
          <button key="learn">Learn More</button>,
          <button key="contact">Contact</button>
        ]}
      >
        <p>This is a card component with imported CSS styling.</p>
      </Card>
    </div>
  );
}

export default App;
```

### CSS Modules

CSS Modules provide locally scoped CSS, solving the global namespace problem by automatically generating unique class names. This prevents style conflicts between components while maintaining familiar CSS syntax.

**Setup in Modern Build Tools:**

CSS Modules work out-of-the-box in Create React App and Vite by naming files with the `.module.css` extension.

**ProductCard.module.css:**

```css
.container {
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  padding: 1rem;
  background: white;
  transition: box-shadow 0.2s;
}

.container:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 4px;
}

.title {
  font-size: 1.125rem;
  font-weight: 600;
  margin: 0.75rem 0 0.5rem 0;
  color: #111827;
}

.price {
  font-size: 1.25rem;
  font-weight: 700;
  color: #059669;
}

.description {
  color: #6b7280;
  margin: 0.5rem 0;
  line-height: 1.5;
}

.badge {
  display: inline-block;
  padding: 0.25rem 0.5rem;
  background: #dbeafe;
  color: #1e40af;
  border-radius: 9999px;
  font-size: 0.75rem;
  font-weight: 500;
}
```

**ProductCard.js:**

```javascript
import React from 'react';
import styles from './ProductCard.module.css';

function ProductCard({ product }) {
  const { name, price, image, description, category, inStock } = product;

  return (
    <div className={styles.container}>
      <img src={image} alt={name} className={styles.image} />
      <h3 className={styles.title}>{name}</h3>
      <p className={styles.price}>${price}</p>
      <p className={styles.description}>{description}</p>
      
      <div>
        <span className={styles.badge}>{category}</span>
        {!inStock && <span className={styles.badge}>Out of Stock</span>}
      </div>
    </div>
  );
}

export default ProductCard;
```

**Combining Classes with CSS Modules:**

```javascript
import React, { useState } from 'react';
import styles from './InteractiveCard.module.css';

function InteractiveCard({ children }) {
  const [isSelected, setIsSelected] = useState(false);
  
  // Combine multiple CSS Module classes
  const cardClasses = [
    styles.card,
    isSelected ? styles.selected : '',
    styles.interactive
  ].filter(Boolean).join(' ');

  return (
    <div 
      className={cardClasses}
      onClick={() => setIsSelected(!isSelected)}
    >
      {children}
    </div>
  );
}

export default InteractiveCard;
```

**Benefits of CSS Modules:**

- **Scoped Styles**: No global namespace pollution
- **Familiar Syntax**: Standard CSS with enhanced capabilities
- **Dependency Tracking**: Unused styles can be detected
- **Composition**: Easy to compose styles from multiple sources
- **TypeScript Support**: Generate type definitions for class names

## CSS-in-JS Solutions

CSS-in-JS has become extremely popular in the React ecosystem, allowing you to write CSS directly in JavaScript with full access to component props and state.

### Styled Components

Styled Components is the most popular CSS-in-JS library, using tagged template literals to style components.

**Installation:**

```bash
npm install styled-components
```

**Basic Usage:**

```javascript
import React from 'react';
import styled from 'styled-components';

const StyledButton = styled.button`
  background: ${props => props.primary ? '#007bff' : 'white'};
  color: ${props => props.primary ? 'white' : '#007bff'};
  padding: 0.75rem 1.5rem;
  border: 2px solid #007bff;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.2s;

  &:hover {
    background: ${props => props.primary ? '#0056b3' : '#f8f9fa'};
    transform: translateY(-1px);
  }

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    transform: none;
  }
`;

function Button({ primary, children, ...props }) {
  return (
    <StyledButton primary={primary} {...props}>
      {children}
    </StyledButton>
  );
}

export default Button;
```

**Theme Support:**

```javascript
import React from 'react';
import styled, { ThemeProvider } from 'styled-components';

const theme = {
  colors: {
    primary: '#007bff',
    secondary: '#6c757d',
    success: '#28a745',
    danger: '#dc3545',
  },
  breakpoints: {
    mobile: '768px',
    tablet: '1024px',
    desktop: '1200px',
  },
  spacing: {
    xs: '0.25rem',
    sm: '0.5rem',
    md: '1rem',
    lg: '1.5rem',
    xl: '2rem',
  }
};

const Card = styled.div`
  background: white;
  border-radius: 8px;
  padding: ${props => props.theme.spacing.lg};
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  
  @media (max-width: ${props => props.theme.breakpoints.mobile}) {
    padding: ${props => props.theme.spacing.md};
  }
`;

const Title = styled.h2`
  color: ${props => props.theme.colors.primary};
  margin-bottom: ${props => props.theme.spacing.md};
`;

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Card>
        <Title>Themed Component</Title>
        <p>This component uses theme values for consistent styling.</p>
      </Card>
    </ThemeProvider>
  );
}

export default App;
```

### Emotion

Emotion is another popular CSS-in-JS library that offers both styled components and CSS prop approaches.

**Installation:**

```bash
npm install @emotion/react @emotion/styled
```

**Styled Component Approach:**

```javascript
import React from 'react';
import styled from '@emotion/styled';

const AlertBox = styled.div`
  padding: 1rem;
  border-radius: 4px;
  margin-bottom: 1rem;
  
  ${props => {
    switch (props.variant) {
      case 'success':
        return `
          background: #d4edda;
          color: #155724;
          border: 1px solid #c3e6cb;
        `;
      case 'warning':
        return `
          background: #fff3cd;
          color: #856404;
          border: 1px solid #ffeaa7;
        `;
      case 'error':
        return `
          background: #f8d7da;
          color: #721c24;
          border: 1px solid #f5c6cb;
        `;
      default:
        return `
          background: #d1ecf1;
          color: #0c5460;
          border: 1px solid #bee5eb;
        `;
    }
  }}
`;

function Alert({ variant, children }) {
  return <AlertBox variant={variant}>{children}</AlertBox>;
}

export default Alert;
```

**CSS Prop Approach:**

```javascript
/** @jsxImportSource @emotion/react */
import { css } from '@emotion/react';

const buttonStyles = css`
  background: linear-gradient(45deg, #fe6b8b 30%, #ff8e53 90%);
  border: 0;
  border-radius: 3px;
  box-shadow: 0 3px 5px 2px rgba(255, 105, 135, 0.3);
  color: white;
  height: 48px;
  padding: 0 30px;
  cursor: pointer;
  
  &:hover {
    box-shadow: 0 6px 10px 4px rgba(255, 105, 135, 0.3);
  }
`;

function GradientButton({ children, ...props }) {
  return (
    <button css={buttonStyles} {...props}>
      {children}
    </button>
  );
}

export default GradientButton;
```

## Utility-First CSS

Utility-first CSS frameworks provide low-level utility classes that let you build custom designs without writing CSS. This approach has gained massive popularity for its developer experience and maintainability.

### Tailwind CSS

Tailwind CSS is the most popular utility-first framework, providing thousands of utility classes for rapid UI development.

**Installation:**

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Basic Usage:**

```javascript
import React, { useState } from 'react';

function ProductCard({ product }) {
  const [isLiked, setIsLiked] = useState(false);
  
  return (
    <div className="max-w-sm mx-auto bg-white rounded-xl shadow-md overflow-hidden">
      <div className="relative">
        <img 
          className="w-full h-48 object-cover" 
          src={product.image} 
          alt={product.name}
        />
        <button
          onClick={() => setIsLiked(!isLiked)}
          className={`absolute top-2 right-2 p-2 rounded-full transition-colors ${
            isLiked 
              ? 'bg-red-500 text-white' 
              : 'bg-white text-gray-600 hover:bg-gray-100'
          }`}
        >
          ♥
        </button>
      </div>
      
      <div className="p-6">
        <div className="flex justify-between items-start mb-2">
          <h3 className="text-lg font-semibold text-gray-900">
            {product.name}
          </h3>
          <span className="text-xl font-bold text-green-600">
            ${product.price}
          </span>
        </div>
        
        <p className="text-gray-600 text-sm mb-4">
          {product.description}
        </p>
        
        <div className="flex justify-between items-center">
          <span className="inline-block bg-blue-100 text-blue-800 text-xs px-2 py-1 rounded-full">
            {product.category}
          </span>
          
          <button className="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded-md transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
            Add to Cart
          </button>
        </div>
      </div>
    </div>
  );
}

export default ProductCard;
```

**Responsive Design:**

```javascript
import React from 'react';

function ResponsiveGrid({ children }) {
  return (
    <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 p-4">
      {children}
    </div>
  );
}

function HeroSection() {
  return (
    <section className="bg-gradient-to-r from-blue-500 to-purple-600 text-white">
      <div className="container mx-auto px-4 py-16 sm:py-24">
        <div className="text-center">
          <h1 className="text-4xl sm:text-5xl md:text-6xl font-bold mb-6">
            Build Amazing Products
          </h1>
          <p className="text-lg sm:text-xl mb-8 max-w-2xl mx-auto opacity-90">
            Create stunning user interfaces with modern React and CSS techniques.
          </p>
          <div className="flex flex-col sm:flex-row gap-4 justify-center">
            <button className="bg-white text-blue-600 px-8 py-3 rounded-lg font-semibold hover:bg-gray-100 transition-colors">
              Get Started
            </button>
            <button className="border-2 border-white text-white px-8 py-3 rounded-lg font-semibold hover:bg-white hover:text-blue-600 transition-colors">
              Learn More
            </button>
          </div>
        </div>
      </div>
    </section>
  );
}

export { ResponsiveGrid, HeroSection };
```

**Custom Utilities with @apply:**

```css
/* styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn-primary {
    @apply bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded-md transition-colors focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2;
  }
  
  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
  
  .form-input {
    @apply border border-gray-300 rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent;
  }
}
```

## Modern CSS Features

2025 brings several exciting CSS features that work excellently with React applications.

### CSS Custom Properties

CSS custom properties (CSS variables) provide dynamic theming and design token management.

**Setting up CSS Variables:**

```css
/* theme.css */
:root {
  --color-primary: #3b82f6;
  --color-primary-dark: #1d4ed8;
  --color-secondary: #64748b;
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-danger: #ef4444;
  
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.25rem;
  
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 1.5rem;
  --spacing-xl: 2rem;
  
  --border-radius: 0.375rem;
  --shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}

[data-theme="dark"] {
  --color-primary: #60a5fa;
  --color-primary-dark: #3b82f6;
  --color-secondary: #94a3b8;
}
```

**Using CSS Variables in Components:**

```javascript
import React, { useState } from 'react';
import './theme.css';

function ThemedButton({ variant = 'primary', size = 'md', children, ...props }) {
  const buttonStyle = {
    '--button-color': `var(--color-${variant})`,
    '--button-size': size === 'sm' ? 'var(--font-size-sm)' : 
                     size === 'lg' ? 'var(--font-size-lg)' : 'var(--font-size-base)',
    '--button-padding': size === 'sm' ? 'var(--spacing-sm) var(--spacing-md)' :
                        size === 'lg' ? 'var(--spacing-lg) var(--spacing-xl)' : 'var(--spacing-md) var(--spacing-lg)'
  };

  return (
    <button 
      className="themed-button"
      style={buttonStyle}
      {...props}
    >
      {children}
    </button>
  );
}

function ThemeToggle() {
  const [isDark, setIsDark] = useState(false);
  
  const toggleTheme = () => {
    setIsDark(!isDark);
    document.documentElement.setAttribute('data-theme', isDark ? 'light' : 'dark');
  };
  
  return (
    <button onClick={toggleTheme} className="theme-toggle">
      {isDark ? '☀️' : '🌙'} Toggle Theme
    </button>
  );
}

export { ThemedButton, ThemeToggle };
```

### Container Queries

Container queries allow components to respond to their container's size rather than the viewport size.

**CSS:**

```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

.card {
  padding: 1rem;
  background: white;
  border-radius: 8px;
  box-shadow: var(--shadow);
}

@container card (min-width: 300px) {
  .card {
    padding: 1.5rem;
  }
  
  .card-layout {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 1rem;
    align-items: center;
  }
}

@container card (min-width: 500px) {
  .card {
    padding: 2rem;
  }
  
  .card-image {
    width: 120px;
    height: 120px;
  }
}
```

**React Component:**

```javascript
import React from 'react';
import './container-queries.css';

function AdaptiveCard({ image, title, description, actions }) {
  return (
    <div className="card-container">
      <div className="card">
        <div className="card-layout">
          {image && <img src={image} alt={title} className="card-image" />}
          <div>
            <h3 className="card-title">{title}</h3>
            <p className="card-description">{description}</p>
            {actions && <div className="card-actions">{actions}</div>}
          </div>
        </div>
      </div>
    </div>
  );
}

export default AdaptiveCard;
```

### Logical Properties

Logical properties provide better internationalization support for RTL languages.

```css
.content-block {
  /* Instead of margin-left/margin-right */
  margin-inline-start: 1rem;
  margin-inline-end: 2rem;
  
  /* Instead of margin-top/margin-bottom */
  margin-block-start: 0.5rem;
  margin-block-end: 1rem;
  
  /* Instead of border-left */
  border-inline-start: 2px solid var(--color-primary);
  
  /* Instead of padding-left/padding-right */
  padding-inline: 1rem;
  
  /* Instead of padding-top/padding-bottom */
  padding-block: 0.5rem;
}
```

## Dynamic Styling with Hooks

Modern React applications often need to change styles based on user interactions, component state, or external data. Here are effective patterns for dynamic styling.

**State-Based Styling:**

```javascript
import React, { useState, useEffect } from 'react';
import styles from './InteractiveButton.module.css';

function InteractiveButton({ children, onClick }) {
  const [isPressed, setIsPressed] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  const handleClick = async () => {
    setIsLoading(true);
    try {
      await onClick?.();
    } finally {
      setIsLoading(false);
    }
  };

  const buttonClasses = [
    styles.button,
    isPressed && styles.pressed,
    isLoading && styles.loading
  ].filter(Boolean).join(' ');

  return (
    <button
      className={buttonClasses}
      onMouseDown={() => setIsPressed(true)}
      onMouseUp={() => setIsPressed(false)}
      onMouseLeave={() => setIsPressed(false)}
      onClick={handleClick}
      disabled={isLoading}
    >
      {isLoading ? 'Loading...' : children}
    </button>
  );
}

export default InteractiveButton;
```

**Custom Hook for Theme Management:**

```javascript
import { useState, useEffect, createContext, useContext } from 'react';

const ThemeContext = createContext();

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState(() => {
    const saved = localStorage.getItem('theme');
    return saved || 'light';
  });

  useEffect(() => {
    localStorage.setItem('theme', theme);
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={% raw %}{{ theme, toggleTheme }}{% endraw %}>
      {children}
    </ThemeContext.Provider>
  );
}
```

**Animation with CSS and State:**

```javascript
import React, { useState, useRef, useEffect } from 'react';
import styles from './AnimatedCard.module.css';

function AnimatedCard({ children }) {
  const [isVisible, setIsVisible] = useState(false);
  const cardRef = useRef();

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.unobserve(entry.target);
        }
      },
      { threshold: 0.1 }
    );

    if (cardRef.current) {
      observer.observe(cardRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <div 
      ref={cardRef}
      className={`${styles.card} ${isVisible ? styles.visible : ''}`}
    >
      {children}
    </div>
  );
}

export default AnimatedCard;
```

**Responsive Hook:**

```javascript
import { useState, useEffect } from 'react';

function useMediaQuery(query) {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    const media = window.matchMedia(query);
    if (media.matches !== matches) {
      setMatches(media.matches);
    }
    
    const listener = () => setMatches(media.matches);
    media.addEventListener('change', listener);
    
    return () => media.removeEventListener('change', listener);
  }, [matches, query]);

  return matches;
}

function ResponsiveComponent() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  const isTablet = useMediaQuery('(max-width: 1024px)');
  
  return (
    <div className={`
      ${isMobile ? 'mobile-layout' : ''}
      ${isTablet && !isMobile ? 'tablet-layout' : ''}
      ${!isTablet ? 'desktop-layout' : ''}
    `}>
      <h2>{isMobile ? 'Mobile View' : isTablet ? 'Tablet View' : 'Desktop View'}</h2>
    </div>
  );
}

export { useMediaQuery, ResponsiveComponent };
```

## Performance and Best Practices

**CSS Loading Performance:**

1. **Critical CSS**: Inline critical styles to prevent render blocking
2. **Code Splitting**: Use dynamic imports for route-specific styles
3. **Purge Unused CSS**: Remove unused styles in production builds
4. **CSS Compression**: Minify and compress CSS assets

**Best Practices Summary:**

- **Choose the Right Tool**: CSS Modules for component libraries, Tailwind for rapid prototyping, CSS-in-JS for dynamic themes
- **Avoid Inline Styles**: Use CSS classes for better performance and maintainability
- **Use CSS Custom Properties**: Enable runtime theming and reduce bundle size
- **Implement Design Systems**: Create consistent, reusable styling patterns
- **Test Across Devices**: Ensure responsive design works on all screen sizes
- **Consider Accessibility**: Use sufficient color contrast and focus indicators
- **Optimize for Performance**: Minimize CSS bundle size and render-blocking resources

The CSS landscape in React continues to evolve rapidly. Choose approaches that align with your team's expertise, project requirements, and long-term maintenance goals.
