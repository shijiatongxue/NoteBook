# 1 ECMAScript6简介
ECMAScript6.0在2015年6月发布，它的目标是使得JavaScript语言可以用来编写复杂的大型应用程序，称为企业级开发语言。

## 相关概念
- Node.js：JavaScript的运行环境
- Babel转码器：可以将ES6代码转码为ES5代码
- Traceur转码器：Google公司的转码器，可以将ES6代码转为ES5代码
- Mocha：一个测试框架
- ESLint：用于静态检查代码的语法和风格
# 2 使用Babel
##  配置.babelrc

- 配置前文件内容：
```json
{
    "presets": [],
    "plugins": []
}
```
- 输入node代码：
```bash
npm install --save-dev babel-preset-latest
npm install --save-dev babel-preset-react
npm install --save-dev babel-preset-stage-2
```
- 然后将这些规则加入.babelrc：
```json
{
    "presets": [
        "latest",
        "react",
        "stage-2"
    ],
    "plugins": []
}
```
## 命令行转码babel-cli
- 安装命令如下
```bash
npm install --global babel-cli
```
- 基本用法
```bash
# 转码结果输出到标准输出
babel example.js

# 转码结果写入一个文件
babel example.js -o compiled.js

# 整个目录转码
babel src -d lib

# -s 参数生成source map文件
babel scr -d lib -s
```
## 将babel安装到项目中
这样可以支持不同的项目使用不同版本的Babel。
```bash
# 安装
npm install --save-dev babel-cli
```
然后改写package.json：
```json
{
    "devDependencied":{
        "babel-cli": "^6.0.0"
    },
    "scripts":{
        "build": "babel src -d lib"
    },
}
```
转码的时候执行下面命令：
```bash
npm run build
```
## babel-node
它是babel-cli自带的ES6命令行环境。
```bash
$ babel-node
> (x => x*2)(1)
2
```
babel-node也可以直接运行ES6脚本。把上面的命令放入es6.js，然后直接运行。
```bash
$ babel-node es6.js
2
```
## babel-polyfill
Babel默认只转换新的JavaScript句法，而不转换新的API。如Promise、Set、Maps等全局对象，以及定义在全局对象上的一些方法（比如Object.assign）都不会转码。

安装命令如下：
```bash
npm install --save babel-polyfill
```
然后，在脚本头部，加入如下一行代码：
```javascript
import 'babel-polyfill';
// 或者
require('babel-polyfill');
```
# 3 let和const命令
## let命令
- let声明的变量，只在代码块内有效。
- 当let与for循环同时使用时，let声明的值只在本轮有效；
- let声明的变量，没有变量提升；
- let声明的变量，存在“暂时性死区”，即使外部有同名变量，在内部没有声明之前，该变量就是不可以用；
- 不允许重复声明；
## 为什么需要块级作用域？
没有块级作用域出现的不合理场景：
- 内层变量可能会覆盖外层变量
- 用来计数的循环变量泄露为全局变量

块级作用域可以声明函数吗？

答：可以，但在块级作用域声明函数，函数声明类似于let。但是实际情况是类似var声明的变量，会提升到全局作用域或函数作用域的头部，同时还会提升到块级作用域头部。这样做是为了兼容老版本的代码。

考虑到环境导致的行为差异太大，应该避免在块级作用域声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

## const命令
const命令声明一个只读的常量。一旦声明，它的值就不可改变。

- 只声明不赋值会报错；
- const声明的值只在块级作用域内有效；
- const声明的常量也不提升，同样存在暂时性死区，只能在声明的后面使用；
- 与let一样，不可以重复声明；
## 本质
const实际上保证的，不是变量的值不改动，而是变量指向的内存地址不得改动。

对于对象，变量指向的内存地址，保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量时必须非常小心。

## ES6声明变量的6中方法
前两种为ES5中的方法，后面4种是新加的方法。
- var
- function
- let
- const
- import
- class
## 顶层对象的属性
ES5中，顶层对象的属性与全局变量是等价的。

这样的设计带来了几个很大的问题：

- 在不经意间创建了全局变量；
- 顶层对象的属性是到处可读写的；
- 无法在编译时就报出变量未声明的错误，只有在运行时才知道；

ES6中，let、const、class声明的全局变量，不属于顶层对象的属性。也就是说，从ES6开始，全局变量逐渐与顶层对象的属性脱钩。

# 4 Symbol
Symbol引入的原因是，解决属性名冲突问题。

ES6引入了一种新的基本数据类型Symbol，表示独一无二的值。

```javascript
let a = Symbol();
```

Symbol值通过Symbol函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来的字符串，一种是新增的Symbol类型。

注意：Symbol函数不能使用new命令，否则会报错。因为Symbol值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

Symbol可以接收一个字符串参数，表示对Symbol实例的描述。主要是为了在控制台显示，或者转化为字符串时比较容易区分。

```javascript
let obj = {
    toString: function(){
        retrun "abc";
    }
}
const sym = Symbol(obj);
sym // => Symbol(abc)
```

如果Symbol接收的参数是一个对象，则会调用对象的toString方法，转化为字符串，然后才生成一个Symbol值。

```javascript
let b = Symbol("b");
let c = Symbol("b");

b === c; // => false
```

注意：传入的参数只是一个描述，即使相同的参数生成的Symbol值也是不同的。
## 对象的属性
当Symbol作为对象的属性时，不可以使用点赋值。
```javascript
let obj = {};
let name = Symbol();
let age = Symbol();

obj[name] = "Shi";
obj.age = 23; // 错误写法

// 写法2 
let obj2 = {
    [name]: "Shi",
    [age]: 23
}
```
## 属性名的遍历
- Object.getOwnPropertySymbols()：返回Symbol键；
- Reflect.ownKeys()：返回所有类型键名。
## Symbol.for()和Symbol.keyFor()
创建相同的Symbol：
```javascript
let name1 = Symbol.for("shi");
let name2 = Symbol.for("shi");
name1 === name2; // => true
```

# 4 Set和Map数据结构
## Set
```javascript
const s =  new Set();
[2,3,4,5,2,2].forEach(x => s.add(x));

for (let i of s){
    console.log(i);
}
// => 2 3 4 5
```

去除数据重复元素：

```javascript
[...new Set(Array)];
```
## Set属性和方法
- Set.prototype.constructor
- Set.prototype.size
  
操作集合的方法：
- add(value)
- delete(value)
- has(value)
- clear()
  
遍历集合的方法：
- keys()
- values()
- entries()
- forEach()

## WeakSet
它于Set很相似，但有以下区别：
- WeakSet的成员只能是对象，而不能是其他类型的值；
- WeakSet中的对象都是弱引用，即垃圾回收不考虑WeakSet对该对象的引用。

```javascript
const ws = new WeakSet();
```
## Map
为了解决对象的属性只能是字符串的缺陷，ES6提供了Map数据结构。

Map的键不限于字符串，各种类型的值都可以当作键。

```javascript
const m = new Map();
const o = {p : 'Hello World!'};

m.set(o, "content");
m.get(o); // => "content"
m.has(o); // => true
m.delete(o); // => true
m.has(o); // => false
```

## Map属性和方法
- size属性
- set(key, value)
- get(key)
- has(key)
- delete(key)
- clear()

遍历方法
- keys()
- values()
- entries()
- forEach()
## WeakMap
WeakMap与Map区别有两点：

- 首先，WeakMap只接收对象作为键；

- 其次，WeakMap的键所指对象不计入垃圾回收机制。

它只有4个方法，且无法遍历：

- get()
- set()
- has()
- delete()

```javascript
let myElement = document.getElementById("logo");
let myWeakmap = new WeakMap();
myWeakmap.set(myElement, {timesClicked: 0});
myElement.addEventListener('click', function(){
    let logoData = myWeakmap.get(myElement);
    logoData.timesClicked++;
}, false);
```