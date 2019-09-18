# Object.getPrototypeOf

Object.getPrototypeOf()方法返回指定对象的原型（内部[[Prototype]]属性的值）。

```js
Object.getPrototypeOf(obj);
```

返回给定对象的原型，如果没有继承属性，则返回null。

```js
var proto = {};
var obj = Object.create(proto);
Object.getPrototypeOf(obj) === proto; // true
```

> 说明，Object.getPrototypeOf(Object)不是Object.prototype。

JavaScript中的Object是构造函数（创建对象的包装器）。

```jd
var obj = new Object();
Object.getPrototypeOf(Object); // f() {[native code]}
Object.getPrototypeOf(Object) === Function.prototype; // true
```

但是如果不是原生的构造函数呢？

- 对于普通的构造函数	

```js
function Student(){}
Object.getPrototypeOf(Student) === Function.prototype; // true
```

- 对于类

```js
class User{}
Object.getPrototypeOf(User) === Function.prototype; // true
```

- 对于继承类

```js
class Student extends User{}
Object.getPrototypeOf(Student) === Function.prototype; // false
```

```js
// 特殊的
Object.getPrototypeOf(Student) === User; // true
Student.__proto__ === User; // true
```

利用Object.getPrototypeOf方法判断一个类是否继承了另一个类。

## 类的prototype属性的__proto__属性

在ES5中，每一个对象都有 `__proto__` 属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和 `__proto__` 属性，因此同时存在两条继承链。

- 子类的 `__proto__`  属性表示构造函数的继承，总是指向父类。
- 子类的 `prototype` 属性 `__proto__`  属性表示方法的继承，总是指向父类的 `prototype` 属性。

```js
class A{}
class B extends A{}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

造成这样的结果是因为类的继承是按照下面的模式实现的。

```js
class A {}
class B {}

// B的实例继承A的实例
Object.setPrototypeOf(B.prototype, A.prototype);

// B的实例继承A的静态属性
Object.setPrototypeOf(B, A);

const b = new B();
```

而Objcet.setPrototypeOf()的实现如下：

```js
Object.setPrototypeOf = function(obj, proto) {
  obj.__proto__ = proto;
  return obj;
}
```

因此可以得到上面的结果：

```js
Object.setPrototypeOf(B.prototype, A.prototype);
// 等同于
B.prototype.__proto__ = A.prototype;

Object.setPrototypeOf(B, A);
// 等同于
B.__proto__ = A;
```

这两条继承链可以这样理解：作为一个对象，子类B的原型（`__proto__`属性）是父类A；作为一个构造函数，子类B的原型（prototype属性）是父类的实例。

---

参考：

MDN

ES6标准入门









