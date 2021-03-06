---
title: "Markdown"
slug: "markdown"
description: "Use Markdown in your HTML email templates. GitHub Flavored Markdown included, too."
---

import Alert from '~/components/Alert.vue'

# Markdown

Maizzle supports Markdown in your email templates.

[markdown-it](https://github.com/markdown-it/markdown-it) is used together with PostHTML and you can fully configure it, either from your environment config, or through Front Matter for each Template.

## Tags

You can use one of two custom tags to add Markdown to your emails:

```html
<markdown>This Markdown will be **compiled** to HTML</markdown>
<md>There's also a _shorter_ version of the tag above.</md>
```

Result: 

```html
<p>This Markdown will be <strong>compiled</strong> to HTML</p>
<p>There's also a <em>shorter</em> version of the tag above.</p>
```

## Attributes

Use attributes if you need the element wrapping your Markdown to be preserved:

```html
<div markdown>This Markdown will be **compiled** to HTML</div>
<p md>There's also a _shorter_ version of the tag above.</p>
```

Result: 

```html
<div>
  <p>This Markdown will be <strong>compiled</strong> to HTML</p>
</div>
<p>There's also a <em>shorter</em> version of the tag above.</p>
```

### Wrapping tag

Use the `tag=""` attribute to specify a tag name to wrap your Markdown with:

```html
<md tag="section">This Markdown will be **compiled** to HTML</md>
```

Result: 

```html
<section>
  <p>This Markdown will be <strong>compiled</strong> to HTML</p>
</section>
```

## Importing files

Already have Markdown somewhere in a file? Simply include it:

```html
<md src="./README.md">
  # You'll see contents of README.md above this heading
</md>
```

Result:

```html
<!-- contents of README.md here -->
<h1>You'll see contents of README.md above this heading</h1>
```

## GFM

[GitHub Flavored Markdown](https://github.github.com/gfm/) is supported - the [Tables](https://help.github.com/articles/organizing-information-with-tables/) and [Strikethrough] (https://help.github.com/articles/basic-writing-and-formatting-syntax/#styling-text) extensions are enabled by default.

## Config

You can configure `posthtml-markdownit` through the `markdown` config object:

```js
// config.js
module.exports = {
  markdown: {
    root: './', // A path relative to which markdown files are imported
    encoding: 'utf8', // Encoding for imported Markdown files
    markdownit: {}, // Options passed to markdown-it
    plugins: [] // Plugins for markdown-it
  }
}
```

Checkout the options for [`posthtml-markdownit`](https://github.com/posthtml/posthtml-markdownit#options) and [`markdown-it`](https://github.com/markdown-it/markdown-it#init-with-presets-and-options).

## Disabling

You can disable the markdown Transformer by setting it to `false`:

```js
// config.js
module.exports = {
  markdown: false
}
```

## Plugins

There are over 300 plugins for `markdown-it` available on NPM! To use a plugin, `npm install` it first and then add it to `config.js`.

For example, imagine we `npm install`ed [`markdown-it-emoji`](https://www.npmjs.com/package/markdown-it-emoji):

```js
// config.js
module.exports = {
  markdown: {
    plugins: [
      {
        plugin: require('markdown-it-emoji'),
        options: {} // Options for markdown-it-emoji
      }
    ]
  }
}
```

We can now use emojis in markdown:

```html
<md>
  You can use emojis :)
</md>
```

Result:

```html
<p>You can use emojis 😃</p>
```

## Front Matter

You can override the base Markdown config from your Template's Front Matter.

```html
---
markdown:
  markdownit:
    linkify: true
---

<extends src="src/layouts/base.html">
  <block name="template">
    <md>
      https://example.com
    </md>
  </block>
</extends>
```

That will output:

```html
<p><a href="https://example.com">https://example.com</a></p>
```

<alert type="danger">JavaScript is not supported in Front Matter, so you can't use functions here.</alert>

## Gotchas

There are some situations where Markdown might not work as expected, or requires some additional work on your side.

### Classes and IDs

Classes and IDs generated by `markdown-it` or its plugins need to be [whitelisted with `removeUnusedCSS`](/docs/code-cleanup/#whitelist-1), otherwise they will be removed.

For example, imagine your have fenced code blocks like this in your Markdown:

<pre class="language-markdown"><code class="language-markdown">```html
<div>Lorem ipsum</div>```</code></pre>

... then you will need to whitelist all classes starting with `lang` in the `removeUnusedCSS` configuration:

```js
// config.production.js
module.exports = {
  removeUnusedCSS: {
    whitelist: ['.lang*']
  }
}
```

<alert>This is assuming you're using the default markdown-it <code>langPrefix</code>, which is set to <code>'language-'</code> - make sure to update the <code>whitelist</code> if you change that.</alert>

### Escaped variables

If you're using expressions to render markdown from a variable that you have defined in your config like this:

```js
module.exports = {
  data: {
    content: '> a markdown string'
  }
}
```

... you will need to use triple curly braces to output the unescaped content:

```html
<extends src="src/layouts/main.html">
  <block name="template">
    {{{ page.data.content }}}
  </block>
</extends>
```

This is required for blockquotes to work - otherwise `>` will be output as `&gt;` and the blockquote will be rendered as a paragraph.
