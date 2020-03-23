# 模块

## 模块简介

模块仅仅是一个文件，一个脚本而已。

以下是JavaScript社区发明的将代码组织委员模块——一种可以按需加载的库：

- AMD：最古老的模块化系统，最开始应用在require.js中。
- CommonJS：为Node.js创建的模块化系统。

- UMD：另一个模块化系统，建议作为通用的模块化系统，它与AMD和CommonJS都是兼容的。

## export

```js
// 导出变量、函数和类
export let name1, name2
export function fc(){}
export class ClassName(){}

// 导出列表
export {name1, name2, name3}

// 导出为
export {variable1 as name1}

// 默认导出
export default expression
export default function(){}
```

## import

```js
// 导入变量列表
import {sayHi, sayBye} from './say.js'
sayHi('John');

// 导入长列表
import * as say from './say.js'
say.sayHi('John');

// 导入为
import {sayHi as hi, sayBye as Bye} from './say.js'
```

## 默认导入/导出

默认导出使得一个模块只做一件事。

默认导出要求下列的export和import语句：

1. export default放在模块主导出之前；
2. import导入时不使用花括号。

```js
// user.js

export default class User{
  ...
}
```

```js
// main.js
import User from './user.js'

// 或
// import {default as x} from './user.js'
```

注意：

- 每个文件只有一个默认导出。

- 另外需要注意的是，命名导出必须具有名称，而export default可能是匿名的。

## Default别名

如果我们已经声明了一个函数，然后导出它（和定义分开）：

```js
function sayHi(){
  
}

export {sayHi as default}
// 等用于
// export default sayHi
```

如果一个文件使用了默认导出和命名导出，如何导入？

```js
// 📁 user.js
export default class User {
  constructor(name) {
    this.name = name;
  }
}

export function sayHi(user) {
  alert(`Hello, ${user}!`);
}
```

```js
// 📁 main.js
import {default as User, sayHi} from './user.js';
```

或这样导入：

```js
// 📁 main.js
import * as user from './user.js';

let User = user.default;
new User('John');
```

## 总结

谨慎使用默认导出，因为默认导入时可以给导入变量随意赋值，不好维护。

---

参考：JavaScript.Info