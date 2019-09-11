# Object.freeze()和Object.seal()

## Object.freeze()

Object.freeze()方法可以冻结一个对象。冻结一个对象则不能像这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有的属性值。

此外，冻结一个对象后该对象的原型也不能被修改。

```js
const obj = {
  prop: 42
}

Object.freeze(obj);

obj.prop = 33; // 在严格模式抛出异常
obj.prop; // 42
Object.setPrototypeOf(obj, {x: 20}); // TypeError
```

该方法返回传递的对象，而不是创建一个冻结的副本。

```js
// 浅冻结
obj = {
  inner: {}
}

Object.freeze(obj);
obj.inner.a = 'value';
obj.inner.a; // 'value'
```

```js
// 实现深冻结
function deepFreeze(obj){
  let propNames = Object.getOwnPropertyNames(obj);
  propNames.forEach((name) => {
  	let prop = obj[name];
    if (typeof prop === 'object' && prop !== null){
    	deepFreeze(prop);
    }
  });
  return Object.freeze(obj);
}

// 测试
obj = {
  inner: {}
}

Object.freeze(obj);
obj.inner.a = 'value';
obj.inner.a; // 'value' // undefined
```

冻结非对象：

```js
Object.freeze(1); // ES5, TypeError
Object.freeze(1); // ES6, 1
```

## Object.seal()

密封的对象可以改变它们现有的属性，但冻结的对象现有属性是不可变的。

它**阻止添加新属性**并将所有**现有属性标记为不可配置**。当前属性的值**只要可写就可以改变**。

它不会影响从原型链继承的属性，但__proto__属性的值也会不能修改。

```js
const obj = {
  age: 42
}

Object.seal(obj);
obj.age = 33;
obj.age; // 33
```

类似于冻结，密封非对象的效果类似。

---

参考：MDN