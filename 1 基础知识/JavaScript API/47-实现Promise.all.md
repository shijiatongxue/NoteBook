# 实现Promise.all

Promise.all接收一个数组作为参数，当这个数组的所有Promise对象变为resolve或有reject状态出现的时候，它才会去调用.then方法，它们是并发执行的。

```js
// 原生promise

let p1 = Promise.resolve(1),
    p2 = Promise.resolve(2),
    p3 = Promise.resolve(3);

Promise.all([p1, p2, p3]).then((res) => {
	console.log(res);
});
```

```js
// 自己实现

function myPromiseAll(promises) {
  return new Promise(function(resolve, reject) {
  	let count = 0;
    let len = promises.length;
    let res = new Array(len);
   	
    for (let i = 0; i < len; i++) {
    	Promise.resolve(promises[i]).then((value) => {
     		count++;
       	res[i] = value;
      	if (count === len) {return resolve(res);}
      }).catch((reason) => {
        return reject(reason);
      })
    }
  })
}
```

---

参考：[JayJunG](https://juejin.im/book/5c90640c5188252d7941f5bb/section/5c9065385188252da6320022)

