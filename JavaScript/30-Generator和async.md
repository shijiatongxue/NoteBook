# Generator和async

## Generator

整个Generator函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方都用yield语句注明。

```js
function* gen(x){
  var y = yield x + 2;
  return y;
}

var g = gen(1);
g.next(); // { value: 3, done: false}
g.next(); // { value: undefined, done: true}
```

## async

ES2017标准引入了async函数，使得异步任务变得更加方便。它是Generator函数的语法糖。

依次读取两个文件：

```js
// Generator
var gen = function* () {
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toStrinig());
}
```

```js
// async
var asy = async function(){
  var f1 = await readFile('/etc/fstab');
  var f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toStrinig());
}
```

async函数将Generator函数的星号*替换成了async，将yield替换成await。

async对Generator的改进体现在一下几点。

- 内置执行器：完全不像Generator函数需要调用next方法或者适用co模块才能真正执行并得到最终结果。

- 更好的语义：async和await比起*和yield语义更加清楚。

- 更广的适用性：async函数的await命令后面，可以使Promise对象和原始类型的值（数值、字符串和布尔值，但这时等用于同步操作）。

- 返回值是Promise：async函数的返回值是Promise对象，这比Generator函数返回的值是Iterator对象方便。

```js
async function f(){
  return 'hello world';
}

f().then(v => {
  console.log(v);
})

// "hello world"
```

async函数返回一个Promise对象，它内部的return返回值会成为then方法回调函数的参数。

```js
// 错误获取

async function f(){
  throw new Error('出错了');
}

f().then(
	v => console.log(v),
  e => console.log(e);
)

// Error: 出错了
```

async函数内部抛出错误会导致返回的Promise对象变为reject状态。抛出的错误对象会被catch方法回调函数收到。

---

参考：阮一峰 ES标准入门





