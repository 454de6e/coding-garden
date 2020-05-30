# Packages

## Modules

When writing packages for NPM, you need to understand at least two types of
modules:

- CommonJS modules (current default)
- ES modules (future default)

### CommonJS modules (`cjs`)

[JavaScript Modules: From IIFEs to CommonJS to ES6 Modules](https://www.youtube.com/watch?v=qJWALEoGge4)

- Each file is a module by default.
- Anything exposed with `module.exports` can be imported using `require`. The
  `module` object is provided by Node.
- In general, the thing exposed is a function and there is only a single value
  exposed per module.
- You could also expose an object with multiple props. But the correct way to
  expose multiple things is via `exports`. The `exports` object is a reference
  to the `exports` prop on `module`.
- CommonJS modules are loaded synchronously and are therefore not suitable for
  browsers. Module bundlers can help make modules load asynchronously in
  browsers.
- In newer codebases you should not use `require` syntax. For
  [tree shaking](https://webpack.js.org/guides/tree-shaking/) to work, bundlers
  analyze the static module structure of `import` and `export` statements.

### ES modules (`esm`)

[ES modules: A cartoon deep-dive](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)

- With ES modules you need to create a graph of instances in 3 steps:
  1. **Construction:** find, download and parse files. Build module records and
     module map.
     1. _Module resolution:_ where is the module located?
     1. _Fetch:_ download or load from file system. Browsers currently support
        URLs as module specifier only.
     1. _Parse:_ figure out imports and exports and create a module record, then
        put module record in a module map using the module specifier as key
        (similar to how Webpack does this).
  1. **Instantiation:** find boxes in memory for exported values, then link
     imports and exports to these boxes. Create module graph.
  1. **Evaluation:** evaluate boxes with actual values.
- In browsers you load the first module with a script tag:
  `<script src="main.js" type="module" />`. But then what module specifiers
  should you use inside `main.js` so a browser still knows what to do? You still
  need a [bundler](/notes/dev/bundlers).
- Starting with v12, Node supports ES modules. Module specifiers point to files.

## Props in `package.json`

| Prop     | Value        | Description                                                                                                                                                                                      |
| :------- | :----------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `main`   | path to file | Entry point for CommonJS and ES modules packages.                                                                                                                                                |
| `module` | path to file | `module` is an inofficial prop in `package.json` that signals that a package is an ES module. It is honored by [Rollup](https://rollupjs.org/guide/en/) and [Webpack](https://webpack.js.org/)). |
| `type`   | `module`     | Signals that this package should be interpreted as ES module. This is the carte blanche approach. If you want to do file by file yuo need to work with the `.mjs` extension.                     |

## Bundlers for NPM packages

If you write your own NPM package, you should support at least CommonJS and ES
modules. Your `package.json` needs to have a `main` entry point for CommonJS and
a `module` entry point for ES modules.

You do not have to use a bundler to create an NPM package, but there are some
benefits:

- A bundler can bundle only what is used and tree shake all dependencies.
- A bundler can create different distributions of your package, eg CJS, ESM and
  UMD.
- For formats that can be loaded from the web, such as UMD, the bundler can
  bundler all dependencies.
- A bundler can optimize CSS or package assets.

These are your tooling options:

### No bundler

This [blog post](https://2ality.com/2019/10/hybrid-npm-packages.html) discusses
options to manually create hybrid NPM packages that support ESM and CJS. If you
do not bundle your package, you need to be aware of all the things ES modules do
not support, eg if you require CommonJS modules from an ES module, named imports
do not work. There is a workaround for that, using package
[`esm`](https://www.npmjs.com/package/esm), but it's another tool when you
probably did not want to use a tool in the first place..

### @pika/pack

[Introducing: @pika/pack](https://www.pika.dev/blog/introducing-pika-pack)

You can think of this package build pipeline as a lightweight bundler.
`@pika/pack` is mostly about choosing good defaults for common tools such as
Babel and Rollup to minimize configuration. It can also help create multiple
distributions.

### Microbundle

With [Microbundle](https://github.com/developit/microbundle) you can use
[Rollup](https://rollupjs.org/guide/en/) with having to become an expert in
configuring it. You get most of Rollup's benefits, such as tree shaking.

Microbundle supports multiple entry points for different distributions, eg.
`main` for CJS and `module` for the ESM. For ESM and CJS it bundles your code
but not dependencies. It also supports UMD with bundled dependencies.

### Rollup

[Rollup](https://rollupjs.org/guide/en/) is the bundler that introduces tree
shaking. It is able to do static code analysis and include only named imports
into the bundle. It's complicated to use and you should therefore consider using
it in its `@pika/pack` or Microbundle incarnation. Rollup will load an ESM
version of a package when the `module` field is present.

### Webpack

Technically you can use Webpack for
[authoring libraries](https://webpack.js.org/guides/author-libraries/). If you
consider Webpack you should probably choose Rollup instead. While it caught up
with Rollup's features, such as tree shaking, it lacks other features such as
support for different distributions.