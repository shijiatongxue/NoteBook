# 概述
在ES6之前，社区制定了一些模块加载方案，最主要的有CommonJS和AMD两种。前者用于服务器，后者用于浏览器。

ES6模块的设计思想是尽量静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS和AMD模块都只能在运行时确定这些东西。

# export命令
一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果希望外部能读取内部的某个变量，就必须使用export关键字输出该变量。
```js
// profile.js
export var firstName = 'Shi';
export var lastName = 'Jia';
export var year = 2019;
```

或者这样写（推荐）：
```js
// profile.js
var firstName = 'Shi';
var lastName = 'Jia';
var year = 2019;
export {firstName, lastName, year};
```

还可以输出函数和类：
```js
function v1(){}
function v2(){}

export{
    v1,
    v2 as streamV2, // as表示重命名
    v2 as streamLatestVersion // 重命名后可以用不同名字输出两次
}
```

导出单个变量：
```js
var m = 1;
export {m}; // 正确
export {m as n}; // 正确
export m; // 错误
```

注意：

- export语句输出的接口与其对应的值是动态绑定关系；
- export命令可以出现在模块顶层作用域的任意位置，不能在块作用域。