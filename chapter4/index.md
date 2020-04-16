# Thinking in HTML, Writing in JS

- [Thinking in HTML, Writing in JS](#thinking-in-html-writing-in-js)
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
  - [Introducing WebPack + Babel](#introducing-webpack--babel)
    - [`export`](#export)
    - [`import`](#import)

## Reviewing HTML

HyperText Markup Language (HTML) uses *elements* to define the layout of a document. These are composed of *tags* that mark the start and end of the element and any content held within it.

Each *opening tag* of an element starts with a less-than sign, `<`, the name of the element, and then a greater-than sign, `>`.

```html
<html>
```

The *closing tag* of an element looks similar to the opening tag, but includes the backslash, `/`, before the end of the ending tag.

```html
</html>
```

**Element Example:**

```html
<html></html>
```

HTML is a *markup* language because it "marks up" a document to define how its content should be structured. HTML is often considered the "bones" of a document because it "holds" the content and defines its internal shape.

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

In the above example, the elements `<head>` and `<body>` are *children* of the parent element, `<html>`.

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

- `<h1>, <h2>, <h3>, <h4>, <h5>`, the headers, for defining different text headings at different levels (1 - 5, in decreasing size).

- `<div>`, the division element, for "dividing up" a page into different sections.

- `<em>`, the emphasis element, for giving text *emphasis*.

- `<strong>`, the strong emphasis element, for giving text **strong emphasis**.

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
<div>
    <article>
      <p>Main Content</p>
    </article>
</div>
```

## Introducing JSX

React was created at Facebook. Its developers came up with the component model through also introducing a new technology to help with using it: JSX.

[JavaScript XML (JSX)](https://facebook.github.io/jsx/) solves a common problem in web development. Often, when working with a project, there will be HTML, JavaScript, and CSS files. Depending on the project, these could grow to dozens of files.

To help solve this problem, they proposed something radical. *Why not put HTML inside the JavaScript code?* In order to process this type of content, they introduced JavaScript XML, which allows for placing HTML-like elements (XML) inside JavaScript where it can be treated like any other value.

However, to do this, they tied it to the existing rules around the Extensible Markup Language (XML).

### JSX Rules

#### One Root Element

HTML already has a root element named `<html>`. However, some sections of a web page may not, themselves, have a root element. JSX, based on XML, demands that any component (collection of elements) *always* have a root element for that component.

#### All Elements Must Close

Most HTML elements have a closing tag. However, some do not. For these, they must "self-close" (include an ending backslash, `/`).

Based on XML rules, any element used must close before anything can start.

#### JSX Expressions

Using the values of variables is a core part of any programming language and the developers at Facebook knew this. They added *JSX expressions* into the language.

An expression in JavaScript is anything that has a value. This includes, of course, variables, which have values, but also functions, which are a type of value.

However, what is *not* an expression are existing keywords and loop structures in JavaScript. The keywords `if`, `else`, `for`, and `while` are *not* expressions and cannot be used within JSX.

All other expressions, variables and functions, can be used through opening and closing curly brackets within the XML.

```javascript
let exampleValue = 5;

let exampleJSX = <div>{exampleValue}</div>;
```

While it can look odd, JSX allows for using HTML (XML) directly inside JavaScript. This also allows components to be collections of elements, too. They can contain JSX that defines the structure of its contents.

## Introducing WebPack + Babel

Along with JSX, React uses another tool: [WebPack](https://webpack.js.org/). Defined as it name might imply, the tool WebPack "packs files for the web." In other words, it takes files used in Node.js and helps make them run easier in browser contexts.

It does this through compacting files together and making them easier for web browsers to load in different chunks instead of in separate files. It also organizes project output into a form that web browsers can understand and adds some loading functionality that can optimize the file-loading process.

React code is written using JavaScript ES6. In order to let the same code run in web browsers, most of which support only parts of JavaScript ES6, React also uses another tool, [Babel](https://babeljs.io/).

In order to move between versions of a language, a process called *transpiling* is used. Babel helps with this and works along with WebPack to not only make a more optimized build for web browsers, but also transpile the code from the JavaScript ES6 used in Node.js into a version of JavaScript ES5 used in all major browsers.

As part of this integration, React -- via WebPack + Babel -- adds two new keywords to JavaScript ES6 usage in Node.js: `export` and `import`.

### `export`

Normally, to "export" a value from one module to another in Node.js, the keyword (and global) **module** is used with its property *exports*. Anything assigned to the property is "exported" from the file and can be *require()*'d in another.

Babel improves this through using the keyword `export`. Instead of using *module.exports*, the keyword `export` takes it place.

For example, the following Node.js code "exports" a class named **Element**.

```javascript
module.exports = Element;
```

Translated into using the `export` keyword, this would become the following:

```javascript
export Element;
```

In fact, to help with the common usage of only exporting a single value, the keyword `default` can be paired with `export` to become the following:

```javascript
export default Element;
```

Internally, Babel (and then WebPack) will translate this through combining all of the files together into code a web browser will understand.

### `import`

If the keyword `export` replaces *module.exports*, the keyword `import` replaces *require()*. Instead of using a function, the keyword `import` is used.

Previously, the argument to the function *reuqire()* was also a path to a file or a package to load. With `import` this combines with the new keyword `from`.

For example, the following code loads the class **Element** from a file.

```javascript
let Element = require('./Element.js');
```

Using the keywords `import` and `from`, this becomes the following code:

```javascript
import Element from './Element.js';
```

Like with *require()*, the keyword `import` also understands destructing assignment from JavaScript ES6.

```javascript
import { someProperty } from './exampleFile.js'
```

In fact, to reduce larger builds for web browsers, React developers are highly encouraged to use the destructing assignment option whenever possible to limit what is combined in the final output of the project. The less overall, the easier and faster a web browser can load it!
