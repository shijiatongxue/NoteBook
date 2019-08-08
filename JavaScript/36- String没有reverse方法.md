# 如何把字符串反转？
有点奇怪的是，字符串没有反转方法。自己写一个函数实现反转功能。

```js
function reverse(str){
    if (typeof str === 'string'){
        return str.split('').reverse().join('');
    }
    console.log('请输入一个合法的字符串');
}
```

# 给String原型加上反转字符串函数

```js
String.prototype.reverse = function(){
    return this.split('').reverse().join('');
}
```