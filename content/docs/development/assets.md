---
title: "Assets"
slug: "assets"
description: "Learn about asset files - like images - in Maizzle, and organize your email templates into folders"
---

# Asset Files

Any files that you add to the `src/templates` directory will be copied over to the root of the build destination directory.

This way, you can organize your email templates into folders.

## Global assets

Additionally, you can also define a global email assets folder that will be copied to the build directory.
The Starter sets it to the `src/assets/images` directory:

```js
// config.js
module.exports = {
  build: {
    templates: {
      // ...
      assets: {
        source: 'src/assets/images',
        destination: 'images'
      }
    }
  }
}
```

Everything inside `assets.source` will be copied to the `assets.destination` directory, which is relative to [`templates.destination.path`](https://maizzle.com/docs/build-config/#path).

## Multiple paths

You can also define multiple asset paths, by using an array for `source`:

```js
// config.js
module.exports = {
  build: {
    templates: {
      // ...
      assets: {
        source: ['src/assets/images', 'src/fonts'],
        destination: 'assets'
      }
    }
  }
}
```
