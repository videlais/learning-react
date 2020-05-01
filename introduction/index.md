# Introduction

## Book Conventions

This book uses the following conventions to define different things and give them emphasis.

Variables and properties of objects are expressed with *emphasis*. HTML element names, classes, or other objects will appear with **strong emphasis**.

Selections of code appear in-line `like this` or, when written across multiple lines like the following:

```html
multiple
line of code
```

## Assumptions

This book makes some assumptions about what you know. It assumes you have seen some HTML and CSS before. It also assumes you have a passing knowledge of using JavaScript. I will attempt to explain many of the fundamental concepts and how they build into learning React, but it really does help if you have see many of these web technologies before and can read and write them.

### HTML

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

### CSS

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

### JavaScript

JavaScript is the *lingua fracna* of the internet. If HTML are the bones and CSS is the skin, JavaScript is the muscle. Any interaction or event that occurs in a webpage is filtered through JavaScript.

In a web browser, it is run and can change the current page through the Document-Object Model (DOM). This defines all of the elements of a page into objects that can be interacted with through a global **document** variable.

It uses `.js` files.

**Example:**

```javascript
let example = document.querySelector('#idExample');
```

## Chapters

Each chapter addresses a different topic and ends with a section called 'Exercises'. These include different questions about the information covered in each chapter.
