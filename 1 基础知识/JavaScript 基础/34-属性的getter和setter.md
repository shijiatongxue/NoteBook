# 属性的getter和setter

## 对象的属性

对象的属性分为两种：

- 数据属性
- 访问器属性

访问器属性本质上是获取和设置值的函数，但从外部代码看像常规属性。

```js
let obj = {
  get propName() {
  	// getter
  }
  set propName() {
  	// setter
  }
}
```

## 例子

```js
// 在获取到fullname的时候打印信息
let user = {
  name: 'John',
  surname: 'Smith',
  get fullName() {
 		console.log('get fullname');
    return `${this.name} ${this.surname}`;
  }
}

user.fullname;
// get fullname
// "John Smith"
```

这时，fullname只有getter，如果赋值，将不会生效。现在可以为fullname设置一个setter。

```js
let user = {
  name: 'John',
  surname: 'Smith',
  get fullName() {
 		console.log('get fullname');
    return `${this.name} ${this.surname}`;
  },
  
  set fullname(value) {
  	[this.name, this.surname] = value.split(' ');
  }
}
```

## 访问器的描述符

对于访问器属性，没有value和writable，但是有get和set函数。

- get：一个没有参数的函数，读取属性式调用
- set：带有一个参数的函数，属性被设置时调用
- enumerable
- configurable

使用denfineProperty创建fullName的访问器，可以使用get和set来传递描述符：

```js
Object.defineProperty(user, 'fullName', {
  get() {
  	return `${this.name} ${this.surname}`;
  },
  set(value) {
  	[this.name, this.surname] = value.split(" ");
  }
})
```

---

参考：

JavaScript.Info