Reflect对象与Proxy对象一样，也是ES6为了操作对象而提供的新的API。

设计目的：
- 将Obeject对象的一些明显属于语言内部的方法放到Reflect对象上。
- 修改某些Object方法的返回结果，让其变得更合理。
```js
if(Reflect.defineProperty(target, property, attributes)){
    // success
}else{
    // failure
}
```
- 让Object操作都变成函数行为。
```js
// 旧写法
'assign' in Object // true

// 新写法
Reflect.has(Object, 'assign'); // true
```

- Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法。

/未完待续/