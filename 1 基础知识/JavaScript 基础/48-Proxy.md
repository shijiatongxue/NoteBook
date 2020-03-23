# Proxy

## 概述

代理提供了一种机制可以对外界的访问进行过滤和改写。

```js
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
  	console.log(`getting ${key})!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
 		console.log(`setting ${key}!`);
 		return Reflect.set(target, key, value, receiver);
  }
})

// 测试
obj.count = 1;
```

Proxy实际上是重载了点运算符。

> 注意，要使Proxy起作用，必须针对Proxy实例进行操作，而不是针对目标对象进行操作。