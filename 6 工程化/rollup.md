# rollup

## 介绍

Rollup 是一个用于 JavaScript 的模块打包器。它将小段代码编译成更大更复杂的东西，比如一个库或应用。它使用标准的 ES 模块引入方式，而不是之前的解决方案，如 CommonJS 和 AMD。Rollup 可以优化 ES 模块，以便更快地在现代浏览器中加载，或输出一种历史模块格式。

## 安装

```bash
npm install --save rollup
```

Rollup 可以通过命令行、文件或 JavaScript API 进行配置。

### 命令行

```bash
# 浏览器

# compile to a <script> containing a self-executing function
rollup main.js --format iife --name "myBundle" --file bundle.js
```

```bash
# Node

# compile to a CommonJS module
rollup main.js --format cjs --file bundle.js
```

```bash
# 两者

# UMD format requires a bundle name
rollup main.js --format umd --name "MyBundle" -- file bundle.js
```

### 配置文件

Rollup 的配置文件是可选的，但是很强大、方便，因此推荐使用。

配置文件是一个 ES 模块，它默认导出配置对象。它可以被命名为 `rollup.config.js`，然后位于项目的根目录。

你也可以在配置文件使用 CJS 模块语法：

```javascript
module.exports = {
  input: 'src/main.js',
  output: {
    file: 'bundle.js',
    format: 'cjs'
  }
};
```

完整的配置列表看这里。[配置列表](https://rollupjs.org/guide/en/#big-list-of-options)

```javascript
// rollup.config.js

export default { // can be an array (for multiple inputs)
  // core input options
  external,
  input, // required
  plugins,

  // advanced input options
  cache,
  inlineDynamicImports,
  manualChunks,
  onwarn,
  preserveModules,
  strictDeprecations,

  // danger zone
  acorn,
  acornInjectPlugins,
  context,
  moduleContext,
  preserveSymlinks,
  shimMissingExports,
  treeshake,

  // experimental
  chunkGroupingSize,
  experimentalCacheExpiry,
  experimentalOptimizeChunks,
  experimentalTopLevelAwait,
  perf,

  output: { // required (can be an array, for multiple outputs)
    // core output options
    dir,
    file,
    format, // required
    globals,
    name,
    plugins,

    // advanced output options
    assetFileNames,
    banner,
    chunkFileNames,
    compact,
    entryFileNames,
    extend,
    footer,
    interop,
    intro,
    outro,
    paths,
    sourcemap,
    sourcemapExcludeSources,
    sourcemapFile,
    sourcemapPathTransform,

    // danger zone
    amd,
    dynamicImportFunction,
    esModule,
    exports,
    externalLiveBindings,
    freeze,
    indent,
    namespaceToStringTag,
    noConflict,
    preferConst,
    strict
  },

  watch: {
    chokidar,
    clearScreen,
    exclude,
    include
  }
};
```

你可以从配置文件导出一个数组，以同时从几个不同不相关的输入构建包，甚至在 `watch` 模式下也可以。

要构建具有相同输入的不同包，需要为每一个输入提供一个输出选项数组：

```javascript
export default [{
  input: 'main-a.js',
  output: {
    file: 'dist/bundle-a.js',
    format: 'cjs'
  }
}, {
  input: 'main-b.js',
  output: [
    {
      file: 'dist/bundle-b1.js',
      format: 'cjs'
    },
    {
      file: 'dist/bundle-b2.js',
      format: 'esm'
    }
  ]
}];
```

## Tree Shaking

除了启用 ES 模块之外，Rollup 还会静态地分析和优化要导入的代码，并排除实际上没有使用的任何代码。这允许你在现有工具和模块的基础上构建，而不需要添加额外的依赖项或增加项目的规模。

例如，使用 CommonJS，整个包或者工具必须被导入。

```javascript
// import the entire utils object with CommonJS
var utils = require( 'utils' );
var query = 'Rollup';
// use the ajax method of the utils object
utils.ajax( 'https://api.example.com?search=' + query ).then( handleResponse );
```

然而，ES 模块管理，不需要引入整个 `util` 对象，我们可以只引入一个 `ajax` 函数：

```javascript
// import the ajax function with an ES import statement
import { ajax } from 'utils';
var query = 'Rollup';
// call the ajax function
ajax( 'https://api.example.com?search=' + query ).then( handleResponse);
```

因为 Rollup 包含最少的内容，所以它产生更轻、更快、更简单的库和应用。由于这种方法基于显示地 `import` 和 `export` 语句，因此它比简单地运行一个自动缩小器来检测编译后的输出代码中未使用的变量要有效地多。

## 兼容性

### 导入 CommonJS

使用插件可以导入已有的 CommonJS 模块。

### 发布 ES 模块

为了确保你的 ES 模块可以与 CommonJS 同时使用，你可以使用 Rollup 把它编译成 UMD 或 CommonJS 格式，然后把`package.json` 文件的 `main` 属性指向编译后的版本 。如果 `package.json` 文件同样有 `module` 域，类似Rollup 和 Webpack 2 的 ES-module-aware 工具会直接导入 ES 模块版本。

更多内容请看[官方文档](https://rollupjs.org/guide/en/)



