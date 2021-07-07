---
title: "Tailwind CSS"
slug: "tailwindcss"
description: "Learn how to use Tailwind CSS to create HTML email templates with CSS utility classes"
---

import Alert from '~/components/Alert.vue'

# Tailwind CSS

Maizzle uses the [Tailwind CSS](https://tailwindcss.com) framework, so you can rapidly prototype email templates with utility classes instead of writing inline styles.

For most of the time, you won't be writing CSS anymore 😎

## Workflow

The compiled Tailwind CSS is available under `page.css`, so you should first add it inside a `<style>` tag in your Layout's `<head>`:

```html
<!DOCTYPE html>
<html>
<head>
  <if condition="page.css">
    <style>{{{ page.css }}}</style>
  </if>
</head>
<body>
  <block name="template"></block>
</body>
```

In the example above, we used a [conditional](/docs/tags/#conditionals) to output the `<style>` tag only if `page.css` is truthy (i.e. not an empty string).

You might have noticed that we used `{{{ }}}` instead of the usual `{{ }}`. 

We do this to avoid double-escaping the CSS, which can break the build process when quoted property values are encountered (for example quoted font family names, background image URLs, etc.).

<alert type="warning">In order to use Tailwind CSS, the Layout that you're extending must output <code>page.css</code> inside a <code>&lt;style&gt;</code> tag.</alert>

### Utility-first

Simply write your HTML markup and add Tailwind classes to elements.

Instead of writing something like this:

```html
<table style="width: 100%;">
  <tr>
    <td style="padding: 24px 0; background-color: #e5e7eb;">
      <h1 style="margin: 0; font-size: 36px; font-family: -apple-system, 'Segoe UI', sans-serif; color: #000000;">Some title</h1>
      <p style="margin: 0; font-size: 16px; line-height: 24px; color: #374151;">Content here...</p>
    </td>
  </tr>
</table>
```

You simply write:

```html
<table class="w-full">
  <tr>
    <td class="py-24 px-0 bg-gray-200">
      <h1 class="m-0 text-4xl font-sans text-black">Some title</h1>
      <p class="m-0 text-base leading-24 text-gray-700">Content here...</p>
    </td>
  </tr>
</table>
```

Read more about the concept of utility-first CSS, and familiarize yourself with the syntax, in the [Tailwind CSS docs](https://tailwindcss.com/docs/utility-first).

### Components

If you find yourself repeating common utility combinations to apply the same styling in many different places (buttons maybe?), you can extract those to a component.

Tailwind includes an [`@apply`](https://tailwindcss.com/docs/extracting-components#extracting-css-components-with-apply) directive that can be used to compose custom CSS classes by "applying" Tailwind utilities to it.

Here's a quick example:

```css
.button-danger {
  @apply px-24 py-12 text-white bg-red-500;
}
```

Unlike utility classes that you add to `tailwind.config.js`, you add that in a CSS file that Maizzle tells Tailwind to compile along with the rest of the CSS.

And that brings us to...

## CSS Files

The [official Maizzle Starter](https://github.com/maizzle/maizzle) uses a `tailwind.css` file stored in `src/css`.

Although optional, this is included in order to provide an example of how you can use custom CSS components that go beyond the utility-first concept of Tailwind. 

For example, it's common practice with HTML emails to use... [creative CSS selectors](https://howtotarget.email/) to get things working in a certain email client; stuff Tailwind can't do out of the box.

`tailwind.css` does two things:

1. it imports Tailwind CSS components and utilities
2. it imports custom CSS files

Maizzle will automatically use this file if it finds it.

If using a custom file, you need to define its path in `config.js`:

```js
// config.js
module.exports = {
  build: {
    tailwind: {
      css: 'src/css/custom/tw-custom.css',
    }
  }
}
```

Again, this is totally optional: you can use Tailwind CSS in Maizzle without creating any CSS file at all! In this case, Tailwind will only generate components and utilities, based on your `tailwind.config.js`.

### Custom CSS

Add custom CSS files anywhere under `src/css`.

Maizzle adds the following ones:

- `resets.css` - browser and email client CSS resets.

- `utilities.css` - custom utility classes that Tailwind CSS doesn't provide.

<alert type="warning">Files that you <code>@import</code> in <code>tailwind.css</code> must be relative to <code>src/css</code></alert>

## Just-in-Time

Maizzle enables Tailwind's [Just-in-Time Mode](https://tailwindcss.com/docs/just-in-time-mode) by default:

```js
// tailwind.config.js
module.exports = {
  mode: 'jit',
}
```

Not only does JIT greatly improve build speeds, but it also enables a lot of useful new features, such as [stackable variants](https://tailwindcss.com/docs/just-in-time-mode#stackable-variants) or [arbitrary value support](https://tailwindcss.com/docs/just-in-time-mode#arbitrary-value-support).

All variants are enabled, so that's one less thing you need to worry about.

There are some [limitations](https://tailwindcss.com/docs/just-in-time-mode#limitations), but most of the time you won't run into them. 

Probably the biggest downside is that you no longer have all of the generated Tailwind classes available in your browser's Devtools, to quickly test out/debug styling right in the browser.

### Disabling JIT

You can disable JIT for production builds if you need to.

###### Define the mode in your Tailwind config

You can toggle JIT based on the current `NODE_ENV` in your `tailwind.config.js`:

```js
module.exports = {
  mode: process.env.NODE_ENV === 'local' ? 'jit' : 'aot',
  purge: [
    'src/**/*.*',
  ],
}
```

That will enable JIT only when developing locally with `maizzle serve`, or when you run `maizzle build` without specifying an environment name.

`maizzle build [env]` commands will use Always-on-Time (AOT) mode, which is the classic, slower mode that outputs all classes based on your Tailwind config.

###### Disable JIT in Maizzle config

Alternatively, you can disable JIT for a certain build environment by using a custom Tailwind config object in the Maizzle `config.[env].js`.

For example, let's disable JIT when we run `maizzle build production` by customizing `config.production.js` to use a Tailwind config that doesn't include the `mode` and `purge` keys:

```js
// config.production.js

const {mode, purge, ...twconfig} = require('./tailwind.config')

module.exports = {
  build: {
    tailwind: {
      config: twconfig,
    }
  }
}
```

## CSS purging

When running `maizzle build [env]`, if `[env]` is not `local`, Maizzle will enable CSS purging in Tailwind. This does a 'first pass' over your CSS and removes any classes that you don't use in your emails.

The CSS inliner and [`email-comb`](/docs/code-cleanup/#removeunusedcss) run _after_ this CSS purging step, so they receive as little CSS as possible to parse (greatly improving build times).

Here's how Maizzle configures Tailwind CSS purging internally:

```js
purge: {
  content: [
    'src/**/*.*',
    {raw: html}
  ],
},
```

Basically, CSS purging is disabled if you run one of these commands:

- `maizzle serve`
- `maizzle build`
- `maizzle build local`

All files inside your project's `src` folder are scanned for CSS selectors to be preserved; 
`{raw: html}` is only used when [compiling templates programmatically](/docs/nodejs/).

### Tailwind CSS purging

You can define custom CSS purging options right in your Tailwind config file:

```js
// tailwind.config.js
module.exports = {
  mode: 'jit',
  purge: {
    content: [
      'src/**/*.*',
      'custom/path/**/*.html',
    ],
    safelist: [
      'bg-blue-500',
      'text-center',
      'hover:opacity-100',
      // ...
      'lg:text-right',
    ]
  },
}
```

### Configuring PurgeCSS

Tailwind uses [PurgeCSS](https://purgecss.com/) to purge unused CSS - you can also configure purging by adding the `purgeCSS` key to your `config.js`:

```js
// config.js
module.exports = {
  purgeCSS: {
    content: [
      'brand/emails/**/*.*'
    ],
    safelist: ['random', 'yep', 'button', /^nav-/],
  }
}
```

The settings you define here will be merged on top of the internal ones, so you can use it to do things like safelisting class names or defining additional purge paths.

<alert type="warning">The <code>safelist</code> option does not support regular expressions when JIT is used.</alert>

## Shorthand CSS

<alert>This section refers to CSS inside <code>&lt;style&gt;</code> tags. For <em>inline</em> CSS shorthand, see the <g-link to="/docs/css-inlining/#mergelonghand">CSS inlining docs</g-link>.</alert>

Maizzle uses [postcss-merge-longhand](https://github.com/cssnano/cssnano/tree/master/packages/postcss-merge-longhand) to rewrite your CSS `padding`, `margin`, and `border` properties in shorthand-form, when possible.

Because utility classes map one-to-one with CSS properties, this normally doesn't have any effect with Tailwind CSS. However, it's useful when you extract utilities to components, with Tailwind's `@apply`.

Consider this template:

```html
<extends src="src/layouts/main.html">
  <block name="template">
    <div class="col">test</div>
  </block>
</extends>
```

Let's use `@apply` to compose a `col` class by  extracting two padding utilities: 

```css
/* src/css/components.css */

.col {
  @apply py-8 px-4;
}
```

Remember to import that file in `src/tailwind.css`:

```css
/**
 * @import here any custom CSS components - that is, classes that
 * you'd want loaded before the Tailwind utilities, so the
 * utilities can still override them.
*/
@import "components.css";
```

When building with CSS inlining enabled, normally that would yield:

```html
<div style="padding-top: 8px; padding-bottom: 8px; padding-left: 4px; padding-right: 4px;">test</div>
```

However, Maizzle will merge those with [`postcss-merge-longhand`](https://www.npmjs.com/package/postcss-merge-longhand), so we get this:

```html
<div style="padding: 8px 4px;">test</div>
```

This results in smaller HTML size, reducing the risk of [Gmail clipping your email](https://github.com/hteumeuleu/email-bugs/issues/41).

Using shorthand CSS for these is well supported in email clients and will make your HTML lighter, but the shorthand border is particularly useful because it's the only way Outlook will render it properly.

<alert>For shorthand CSS to work with <code>padding</code> or <code>margin</code>, you need to specify property values for all four sides. For borders, keep reading.</alert>

### Shorthand borders

To get shorthand-form CSS borders, you need to specify all these:

- border-width
- border-style
- border-color

With Tailwind's `@apply`, that means you can do something like this:

```css
.my-border {
  @apply border border-solid border-blue-500;
}
```

... which will turn this:

```html
<div class="my-border">Border example</div>
```

... into this:

```html
<div style="border: 1px solid #3f83f8;">Border example</div>
```

## Plugins

To use a Tailwind CSS plugin, simply `npm install` it and follow its instructions to add it to `plugins: []` in your `tailwind.config.js`. 
See the [Tailwind CSS docs](https://tailwindcss.com/docs/configuration#plugins).

## Use in Template

You can use Tailwind CSS, including directives like `@apply`, `@responsive`, and even nested syntax, right inside a template.
You simply need to use a `<block>` to push a `<style postcss>` tag to the Layout being extended.

First, add a `<block name="head">` inside your Layout's `<head>` tag:

```html
<!DOCTYPE html>
<html>
<head>
  <if condition="page.css">
    <style>{{{ page.css }}}</style>
  </if>
  <block name="head"></block>
</head>
<body>
  <block name="template"></block>
</body>
```

Next, use that block in a Template:

```html
<extends src="src/layouts/main.html">
  <block name="head">
    <style postcss>
      a {
        @apply text-blue-500;
      }

      @screen sm {
        table { 
          @apply w-full;
        }
      }
    </style>
  </block>

  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

[posthtml-content](https://github.com/posthtml/posthtml-content) is used to parse the contents of any `<style>` tag that has a `postcss` attribute - the contents are compiled with PostCSS.

<alert>The <code>postcss</code> attribute is only required if you want the CSS to be compiled with PostCSS - for example, when using Tailwind CSS syntax. If you're just writing regular CSS syntax, you don't need to include this attribute.</alert>

### Prevent inlining

When adding a `<style>` tag inside a Template, you can prevent all rules inside it from being inlined by using a `data-embed` attribute:

```html
<extends src="src/layouts/main.html">
  <block name="head">
    <style postcss data-embed>
      /* This rule will not be inlined */
      img {
        border: 0;
        @apply leading-full align-middle;
      }
    </style>
  </block>

  <block name="template">
    <!-- ... -->
  </block>
</extends>
```

<alert>Although it won't be inlined, the CSS will still be processed by the <a href="/docs/code-cleanup/#removeunusedcss">removeUnusedCSS</a> Transformer.</alert>

## Gotchas

Some things might not work as you'd expect. We'll try to explain them.

### New utilities

When developing locally with `maizzle serve`, if you add a new utility to `tailwind.config.js` or some custom class to one of the CSS files, saving the changes will rebuild all templates and reload the browser window. As expected.

However, when you go add that class to a Template and save, changes will not be reflected: the class won't exist in the compiled CSS.

This happens because Tailwind compilation is done once for all Templates and it's not re-compiled when you save changes to a Template. 

CSS purging also happens when Tailwind is compiled, so basically when you add a new utility to your Tailwind config, the CSS purging library won't see that utility being used anywhere. So it'll purge it.

<alert>This is not an issue when using JIT mode.</alert>

**Solution**

Save your Tailwind config (again?) or a Layout/Component after you've added the class(es) to your HTML. This will trigger the re-build of all Templates, and it will re-compile Tailwind as well - this time, CSS purging will 'see' the class in your HTML.
