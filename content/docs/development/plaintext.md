---
title: "Plaintext"
slug: "plaintext"
description: "Automatically create plaintext versions of your HTML emails in Maizzle"
---

import Alert from '~/components/Alert.vue'

# Plaintext

Maizzle can automatically create plaintext versions of your HTML emails.

## Global

Generate a plaintext version for all your email templates by adding a `plaintext` key in your `config.js`:

```js
module.exports = {
  plaintext: true
}
```

## Custom path

You may configure the `path` and `extension` of the plaintext files:

```js
module.exports = {
  plaintext: {
    destination: {
      path: 'dist/brand/plaintext',
      extension: 'rtxt'
    }
  }
}
```

<alert>The <code>path</code> option must be a directory path, otherwise a single plaintext file will be generated for all of your emails.</alert>

Using multiple Template sources? You can enable plaintext on a per-source basis:

```js
module.exports = {
  build: {
    templates: [
      {
        source: 'src/templates',
        destination: {
          path: 'build-1',
        },
        plaintext: true // build-1 folder only: output plaintext files next to the HTML counterparts
      },
      {
        source: 'src/templates',
        destination: {
          path: 'build-2',
        },
        // build-2 folder only: output plaintext files in the `plaintext` subdirectory, with custom extension
        plaintext: {
          destination: {
            path: 'build-2/plaintext',
            extension: 'rtxt'
          }
        },
      },
      // plaintext won't be generated in the `build-3` directory, because we didn't enable it
      {
        source: 'src/templates',
        destination: {
          path: 'build-3',
        }
      },
    ]
  }
}
```

## Front Matter

Generate a plaintext version for a single Template by enabling it in its Front Matter:

```html
---
plaintext: true
---

<extends src="src/layouts/main.html">
  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

A `.txt` file will be output at the same location with the compiled Template.

## Permalink

If you're using the [`permalink`](/docs/build-config/#permalink) Front Matter key in your Template, Maizzle will output the `.txt` file at that location:

```html
---
permalink: example/email.html
plaintext: true
---

<extends src="src/layouts/main.html">
  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

For the Template above, `example/email.txt` will be generated.

## Configuration

By default, the plaintext generator in Maizzle uses most default options from [`string-strip-html`](https://codsen.com/os/string-strip-html/#optional-options-object), with this exception:

```js
dumpLinkHrefsNearby: {
  enabled: true
},
```

This ensures URLs from anchors are actually output in the plaintext version.

You can use a `plaintext` object in your `config.js` to overwrite any of the defaults from `string-strip-html`.

```js
module.exports = {
  plaintext: {
    ignoreTags: [],
    onlyStripTags: [],
    stripTogetherWithTheirContents: ['script', 'style', 'xml'],
    skipHtmlDecoding: false,
    trimOnlySpaces: false,
    dumpLinkHrefsNearby: {
      enabled: false,
      putOnNewLine: false,
      wrapHeads: '',
      wrapTails: ''
    },
    cb: null
  }
}
```

<alert>With the config above, Maizzle will output plaintext versions for all Templates.</alert>

### Front Matter override

Using `plaintext: true` like in the [Front Matter example](/docs/plaintext/#front-matter) will override your plaintext config object if you have it defined in `config.js` like above.

If you need to control `string-strip-html` options when generating plaintext for a single Template, you need to use `enabled: true`. 

You basically add the options object as shown above, but in Front Matter syntax:

```html
---
plaintext: 
  dumpLinkHrefsNearby:
    enabled: true
    putOnNewLine: true,
    wrapHeads: '['
    wrapTails: ']'
---

<extends src="src/layouts/main.html">
  <block name="template">
    <a href="https://example.com">Click here</a>
  </block>
</extends>
```

That will output:

```
Click here

[https://example.com]
```

## Plaintext-only

You can output content only in the plaintext version, with the `<plaintext>` tag:

```html
---
plaintext: true
---

<extends src="src/layouts/main.html">
  <block name="template">
    This text shows in both the HTML and the plaintext versions.
    <plaintext>This will be output only in the plaintext version</plaintext>
  </block>
</extends>
```

## Use in Node.js

You can render an HTML string to plaintext in your application, with the `plaintext()` method in Maizzle.

```js
const Maizzle = require('@maizzle/framework')

const html = `<p>your html string</p>`

const {plaintext} = Maizzle.plaintext(html)

// your html string
```

You can also pass a config object to this method:

```js
const {plaintext} = Maizzle.plaintext(html, {
  plaintext: {
    // string-strip-html options
  }
})
```

The object that you pass here must contain a `plaintext: {}` key, as explained in the [configuration section](/docs/plaintext/#configuration) above.
