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

Starting with ES6, JavaScript is now a true object-oriented programming language. Previous to ES6, it was possible to replicate most object-oriented programming functionality. However, ES6 added two important keywords, `class` and `extends`, that enabled full OOP support.

#### `class`

The `class` keyword is used to create classes in JavaScript. It defines a set of properties and functions that exist within the class as enclosed within curly brackets.

```javascript
class Example {

}
```

When used with the `class` keyword, `extends` enables inheritance between classes. In object-oriented programming terms, this allows one object to 'extend' an existing one through gaining everything it has and adding more.

**index.js:**

```javascript
class Example {

}

class Another extends Example {

}
```

#### *constructor()*

Classes are created through the use of the `new` keyword. This creates an object based on the class. In this way, classes are often thought of as 'blueprints' for the object to be created.

**index.js:**

```javascript
// Define the class 'Example'
class Example {
}

// Create an object based on the class 'Example'
let another = new Example();
```

In object-oriented terms, this process *constructs* a new object based on the class. In fact, JavaScript, like many other object-oriented programming languages, supplies a special function named *constructor()* for that purpose.

When *constructor()* function is added inside a class, it is the first function called when an object is being "constructed." (If not supplied, JavaScript will automatically generate one.)

**index.js:**

```javascript
// Define the class 'Example'
class Example {
  // Create a constructor()
  constructor() {
    // Anything inside the constructor()
    //  will be called during the
    //  "construction" process.
    console.log("This will be called!");
  }
}

// Create an object based on the class
//  'Example'
let another = new Example();
```

The *constructor()* also serves an additional purpose: it allows a class to accept values during the "construction" process. Because it is a function, the *constructor()* function can also accept values.

```javascript
// Define the class 'Example'
class Example {
  // Because constructor() is a function,
  //  it can also accept values.
  constructor(value) {
    // This will show '5'
    console.log(value);
  }
}

// Create an object based on the class
//  'Example' and pass it a value, 5.
let another = new Example(5);
```

##### Class Scope

Within a class defined in JavaScript, the `this` refers to the class itself. Like with function scope, this allows a class to have properties and functions that can be accessed within itself and externally through referencing an object based on the class.

**index.js:**

```javascript
// Define the class 'Example'
class Example {
  constructor() {
    this.someValue = 5;
  }
}

// Create an object based on the class 'Example'
let another = new Example();

// This will output '5'
console.log(another.someValue);

```

#### *super()*

The use of the keyword `extends` allows one class to "extend" another. However, what if there was a need to pass a values from one *constructor()* to another? JavaScript provides the function *super()* to do that.

In OOP terms, these classes exist a parent-child relationship. The first, original class is the 'parent' and the class that is extending it is the 'child'. Using the function *super()* calls the parent's *constructor()* and pass values to it.

**index.js:**

```javascript
// Define the class 'Example'
class Example {
  // Define a constructor()
  constructor(value) {
    // Print the value to the console
    console.log(value);
  }
}

// Define the class 'Another'
//  based on the class 'Example'
class Another extends Example {
  // Define a constructor()
  constructor(value) {
    // The parent's constructor()
    super(value);
  }
}

// Create an object based on 'Another'
//  (which is based on 'Example')
//
// This will print the value 5
//  because it is being passed
//  from one constructor() to another.
let another = new Another(5);
```

### Promises

In JavaScript, using a *callback* functions has a long history. Using an anonymous function to process entries in an array, for example, or signal that an event has occurred shows up frequently in code. However, having one callback function call another that calls another can become hard to debug.

To address this issue, ES6 also added Promises.

A promise is a future outcome. A person may promise to love someone forever or that they will not do something. It is something that occurs in the future.

In JavaScript, a Promise is a special type of functionality that will either resolve or reject in the future. When that happens, either one callback function or another will be called.

Some functionality in JavaScript are built on Promises, but one can be created (like any object) through using the `new` keyword.

```javascript
let example = new Promise();
```

The **Promise** object accepts a callback function with two arguments: *resolve()* and *reject()*. The *resolve()* function "resolves" the promise and fulfils it. The *reject()* function rejects the promise.

#### *then()*

A promise can only be resolved or rejected through a special function named *then()*. The *then()* function also accepts two function parameters. If the promise was fulfilled, it calls the first function. If the promise was rejected, it calls the second.

```javascript
// Create a new object based on 'Promise'
let testing = new Promise(
  // Pass a function to 'Promise'
  //
  // The first argument to the function
  //  is resolve() and the second, reject()
  (resolve, reject) => {
    let someValue = 5;
    // Resolve the promise and return a value
    resolve(someValue);
});

// Using then(), determine what happened.
//
// If the promise resolved, the first function
//  will be called. If it was rejected, the
//  second function will be called.
testing.then(
  // First, resolving function
  (value) => {
    console.log(value);
  },
  // Second, rejecting function
  (value) => {
    console.log(value);
  }
);
```

Promises allow for *asynchronous* functions. Because promises ar either fulfilled or reject in the future, the callback functions as part of its *then()* can be called at a different time than the code around it. This allows for things like waiting to connect to a server or processing large amounts of data. Once the promise finishes, it will either resolve or reject, and then the parameters to the *then()* function will be called.

### `async` and `await`

Promises allow for *asynchronous* functions. However, they can also create problems when code is waiting for a promise to either fulfill or reject before continuing. To prevent a problem of more callback functions call others that call others, ES6 also added two new keywords that work specifically with promises: `async` and `await`.

The `await` keyword, as its name might imply, "waits" for a promise to finish. It also simply takes whatever would have been given to the *then()* functions, whatever is passed to the internal *resolve()* or *reject()* functions, and returns it.

However, to signal that some code should "wait," the keyword `async` must always be used in front of the function in which the `await` keyword is used. This marks the function as *asynchronous* and tells JavaScript that it will finish at some future point and it should not wait for it.

TODO
