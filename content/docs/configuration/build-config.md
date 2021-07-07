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
    browsersync: {
      directory: true,
      notify: false,
      open: false,
      port: 3000,
      tunnel: false,
      ui: {port: 3001},
      watch: [],
    },
    components: {
      root: './',
    },
    layouts: {
      root: './',
    },
    templates: {
      filetypes: 'html',
      source: 'src/templates',
      destination: {
        path: 'build_local',
        extension: 'html',
      },
      assets: {
        source: 'src/images',
        destination: 'images',
      },
    },
    tailwind: {
      css: 'src/css/tailwind.css',
      config: 'tailwind.config.js',
    },
    posthtml: {
      plugins: [],
      options: {},
    },
    postcss: {
      plugins: [],
    },
  },
  // ...
}
```

## browsersync

When running the `maizzle serve` command, Maizzle uses [Browsersync](https://browsersync.io/) to start a local server and open a directory listing of your emails in your default browser.

You can then make changes to your emails, save them, and watch the browser automatically refresh the page for you.

You can use any of the [Browsersync options](https://browsersync.io/docs/options) in your config, and Maizzle comes with a few defaults that you can override:

### directory

Type: `boolean`
<br>
Default: `true`

When running `maizzle serve` with this setting enabled, Browsersync will open a file explorer in your browser, starting at the root of the build directory.

If you set this to `false`, the page opened by Browsersync will be blank, and you'll need to manually navigate to your emails directory.

<alert type="warning">If using the <code>tunnel</code> option for a client demo, use <code>directory: false</code>, so they can't freely browse all your emails by going to the root URL.</alert>

### notify

Type: `boolean`
<br>
Default: `false`

Toggle Browsersync's annoying pop-over notifications. Off by default ✌

### open

Type: `boolean`
<br>
Default: `false`

Decide which URL to open automatically when Browsersync starts. 

Can be `true`, `local`, `external`, `ui`, `ui-external`, `tunnel` or `false`

See [Browsersync docs](https://browsersync.io/docs/options#option-open) for details.

### port

Type: `integer`
<br>
Default: `3000`

Set the server port number - by default, your local development server will be available at <code>http&zwnj;://localhost:<strong>3000</strong></code>.

### tunnel

Type: `boolean|string`
<br>
Default: `false`

When set to `true`, Maizzle will enable localhost tunneling in Browsersync, so you can live-share a URL to an email that you're working on right now, with a colleague or a client. Under the hood, [localtunnel.me](https://localtunnel.me) will be used.

Both parties see the same thing, and scrolling is synced, too.

You can also use a string instead of a boolean - for example `tunnel: 'mybrand'`. In this case, Browsersync will attempt to use a custom subdomain for the URL, i.e. `https://mybrand.localtunnel.me`.
If that subdomain is unavailable, you will be allocated a random name as usual.

### ui

Type: `object|boolean`
<br>
Default: `{port: 3001}`

Browsersync includes a user-interface that is accessed via a separate port, and which allows control over all devices, push sync updates and much more.

You can disable it by setting it to `false`.

### watch

Array of extra paths for Browsersync to watch. By default, all files in your project's `src` folder and your `tailwind.config.js` file are watched.

You can define extra file and folder paths to watch when developing locally:

```js
// config.js
module.exports = {
  build: {
    browsersync: {
      watch: [
        './some/folder',
        'some-file.js'
      ]
    }
  }
}
```

When a file in any of these watch paths is updated, Browsersync will trigger a rebuild and changes will be reflected in the browser.

## components

Control where your Components live and how you reference them.

### root

You can define the path where your Components are located:

```js
build: {
  components: {
    root: 'src/components'
  }
}
```

Given `src/components/mycomponent.html`, you may now reference it like this:

```html
<component src="mycomponent.html"></component>
```

### tags

Additionally, you may also customize the tag and attribute names:

```js
build: {
  components: {
    tag: 'module',
    attribute: 'href'
  }
}
```

The above would allow you to pull in Components using the following markup:

```html
<module href="..."></module>
```

## layouts

Control where your Layouts are located, and how you reference them.

### root

You can define the path where your Layouts are located:

```js
build: {
  layouts: {
    root: 'src/layouts'
  }
}
```

You could then extend Layouts by referencing them relative to that path - no need to write out the full path relative to your project root:

```html
<extends src="main.html">
  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

<alert>Maizzle doesn't include this <code>layouts</code> key in the Starter config.</alert>

<alert type="danger">If you're extending a file that also extends a file (i.e. when extending a Template), this will not work. Instead, don't define the <code>root</code> key and only use project root-relative paths (i.e. <code>&lt;extends src="/templates/template.html"&gt;</code>)</alert>

### tagName

You may also change the `<extends>` tag name to something custom:

```js
build: {
  layouts: {
    tagName: 'layout'
  }
}
```

This would allow using a `<layout>` tag for extending Layouts:

```html
<layout src="src/layouts/main.html">
  <block name="template">
    <!-- ... -->
  </block>
</layout>
```

## templates

Configure where your Templates live, where they should be output, as well as what file extensions to use/look for and which assets should be copied over in the process.

```js
build: {
  templates: {
    filetypes: 'html',
    source: 'src/templates',
    destination: {
      path: 'build_local',
      extension: 'html'
    },
    assets: {
      source: './src/images',
      destination: 'images'
    }
  }
}
```

Starting with Maizzle `2.0`, you can define multiple `templates` sections:

```js
build: {
  templates: [
    {
      source: 'src/templates',
      destination: {
        path: 'build_local'
      }
    },
    {
      source: 'src/amp-templates',
      destination: {
        path: 'build_amp'
      }
    }
  ]
}
```

#### filetypes

Define what file extensions you use for your Templates. 

`filetypes` can be a string, but it can also be an array or a pipe|delimited list:

```js
build: {
  templates: {
    filetypes: ['html', 'blade.php'] // or 'html|blade.php'
  }
}
```

Maizzle will only look for files with these extensions when searching your `build.templates.source` directory for Templates to build.

This means you can keep other files alongside your Templates, and Maizzle will not try to compile them - it will simply copy them over to the build destination directory.

<alert>If <code>build.templates.filetypes</code> is not defined, Maizzle will default to <code>html</code>.</alert>

#### source

Define the source directory where Maizzle should look for Templates to compile. It's also used by `postcss-purgecss` when scanning for selectors to preserve.

```js
build: {
  templates: {
    source: 'src/templates'
  }
}
```

<alert>Remember, Maizzle will copy these folders to the <code>templates.destination.path</code> directory, with <em>everything</em> inside them.</alert>

### destination

This allows you to customize the output path and file extension.

###### path

Directory path where Maizzle should output the compiled emails.

```js
build: {
  templates: {
    destination: {
      path: 'build_local'
    }
  }
}
```

If you omit this key, a Jigsaw-inspired `build_${env}` directory name will be used.

<alert type="danger">Using multiple <code>templates</code> config blocks? Make sure to have unique <code>destination.path</code> names! Defaulting to <code>build_${env}</code> can result in files with the same name being overwritten.</alert>

###### extension

Define the file extension - without the leading dot - to be used for the compiled templates. 
For example, let's output [Laravel Blade](https://laravel.com/docs/7.x/blade) files:

```js
build: {
  templates: {
    destination: {
      path: 'build_laravel',
      extension: 'blade.php'
    }
  }
}
```

###### permalink

You can override `destination.path` for each Template, with the help of the `permalink` Front Matter key:

```html
---
permalink: path/to/file.html
---

<extends src="src/layouts/main.html">
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

### assets

Source and destination directories for your asset files.

At build time, `templates.assets.destination` will be created relative to `templates.destination`, and everything inside `templates.assets.source` will be copied into it:

```js
build: {
  templates: {
    assets: {
      source: 'src/images',
      destination: 'images'
    }
  }
}
```

You can use it to store _any_ files you might need, not just images.

Of course, if using multiple `templates` blocks, you can have different asset configurations for each block:

```js
build: {
  templates: [
    {
      source: 'src/templates',
      destination: {
        path: 'build_basic'
      },
      assets: {
        source: 'src/images',
        destination: 'images' // assets output to build_basic/images
      }
    },
    {
      source: 'src/amp-templates',
      destination: {
        path: 'build_amp'
      },
      assets: {
        source: 'src/assets/amp',
        destination: 'media' // assets output to build_amp/media
      }
    }
  ]
}
```

## tailwind

Paths for Tailwind CSS.

```js
build : {
  tailwind: {
    css: 'src/css/tailwind.css',
    config: 'tailwind.config.js'
  }
}
```

### css

Default: `src/css/tailwind.css`

Path to your [main CSS file](/docs/tailwindcss/#maincss), that will be compiled with Tailwind CSS.

### config

Default: `tailwind.config.js`

Path to the Tailwind CSS config file to use.

You can use this to specify a Tailwind config file for any build scenario.

For example, you might want to use a separate Tailwind config, where you:

- define fewer theming options (faster CSS compilation)
- disable `!important` (like in ⚡4email templates)
- use different Tailwind plugins

<alert>If the <code>config</code> key is missing, Maizzle will try loading <code>tailwind.config.js</code> from your project root. If that file is also missing, Tailwind will be compiled using its default settings (which are not email-optimized).</alert>

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
      require('posthtml-spaceless')()
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
        { name: '?php', start: '<', end: '>' }
      ]
    }
  }
}
```

## postcss

You can add extra PostCSS plugins:

```js
build: {
  postcss: {
    plugins: [
      require('autoprefixer')()
    ]
  }
}
```

## Build Errors

By default, when a build error occurs, Maizzle will throw an error.

You can configure how build errors are handled when developing with the CLI commands, by adding a `build.fail` key to your config:

```js
module.exports = {
  build : {
    fail: 'silent' // or 'verbose'
  }
}
```

- `silent` will just log the paths to the files it failed build, in the console
- `verbose` will additionally log the error stack trace

Omitting it or using any other value will throw an error (log stack trace and exit script).
