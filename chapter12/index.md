# Working with File Loader

- [Working with File Loader](#working-with-file-loader)
  - [WebPack and File Importing](#webpack-and-file-importing)
    - [Loading Images](#loading-images)
    - [Computed Values](#computed-values)
  - [Working with `public` Directory](#working-with-public-directory)
    - [Global `%PUBLIC_URL%`](#global-publicurl)
    - [Global *process.env.PUBLIC_URL*](#global-processenvpublicurl)
    - [Limit `public` Usage](#limit-public-usage)

## WebPack and File Importing

React uses WebPack to handle all file importing and inclusion. This goes for JavaScript and JSX files (using modules) as well as something that may not seem as obvious: working with multimedia files.

### Loading Images

If the filetype of a file is not `.css`, `.js` or some other, known type WebPack considers a *module*, it will not the file. Instead, it will load its relative URI within the project. (This will then be changed to the final, and correct, URL when the project is compiled.)

For images, this works well to import the file into a component. (*Components take care of themselves*) However, as the file is not treated as a module, its URL can be used to with `<img />` and other elements.

```javascript
import Image from './image.png'
```

**Note:** As with previous chapters on using CSS and creating a `components` folder. Any images that are used with a component should be in its own sub-directory.

```javascript
import Image from './image.png';

const example = () => {
    return (
        <div>
          <img src={Image} alt="Loading an image" />
        </div>
    );
}


default export example;
```

### Computed Values

As URIs are string values, they can also be computed as well. These can be created during run-time based on other, existing values such as run-time or environmental variables.

**Note:** In the following example, the variable *process.env.PUBLIC_URL* is assumed to be the base server path.

```javascript
import React from 'react';
import logo from './logo.svg';

function App() {
  return (
    <div>
      <img src={process.env.PUBLIC_URL + logo} alt="Example" />
    </div>
  );
}

export default App;
```

## Working with `public` Directory

Along with the `src` directory in React projects, there is also a `public` directory. While images and other files should, whenever possible, be stored in the subdirectory of the component to better organize complex project, the `public` also serves as a way to include files.

### Global `%PUBLIC_URL%`

React projects support a global value of `%PUBLIC_URL%` that will be replaced with the eventual placement of the `public` directory within the project. Any files stored in the `public` directory can always be reached through this global value.

```javascript
import React from 'react';

function App() {
  return (
    <div>
      <img src={%PUBLIC_URL% + "/logo192.png"} alt="Example" />
    </div>
  );
}

export default App;
```

### Global *process.env.PUBLIC_URL*

Within Node.js projects, there is a common pattern to use the *process.env* object to create values used through a project. In the case of React, there is a property *process.env.PUBLIC_URL* that will hold the eventual placement of the `public` directory in the compiled code.

Like with `%PUBLIC_URL%`, this can also be used to compute the path to a file.

```javascript
import React from 'react';

function App() {
  return (
    <div>
      <img src={process.env.PUBLIC_URL + "/logo192.png"} alt="Example" />
    </div>
  );
}

export default App;
```

### Limit `public` Usage

The `public` directory is often described as an "escape hatch" for React projects. Whenever possible, images and other resources *should* be kept with the other, related code in component directories. However, for certain cases like a large collection of files or loading of larger files, the globals `%PUBLIC_URL%` or *process.env.PUBLIC_URL* can be used.

As files in `public` are not part of the importing process, they are not optimized. WebPack does not process in the `public` directory. Nor does Babel. Anything kept in the `public` directory is considered "public" and something potentially shared or access by multiple components.
