# let命令
- 只在代码块内有效
```js
{
    let a = 10;
    var b = 1;
}
a // ReferenceError: a is not defined.
b // 1
```
- let声明的变量用在for循环时只在本轮有效

在这里，每一轮的变量i都是重新声明的，之所以可以获取上一轮变量的值，是因为JavaScript引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算。
```js
var a = [];
for (let i = 0; i < 10; i++){
    a[i] = function(){
        console.log(i)
    };
}
a[6](); // 6
```
- for循环变量声明作用域和循环体作用域

设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```js
for (let i = 0; i < 3; i++){
    let i = 'abc';
    console.log(i);
}
// abc
// abc 
// abc
```
- 不存在变量提升

let声明的变量一定要在声明之后引用，否则会报错。
```js
// var的情况
console.log(foo); // 输出undefined
var foo = 2; 

// let的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
- 暂时性死区

只要在块级作用域内存在let命令，它所声明的变量就“绑定”这个区域，不再受外部影响。
```js
var tmp = 123;

if (true){
    tmp = 'abc'; // ReferenceError
    let tmp;
}
```
⬆ 在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上称为“暂时性死区”。

- 隐蔽的死区

参数x的默认值等于另一个参数y，而此时y还没有声明，属于“死区”。
```js
function bar(x=y, y=2){
    return [x, y]
}

bar(); // 报错
```
- 不允许重复声明

let不允许在相同作用域内重复声明同一个变量。
```js
// 报错
function(){
    let a = 10;
    var a = 1;
}

// 报错 
function(){
    let a = 10;
    let a = 1;
}

// 报错
function fc(arg){
    let arg;
}
```

↑ 另外可以看出，函数参数作用域和函数体作用域是一个作用域。而参数是相当于函数的局部变量。