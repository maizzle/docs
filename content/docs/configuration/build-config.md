---
title: "Build Config"
slug: "build-config"
description: "Configure build settings for Maizzle to use when processing and outputing your HTML email templates."
---

import Alert from '~/components/Alert.vue'

# Build Config

This is where you can customize the build settings for Maizzle to use.

Let's first take a look at all the options:

```js
// config.js
module.exports = {
  build: {
    assets: {
      source: './src/assets/images',
      destination: 'images',
    },
    destination: {
      path: 'build_local',
      extension: 'html',
    },
    layouts: {
      root: './',
    },
    includes: {
      root: './'
    },
    templates: {
      root: 'src/templates',
      extensions: 'html',
    },
    tailwind: {
      css: './src/assets/css/main.css',
      config: 'tailwind.config.js',
    },
    posthtml: {
      plugins: [],
      options: {},
    },
  },
  // ...
}
```

## assets

Source and destination directories for your asset files.

At build time, `assets.destination` will be created relative to `build.destination`, and everything inside `assets.source` will be copied into it:

```js
assets: {
  source: 'src/assets/images',
  destination: 'images',
},
```

You can use it to store _any_ global email assets, not just images.

## destination

This allows you to customize the output path and file extension.

### path

Directory path where Maizzle should output the compiled emails.

```js
destination: {
  path: 'build_local',
},
```

If you omit this key, a Jigsaw-inspired `build_${env}` directory name will be used.

### extension

Define the file extension (without the dot) to be used for all templates that are output. Useful if you need to pass the file to other frameworks or templating languages.

For example, let's output [Laravel Blade](https://laravel.com/docs/5.8/blade) files:

```js
destination: {
  extension: 'blade.php',
},
```

### permalink

You can override `destination.path` for each Template, with the help of the `permalink` <abbr title="Front Matter">FM</abbr> key:

```html
---
permalink: path/to/file.html
---

<extends src="src/layouts/base.html">
  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

You can use both relative and absolute file paths.

Output one level above project directory:

```html
---
permalink: ../newsletter.html
---
```

Output at a specific system location:

```html
---
permalink: C:/Users/Cosmin/Newsletter/2019/12/index.html
---
```

<alert type="warning"><code>permalink</code> must be a <em>file</em> path, and can be used only in the Template's Front Matter. Using a directory path will result in a build error.</alert>

## layouts

You can define the path where your Layouts are located:

```js
build: {
  layouts: {
    root: 'src/layouts',
  }
}
```

You could then extend Layouts by referencing them relative to that path - no need to write out the full path relative to your project root:

```html
<extends src="base.html">
  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

<alert>Maizzle doesn't include this <code>layouts</code> key in the Starter config.</alert>

<alert type="danger">If you're extending a file that also extends a file (i.e. when extending a Template), this will not work. Instead, don't define the <code>root</code> key and only use project root-relative paths (i.e. <code>&lt;extends src="/templates/template.html"&gt;</code>)</alert>

## templates

Define your Template's `source` directories and file extensions.

```js
build: {
  templates: {
    root: 'src/templates',
    extensions: 'html',
  },
}
```

#### root

Define the path(s) to your [Templates](/docs/templates/). This is where Maizzle looks for templates to compile. It's also used by `postcss-purgecss` when scanning for selectors.

It can be a string:

```js
build: {
  templates: {
    root: 'src/templates',
  },
}
```

Or an array of strings:

```js
build: {
  templates: {
    root: ['src/templates', '/path/to/more/templates'],
  },
}
```

<alert>Remember, Maizzle will copy these folders over to the <code>destination.path</code> directory, with <em>everything</em> inside them.</alert>

#### extensions

Define what file extensions you use for your Templates. 

`extensions` can be a string, but it can also be an array or a pipe|delimited list:

```js
build: {
  templates: {
    extensions: ['html', 'blade.php'], // can also do 'html|blade.php'
  },
}
```

Maizzle will only look for files ending in _these_ extensions, when searching your `build.templates.root` directory for Templates to build.

This means you can keep other files alongside your Templates, and Maizzle will simply copy them over to the build destination directory - it will not try to parse them.

<alert>If <code>build.templates.extensions</code> is missing, Maizzle will default to <code>html</code>.</alert>

## tailwind

Paths for Tailwind CSS.

```js
build : {
  tailwind: {
    css: 'src/assets/css/main.css',
    config: 'tailwind.config.js',
  },
},
```

### css

Paths to your [main CSS file](/docs/tailwindcss/#maincss), that will be compiled with Tailwind CSS.

### config

Path to the Tailwind CSS config file to use.

You can use this to specify a Tailwind config file for any build scenario.

For example, you might want to use a separate Tailwind config, where you:

- define fewer theming options (faster CSS compilation)
- disable `!important` (like in ⚡4email templates)
- use different Tailwind plugins

<alert type="warning">
  <div class="font-semibold mb-2 text-gray-800">No effect in Front Matter</div>
  <div>Since Tailwind CSS is compiled only once, <em>before</em> any Templates are built, using <code>build.tailwind.config</code> in Front Matter will have no effect.</div>
</alert>

## posthtml

You can pass plugins or options to the templating engine.

### plugins

Register any PostHTML plugins you would like to use:

```js
build: {
  posthtml: {
    plugins: [
      require('posthtml-spaceless')(),
    ]
  }
}
```

Maizzle already comes with the following plugins, no need to add them:

- [`posthtml-extend`](https://github.com/posthtml/posthtml-extend)
- [`posthtml-include`](https://github.com/posthtml/posthtml-include)
- [`posthtml-modules`](https://github.com/posthtml/posthtml-modules)
- [`posthtml-fetch`](https://github.com/posthtml/posthtml-fetch)
- [`posthtml-expressions`](https://github.com/posthtml/posthtml-expressions)

### options

Pass options to PostHTML.

For example, tell it to ignore `<?php ?>` tags:

```js
build: {
  posthtml: {
    options: {
      directives: [
        { name: '?php', start: '<', end: '>' },
      ],
    }
  }
}
```

## Build Errors

By default, when a build error occurs, Maizzle will throw an error.

You can configure how build errors are handled when developing with the CLI commands, by adding a `build.fail` key to your config:

```js
module.exports = {
  build : {
    fail: 'silent', // or 'verbose'
  },
}
```

- `silent` will just log the paths to the files it failed build, in the console
- `verbose` will additionally log the error stack trace

Omitting it or using any other value will throw an error (log stack trace and exit script).