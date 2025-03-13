---
title: "PostCSS configuration"
description: "Configuring PostCSS in Maizzle."
---

# PostCSS configuration

You may use custom PostCSS plugins in Maizzle.

## Plugins

Here's how you can add PostCSS plugins - we'll use [Autoprefixer](https://github.com/postcss/autoprefixer).

First, install the plugin:

```sh
npm install autoprefixer
```

Then register it in `config.js`:

```js [config.js]
import autoprefixer from 'autoprefixer'

export default {
  build: {
    postcss: {
      plugins: [
        autoprefixer,
      ]
    }
  }
}
```

Any plugins that you register in the `plugins` array will be added at the end of the PostCSS plugins stack, which means they'll run _after_ Tailwind CSS.

## Options

You may also configure PostCSS options:

```js [config.js]
export default {
  build: {
    postcss: {
      options: {
        map: true,
      }
    }
  }
}
```
