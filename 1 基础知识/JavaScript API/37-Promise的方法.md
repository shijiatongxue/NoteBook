# Promise的方法

## Promise.prototype.then()

Promise实例具有then方法，它的作用是为Promise实例添加状态改变时的回调函数。then方法的第一个参数是Resolved状态的回调函数，第二个参数（可选）是Rejected状态的回调函数。

then方法返回的是一个新的Promise实例。因此可以采用链式写法。

```js
getJSON("/posts.json").then(function(json){
  return json.post;
}).then(function(post){
  // ...
});
```

```js
// 箭头函数写法
getJSON("/post/1.json").then(
 post => getJSON(post.commentURL)
).then(
	comments => console.log("Resolved：", comments),
  err => console.log("Rejected：", err)
)
```

## Promise.prototype.catch()

Promise.prototype.catch()方法是.then(null, rejectction)的别名，用于指定发生错误时的回调函数。

```js
p.then(val => console.log('fulfiled:', val))
	.catch(err => console.log('rejected:', err));

// 等价于

p.then(val => console.log('fulfiled:', val))
	.then(null, err => console.log('rejected:', err));
```

一般来说，不要在then方法中定义Rejected状态的回调函数（即then的第二个参数），而应总是使用catch方法。

原因是catch方法可以捕获前面then方法执行中的错误，也更接近同步的写法（try/catch）。

## Promise.all()

Promise.all()方法用于将多个Promise实例包装成一个新的Promise实例。

```js
var p = Promise.all([p1, p2, p3]);
```

Promise.all方法接收一个数组（或具有Iterator接口，且返回的每个成员都市Promise实例）作为参数，p1、p2、p3都是Promise对象的实例；如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为Promise实例，再进一步处理。

p的状态由p1、p2、p3决定，分为两种情况：

- 三者全为Fulfilled，p的状态才会变成Fulfilled，此时三者的返回值组成一个数组，传递给p的回调函数；
- 只要三者有一个被Rejected，p的状态就变成Rejected，此时第一个被Rejected的实例的返回值会传递给p的回调函数。

例子：

```js
var promises = [1, 2, 3].map(id => getJSON('/post/' + id + ".json"));

Promise.all(promises).then(funciton(posts){
	// ...
	}).catch(function(reason){
  // ...
});
```

## Promise.race()

与Promise.all类似，Promise.race方法同样是将多个Promise实例包装成一个新的Promise实例。

```js
var p = Promise.race([p1, p2, p3]);
```

上面的代码中，只要p1、p2、p3中有一个实例率先改变状态，p的状态就跟着改变。那么率先改变的Promise实例的返回值就传递给p的回调函数。

## Promise.resolve()

有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用。

```js
var jsPromise = Promise.resolve($.ajax('/1.json'));
```

Promise.resolve等价于下面的写法：

new Promise(resolve=>resolve(‘foo’));

Promise.resolve方法的参数分为以下4种情况：

- 参数是一个Promise实例

如果参数是一个Promise实例，那么Promise.resolve将不做任何修改，原封不动地返回这个实例。

- 参数一个thenable对象

thenable对象是指具有then方法的对象，比如下面这个对象。

```js
let thenable = {
  then: function(resolve, reject) {
  	resolve(42);
  }
};
```

Promise.resolve方法会将这个对象转为Promise对象，然后立即执行thenable对象的then方法。

```js
let thenable = {
  then: function(resolve, reject) {
  	resoleve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value){
  console.log(value); // 42
})
```

上面的代码中，thenable对象的then方法执行后，对象p1的状态就变为resolved，从而立即执行最后的then方法指定的回调函数，输出42。

- 参数不是具有then方法的对象或根本不是对象

如果参数是一个原始值，或者是一个不具有then方法的对象，那么Promise.resolve方法返回一个新的Promise对象，状态为Resolved。

```js
var p = Promise.resolve('Hello');

p.then(function(s){
  console.log(s)
});
// Hello
```

- 不带有任何参数

同上，直接返回一个Resolved状态的Promise对象。

```js
var p = Promise.resolve();

p.then(function(){
  // ...
});
```

## Promise.reject()

Promise.reject(reason)也会返回一个新的Promise实例，状态为Rejected。

```js
var p = Promise.reject('出错了');
// 等同于
var p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function(s){
  console.log(s)
});
// 出错了
```

## 两个有用的附加方法

### done

无论Promise对象的回调链以then方法还是catch方法结尾，只要最后一个方法抛出错误，都有可能被捕捉到。为此，我们可以提供一个done方法，它总是处于回调链的尾端，保证抛出任何可能出现的错误。

```js
asyncFunc()
	.then(f1)
	.catch(f2)
	.then(f2)
	.done();
```

### finally

finally方法用于指定不管Promise对象最后状态如何都会执行的操作。它与done方法最大的区别在于，它接收一个普通的回调函数作为参数，该函数不管怎样都必须执行。

下面是个例子，服务器使用Promise处理请求，然后使用finally方法关掉服务器：

```js
server.listen(0)
	.then(function(){
  	// run test
	})
	.finally(server.stop);
```

---

参考：《ES6标准入门》





