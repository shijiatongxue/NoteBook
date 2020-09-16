# 简单介绍

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时。

## 与浏览器的区别

不包括 BOM，DOM，没有 window 全局对象。

使用 CommonJS 模块化系统，浏览器使用 import。

## 编译性语言与解释性语言

- 编译型语言：在程序执行之前，需要一个专门的编译过程，把程序编译成机器语言文件。因为翻译只做了一次，运行时不需要翻译，所以编译型语言的程序执行效率高。
- 解释性语言：不需要编译，在运行程序的时候才翻译。比如 Java 语言，专门有一个解释器能够直接执行 Java 程序，每个语句都是执行的时候才翻译。因为每次执行都要翻译一次，效率较低。
- 脚本语言：脚本语言是解释性语言。

# 全局变量

- Buffer

- global

- console
- process
- module
- exports
- URL
- URLSearchParams
- __dirname：当前模块目录名
- __filename：当前模块绝对路径。
- require()：用于引入模块，JSON 文件或本地文件。
- setImmediate(callback[, ...args])
- setInterval(callback, delay[, ...args])
- setTimeout(callback, delay[, ...args])
- clearImmediate(immediateObject)
- clearInterval(intervalObject)
- clearTimeout(timeoutObject)

# 模块

- fs
- path
- os
- events
- http
- querystring：编码与解码 URL
- url：处理与解析 URL
- zlib：压缩功能