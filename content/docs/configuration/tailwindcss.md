---
title: "Tailwind CSS Config"
slug: "tailwindcss-config"
description: "See how the Tailwind CSS configuration is customized for email development in Maizzle"
---

import Alert from '~/components/Alert.vue'
import TailwindColors from '~/components/TailwindColors.vue'

# Tailwind CSS Config

Maizzle comes with an email-tailored `tailwind.config.js` that changes a few things for better email client compatibility.

These are the differences from the original Tailwind config.

## Units

Because of poor email client support, `rem` units have been replaced with `px`.

This affects the following utilities:

- `spacing` (width, height, margin, padding, etc)
- `maxWidth`
- `borderRadius`
- `fontSize`
- `lineHeight`

### spacing 

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      spacing: {
        screen: '100vw',
        full: '100%',
        px: '1px',
        0: '0',
        2: '2px',
        3: '3px',
        4: '4px',
        5: '5px',
        6: '6px',
        7: '7px',
        8: '8px',
        9: '9px',
        10: '10px',
        11: '11px',
        12: '12px',
        14: '14px',
        16: '16px',
        20: '20px',
        24: '24px',
        28: '28px',
        32: '32px',
        36: '36px',
        40: '40px',
        44: '44px',
        48: '48px',
        52: '52px',
        56: '56px',
        60: '60px',
        64: '64px',
        72: '72px',
        80: '80px',
        96: '96px',
        600: '600px',
        '1/2': '50%',
        '1/3': '33.333333%',
        '2/3': '66.666667%',
        '1/4': '25%',
        '2/4': '50%',
        '3/4': '75%',
        '1/5': '20%',
        '2/5': '40%',
        '3/5': '60%',
        '4/5': '80%',
        '1/6': '16.666667%',
        '2/6': '33.333333%',
        '3/6': '50%',
        '4/6': '66.666667%',
        '5/6': '83.333333%',
        '1/12': '8.333333%',
        '2/12': '16.666667%',
        '3/12': '25%',
        '4/12': '33.333333%',
        '5/12': '41.666667%',
        '6/12': '50%',
        '7/12': '58.333333%',
        '8/12': '66.666667%',
        '9/12': '75%',
        '10/12': '83.333333%',
        '11/12': '91.666667%',
      },
    }
  }
}
```

### maxWidth

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      maxWidth: theme => ({
        ...theme('spacing'),
        xs: '160px',
        sm: '192px',
        md: '224px',
        lg: '256px',
        xl: '288px',
        '2xl': '336px',
        '3xl': '384px',
        '4xl': '448px',
        '5xl': '512px',
        '6xl': '576px',
        '7xl': '640px',
      }),
    }
  }
}
```

### borderRadius

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      borderRadius: {
        none: '0px',
        sm: '2px',
        DEFAULT: '4px',
        md: '6px',
        lg: '8px',
        xl: '12px',
        '2xl': '16px',
        '3xl': '24px',
        full: '9999px',
      },
    }
  }
}
```

### fontSize

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      fontSize: {
        0: '0',
        xs: '12px',
        sm: '14px',
        base: '16px',
        lg: '18px',
        xl: '20px',
        '2xl': '24px',
        '3xl': '30px',
        '4xl': '36px',
        '5xl': '48px',
        '6xl': '60px',
        '7xl': '72px',
        '8xl': '96px',
        '9xl': '128px',
      },
    }
  }
}
```

### lineHeight

The `lineHeight` utilities have been extended to include all `spacing` scale values:

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      lineHeight: theme => ({
        ...theme('spacing'),
      }),
    }
  }
}
```

So you can use `leading` utilities to easily create vertical spacing, like this:

```html
<div class="leading-64">&zwnj;</div>
<!-- Result: <div style="line-height: 64px">&zwnj;</div> -->
```

## Colors

Maizzle doesn't define any colors, it uses the [default colors](https://tailwindcss.com/docs/customizing-colors) in Tailwind:

<tailwind-colors class="mb-8" />

You can define your own colors, or even extend or change the default colors by adding a `colors` key in your Tailwind config:

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        blue: {
          // change 'blue-500'
          500: '#03a9f4',
          // add 'blue-1000'
          1000: '#101e47',
        },
        // custom color
        primary: '#FFCC00',
      }
    }
  }
}
```

Tailwind 2.x comes with many other color palettes, which are not included by default.

You can import and use them like this:

```js
// tailwind.config.js
const colors = require('tailwindcss/colors')

module.exports = {
  theme: {
    extend: {
      colors: {
        gray: colors.coolGray,
        blue: colors.lightBlue,
        yellow: colors.amber,
      }
    }
  }
}
```

See the [Tailwind color palette reference](https://tailwindcss.com/docs/customizing-colors#color-palette-reference).

## !important

Emails still need to use inline CSS, most notably for these reasons:

- Outlook only reads the first class in a `class=""` attribute, and ignores the rest. 
  So it'll only use `a` from `class="a b"`
- Some email clients don't support embedded CSS (i.e. in `<style>`)

Because of this, the `important` option is set to `true` by default, so that responsive utilities can actually override inlined CSS.

<alert>This applies only to <code>&lt;head&gt;</code> CSS, inlined CSS will not contain <code>!important</code></alert>

You may disable this by adding the `important` key to your Tailwind config:

```js
// tailwind.config.js
module.exports = {
  important: false
}
```

## Separator

Characters like `:` in `hover:bg-black` need to be \escaped in CSS. 

Because some email clients (Gmail 👀) fail to parse selectors with escaped characters, Maizzle normalizes all your CSS selectors and HTML classes, replacing any escaped characters it finds with email-safe alternatives.

So you can safely use Tailwind's awesome default separator and write classes like `sm:w-1/2` - Maizzle will convert that to `sm-w-1-2` in your compiled template:

```js
// tailwind.config.js
module.exports = {
  separator: ':',
  theme: {
    width: {
      '2/5': '40%', // w-2-5
      '50%': '50%', // w-50pc
      '2.5': '0.625rem', // w-2_5
    }
  }
}
```

You can also [configure the replacement mappings](/docs/code-cleanup#safeclassnames).

## Screens

Maizzle uses a desktop-first approach with `max-width`, instead of Tailwind's default mobile-first with `min-width`. 

Of course, you're free to adjust this as you like:

```js
screens: {
  sm: {max: '600px'}
},
```

More on screens, in the [Tailwind CSS docs](https://tailwindcss.com/docs/responsive-design).

## Plugins

You can of course use any Tailwind plugin, please [see the docs](https://tailwindcss.com/docs/configuration#plugins).

### Disabled

Due to poor support in email clients, Maizzle disables the following Tailwind plugins:

- animation
- backgroundOpacity
- borderOpacity
- divideOpacity
- placeholderOpacity
- ringColor
- ringWidth
- ringOpacity
- ringOffsetColor
- textOpacity

If you want to use one of these plugins, simply set it to `true` in `corePlugins` at the bottom of your `tailwind.config.js`:

```diff
corePlugins: {
+ animation: true,
  backgroundOpacity: false,
  borderOpacity: false,
  divideOpacity: false,
  placeholderOpacity: false,
  textOpacity: false,
}
```

### boxShadow

Since v3.1.5, Maizzle uses [tailwindcss-box-shadow](https://www.npmjs.com/package/tailwindcss-box-shadow) to output box-shadow CSS values exactly as you have them defined in your Tailwind config.

The default Tailwind CSS variables-based box shadows are suppressed internally, because email clients have very poor support for CSS variables.
