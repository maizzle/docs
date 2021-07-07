---
title: "Getting Started"
slug: "installation"
description: "Installing the Maizzle Email Framework on your machine and creating a new project"
---

import Alert from '~/components/Alert.vue'
import MaizzleVersion from '~/components/LatestReleaseNumber.vue'

# Getting Started

## Requirements

Maizzle needs a few tools installed on your machine.

### Node.js

You'll need [Node.js](https://nodejs.org/en/download/) installed. Either download and install the LTS version from the link, or use this command to check if you already have it:

```bash
node -v
```

<alert>Currently, Maizzle requires at least Node v12.13.0</alert>

### Git

The `maizzle new` command for scaffolding a new project works by cloning a Git repository, so you'll also need to have [Git](https://help.github.com/en/articles/set-up-git#setting-up-git) installed. 

Check if you already have it by running this command:

```bash
git --version
```

## Installation

Maizzle consists of:

- a <abbr title="Command Line Interface">CLI</abbr> tool
- the Framework
- a Starter project

When developing emails on your local machine with Maizzle, you typically run a CLI command inside your Starter project directory. 

That command asks the Framework to build the emails inside your current project, based on config files that it finds in there.

Install the CLI tool globally, so the `maizzle` executable gets added to your `$PATH` :

```bash
npm install -g @maizzle/cli
```

## Create a project

Create your first Maizzle project by opening a Terminal and running:

```bash
maizzle new
```

This will bring up an interactive prompt which will guide you through the setup:

![maizzle new interactive prompt](https://raw.githubusercontent.com/maizzle/cli/master/preview.gif)

Of course, you can also skip the prompt and scaffold a project immediately:

```bash
maizzle new https://github.com/maizzle/maizzle.git
```

Once that's done, change your current directory to the project root: 

```bash
cd maizzle
```

Congratulations, you can now start using Maizzle! 

## Development

Maizzle includes different commands for developing locally on your machine and building production-ready emails.

### Local

Start local email development by running the [`serve`](/docs/commands/#serve) command:

```bash
maizzle serve
```

This will start a local server that you can access at `http://localhost:3000` - when you make a change to a template and save it, the browser will refresh to reflect that.

### Production

Build production-ready emails, with inlined CSS and many other optimizations, by running the following command:

```bash
maizzle build production
```

This will use settings in your `config.production.js` to compile email templates that you can send via your <abbr title="Email Service Provider">ESP</abbr>.

Check out the [Commands docs](/docs/commands/) for more details.

## Updating

Maizzle is listed as a dependency in your project's `package.json` file:

```
"dependencies": {
  "@maizzle/framework": "^3.0.0"
}
```

To update, first bump that number to the Maizzle release that you want to upgrade to:

<pre class="language-text"><code class="language-text">"dependencies": {
  "@maizzle/framework": "^<maizzle-version />"
}</code></pre>

Then, re-install dependencies by running `npm install` in your project's root folder.

<alert>Note: latest Maizzle release number is <maizzle-version class="italic" /></alert>

### Clean update

If for some reason you're not getting the latest version or are running into installation issues, simply delete your `node_modules` folder and your `package-lock.json` file from the root of your project, and then run `npm install`.

This will do a fresh install of all dependencies.
