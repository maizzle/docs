---
title: "Shorthand CSS"
description: "Group CSS properties of the same type into shorthand inline CSS."
---

# Shorthand CSS

Rewrite longhand CSS inside `style` attributes with shorthand syntax. Only works with `margin`, `padding` and `border`, and only when all sides are specified.

Shorthand syntax for CSS properties means less code, so fewer bytes to send over the wire. Today, most email clients support shorthand CSS.

Something like this:

```html
<p class="mx-2 my-4">Example</p>
```

... instead of becoming this:

```html
<p style="margin-left: 2px; margin-right: 2px; margin-top: 4px; margin-bottom: 4px;">Example</p>
```

... is rewritten to this:

```html
<p style="margin: 4px 2px;">Example</p>
```

By default, `shorthandCSS` is disabled.

## Usage

Enable it for all tags:

```js [config.js]
export default {
  css: {
    shorthand: true,
  }
}
```

Enable it only for a selection of tags:

```js [config.js]
export default {
  css: {
    shorthand: {
      tags: ['td', 'div'],
    }
  }
}
```

## Disabling

Set it to `false` or simply omit it:

```js [config.js]
export default {
  css: {
    shorthand: false,
  }
}
```

## API

```js [app.js]
import { shorthandCSS } from '@maizzle/framework'

const html = await shorthandCSS('html string')
```
