> 摘录自：https://www.section.io/engineering-education/npm-vs-npx/

NPM 于 2010.01.12 发布，随着 Node.js 的发展，NPM 引入一个叫 NPX 的工具。尽管 NPX 与 NPM 绑定在一起，但是两者对包的处理方式是不同的。

# NPM

NPM 表示 Node Package Manager。在你安装 Node.js 时同时会安装 NPM。NPM 用于安装 Node.js 包，它便于开发者分享和重用开源代码。

# NPX
NPX 是 NPM 包执行器。它首次发布与 2017 年 7 月。NPX 起初和其他 NPM 包一样，可以通过 NPM 安装，现在 NPX 会在你安装 NPM（v5.2.0+） 时同时安装上。

# NPM 如何管理包
使用 NPM 时，安装包有两种方式：

- 局部：安装在项目的 ./node_modules/.bin/ 下。
- 全局：安装在用户环境路径下，`/usr/local/bin`（Linux） 或 `AppData%/npm` （Windows）下。

当你使用 NPM 安装可执行文件时，Node.js 把他们从本地或全局路径链接起来。NPM 并不直接执行一个包。

去执行一个本地安装的包，你需要在 `package.json` `scripts` 模块这样写：

```json
"scripts": {
    "your-package": "your-package-name"
},
```

然后运行：

```bash
npm run your-package
```

或者：

```bash
./node_modules/.bin/your-package-name
```

NPX 执行起来要方便一点。

# 使用 NPX
在使用 NPX 时，你可以执行一个包而无需从本地或者全局安装它。

当一个包已经被安装了，NPX 会搜索包的二进制文件，然后运行它。

如果一个包没有预先安装，NPX 并不会把它安装到你的系统；相反它会创建一个临时的缓存保存这个包的二进制文件。一旦它执行完，NPX 会从你的系统删除这个缓存的二进制文件。

这样的话，你的全局安装目录会保持干净，也可以节省磁盘空间并允许你在需要时执行一个包。它也可以让你在不显示安装一个包的情况下测试包。

以下是一些 NPX 命令：

```bash
which npx
```

如果没有安装的话，可以从全局或者本地安装它。

```bash
npm i -g npx

# or locally

npm i npx
```

执行一个包：

```bash
npx your-package
```

打印帮助信息：

```bash
npx --help
```

# NPX 之于 NPM scripts

- 不需要编辑 package.json
- 可以直接在命令行执行

> 其实 NPX 或 NPM scripts 的适用场景不太一样。NPX 需要记住这些命令（当然你也可以单独开一个文件写上这些命令），而 NPM scripts 写在文件里，直接执行就可以了，这也是 NPM scripts 的优势。