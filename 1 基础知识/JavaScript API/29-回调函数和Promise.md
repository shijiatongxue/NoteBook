# 回调函数和Promise

## 回调函数

所谓回调函数，就是把任务的第二阶段单独写在一个函数里，等到重新执行这个任务时便直接调用这个函数。

读取文件进行处理的操作如下：

```js
fs.readFile('/etc/passwd', 'utf-8', function (err, data){
  if (err) throw err;
  console.log(data);
})
```

readFile的第三个参数就是回调函数，等到操作系统返回/etc/passwd文件后，回调函数才会执行。

## Promise

回调函数本身没有问题，它的问题出现在多个对调函数嵌套上。

```js
fs.readFile(fileA, 'utf-8', function(err, data){
  fs.readFile(fileB, 'utf-8', function(err, data){
  	// ...
  });
});
```

依次读两个文件会出现多重嵌套。因为多个异步操作形成了强耦合，只要有一个操作需要修改，它的上层回调和下层回调函数就都要跟着修改。

Promise~

它不是一种新的语法功能，而是一种新的写法，允许将回调函数的嵌套改写成链式调用。

```js
var readFile = require('fs-readfile-promise');

readFile(fileA)
.then(function(data){
  console.log(data.toString());
})
.then(function(){
  return readFile(fileB);
})
.then(function(data){
  console.log(data.toString());
})
.catch(function(err){
  console.log(err)
});
```

Promise的写法是回调函数的改进，使用then方法以后，异步任务的两段执行更加清楚了。

缺点：

Promise的最大问题是代码冗余，原来的任务被Promise包装之后，无论什么操作，一眼看去都是许多then的堆积，原来的语义变得很不清楚。

---

参考：阮一峰 ES6标准入门





