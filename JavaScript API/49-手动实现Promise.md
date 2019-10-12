# 手动实现Promise

```js
// MyPromise
let p = new MyPromise(function(resolve, reject) {
  resolve('hello');
})
```

实现构造函数：

```js
const PENDING = Symbol()
const FULFILLED = Symbol()
const REJECTED = Symbol()

function MyPromise(fn) {
  // 判断fn是否为函数
  if (typeof fn !== 'function') {throw new Error('fn must be a function!')}
  
  let state = PENDING; // 当前状态
  let value = null; // 返回值
  
  function fulfill(res) {
  	state = FULFILLED;
   	value = res;
  }
  
  // 完成时调用
  function resolve(res) {
  	try {
    	fulfill(res);
    } catch (err) {
    	reject(err);
    }
  }
  
  // 失败时调用
  function reject(err) {
  	state = REJECTED;
   	value = err;
  }
  
  fn(resolve, reject);
}
```

实现then方法：

```js
// MyPromise
let p = new MyPromise(function(resolve, reject) {
  resolve('hello');
})

// 调用then方法
p.then(function(val){
  console.log(val);
})
```

```js
function MyPromise(fn) {
	// ...
  
  // 新增
  this.then = function(onFulfill, onReject) {
  	if (state === FULFILLED) {
    	onFulfill(value);
    } else if (state === REJECTED) {
   		onReject(value);
    }
  }
}
```

在Promise使用异步：

```js
// MyPromise
let p = new MyPromise(function(resolve, reject) {
  setTimeout(() => {
  	resolve('hello');
  }, 0)
})

// 调用then方法
p.then(function(val){
  console.log(val);
})
```

但是此时控制台没有打印出hello，原因是MyPromise执行异步代码，而执行then方法的时候，拿到的state状态还是PENDING。这时要在then方法的分支进行状态的判断，当状态是PENDING时进行进一步操作。

```js
const PENDING = Symbol()
const FULFILLED = Symbol()
const REJECTED = Symbol()

function MyPromise(fn) {
  if (typeof fn !== 'function') {throw new Error('fn must be a function!')}
  
  let state = PENDING;
  let value = null;
  // 新增
  let handler = {};
  
  function fulfill(res) {
  	state = FULFILLED;
   	value = res;
    handler.onFulfill(res); // 新增
  }
  
  function resolve(res) {
  	try {
    	fulfill(res);
    } catch (err) {
    	reject(err);
    }
  }
  
  function reject(err) {
  	state = REJECTED;
   	value = err;
    handler.onReject(err); // 新增
  }
  
  this.then = function(onFulfill, onReject) {
  	switch (state) {
        case FULFILLED:
        	onFulfill(value);
       		break;
        case REJECTED:
        	onReject(value);
        	break;
        case PENDING:
        	handler = { onFulfill, onReject }
    }
  }
  
  fn(resolve, reject);
}
```

...未完