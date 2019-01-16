# Bundles Core

> Bundles is a file bundler -- similar to [WebPack](https://webpack.js.org/), [RollupJS](http://rollupjs.org), [Parcel](https://parceljs.org/), [PostCSS](https://postcss.org/), etc. -- **but for anything**. The main difference between Bundles and other bundlers is that while other bundlers compile to a specific type of output (like JavaScript, CSS, etc.), Bundles can compile to anything. This means Bundles can compile to... well, anything; whether JavaScript, CSS, MarkDown, JSON, or literally _anything_! Bundles takes input, runs it through a series of "bundlers" (i.e., simple plugins), and processes your data however you tell it to.

<!-- Shields. -->
<p>
    <!-- NPM version. -->
    <a href="https://www.npmjs.com/package/@brikcss/bundles-core"><img alt="NPM version" src="https://img.shields.io/npm/v/@brikcss/bundles-core.svg?style=flat-square"></a>
    <!-- NPM downloads/month. -->
    <a href="https://www.npmjs.com/package/@brikcss/bundles-core"><img alt="NPM downloads per month" src="https://img.shields.io/npm/dm/@brikcss/bundles-core.svg?style=flat-square"></a>
    <!-- Travis branch. -->
    <a href="https://github.com/brikcss/bundles-core/tree/master"><img alt="Travis branch" src="https://img.shields.io/travis/rust-lang/rust/master.svg?style=flat-square&label=master"></a>
    <!-- Codacy. -->
    <a href="https://www.codacy.com/app/thezimmee/bundles-core"><img alt="NPM version" src="https://img.shields.io/codacy/grade//master.svg?style=flat-square"></a>
    <!-- Coveralls -->
    <a href='https://coveralls.io/github/brikcss/bundles-core?branch=master'><img src='https://img.shields.io/coveralls/github/brikcss/bundles-core/master.svg?style=flat-square' alt='Coverage Status' /></a>
    <!-- JS Standard style. -->
    <a href="https://standardjs.com"><img alt="JavaScript Style Guide" src="https://img.shields.io/badge/code_style-standard-brightgreen.svg?style=flat-square"></a>
    <!-- Prettier code style. -->
    <a href="https://prettier.io/"><img alt="code style: prettier" src="https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat-square"></a>
    <!-- Semantic release. -->
    <a href="https://github.com/semantic-release/semantic-release"><img alt="semantic release" src="https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg?style=flat-square"></a>
    <!-- Commitizen friendly. -->
    <a href="http://commitizen.github.io/cz-cli/"><img alt="Commitizen friendly" src="https://img.shields.io/badge/commitizen-friendly-brightgreen.svg?style=flat-square"></a>
    <!-- MIT License. -->
    <a href="https://choosealicense.com/licenses/mit/"><img alt="License" src="https://img.shields.io/npm/l/express.svg?style=flat-square"></a>
    <!-- Greenkeeper. -->
    <a href="https://greenkeeper.io/"><img src="https://badges.greenkeeper.io/brikcss/bundles-core.svg?style=flat-square" alt="Greenkeeper badge"></a>
</p>

## Environment support

| Node | CLI | ES Module | Browser | UMD |
| :--: | :-: | :-------: | :-----: | :-: |
|  ✓   |  ✓  |     x     |    x    |  x  |

## Install

```sh
npm install @bundles/core -D
```

## Terminology

To make things easier to understand:

- _Bundles_ (capitalized): The core package / tool for Bundles.
- _bundles_: Results compiled by Bundles. More specifically this refers to the `results.bundles` returned by `Bundles`. This may also refer to `config.bundles`, which eventually turns into `results.bundles`.
- _bundle (noun)_: A single or specific result compiled by Bundles. For example: `results.bundles[x]`.
- _bundle (verb)_: The process of running input through a series of `bundlers`.
- _bundler_: A simple function which allows Bundles to process / compile input however you like. Without `bundlers`, Bundles will simply output the same source content. With `bundlers`, Bundles can output just about anything you want.
- _config_: Refers to user configuration.
  - _global config_: Refers to the global `config` Object.
  - _bundles config_: Refers to `config.bundles`.
  - _bundle config_: Refers to a specific bundle, i.e., `config.bundles[x]`.

## Usage

Bundles can be run in Node or the command line:

**Node:**

```js
const bundles = require('@bundles/core');
bundles(config, options);
```

**CLI:**

```sh
bundles <source files> [options]
```

_Note: All runtime `options` get merged down to `config.options`. `options` only exists separate from `config.options` to allow user to pass runtime options (i.e., the `watch` flag), which override `config.options`._

## Configuration

Bundles' API is designed to be as minimal as possible so as to be easy to use, while also retaining a reasonable amount of flexibility. It is intended that most configuration can and should come from bundlers.

- **`bundles`** (_required_) _{Object[]}_ Bundle configuration Objects which determine how each bundle is to be processed. Each `bundle` has the following properties:

  - **`input`** (_required_) _{String|Glob}_ Input files to process.
  - **`bundlers`** (_required_) _{String[]|Function[]|Object[]}_ Each bundler processes `input` in a specified way. A bundler can be any of the following (see also [creating a bundler](#creating-a-bundler)):
    - A Function: `(bundle = {}, bundler = {}) => { // Do something cool. }`.
    - A Node module: `'my-cool-bundler'` or `'./path/to/bundler'`
    - An Object, where the `run` property is a Node module or a Function.
  - **`id`** _{String}_ ID / identifier for this bundle. If this doesn't exist, Bundles will use its index position in `bundles`.

- **`on`** _{Object}_ Callback functions which hook into the Bundles workflow. Potential hooks include:

  - **`afterChange( bundle, { filepath, config } )`** _{Function}_ Runs after a source file changes, but only if the file is being watched.

- **`options`** _{Object}_ Configuration options. _IMPORTANT: Any `options` passed to Bundles -- either from the command line or in Node -- get merged here._

  - **`bundles`** _{String|Array}_ Should be a comma-separated list or an Array of bundle IDs. This option tells Bundles which bundles to run. It allows you to only run a selected number of bundles rather than all that are configured.
  - **`watch`** _{Boolean|String}_ Set `true` to watch all bundles. Pass a comma-separated String to only watch the bundle IDs listed.
  - **`glob`** _{Object}_ Options passed directly to [globby](https://github.com/sindresorhus/globby).
  - **`frontMatter`** _{Object}_ Options passed directly to [gray-matter](https://github.com/jonschlinkert/gray-matter).
  - **`chokidar`** _{Object}_ Options passed directly to [chokidar](https://github.com/paulmillr/chokidar).

## The Result

Bundles returns a result Object after running all configured bundlers. The result Object mirrors the `config` Object and, at a minimum, has the following properties:

- **`success`** _{Boolean}_ Whether all bundles compiled successfully.
- **`bundles`** _{Object[]}_ Bundles that ran. Each bundle has the same properties as bundles in the config Object, along with the following additions:
  - **`success`** _{Boolean}_ Whether bundle compiled successfully.
  - **`output`** _{Object[]}_ File(s) to output.
  - **`watch`** _{Boolean}_ Whether the bundle is being watched.
  - **`_meta`** _{Object}_ Metadata used internal.
- **`bundlesMap`** _{Object}_ Dictionary of bundles that ran, organized by bundle ID. Provided simply for an alternative way to look up bundle results.
- **`options`** _{Object}_ Run time options, merged with configuration options.
- **`watchers`** _{Object}_ A dictionary of watchers created by [chokidar](https://github.com/paulmillr/chokidar), organized by bundle ID.
- **`on`** _{Object}_ A dictionary of configured callbacks, organized by callback name.
- **`_meta`** _{Object}_ Metadata used internally. May change at any time.

## Creating a bundler

It is easy to create your own custom bundler. A bundler is simply a function which returns the `bundle`. Here are two simple examples:

```js
const fs = require('fs');
module.exports = (bundle = {}, bundler = {}) => {
  bundle.output.forEach((result) => {
    result.content = result.content += '\n';
    return result.content;
  });
  // Must return the bundle Object.
  return bundle;
};
```

You may also return a Promise which returns the `bundle` Object:

```js
const fs = require('fs');
module.exports = (bundle = {}, bundler = {}) => {
  // Return a promise...
  return new Promise((resolve) => {
    bundle.output.forEach((result) => {
      result.content = result.content += '\n';
      return result.content;
    });
    // ...which resolves the bundle Object.
    return resolve(bundle);
  });
};
```

### Guidelines for creating a bundler

1. A bundler must return a function which returns the modified `bundle` Object. The function receives, and allows you to use, the following parameters (see examples above):

   - **`bundle`** _{Object}_ The bundle Object. This is the Object you want to modify and return. It has the following properties:

     - **`id`** _{String}_ The bundle's ID provided by user, or its index position.
     - **`input`** _{String[]}_ The list of input file paths returned by [globby](https://github.com/sindresorhus/globby).
     - **`output`** _{Object[]}_ Each Object is an input / source file, with the following properties:
       - **`source`** _{Object}_ The source file is read in and this `source` Object is created by [gray-matter](https://www.npmjs.com/package/gray-matter#returned-object).
       - **`from`** _{String}_ Source file path.
       - **`content`** _{String}_ Output content (to be modified).
       - **`data`** _{String}_ Output front matter data (can be modified).
     - **`bundlers`** _{Object[]}_ Bundlers that process this bundle (see `config.bundlers`).
     - **`watch`** _{Boolean}_ Whether this bundle is configured to be watched.
     - **`on`** _{Object}_ Callback functions to hook into core functionality.

   - **`bundler`** _{Object}_ The bundler configuration. This allows users to provide bundler-specific configuration.
       <!-- - **`config`** _{Object}_ The global configuration Object. _IMPORTANT: This is provided for access to global user options but should not be modified._ -->

2. Only modify the `bundle` Object. Other parameters are provided as read-only context, and it is strongly encouraged not to modify these Objects.

3. Internally, the `bundler` Object only uses a few properties. This allows bundler authors flexibility in how they provide their custom set of configuration properties for their users. However, as a caution, Bundles reserves the right to add more internal properties in the future (any breaking changes, of course, will be [semantically versioned](https://semver.org/)), so it bundler authors are encouraged to wrap their configuration in a single parent Object such as `options` (i.e., `bundler.options`). Currently the `bundler` has the following internal properties:

   - **`success`** _{Boolean}_ Whether the bundler ran successfully.
   - **`error`** _{Error}_ Error Object if an error occurs running the bundler.
   - **`run`** _{Function}_ The bundler Function which processes the bundle.
   - **`_meta`** _{Object}_ Private metadata about the bundler.
