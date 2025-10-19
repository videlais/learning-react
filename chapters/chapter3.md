---
title: "Thinking in HTML, Writing in JS"
order: 3
chapter_number: 3
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Review** fundamental HTML structure and semantic elements including `<head>` and `<body>`.
- **Compare** the composition model approach to traditional HTML document structure.
- **Construct** basic JSX elements and understand the syntax differences from HTML.
- **Evaluate** when to use JSX over traditional HTML in React applications.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Reviewing HTML](#reviewing-html)
  - [`<head>`](#head)
  - [`<body>`](#body)
  - [Attributes](#attributes)
    - [`id` and `class`](#id-and-class)
- [Introducing the Composition Model](#introducing-the-composition-model)
  - [Composition Model](#composition-model)
- [Introducing JSX](#introducing-jsx)
  - [JSX Rules](#jsx-rules)
    - [One Root Element](#one-root-element)
    - [All Elements Must Close](#all-elements-must-close)
    - [JSX Expressions](#jsx-expressions)
- [Modern Build Tools and Development](#modern-build-tools-and-development)
  - [`export`](#export)
  - [`import`](#import)

## Reviewing HTML

HyperText Markup Language (HTML) uses *elements* to define the layout of a document. These are composed of *tags* that mark the start and end of the element and enclose any content held within it.

Each *opening tag* of an element starts with a less-than sign, `<`, the name of the element, and then a greater-than sign, `>`.

```html
<html>
```

The *closing tag* of an element looks similar to the opening tag, but includes the backslash, `/`, before the name of the ending tag.

```html
</html>
```

**Element Example:**

```html
<html></html>
```

HTML is a *markup* language because it "marks up" a document to define how its content should be structured. HTML is often considered the "bones" of a document because it "holds up" the content and defines its internal shape.

The first element of every document `<html>`. Inside of this element are two others: `<head>` and `<body>`.

Like with the human body, people look at the "body" but the "head" keeps details about the body. They are interconnected and work with each other to present a complete whole.

When elements are described in connection to each other, the metaphor of a family is used. Elements are a 'parent' when they have 'children'.

```html
<html>
  <head>
  </head>
  <body>
  <body>
</html>
```

**HTML Document Structure Hierarchy:**

The HTML document follows a tree-like structure where each element can contain other elements:

- **Root Level:** `<html>` serves as the document root
- **Second Level:** `<head>` and `<body>` are direct children of `<html>`
- **Deeper Levels:** Elements within `<body>` can nest multiple levels deep

This hierarchical relationship helps organize content logically and allows CSS and JavaScript to target specific elements through their relationships.

(In the above example, the elements `<head>` and `<body>` are *children* of the parent element, `<html>`.)

### `<head>`

The `<head>` element defines details about the page such as its author, title, and other resources that are linked to the document like stylesheets.

One of the most common child elements of `<head>` is `<title>`. This defines, as it name implies, the *title* of the document.

```html
<html>
  <head>
    <title>This is the title!</title>
  </head>
</html>
```

### `<body>`

The `<body>` element contains everything not in the `<head>`. Anything that defines structure and layout belongs in the `<body>`. There are dozens of possible elements that can be used in `<body>`, but a few of the common elements are:

- `<p>`, the paragraph element, for defining selections of text.

- `<h1>, <h2>, <h3>, <h4>, <h5>, <h6>`, the headers, for defining different text headings at different levels (1 - 6, in decreasing size).

- `<div>`, the division element, for "dividing up" a page into different sections.

- `<em>`, the emphasis element, for giving text *emphasis*.

- `<strong>`, the strong emphasis element, for giving text **strong emphasis**.

**Modern Semantic Elements:** HTML5 introduced semantic elements that provide meaning to the structure:

- `<header>`, for page or section headers
- `<nav>`, for navigation links
- `<main>`, for the primary content of the page
- `<section>`, for thematic groupings of content
- `<article>`, for standalone content (blog posts, news articles)
- `<aside>`, for sidebar content or supplementary information
- `<footer>`, for page or section footers

These semantic elements improve accessibility and help screen readers understand page structure.

### Attributes

Every element supports *attributes*, ways to influence how the element is structured or understands its content. Attributes are written using a `name='value'` syntax where the name of the attribute is written inside of the opening tag and its value is inside either single- or double-quotation marks.

**Example:**

```html
<div class="redText"></div>
```

Two of the most common attributes, *id* and *class*, connect HTML to CSS, allowing the element to be "selected" from the document and styled in certain ways.

#### `id` and `class`

The attributes `id` and `class` have special usages in HTML. In the document, an `id` value can only be used once. This gives it a unique *identification*.

```html
<html>
  <body>
    <div id="sidebar"></div>
    <div id="content-body"></div>
  </body>
</html>
```

The `class` attribute, on the other hand, can be thought of as a *classification*. It can be applied to many elements.

```html
<html>
  <body>
    <div id="sidebar" class="redFont"></div>
    <div id="content-body" class="redFont"></div>
  </body>
</html>
```

## Introducing the Composition Model

Traditionally, a document would be divided up into different *sections* based on how it was written. It might use different elements such as `<nav>`, `<article>`, or different collections of `<div>` elements to organize the document.

**Example:**

```html
<html>
  <body>
    <div id='sidebar'>
      <p>Sidebar Content</p>
    </div>
    <div id="content-body">
      <article>
        <p>Main Content</p>
      </article>
    </div>
  </body>
</html>
```

### Composition Model

The *Composition Model* transforms each "section" into its own component. Instead of viewing the page as a single unit, it breaks each different part into its own unit. Each *component* , then, takes care of itself. It has its own elements and its connection to the larger document is through its parent component.

Each component is also its own *pseudo* element that stands in for a set of elements that could, themselves, be inside other components. Arranging these components -- *composition* -- changes how a document is considered.

**Composition Model Visualization:**

**Traditional Approach:** One large HTML file containing all page sections mixed together:

- All HTML elements exist in a single document
- Structure is defined by `div` elements with descriptive IDs
- Changes require editing the main HTML file
- Code reuse is difficult across different pages

**Composition Approach:** Page broken into independent, reusable components:

- Each component manages its own HTML structure
- Components can be developed and tested independently  
- Components can be reused across multiple pages
- Changes to a component automatically update everywhere it's used

**Updated Example:**

```html
<html>
  <body>
    <Sidebar />
    <MainContent />
  </body>
</html>
```

**`<Sidebar />`:**

```html
<div class='sidebar'>
    <p>Sidebar Content</p>
</div>
```

**`<MainContent />`:**

```html
<main>
    <article>
      <p>Main Content</p>
    </article>
</main>
```

**Accessibility Note:** When using the composition model, consider using semantic HTML elements (`<main>`, `<nav>`, `<header>`, etc.) within your components to maintain proper document structure for screen readers and other assistive technologies.

## Introducing JSX

React was created at Facebook. Its developers came up with the component model through also introducing a new technology to help with using it: JSX.

[JavaScript XML (JSX)](https://facebook.github.io/jsx/) solves a common problem in web development. Often, when working with a project, there will be HTML, JavaScript, and CSS files. Depending on the project, these could grow to dozens of files. Updating some code could require, in large projects, updating many files in multiple places.

To help solve this problem, they proposed something radical. *Why not put HTML inside the JavaScript code?* In order to process this type of content, they introduced JavaScript XML, which allows for placing HTML-like elements (XML) inside JavaScript where it can be treated like any other value.

However, to do this, they tied it to the existing rules around the Extensible Markup Language (XML).

### JSX Rules

#### One Root Element

HTML already has a root element named `<html>`. However, some sections of a web page may not, themselves, have a root element. JSX, based on XML, demands that any component (collection of elements) *always* have a root element for that component.

**Traditional Approach:**

```html
<div>
  <!-- Code -->
</div>
```

**Modern Approach with React Fragments:** To avoid creating unnecessary wrapper elements in the DOM, React provides Fragments:

```jsx
<>
  <h1>Title</h1>
  <p>Content</p>
</>
```

Or using the explicit syntax:

```jsx
<React.Fragment>
  <h1>Title</h1>
  <p>Content</p>
</React.Fragment>
```

Fragments let you group elements without adding extra nodes to the DOM.

**React Fragment Benefits Explained:**

**Without Fragments:** When you need to return multiple elements from a component, you must wrap them in a container element like `<div>`. This creates an extra DOM node that serves no semantic purpose and can interfere with CSS layouts.

**With Fragments:** React Fragments allow you to group multiple elements without creating wrapper DOM nodes. The Fragment acts as an invisible container during development but disappears in the final rendered HTML.

**Real-world Impact:**

- Cleaner HTML output with fewer unnecessary wrapper elements
- Better CSS layout control (no unexpected containers)
- Improved semantic HTML structure
- Reduced DOM complexity for better performance

#### All Elements Must Close

Most HTML elements have a closing tag. However, some do not. For these, they must "self-close" (include an ending backslash, `/`).

Based on XML rules, any element used must close before another can start.

**Example:**

```html
<element></element>
<another />
```

#### JSX Expressions

Using the values of variables is a core part of any programming language and the developers at Facebook knew this. They added *JSX expressions* into the language.

An expression in JavaScript is anything that has a value. This includes, of course, variables, which have values, but also functions, which are a type of value.

However, what is *not* an expression are existing keywords and loop structures in JavaScript. The keywords `if`, `else`, `for`, and `while`, for example, are *not* expressions and cannot be used within JSX.

All other expressions, variables and functions, can be used through opening and closing curly brackets within the XML.

```javascript
let exampleValue = 5;

let exampleJSX = <div>{exampleValue}</div>;
```

**Common JSX Expression Patterns:**

```jsx
// Conditional rendering with ternary operator
const isLoggedIn = true;
const greeting = <div>{isLoggedIn ? 'Welcome back!' : 'Please log in'}</div>;

// Conditional rendering with logical AND
const hasMessages = true;
const messageAlert = <div>{hasMessages && <span>You have new messages!</span>}</div>;

// Rendering lists with map()
const items = ['apple', 'banana', 'orange'];
const itemList = (
  <ul>
    {items.map((item, index) => (
      <li key={index}>{item}</li>
    ))}
  </ul>
);
```

These patterns are essential for dynamic content in React applications.

**JSX Expression Pattern Guide:**

**Ternary Operator (`condition ? true : false`):**
Use when you need to choose between two different elements or values based on a condition. This is the most common pattern for conditional rendering in JSX.

**Logical AND (`condition && element`):**
Use when you want to show an element only if a condition is true, and show nothing when false. This pattern is cleaner than ternary when you don't need an "else" case.

**Array Map (`array.map(item => <element>)`):**
Use to transform an array of data into an array of JSX elements. Essential for rendering lists, tables, or any repeated content. Always include a unique `key` prop for each mapped element.

**When to Use Each Pattern:**

- **Ternary:** Toggle between different content (login vs logout button)
- **Logical AND:** Show optional content (notifications, alerts)  
- **Map:** Display lists of data (user profiles, products, comments)

While it can look odd, JSX allows for using HTML (XML) directly inside JavaScript. This also allows components to be collections of elements, too. They can contain JSX that defines the structure of its contents.

## Modern Build Tools and Development

React applications require build tools to transform JSX and modern JavaScript into code that browsers can understand. While there are several approaches, the most common are:

**Create React App:** The traditional and beginner-friendly way to start React projects. It uses [Webpack](https://webpack.js.org/) and [Babel](https://babeljs.io/) behind the scenes but hides the configuration complexity.

**Vite:** A modern, faster build tool that provides near-instantaneous development server startup and hot module replacement. Vite is becoming increasingly popular due to its speed advantages.

**Next.js:** A full-stack React framework that includes built-in build tools, routing, and server-side rendering capabilities.

These tools handle:

- **Transpiling:** Converting JSX and modern JavaScript (ES6+) into browser-compatible code
- **Bundling:** Combining multiple files into optimized packages
- **Hot Reloading:** Automatically updating the browser when code changes
- **Asset Processing:** Handling images, CSS, and other static files

**Note:** Modern browsers now support most ES6+ features natively, but build tools are still essential for JSX processing and development workflow optimization.

**Build Tool Workflow Explained:**

**Development Phase:**

1. **Source Code:** You write JSX, modern JavaScript, and import statements
2. **Build Tool Processing:** Tool transforms your code in real-time
3. **Browser Output:** Transformed code runs in the browser
4. **Hot Reloading:** Changes automatically appear without manual refresh

**Production Phase:**

1. **Optimization:** Code is minified and bundled for performance
2. **Asset Processing:** Images, CSS, and other files are optimized
3. **Browser Compatibility:** Code is transformed to work in older browsers
4. **Deployment:** Optimized files are ready for web servers

**Tool Comparison:**

- **Create React App:** Beginner-friendly, hides complexity, slower builds
- **Vite:** Faster development server, modern approach, growing popularity
- **Next.js:** Full-stack framework, includes routing and server features

Modern JavaScript uses ES6 module syntax with the keywords `export` and `import`, which are now standard across all modern environments:

### `export`

While Node.js traditionally used CommonJS syntax with **module.exports** and **require()**, modern JavaScript (and React) uses ES6 modules with the `export` keyword.

**Examples of export syntax:**

```javascript
// Named export
export const myFunction = () => { /* ... */ };
export class Element { /* ... */ }

// Default export (one per file)
export default Element;

// Export multiple items
export { myFunction, Element };
```

**Comparison with CommonJS:**

```javascript
// Old CommonJS (still used in Node.js)
module.exports = Element;
const Element = require('./Element.js');

// Modern ES6 modules
export default Element;
import Element from './Element.js';
```

### `import`

The `import` keyword loads modules and works with the `from` keyword to specify the source.

**Import Examples:**

```javascript
// Default import
import Element from './Element.js';

// Named imports (destructuring)
import { someFunction, anotherFunction } from './utilities.js';

// Mixed import
import Element, { someFunction } from './Element.js';

// Import all as namespace
import * as Utils from './utilities.js';

// Import for side effects only
import './styles.css';
```

**Import/Export Pattern Decision Guide:**

**Default Export/Import:** Use when a module has one primary purpose or main component:

- React components typically use default exports
- Utilities with a single main function
- Configuration objects or constants

**Named Export/Import:** Use when a module provides multiple related functions:

- Utility libraries with multiple functions
- Constants that are used together
- When you want to import only specific parts

**Tree-Shaking Benefits:** Modern build tools can eliminate unused code when you use named imports. If you only import specific functions, unused functions won't be included in your final bundle, reducing file size.

**Best Practices:**

- Use named imports when importing specific functions or components
- Use default imports for the main export of a module
- Prefer named imports to reduce bundle size through tree-shaking
- Modern build tools automatically optimize imports for production
