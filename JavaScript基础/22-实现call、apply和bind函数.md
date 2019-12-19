# 实现call、apply和bind函数

## call()

call()方法在使用一个指定this值和若干指定的参数值的前提下调用某个函数或方法。

```js
var foo = {
  value:1
};

function bar() {
  cosole.log(this.value);
}

bar.call(foo); // 1
```

注意两点：

1. call改变了this的指向
2. bar函数执行了

```js
// 模拟实现第一步

var foo = {
  vlaue: 1,
  bar: function() {
  	console.log(this.value);
  }
};

foo.bar(); // 1

// 但是不能给对象添加属性
// 使用完之后应该删除

// 步骤：
// 1. 添加函数
// 2. 执行函数
// 3. 删除函数
```

```js
// 第一版
Function.prototype.call2 = function(context) {
  // step: 1
  context.fn = this; // 通过this获取调用的函数
  // step: 2
  context.fn();
  // step: 3
  delete context.fn;
}

// 测试
var foo = {
  value; 1
};

function bar() {
  console.log(this.value);
}

bar.call2(foo); // 1
```

```js
// 第二版，给调用的函数传参

Function.prototype.call2 = function(context, ...val) {
  context.fn = this;
  const result = context.fn(...val);
  delete context.fn;
  return result;
}

var foo = {value: 1};

function bar(name, age) {
  console.log(name);
  console.log(age);
  console.log(this.value);
}

bar.call2(foo, 'shi', 23);
// "shi"
// 23
// 1
```

```js
// 第三版，this可为null
// this为null时，视为指向window

Function.prototype.call2 = function(context, ...val) {
  let context = context || window;
  context.fn = this;
  const result = context.fn(...val);
  delete context.fn;
  return result;
}
```

## apply()

它与call类似，只不过第二个参数是一个数组或类数组对象。

```js
// 把元素附加到数组后面

let array = ['a', 'b'];
let elements = [0, 1, 2];
array.push.apply(array, elements);

console.log(array);
//  ["a", "b", 0, 1, 2]
```

## bind()

bind()方法会创建一个新函数。当这个新函数被调用时，bind()的第一个参数将作为它运行时的this，之后的一序列参数将会在传递的实参前传入作为它的参数。

bind()函数有两个特点：

- 返回一个函数
- 可以传入参数

```js
// 例子

var foo = {value: 1};
function bar() {
  console.log(this.value);
}

var bindFoo = bar.bind(foo); // 返回一个函数
bindFoo();  // 调用函数
// => 1
```

```js
// 模拟实现
Function.prototype.bind2 = function(context, ...val) {
  context.fn = this;
  return function() {
  	const result = context.fn(...val);
    delete context.fn;
    return result;
  }
}
```

---

参考：

[冴语](https://github.com/mqyqingfeng/Blog/issues/12)

黑马教程