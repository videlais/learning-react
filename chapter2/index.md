# Introducing Node.js

- [Introducing Node.js](#introducing-nodejs)
  - [Node.js](#nodejs)
  - [Downloading and Installing Node.js](#downloading-and-installing-nodejs)
  - [Node.js Terms](#nodejs-terms)
  - [Using Node.js](#using-nodejs)
    - [`node`](#node)
    - [`npm`](#npm)
    - [`package.json`](#packagejson)
    - [`node_modules`](#nodemodules)
    - [`npx`](#npx)
  - [Working with Node Projects](#working-with-node-projects)
    - [Hello World Example](#hello-world-example)
      - [Preparing the Folder](#preparing-the-folder)
      - [Creating and Running a File](#creating-and-running-a-file)
  - [Working with Modules](#working-with-modules)
    - [*require()*](#require)
    - [**module.exports**](#moduleexports)
  - [Installing Packages](#installing-packages)
    - [Example: Installing and Using Chalk](#example-installing-and-using-chalk)
    - [Examining `package.json`](#examining-packagejson)
  - [Exercises](#exercises)

## Node.js

Previous to 2009, JavaScript was primarily used in web browsers. However, the introduction of Node.js allowed for writing JavaScript as a *server-side* scripting language, opening up the way to use JavaScript as a programming language both for *front-end* (in the web browser) and as a *back-end* (server technology).

## Downloading and Installing Node.js

Node.js can be freely [downloaded](https://nodejs.org/en/) for multiple operating systems.

It can be installed through following the prompts of the particular installer depending on the operating system and versions, 32- or 64-bit, matching the architecture of the local computer.

## Node.js Terms

* **Module**: In Node.js, each `.js` file is its own module.

* **Package**: A collection of modules is a package. These can be accessed through a world-wide listing on the website `npmjs.org` or through the command `npm`.

* **Project Directory**: Each new project created using Node.js should be in its own directory. When the command `npm` is first used, it assumes the current working directory is the *project directory* and will install packages accordingly.

## Using Node.js

Once installed, Node.js added three new commands.

### `node`

The command `node` runs a JavaScript file given a path to its location. It accepts both absolute and relative paths.

### `npm`

The command `npm` allows for installing, updating, or removing of packages.

When the command `npm init` is used, the current working directory is assumed to be a project directory and a file is added: `package.json`.

The command `npm install X` installs the package `X` in the project directory and adds it to the `package.json` file with its current version. If the package has dependencies, other packages it depends on to work, these are also installed.

The command `npm remove X` removes the package `X` from the project directory.

### `package.json`

Each project directory has its own `package.json` file. This is created through the command `npm init` and contains information about the project and what, if any, packages it is using and their current version.

### `node_modules`

Whenever the command `npm install` is used to install a package, it is added to the `node_modules` directory. The command `npm remove X` removes a package named `X` from the `node_modules` directory.

### `npx`

The command `npx` runs an existing package from `npmjs.org` instead of installing it. Common processes, such as creating a new React project, are defined as packages that can be used with the `npx` command and additional arguments.

## Working with Node Projects

The first step in creating a new Node.js project should always be creating a directory. Next, a project can be initialized and files added to it.

### Hello World Example

#### Preparing the Folder

Open the command-line tool for the operating system (PowerShell, Terminal, or Shell/Terminal).

Type `mkidir example` and press Enter.

Change to the new directory: type `cd example` and press Enter.

In the new directory, type `npm init` and press Enter. Answer the questions from the prompts or press Enter to accept the default values.

Once all questions have been answered, it will ask for confirmation. Type `y` for yes or `n` to restart the process.

When confirmed, a new file will be created: `package.json`.

The `package.json` file will look something like the following (assuming all default values were accepted):

```json
{
  "name": "example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

#### Creating and Running a File

Create a new name named `index.js`.

Add the following code to the `index.js` file:

```javascript
console.log("Hi, there!");
```

Save the file.

In the project directory, type the command `node index.js` and press Enter.

When run, the file `index.js` will produce the line "Hi, there!".

## Working with Modules

While a few lines of code in one file can be useful, Node.js provides functionality for working across modules: *require()* and **module.exports**.

### *require()*

When working with multiple files or packages, the function *require()* can be used to "include" whatever another file exports. In other words, if certain values are stored in one file, they can be 'exported' out from their file and *require()*'d in another.

```javascript
// Using a local file
var example = require('./another.js');
```

The function *require()* accepts a path to a local JS file or the name of an installed package.

```javascript
// Using an installed package
var chalk = require('chalk');
```

### **module.exports**

The property **module.exports** can be used to 'export' any values from a file. The object **module** is a global variable available in all modules. Its *exports* property is what the module is "exporting."

**another.js:**

```javascript
//Exporting a value
module.exports = 5;
```

**index.js:**

```javascript
// 'example' will be the value 5
var example = require('./another.js');
// Show the value of 'example' on the console
console.log(example);
```

**Note:** Values in JavaScript can be simple integers like `5`, but they can also be more complex structures like functions and objects. Many packages 'export' complex structures and allow users to use different functionality.

## Installing Packages

The command `npm` is used to install packages from the listing provided on `npmjs.org`.

### Example: Installing and Using Chalk

Create a new project directory using the command `mkdir chalkexample`.

Change into the new directory: `cd chalkexample`.

Initialize a new NPM project: `npm init`.

Accept all default values and type `y` and press Enter from the prompts.

Type the command `npm install chalk` and press Enter. (This will install the package [chalk](https://www.npmjs.com/package/chalk)).

Create a new file named `index.js` and add the following code:

**index.js:**

```javascript
// Require the package 'chalk'
var chalk = require('chalk');

// Use the function red() from the object 'chalk'
//  inside a console.log() function.
//
// This will show text in red on the console.
console.log(chalk.red('Hi!'));
```

Save the file.

Run the new file using `node index.js` in the project directory. This will show the word `Hi!` in red.

**Note:** Some command-line programs are not supported. Certain older versions of PowerShell and Command on Windows will not show colors.

### Examining `package.json`

Whenever a new package is installed, it will be added to the `node_modules` folder in the project directory. It will also be added under the *dependencies* in the `package.json` file with the version installed.

```json
{
  "name": "chalkexample",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "chalk": "^4.0.0"
  }
}
```

The `package.json` file tracks all of the packages used and information about the project including its *name*, *version*, and other details like *author* and *license*.

## Exercises

1) What are the three commands Node.js adds?

1) What is a package?

1) What files does `npm` create?

1) What is the name of the function used to "include" the contents exported from another module in Node.js?

1) What property is used to "export" values from a module in Node.js?
