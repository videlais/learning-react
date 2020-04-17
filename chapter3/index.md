# Reviewing JavaScript ES6

- [Reviewing JavaScript ES6](#reviewing-javascript-es6)
  - [Introducing ES6](#introducing-es6)
  - [Differences between ES5 and ES6](#differences-between-es5-and-es6)
    - [Creating Variables in ES6](#creating-variables-in-es6)
      - [`let`](#let)
      - [`const`](#const)
    - [Arrow Functions](#arrow-functions)
      - [Arrow Function Expressions](#arrow-function-expressions)
    - [Template Literals](#template-literals)
    - [`class` and `extends`](#class-and-extends)
      - [`class`](#class)
      - [*constructor()*](#constructor)
        - [Class Scope](#class-scope)
      - [*super()*](#super)
    - [Promises](#promises)
      - [*then()*](#then)
    - [`async` and `await`](#async-and-await)
    - [Destructing Assignment](#destructing-assignment)
    - [Spread Operator](#spread-operator)
    - [Default Parameters](#default-parameters)
    - [Public Class Fields](#public-class-fields)
    - [Static Class Properties and Functions](#static-class-properties-and-functions)

## Introducing ES6

JavaScript has two main branches: ES5 and ES6 (and later). The letters used in "ES5" and "ES6" stand for the specification that defines the language: ECMAScript.

Before 1994, the European Computer Manufacturers Association introduced and maintained specifications for programming languages, protocols, and other, related technologies across Europe. If something software related interacted with some other software, it was regulated in part through the European Computer Manufacturers Association and rules it helped develop with its partner companies and organizations.

Starting in 1994, the European Computer Manufacturers Association became simply the letters ECMA to better reflect the global nature of its organization and partners. Around the same time, parts of JavaScript were appearing as early as 1995, but its first official version was in 1997. This was also when it became part of ECMA and under its combined governance.

While new functionality was added to JavaScript from time to time based on suggestions from companies and organizations, in 2015 it was decided to move JavaScript to its 6th edition. This version, technically called ECMAScript 2015, marked a clear dividing point between early JavaScript and modern conventions and syntax. Because of its sixth edition, it became known as JavaScript ES6 (JavaScript - ECMAScript, 6th Edition).

Starting in 2015, it was also decided that a new version of JavaScript would come out every year instead of in parts every few years. Since then, JavaScript ES10 (ECMAScript 2019), has come out and new functions and keywords have been added.

## Differences between ES5 and ES6

Within the JavaScript programming community, everything that has been added since 2015 is commonly called part of "JavaScript ES6." Anything from before that point is part of "JavaScript ES5."

Starting in 2016, all modern web browsers started supporting parts of JavaScript ES6 with more functionality added every year. At the same time, Node.js, since it is a single set of tools, often has support for cutting-edge parts of JavaScript in its newest versions before they become official for testing purposes.

### Creating Variables in ES6

In JavaScript ES5, the keyword `var` was used to define a variable. ES6 added two new ways to create variables: `let` and `const`.

#### `let`

The keyword `let` is used for values that will or could change during execution. It also has a tighter sense of scope than `var` and is the preferred way to create most variables.

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

Because this can easily create confusion about which *this* an anonymous function is trying to access -- its own or the one inside the function being used? -- a different type of function was introduced: an arrow function.

The term "arrow function" is named that way because it uses the equal sign, `=`, and the greater-then sign, `>`, together to create an 'arrow': `=>`. The 'arrow' points toward the body of the function.

To solve the issue around confusing usages of *this*, arrow functions have a unique feature of functions: their *this* is defined when they are, not where they are used.

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

They can also be used as part of *arrow function expressions* in a more compact form where, instead of curly brackets, they can contain a single expression that is assumed to be run and returned.

```javascript
() => ()
```

In fact, in these cases, the initial parentheses can even be dropped when only one parameter will be passed to the function, which becomes the following:

```javascript
variable => ()
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

In JavaScript ES5, adding the value of a variable to a complex string required multiple concatenation actions.

```javascript
let example = 5;
console.log('This uses a template literal: ' + example + '!');
```

In JavaScript ES6, template literals are enclosed in backticks, `` ` ``. Any use of a variable starts with the dollar-sign, `$`, and then is enclosed in opening and closing curly brackets.

```javascript
let example = 5;
console.log(`This uses a template literal: ${example}!`);
```

No concatenation is needed. The template literal handles all of the work to combine the value of the variable with the string around it.

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

When the *constructor()* function is added inside a class, it is the first function called when an object is being "constructed." (If not supplied, JavaScript will automatically generate one.)

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

The use of the keyword `extends` allows one class to "extend" another. However, what if there was a need to pass values from one *constructor()* to another? JavaScript provides the function *super()* to do that.

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

The keyword `super` also allows a child class to call a parent's *functions* as well. Because it has access to the parent's functions, it can call a function as if it was the parent through the keyword `super`.

```javascript
// Define the class 'Example'
class Example {
  // Define a function
  parentExample() {
    console.log("I'm the parent!");
  }

}

// Define the class 'Another'
//  based on the class 'Example'
class Another extends Example {
  // Define a constructor()
  constructor() {
    // Call the parent's constructor()
    super();
    // Call a function as if the parent
    super.parentExample();
  }
}

// Create an object based on 'Another'
//  (which is based on 'Example')
let another = new Another();
```

The one rule with using the keyword `super` in this way is that the parent's constructor must be called via *super()* before any other usages in the class. It has to be created before its functions can be called!

### Promises

In JavaScript, using a *callback* functions has a long history. Using an anonymous function to process entries in an array, for example, or signal that an event has occurred, shows up frequently in code. However, having one callback function call another that calls another can become hard to debug.

To address this issue, ES6 also added Promises.

A promise is a future outcome. A person may promise to love someone forever or that they will not do something. It is something that occurs in the future.

In JavaScript, a Promise is a special type of functionality that will either resolve or reject in the future. When that happens, either one callback function or another will be called.

Some functionality in JavaScript are built on Promises, but one can be created (like any object) through using the `new` keyword.

```javascript
let example = new Promise();
```

The **Promise** object accepts a callback function with two arguments: *resolve()* and *reject()*. The *resolve()* function "resolves" the promise and fulfils it. The *reject()* function rejects the promise.

```javascript
let example = new Promise((resolve, reject) => {
  // To resolve, call resolve().
  // To reject, call reject().
});
```

#### *then()*

The result of a promise, resolved or rejected, can be processed through a special function named *then()*. It also accepts two function parameters. If the promise was fulfilled, it calls the first function. If the promise was rejected, it calls the second.

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

Promises allow for *asynchronous* functions. However, they can also create problems when code is waiting for a promise to either fulfill or reject before continuing. To prevent a problem of more callback functions calling others that call others, ES6 also added two new keywords that work specifically with promises: `async` and `await`.

The `await` keyword, as its name might imply, "waits" for a promise to finish. It also simply takes whatever would have been given to the *then()* functions, whatever is passed to the internal *resolve()* or *reject()* functions, and returns it.

However, to signal that some code should "wait," the keyword `async` must always be used in front of the function in which the `await` keyword is used. This marks the function as *asynchronous* and tells JavaScript that it will finish at some future point and it should not wait for it.

```javascript
async function AsyncExample() {

  let testing = new Promise((resolve, reject) => {
    let someValue = 5;
    resolve(someValue);
  });

  let promiseResult = await testing;

  console.log(promiseResult);

}

AsyncExample();
```

### Destructing Assignment

Often, only a few properties or the first couple of positions of an array are needed. To help with those use cases, JavaScript ES6 also adds "destructing" functionality as part of assignment operations.

In JavaScript ES5, it was possible to assign the value of an object's property in the following manner:

```javascript
let exampleObject = {
  someProperty: 5
};


let someValue = exampleObject.someProperty;
```

However, where this same operation occurs most frequently is not in the above use case, but a more common one in Node.js: *require()*-ing in an object exported by another file.

**Example.js:**

```javascript
module.exports = {
  oneProperty: 1,
  twoProperty: 2,
  threeProperty: 3
};
```

**index.js:**

```javascript
let example = require('./Example.js');

let someValue = example.oneProperty;
```

Instead of pulling in additional properties not needed or used in the file, the assignment operation can be paired with the ability to 'destruct' an object.

**Example.js:**

```javascript
module.exports = {
  oneProperty: 1,
  twoProperty: 2,
  threeProperty: 3
};
```

**index.js:**

```javascript
let { oneProperty } = require('./Example.js');

let someValue = oneProperty;
```

In the above example, only the property *oneProperty* was *require()*'d into the file. Everything else was ignored. What was previously the object **example** became an object through which the property *oneProperty* was destructed.

This functionality also works on arrays, too. Like with objects where the use of the curly brackets marks the object, it is also possible to use square brackets and give names matching the position of values within an array to destruct it.

```javascript
// Destructure an array
let [one, two] = [5, 6, 7];
// Print the value 6
console.log(two);
```

### Spread Operator

In JavaScript ES5, either a `for()` loop or some other functionality was needed to use the entire contents of an array. In ES6, an additional operator, `...`, the spread operator, was added to for these cases. Used in front of an array, it "spreads" its contents.

```javascript
// Create an array
let arrayExample = [1,2,3,4,5,6,7,8,9,10];
// Use the spread operator to print
//  the entire contents.
console.log(...arrayExample);
```

### Default Parameters

In JavaScript ES5, it was often necessary to define the "default" values within functions and then test what was passed to it. (When not given a value, JavaScript defaults a parameter to the value of `undefined`.)

```javascript
function example(someValue) {
  
  // Set default value
  this.someProperty = "Default";
  
  // Test if 'someValue' has a value or not
  if(typeof someValue !== 'undefined') {
    // Overwrite value
    this.someProperty = someValue;
  }

  // Output value
  console.log(this.someProperty);
  
}

// Will output "Default"
console.log(example());

// Will output "Not default"
console.log(example("Not default"));

```

With multiple parameters, this approach requires lots of extra lines of code! To fix this common pattern, JavaScript ES6 added *default parameters*. These can be written inside of the parentheses of the function's definition.

```javascript
function example(someValue = "Default") {
  // Output value
  console.log(someValue);
  
}

// Will output "Default"
console.log(example());

// Will output "Not default"
console.log(example("Not default"));
```

### Public Class Fields

In JavaScript ES6, it is possible to create a *public class field*. What this means is that a field (a property or function) is created outside of the *constructor()* function within a class. It is *public* because it acts like a property of the class and is a *field* because it is defined outside of using the `this` keyword.

While still an experimental feature, it can be used inside of React because Babel understands and can transpile the code for other programs like browsers to understand.

```javascript
class Element {
  arrowFunctionExample = () => {
  }
}
```

In the above code, the value of the property *arrowFunctionExample* is an arrow function. Using *public class fields* allows allows the value of a function to use the special context of arrow functions: their `this` is defined where they are, not where they are used.

### Static Class Properties and Functions

Some JavaScript environments support using the experimental keyword `static`. It can be paired with both functions and public fields in a class.

When used with a function, it changes a function from being part of an instance (object) to being part of the *class* itself. It creates a single copy across *all* instances of the class, making it "static" to the class. It also, because it is static, comes with some extra rules.

A static function:

- Can be called using the name of the class
- Can only call or reference other static functions and values
- Cannot use `this`
- Cannot use *super()*

As the function is static to the class and not any particular instance, it does not have access to `this` or *super()*. However, it can be called using the name of the class outside of any instance of the class.

```javascript
class ExampleClass {
  static staticFunction() {
    return 'Static method has been called';
  }
}

console.log( ExampleClass.staticFunction() );
```

A static public field in a class works the same as a static function: it can be used with the name of the class outside of any instance. It is "static" to the class, after all.

```javascript
class ExampleClass {

  static publicFieldExample = 5;

}

console.log( ExampleClass.publicFieldExample );

```
