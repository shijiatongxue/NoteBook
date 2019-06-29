# 概述
Proxy可以理解为在目标对象架设一个“拦截”层，外界对该对象的访问必须先通过这层拦截。

在[33-defineProperty]()中，知道了通过Obeject.defineProperty()方法可以监听对象内部的属性变化，但是只能监听get()和set()变化。

Proxy除了可以监听上面两种方法外，还可以监听更多的方法。
# 语法
```js
let p = new Proxy(target, handler);
```
参数： 
- target：用Proxy包装的目标对象；
- handler：一个对象，其属性是当执行一个操作时定义代理的行为函数。
# Proxy实例的方法
/未完待续/