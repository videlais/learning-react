# Reviewing JavaScript ES6

JavaScript has two main branches: ES5 and ES6 (and later). The letters used in "ES5" an "ES6" stands for the specification that defines the language: ECMAScript.

Before 1994, the European Computer Manufacturers Association kept track specifications for different programming languages, protocols, and other, related technologies across Europe. If something software related interacted with some other software, it was regulated in part through the European Computer Manufacturers Association and rules it helped develop with its partner companies and organizations.

Starting in 1994, the European Computer Manufacturers Association became simply the letters ECMA to reflect the global nature of its organization and partners. Around the same time, parts of JavaScript were appearing as early as 1995, but its first official version was in 1997. This was also when it became part of ECMA and under its combined governance.

While new functionality was added to JavaScript from time to time based on suggestions from companies and organizations, in 2015 it was decided to move JavaScript to its 6th edition. This version, technically called ECMAScript 2015, marked a clear dividing point between early JavaScript and modern conventions and syntax.

Starting in 2015, it was also decided that a new version of JavaScript would come out every year instead of in parts every few years. Since then, JavaScript ES10 (ECMAScript 2019), has come out and new functions and keywords have been added.

## Differences between ES5 and ES6

Within the JavaScript programming community, everything that has been added since 2015 is commonly called part of "JavaScript ES6". Anything from before that point is part of "JavaScript ES5".

Starting in 2016, all modern web browsers started supporting parts of JavaScript ES6 with more functionality added every year. At the same time, Node.js, since it is a single set of tools, often has support for cutting-edge parts of JavaScript in its newest versions before they come official for testing purposes.

### Creating Variables in ES6

In JavaScript ES5, the keyword `var` was used to define a variable. ES6 added two new ways to create variables: `let` and `const`.

#### `let`

The keyword `let` is used for values that will or could change during execution. It has a tighter sense of scope than `var` and is the preferred way to create most variables.

```javascript
let example = 5;
example = 6;
```

#### `const`

The keyword `const` is used for values that will not change after creation. These values are *constant*. Any attempt to change their values will result in a runtime error.

```javascript
const example = 5;
// The following code will result in an error!
example = 6;
```

### Arrow Functions

In JavaScript ES5, it was often common to use an anonymous function as part of different built-in functionality in JavaScript.

**Note:** An anonymous function is simply one without a name.

```javascript
let arrayExample = [1,2,3,4];

arrayExample.forEach(function(entry, index) {
  console.log("Value: " + entry + "  Position: " + index);
});
```

In JavaScript ES5, the use of the anonymous function would also create a new function scope and an internal use of the *this* keyword local to that function.

Because this can easily led to confusion about which *this* an anonymous function is trying to access -- its own or the one inside the function being used? -- a different type of function was introduced: an arrow function.

The term "arrow function" is named that way because it used the equal sign, `=`, and the greater-then sign, `>`, together to create an 'arrow': `=>`. The 'arrow' points toward the body of the function.

To solve the issue around confusing usages of *this*, arrow functions have a unique feature in all JavaScript: their *this* is defined when they are, not where they are used.

For example, consider the following code combining different functions:

```javascript
function returnLastEntry(arrayExample) {

    this.lastValue = null;

    arrayExample.forEach( (entry, value) => {
        this.lastValue = entry;
    });

    return this.lastValue;
}
```

In the above code, the use of the arrow function allows the code inside it to access the *this* of the function it is defined within, *returnLastEntry()*. While a silly example, it shows how an arrow function can access the *this* where it is defined.

#### Arrow Function Expressions

Arrow functions can use the normal syntax of functions like the following where at least one statement would be used inside the body of the function:

```javascript
() => {}
```

They can also be used as part of *arrow function expressions* in a more compact form where, instead of curly brackets, they can contain a single expression that is assumed to be returned.

```javascript
() => ()
```

In fact, in these cases, the initial parentheses can even be dropped when only one parameter will be passed to the function, which becomes the following:

```javascript
 => ()
```

For example, consider the following code:

```javascript
const animals = [
  'cat',
  'dog'
];

console.log(animals.map(animal => animal.length));
```

### Template Literals

Concatenating strings is a common activity in JavaScript. To help clean up code, ES6 added new functionality: template literals.

These are enclosed in backticks, `` ` ``. Any use of a variable starts with the dollar-sign, `$`, and then is enclosed in opening and closing curly brackets.

```javascript
let example = 5;
console.log(`This uses a template literal: ${example}`);
```

### `class` and `extends`

Starting with ES6, JavaScript is now a true object-oriented programming language. Previous to ES6, it was possible to replicate most object-oriented programming functionality, ES6 added two important keywords: `class` and `extends`.

TODO
