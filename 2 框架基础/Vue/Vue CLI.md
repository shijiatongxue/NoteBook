# Vue CLI

它基于 webpack 构建，并带有合理的默认配置。

- CLI

CLI（@vue/cli）是一个全局安装的npm包，提供了终端里的 ```vue``` 命令。它可以通过 ```vue create ```快速创建一个新项目的脚手架，或者直接通过 ```vue serve ``` 构建新想法的原型。

- CLI服务

CLI服务构建于webpack和webpack-dev-serve之上。它包含：

1. 加载其他CLI插件的核心服务；
2. 一个针对绝大部分应用优化过的内部webpack配置；
3. 项目内部的vue-cli-service命令，提供service、build和inspect命令。

CLI服务（@vue/cli-service）是一个开发环境依赖。它是一个npm包，局部安装在每个 ```@vue/cli``` 创建的项目中。

- CLI插件

CLI插件是项目的Vue项目提供可选功能的npm包，例如Babel/TypeScript转译、ESLint集成、单元测试和end-to-end测试等。

## 安装

```bash
npm install -g @vue/cli
```

安装完成后，可以在命令行访问 ```vue``` 命令：

```bash
vue --version
# 3.10.0
```

## 基础

### 创建一个项目

- vue create

```bash
vue create hello-world

# 取代vue-cli 2.x
# vue init
```

- 使用图形化界面

```js
vue ui
```

上述命令行会打开一个浏览器窗口，并以图形化界面将你引导至项目创建的流程。

![](ui-new-project.png)

### 插件和预设配置

- 插件

Vue CLI使用了一套基于插件和架构。如果你查阅一个新创建项目的package.json，就会发现依赖都是以``` @vue/cli-plugin ``` 开头的。插件可以修改内部webpack配置，也可以向 ``` vue-cli-service ```注入命令。

- 在现有项目中安装插件

```js
vue add eslint
```

> vue add 的设计意图是为了安装和调用Vue CLI插件。对于普通的npm包而言，你仍然需要使用包管理器。

这个命令将 ``` @vue/eslint ``` 解析为完整的包名 ``` @vue/cli-plugin-eslint ```， 然后从 npm 安装它，调用它的生成器。

```js
# 等价写法
vue add cli-plugin-eslint
```

### CLI服务

- 使用命令

在一个Vue CLI项目中， @vue/cli-service 安装了一个名为 ``` vue-cli-service ``` 的命令。

这是你使用默认preset的项目的 ``` package.json ```:

```json
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build"
  }
}
```

你可以通过npm或yarn调用这些script：

```bash
npm run serve
# OR
yarn serve
```

或使用npx（最新版的npm应该已经自带），也可以直接这样调用：

```bash
npx vue-cli-service serve
```

> 也可以通过 ```  vue ui ``` 命令使用GUI运行更多的特性脚本。

- vue-cli-service serve

```
用法：vue-cli-service serve [options] [entry]

选项：

  --open    在服务器启动时打开浏览器
  --copy    在服务器启动时将 URL 复制到剪切版
  --mode    指定环境模式 (默认值：development)
  --host    指定 host (默认值：0.0.0.0)
  --port    指定 port (默认值：8080)
  --https   使用 https (默认值：false)
```

``` vue-cli-service ```命令会启动一个开发服务器并附带开箱即用的模块热重载。

- vue-cli-service build

```
用法：vue-cli-service build [options] [entry|pattern]

选项：

  --mode        指定环境模式 (默认值：production)
  --dest        指定输出目录 (默认值：dist)
  --modern      面向现代浏览器带自动回退地构建应用
  --target      app | lib | wc | wc-async (默认值：app)
  --name        库或 Web Components 模式下的名字 (默认值：package.json 中的 "name" 字段或入口文件名)
  --no-clean    在构建项目之前不清除目标目录
  --report      生成 report.html 以帮助分析包内容
  --report-json 生成 report.json 以帮助分析包内容
  --watch       监听文件变化
```

``` vue-cli-service build ``` 会在 ``` dist/ ``` 目录产生一个可用于生产环境的包，还有JS/CSS/HTML的压缩，和为更好的缓存而做的自动vendor chunk splitting。

- vue-cli-service inspect

```
用法：vue-cli-service inspect [options] [...paths]

选项：

  --mode    指定环境模式 (默认值：development)
```

你可以使用 ```vue-cli-service inspect ``` 来审查一个Vue CLI项目的webpack config。

## 开发

### 浏览器的兼容性

- browserlist

package.json文件里的browserlist字段或一个单独的.broserlistrc文件，指定了项目的目标浏览器的范围。这个值会被@babel/preset-env和Autoprefixer用来确定需要转译的JavaScript特性和需要添加的CSS浏览器前缀。

以下几个方式配置都是可以的：

**package.json**

```json
{
  "private": true,
  "dependencies": {
    "autoprefixer": "^6.5.4"
  },
  "browserslist": [
    "last 1 version",
    "> 1%",
    "IE 10"
  ]
}
```

**.browserlistrc**

```
# Browsers that we support

last 1 version
> 1%
IE 10 # sorry
```

[browserlist文档](https://github.com/browserslist/browserslist)

### HTML和静态资源

#### HTML

- 插值

```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico"
```

- Preload

<link rel="preload">是一种resource hint，用来指定页面加载后很快被用到的资源，所以在页面加载的过程中，我们希望在浏览器开始主题渲染之前今早preload。

默认情况下，一个Vue CLI应用会为所有初始化渲染需要的文件自动生成preload提示。

- Prefetch

<link rel="prefetch">是一种resource hint，用来告诉浏览器在页面加载完成后，利用空闲时间提前获取用户未来可能会访问的内容。

默认情况下，一个Vue CLI应用会为所有作为async chunk生成的JavaScript文件（通过动态 ```import()``` 按需code splitting的产物）自动生成prefetch提示。

- 构建一个多页应用

不是每个应用都需要是一个单页应用。Vue CLI支持使用 ``` vue.config.js ``` 中的 ``` pages ``` 选项构建一个多页面应用。

#### 处理静态资源

静态资源可以通过两种方式进行处理：

1. 在JavaScript被导入或在template/CSS中通过相对路径被引用。这类引用会被webpack处理。
2. 放置在 ```public``` 目录下或通过绝对路径被引用。这类资源将会直接被拷贝，而不会经过webpack的处理。

- ```public``` 文件夹

任何放在这个文件夹的静态资源都会被简单的负值，而不经过webpack。你需要通过绝对路径来引用它们。

然而，将资源作为你的模块依赖图的一部分导入，这样它们会通过webpack的处理并获得如下好处：

1. 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求。
2. 文件丢失会直接在编译时报错，而不是到了用户端才产生404错误。
3. 最终生成的文件名包含了内容哈希，因此不必担心浏览器会缓存它们的老版本。

### CSS相关

Vue CLI项目天生支持PostCSS、CSS Modules和包含Sass、Less、Stylus在内的预处理器。

- 引用静态资源

所有编译后的CSS都会通过css-loader来解析其中的 ```url()``` 引用，并将这些引用作为模块请求来处理。

- 预处理器

初始化项目逇时候可以选择，没有选择会默认安装所有。也可以后期手动安装。

```bash
# Sass
npm install -D sass-loader sass

# Less
npm install -D less-loader less

# Stylus
npm install -D stylus-loader stylus
```

- PostCSS

Vue CLI内部使用了PostCSS。可以通过 ```.postcssrc``` 或 ```vue.config.js``` 中的 ```css.loaderOptions.postcss``` 配置postcss-loader。

详细信息请查看[文档]([https://cli.vuejs.org/zh/guide/css.html#css-%E7%9B%B8%E5%85%B3](https://cli.vuejs.org/zh/guide/css.html#css-相关))。

### webpack相关

- 简单的配置方式

调整webpack配置就是在 ```vue.config.js``` 中的 ``` configWebpack``` 选项提供一个对象：

```js
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```

该对象将会被webpack-merge合并入最终的webpack配置。

> 警告：有些webpack选项是基于vue.config.js中的值设置的，所以不能直接修改。这样做是因为vue.config.js中的值会被用在配置的多个地方，以确保所有的部分都能正常工作在一起。

如果你需要基于环境有条件地配置行为，或者想要直接修改配置，那就换成一个函数。该方法的第一个参数会受到已经解析好的配置。在函数内，你可以直接修改配置，或者返回一个将会被合并的对象。

```js
// vue.config.js
module.exports = {
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
  }

```

### 环境变量和模式

- 模式

模式是Vue CLI项目中一个重要的概念。默认情况下，一个Vue CLI项目有三个模式：

1. development模式用于vue-cli-service serve
2. production模式用于vue-cli-service build和vue-cli-service test:e2e
3. test模式用于vue-cli-service test:unit

你可以通过传递--mode选项参数为命令行覆写默认的模式。例如，如果你想要在构建命令中使用开发环境变量，请在你的package.json脚本中加入：

```json
"dev-build": "vue-cli-service build --mode development",
```

---

参考：[Vue CLI](https://cli.vuejs.org/zh/guide/)