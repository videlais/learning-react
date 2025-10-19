---
title: "Conventions and Concepts"
order: 0
chapter_number: 0
layout: chapter
---

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Book Conventions](#book-conventions)
  - [Typography](#typography)
  - [Code Examples](#code-examples)
  - [Annotations](#annotations)
- [Technology Overview](#technology-overview)
  - [Modern JavaScript (ES2020+)](#modern-javascript-es2020)
  - [TypeScript](#typescript)
  - [React and JSX](#react-and-jsx)
  - [Build Tools](#build-tools)
- [Core Web Technologies](#core-web-technologies)
  - [HTML](#html)
  - [CSS](#css)
  - [JavaScript](#javascript)
- [Prerequisites](#prerequisites)
- [Reviewing Core Concepts](#reviewing-core-concepts)
  - [Concept: HTML](#concept-html)
  - [Concept: CSS](#concept-css)
  - [Concept: JavaScript](#concept-javascript)

## Book Conventions

This book uses consistent conventions throughout to help you identify different types of information.

### Typography

- **Component names, HTML elements, and important concepts** appear in **bold**
- *Variables, properties, and parameters* appear in *italics*
- `Code`, filenames, and terminal commands appear in `monospace`
- File paths are shown as `/path/to/file.ts`
- Terminal commands use the `$` prefix: `$ npm install`

### Code Examples

Code examples are shown with syntax highlighting and language specification:

**TypeScript/React:**

```typescript
function Welcome({ name }: { name: string }) {
  return <h1>Hello, {name}!</h1>;
}
```

**Terminal commands:**

```bash
npm install react react-dom
npm run dev
```

**Configuration files:**

```json
{
  "name": "my-app",
  "version": "1.0.0"
}
```

### Annotations

Throughout the book, you'll see these indicators:

**✅ Recommended** - Modern best practices for 2025

**❌ Avoid** - Outdated or problematic patterns

**⚠️ Warning** - Important caveats or gotchas

**💡 Tip** - Helpful suggestions and optimizations

**📝 Note** - Additional context or explanations

## Technology Overview

This book focuses on modern React development using 2025 best practices and tooling.

### Modern JavaScript (ES2020+)

The book uses modern JavaScript features throughout:

**Arrow Functions:**

```javascript
// Traditional function
function add(a, b) {
  return a + b;
}

// Arrow function
const add = (a, b) => a + b;
```

**Destructuring:**

```javascript
// Object destructuring
const { name, email } = user;

// Array destructuring
const [first, second] = items;

// Props destructuring
function Welcome({ name, age }) {
  return <div>Hello, {name}!</div>;
}
```

**Async/Await:**

```javascript
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  const user = await response.json();
  return user;
}
```

**Template Literals:**

```javascript
const greeting = `Hello, ${name}! You have ${count} messages.`;
```

**Optional Chaining and Nullish Coalescing:**

```javascript
// Optional chaining
const userName = user?.profile?.name;

// Nullish coalescing
const displayName = userName ?? 'Guest';
```

### TypeScript

Most examples in this book use TypeScript for type safety:

**Type Annotations:**

```typescript
const name: string = 'Alice';
const age: number = 30;
const isActive: boolean = true;
```

**Interfaces:**

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  age?: number; // Optional property
}
```

**Function Types:**

```typescript
function greet(name: string): string {
  return `Hello, ${name}!`;
}

const add = (a: number, b: number): number => a + b;
```

**React Component Types:**

```typescript
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

function Button({ label, onClick, disabled = false }: ButtonProps) {
  return <button onClick={onClick} disabled={disabled}>{label}</button>;
}
```

**Generics:**

```typescript
function useState<T>(initialValue: T): [T, (value: T) => void] {
  // Implementation
}
```

### React and JSX

React uses JSX (JavaScript XML) syntax to define UI components:

**Basic JSX:**

```jsx
function Welcome() {
  return (
    <div className="welcome">
      <h1>Hello, World!</h1>
      <p>Welcome to React</p>
    </div>
  );
}
```

**JSX with TypeScript (TSX):**

```tsx
interface WelcomeProps {
  name: string;
}

function Welcome({ name }: WelcomeProps) {
  return <h1>Hello, {name}!</h1>;
}
```

**JSX Expressions:**

```jsx
function UserGreeting({ user, isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? (
        <h1>Welcome back, {user.name}!</h1>
      ) : (
        <h1>Please sign in.</h1>
      )}
    </div>
  );
}
```

**Key JSX Rules:**

1. Components must start with a capital letter: `<MyComponent />` not `<myComponent />`
2. Use `className` instead of `class`: `<div className="container">`
3. Close all tags: `<img />` not `<img>`
4. Return single root element or use fragments: `<>...</>` or `<React.Fragment>...</React.Fragment>`

### Build Tools

This book uses modern build tools:

**Vite** - Fast, modern build tool for single-page applications

```bash
npm create vite@latest my-app -- --template react-ts
```

**Next.js** - Full-featured React framework with App Router

```bash
npx create-next-app@latest my-app
```

**Package Management:**

```bash
# npm (Node Package Manager)
npm install package-name

# pnpm (faster alternative)
pnpm install package-name
```

## Core Web Technologies

Understanding these foundational technologies is essential for React development.

### HTML

Hypertext Markup Language (HTML) is the foundation of web pages. It defines the structure and content using elements.

**Modern HTML5 Structure:**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React Application</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

**Semantic HTML Elements:**

```html
<header>
  <nav>Navigation</nav>
</header>
<main>
  <article>
    <section>Content</section>
  </article>
</main>
<footer>Footer</footer>
```

### CSS

Cascading Style Sheets (CSS) define the presentation and styling of web pages.

**Modern CSS:**

```css
/* CSS Variables */
:root {
  --primary-color: #0066cc;
  --spacing: 1rem;
}

/* Modern Selectors */
.container {
  display: grid;
  gap: var(--spacing);
  padding: var(--spacing);
}

/* Flexbox Layout */
.flex-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Grid Layout */
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}

/* Media Queries */
@media (max-width: 768px) {
  .container {
    padding: 0.5rem;
  }
}
```

**CSS Modules (in React):**

```css
/* Button.module.css */
.button {
  background-color: var(--primary-color);
  color: white;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
}

.button:hover {
  opacity: 0.9;
}
```

```tsx
// Button.tsx
import styles from './Button.module.css';

function Button({ children }: { children: React.ReactNode }) {
  return <button className={styles.button}>{children}</button>;
}
```

### JavaScript

JavaScript powers the interactivity of web applications. React is built on JavaScript.

**DOM Manipulation (traditional):**

```javascript
// Traditional DOM manipulation
const element = document.querySelector('#root');
element.textContent = 'Hello, World!';
element.addEventListener('click', handleClick);
```

**Modern JavaScript Patterns:**

```javascript
// Array methods
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);

// Spread operator
const newArray = [...oldArray, newItem];
const newObject = { ...oldObject, newProperty: value };

// Object methods
const entries = Object.entries(obj);
const keys = Object.keys(obj);
const values = Object.values(obj);

// Promise handling
fetch('/api/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));

// Async/await (preferred)
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Failed to fetch:', error);
  }
}
```

## Prerequisites

To get the most out of this book, you should have:

**Required Knowledge:**

- ✅ Basic HTML, CSS, and JavaScript
- ✅ Understanding of ES6+ JavaScript features
- ✅ Familiarity with the command line/terminal
- ✅ Basic understanding of npm or package managers

**Recommended Knowledge:**

- 📚 TypeScript basics (covered in the book)
- 📚 Async JavaScript (promises, async/await)
- 📚 Git version control
- 📚 Web development fundamentals

**Development Environment:**

- **Node.js** 18.0 or higher
- **Code editor** (VS Code recommended)
- **Modern web browser** (Chrome, Firefox, Safari, or Edge)
- **Terminal/Command line** access

**Installation Check:**

```bash
# Check Node.js version
$ node --version
# Should output: v18.0.0 or higher

# Check npm version
$ npm --version
# Should output: 9.0.0 or higher
```

**Ready to Start:**

Once you have these prerequisites, you're ready to begin your React journey! The book will guide you through modern React development from fundamentals to production deployment.

---

**Next Chapter:** [Getting Started with React →](/chapters/chapter1/)

## Reviewing Core Concepts

### Concept: HTML

Hypertext Markup Language (HTML) is the underlining language of the internet. It uses different elements to define the layout of a document. It uses `.html` or `.htm` files.

**Example**:

```html
<html>
  <head>
    <title>HTMl Example</title>
  </head>
  <body>
  </body>
</html>
```

### Concept: CSS

Cascading StyleSheets (CSS) defines the presentation of a webpage. It uses *selectors* that will find and apply different style rules to a HTML document. These include the ID, `#`, and the class selector, `.`, among many others. It uses `.css` files.

**Example**:

```css
.classExample {
    color: white;
}
#idExample {
    background-color: red;
}
```

### Concept: JavaScript

JavaScript is the *lingua franca* of the internet. If HTML are the bones and CSS is the skin, JavaScript is the *muscle*. Any interaction or event that occurs in a webpage is filtered through JavaScript.

In a web browser, it is run and can change the current page through the Document-Object Model (DOM). This defines all of the elements of a page into objects that can be interacted with through a global **document** variable.

It uses `.js` files.

**Example:**

```javascript
let example = document.querySelector('#idExample');
```
