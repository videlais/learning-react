---
title: "Introducing Node.js"
order: 2
chapter_number: 2
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Explain** the purpose and role of Node.js in modern web development.
- **Install** Node.js and verify the installation on their operating system.
- **Distinguish** between key Node.js tools including `node`, `npm`, `package.json`, and `node_modules`.
- **Create** a basic Node.js project using npm commands and package management.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Node.js](#nodejs)
- [Downloading and Installing Node.js](#downloading-and-installing-nodejs)
- [Node.js Terms](#nodejs-terms)
- [Using Node.js](#using-nodejs)
  - [`node`](#node)
  - [`npm`](#npm)
  - [`package.json`](#packagejson)
  - [`node_modules`](#node_modules)
  - [`package-lock.json`](#package-lockjson)
  - [`npx`](#npx)
- [Working with Node Projects](#working-with-node-projects)
  - [Hello World Example](#hello-world-example)
    - [Preparing the Folder](#preparing-the-folder)
    - [Creating and Running a File](#creating-and-running-a-file)
- [Working with Modules](#working-with-modules)
  - [**require()**](#require)
  - [**module.exports**](#moduleexports)
- [Installing Packages](#installing-packages)
  - [Example: Installing and Using Chalk](#example-installing-and-using-chalk)
  - [Examining `package.json`](#examining-packagejson)

## Node.js

Previous to 2009, JavaScript was primarily used in web browsers. However, the introduction of Node.js allowed for writing JavaScript as a *server-side* scripting language, opening up the way to use JavaScript as a programming language both for *front-end* (in the web browser) and as a *back-end* (server technology).

Node.js follows a Long Term Support (LTS) release schedule. As of 2025, the current LTS versions are Node.js 18.x and 20.x, which provide stability and security updates for production applications.

## Downloading and Installing Node.js

Node.js can be freely [downloaded](https://nodejs.org/en/) for multiple operating systems. It's recommended to download the LTS (Long Term Support) version for stability.

It can be installed through following the prompts of the particular installer depending on the operating system and versions, 32- or 64-bit, matching the architecture of the local computer.

**Alternative Installation Methods:**

- **macOS:** Use Homebrew with `brew install node`
- **Windows:** Use Chocolatey with `choco install nodejs` or Windows Package Manager with `winget install OpenJS.NodeJS`
- **Linux:** Use your distribution's package manager or NodeSource repositories

## Node.js Terms

- **Module**: In Node.js, each `.js` file is its own module.

- **Package**: A collection of modules is a package. These can be accessed through a world-wide listing on the website `npmjs.org` or through the command `npm`.

- **Project Directory**: Each new project created using Node.js should be in its own directory. When the command `npm` is first used, it assumes the current working directory is the *project directory* and will install packages accordingly.

## Using Node.js

Once installed, Node.js adds three new commands.

### `node`

The command `node` runs a JavaScript file given a path to its location. It accepts both absolute and relative paths.

```bash
node fileToRun.js
```

### `npm`

The command `npm` allows for installing, updating, or removing of packages.

When the command `npm init` is used, the current working directory is assumed to be a project directory and a file is added: `package.json`.

The command `npm install X` installs the package `X` in the project directory and adds it to the `package.json` file with its current version. If the package has dependencies, other packages it depends on to work, these are also installed.

**Example:**

```bash
npm install chalk
```

The command `npm remove X` removes the package `X` from the project directory.

**Example:**

```bash
npm remove chalk
```

**Additional NPM Commands:**

- `npm audit` - Scans your project for security vulnerabilities
- `npm audit fix` - Automatically fixes security issues where possible
- `npm ci` - Clean install for production (faster than `npm install`)
- `npm outdated` - Shows which packages have newer versions available

### `package.json`

Each project directory has its own `package.json` file. This is created through the command `npm init` and contains information about the project and what, if any, packages it is using and their current version.

**`package.json` Example:**

```json
{
  "name": "example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "MIT"
}

```

### `node_modules`

Whenever the command `npm install` is used to install a package, it is added to the `node_modules` directory. The command `npm remove X` removes a package named `X` from the `node_modules` directory.

**Important:** The `node_modules` directory can be very large and should never be committed to version control. Always add it to your `.gitignore` file.

### `package-lock.json`

When `npm install` is run, npm automatically creates a `package-lock.json` file. This file locks the exact versions of all dependencies and their sub-dependencies, ensuring consistent installations across different environments.

- **Never delete** `package-lock.json` manually
- **Always commit** `package-lock.json` to version control
- Use `npm ci` instead of `npm install` in production environments for faster, reliable builds

### `npx`

The command `npx` runs an existing package from `npmjs.org` instead of installing it. Common processes, such as creating a new React project, are defined as packages that can be used with the `npx` command and additional arguments.

**Example:**

```bash
npx create-react-app example
```

## Working with Node Projects

The first step in creating a new Node.js project should always be creating a directory.

Next, a project can be initialized and files added to it.

### Hello World Example

#### Preparing the Folder

Open the command-line tool for the operating system (Windows Terminal, Terminal, or Shell/Terminal).

Type `mkdir example` and press Enter.

Change to the new directory: `cd example` and press Enter.

In the new directory, type `npm init` and press Enter. Answer the questions from the prompts or press Enter to accept the default values.

**Quick Alternative:** Use `npm init -y` to automatically accept all default values without prompts.

Once all questions have been answered, it will ask for confirmation. Type `y` for yes or `n` to restart the process.

When confirmed, a new file will be created: `package.json`.

**Best Practice:** Create a `.gitignore` file in your project directory with the following content:

```gitignore
node_modules/
npm-debug.log*
.npm
```

This prevents the large `node_modules` directory from being committed to version control.

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

While a few lines of code in one file can be useful, Node.js provides functionality for working across modules: **require()** and **module.exports**.

### **require()**

When working with multiple files or packages, the function **require()** can be used to "include" whatever another file exports. In other words, if certain values are stored in one file, they can be 'exported' out from their file and **require()**'d in another.

```javascript
// Using a local file
const example = require('./another.js');
```

The function **require()** accepts a path to a local JS file or the name of an installed package.

```javascript
// Using an installed package
const chalk = require('chalk');
```

**Best Practice:** Use `const` for values that won't be reassigned, and `let` for variables that will change. Avoid `var` in modern JavaScript.

### **module.exports**

The object **module.exports** can be used to 'export' any values from a file. The object **module** is a global variable available in all modules. It *exports* property is what the module is "exporting."

**another.js:**

```javascript
//Exporting a value
module.exports = 5;
```

**index.js:**

```javascript
// 'example' will be the value 5
const example = require('./another.js');
// Show the value of 'example' on the console
console.log(example);
```

**Note:** Values in JavaScript can be simple integers like `5`, but they can also be more complex structures like functions and objects. Many packages 'export' complex data structures via object literals and allow users to use different functionality from them.

**Modern Alternative:** Node.js also supports ES6 modules (import/export) when using `.mjs` files or setting `"type": "module"` in `package.json`:

```javascript
// Modern ES6 syntax (another.mjs)
export default 5;

// Modern ES6 syntax (index.mjs)
import example from './another.mjs';
console.log(example);
```

## Installing Packages

The command `npm` is used to install packages from the listing provided on `npmjs.org`.

### Example: Installing and Using Chalk

Create a new project directory using the command `mkdir chalkexample`.

Change into the new directory: `cd chalkexample`.

Initialize a new NPM project: `npm init`.

Accept all default values and type `y` and press Enter from the prompts.

Type the command `npm install chalk@4` and press Enter. (This will install version 4.x of the package [chalk](https://www.npmjs.com/package/chalk), which works with CommonJS).

**Note:** Chalk version 5 and later require ES6 modules. Using `chalk@4` ensures compatibility with the CommonJS `require()` syntax used in this example.

Create a new file named `index.js` and add the following code:

**index.js:**

```javascript
// Require the package 'chalk'
const chalk = require('chalk');

// Use the function red() from the object 'chalk'
//  inside a console.log() function.
//
// This will show text in red on the console.
console.log(chalk.red('Hi!'));
```

Save the file.

Run the new file using `node index.js` in the project directory. This will show the word `Hi!` in red.

**Note:** Some command-line colors are not supported. Certain older versions of PowerShell and Command on Windows will not show colors.

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

**Understanding Semantic Versioning:** The version number `^4.0.0` follows semantic versioning (semver):

- `4.0.0` - Major.Minor.Patch version
- `^4.0.0` - Caret allows compatible changes (4.x.x, but not 5.0.0)
- `~4.0.0` - Tilde allows patch updates only (4.0.x)
- `4.0.0` - Exact version (no automatic updates)

This system helps prevent breaking changes from automatically being installed.
