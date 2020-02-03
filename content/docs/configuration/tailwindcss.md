---
title: "Tailwind CSS Config"
slug: "tailwindcss-config"
description: "Tailwind CSS email-tailored configuration in Maizzle"
---

# Tailwind CSS Config

Maizzle comes with an email-tailored `tailwind.config.js`

## Spacing units

Because they are poorly supported in email, `rem` units have been replaced with `px` ones, with values better suited for email client viewports.

## !important

Emails still need to use inline CSS, most notably for these reasons:

- Outlook only reads the first class in a `class=""` attribute, and ignores the rest. 
  So it'll only use `a` from `class="a b"`
- Some email clients don't support embedded CSS (i.e. in `<style>`)

Because of this, the `important` option is set to `true`, in order for the responsive utilities to actually override inlined CSS:

```js
// tailwind.config.js
module.exports = {
  important: true,
}
```

<div class="bg-gray-100 border-l-4 border-gradient-b-ocean-light p-4 mb-4 text-md" role="alert">
  <div class="text-gray-600">This applies only to <code class="shiki-inline">&lt;head&gt;</code> CSS, inlined CSS will not contain <code class="shiki-inline">!important</code></div>
</div>

## Separator

Separators like `:` in `hover:bg-black` or `/` in `w-1/2` need to be \escaped in CSS. 

Because some email clients (Gmail 👀) fail to parse selectors with escaped characters, 
Maizzle normalizes all your CSS selectors and HTML classes, replacing any escaped characters it finds with `-`.

So you can safely use Tailwind's awesome default separator and write classes like `sm:w-1/2` - 
Maizzle will convert that to `sm-w-1-2` in your compiled template:

```js
// tailwind.config.js
module.exports = {
  separator: ':',
  theme: {
    width: {
      '1/2': '50%', // w-1-2
      '50%': '50%', // w-50pc
    }
  }
}
```

<div class="bg-gray-100 border-l-4 border-gradient-b-orange-dark p-4 mb-4 text-md" role="alert">
  <div class="text-gray-600">Maizzle normalizes only <code class="shiki-inline">:</code>, <code class="shiki-inline">/</code>, and <code class="shiki-inline">%</code> in your class names. If you use other special characters, it is your responsibility to convert them.</div>
</div>

## Screens

A custom set of `@media` query breakpoints is defined.

Maizzle uses a desktop-first approach with `max-width`, instead of Tailwind's default mobile-first with `min-width`. Of course, you're free to adjust these as you like:

```js
screens: {
  all: {'raw': 'screen'},
  sm: {'max': '600px'},
},
```

### @screen all
  
This creates an `@media screen {}` query. You can use it for things like: 
    
- defining (web) font stacks for modern email clients 
- preventing Juice from inlining a utility class

### @screen sm

A breakpoint for mobile devices, adjust as needed.

More on screens, in the [Tailwind CSS docs &nearr;](https://tailwindcss.com/docs/responsive-design)

## Plugins

Maizzle comes with the [tailwindcss-gradients](https://www.npmjs.com/package/tailwindcss-gradients) plugin.
See the [Gradients docs](/docs/gradients/) for examples showing how to configure and use it.

Of course, you can add any plugin you want, like [CSS grid](https://www.npmjs.com/package/tailwindcss-grid) or [border image gradients](https://www.npmjs.com/package/tailwindcss-border-gradients).