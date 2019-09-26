# 实现Promise.race

```js
// 原生promise

let p1 = Promise.resolve(1),
    p2 = Promise.reject(2),
    p3 = Promise.resolve(3);

Promise.race([p1, p2, p3]).then((res) => {
	console.log(res);
}).catch((res) => {
  console.log(res);
});
```

```js
// 自己实现

function myPromiseRace(promises) {
  return new Promise((resolve, reject) => {
  	for (let i = 0; i < promises.length; i++) {
    	promises[i].then((res) => {
      	return resolve(res);
      }).catch((res) => {
      	return reject(res);
      })
    }
  })
}
```



