# Working with Multimedia Files

- [Working with Multimedia Files](#working-with-multimedia-files)
  - [WebPack + Multimedia File Importing](#webpack--multimedia-file-importing)
    - [Loading Images](#loading-images)
    - [Computed Values](#computed-values)
  - [Importing Video](#importing-video)

## WebPack + Multimedia File Importing

React uses WebPack to handle all file importing and inclusion. This goes for JavaScript and JSX files (using modules) as well as something that may not seem as obvious: working with multimedia files.

### Loading Images

WebPack understands the following image filetypes:

- `bmp`
- `gif`
- `jpg`
- `jpeg`
- `png`

If it encounters them, it will treat the file as its *URI* instead of attempting to load it.

```javascript
import Image from './image.png'
```

In these cases, the imported object again becomes its URI value. This can then be used as part of writing `<img />` or other JSX elements that would load the image based on its URI.

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

**Note:** In the following example, the variable *process.env.path* is assumed to be the base server path.

```javascript
import Image from './image.png';

const example = () => {
    return (
        <div>
          <img src={process.env.path + Image} alt="Loading an image" />
        </div>
    );
}


default export example;
```

## Importing Video

Like with images, video files can also be loaded.

TODO
