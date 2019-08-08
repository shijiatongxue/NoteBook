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

# import命令
```js
// main.js
import {firstName, lastName, year} from './profile';

function setName(ele){
    ele.textContent = firstName + ' ' + lastName;
}
```

注意：
- import命令有提升效果，会提升到整个模块的头部并首先执行；
- 由于import是静态执行，所以不能使用表达式和变量；

# 模块的整体加载
```js
import * as circle from './circle';
```

# export default命令
```js
// export-default.js
export default function(){
    console.log('foo');
}
```
其他模块在加载该模块时，import命令可以为该匿名函数指定任意名字。
```js
import customName from './export-default';
customName(); // 'foo'
```

注意：
- 这时import命令后面不使用大括号；
- 一个模块只能有一个默认输出，因此export default命令只能使用一次。所以import命令后面才不用加大括号，因为只能对应一个方法；