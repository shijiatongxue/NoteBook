# 简介
Object.defineProperty()方法会直接在一个对象上定义一个新属性或，或者修改一个对象的现有属性，并返回这个对象。

# 语法
```js
Object.defineProperty(obj, prop, descriper);
```
# 属性描述符
属性描述符分为两种：
- 数据描述符
- 存取描述符

**存取描述符**同时具有以下可选键：
- get：获取对象的属性时，get方法会执行，方法执行时没有参数传入（默认传参this）。
- set：当属性值修改时触发这个方法。该方法接收唯一的参数，就是新的属性值。

```js
var obj = {}, value = null;
Object.defineProperty(obj, 'num', {
    get: function(){
        console.log('get方法已调用');
        return value;
    }
    set: function(newValue){
        console.log('set方法已调用');
        value = newValue;
    }
});

obj.num = 1; // set方法已调用
obj.num; // get方法已调用 // 1
```

可以看出，通过set()方法和get()方法，我们可以监听对象内部的变化。

封装一下：
```js
function Pack(){
    var value = null;
    var pack = [];

    Object.defineProperty(this, 'num', {
        get: function(){
            console.log('调用了get方法');
            return value;
        }
        set: function(newValue){
            console.log('调用了set方法');
            value = newValue;
            pack.push({val: value});
        }
    });

    this.getPack = function(){return pack;};
}

var aPack = new Pack();
aPack.num; // 调用了get方法
aPack.num = 1; // 调用了set方法
aPack.num = 12; // 调用了set方法
console.log(aPack.getPack()); // [{val: 1}, {val: 12}]
```