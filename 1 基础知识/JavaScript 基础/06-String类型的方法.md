# String类型的方法

## 字符方法

- charAt()
- charCodeAt()
- []

```js
let str = 'hello';
str[0]; // 'h'
```

注意，方括号表达式在IE7以及更低版本会返回undefined。

## 字符串操作方法

- concat()

- slice()
- substr()
- substring()

```js
// concat()返回新串
let str = 'hello';
let res = str.concat(' world');
str; // 'hello'
res; // 'hello world'
```

```js
// slice(start[, end])
// substr(start[, num])
// substring(start[, end])

let str = 'hello world';
let len = str.length; 
len; // 11

// slice会把传入的负值与字符串的长度相加
str.slice(0, -1); // 'hello worl'，相当于str.slice(0, -1 + 11)
str.slice(-3, -1); // 'rl'，相当于str.slice(8, 10)

// substr会将负的第一个参数加上字符串的长度，负的第二个参数转换为0
str.substr(-3, 2); // 'rl'，相当于str.substr(8, 2)
str.substr(-3, -1); // ''，相当于str.slice(8, 0)

// substring会把所有负的参数都转换为0
str.substring(-3, 3); // 'hel'
str.substring(3, -1); // 'hel'，它还会把较小的数作为开始位置，相当于str.substring(0, 3)
```

## 字符串位置方法

- indexOf()
- lastIndexOf()

如果找到返回子字符串的位置，没有找到返回-1。

## trim方法

```js
// 这个方法不修改原来的串
let str = '   hello   ';
str.trim(); // 'hello'
str; // '   hello   '
```

## 字符串大小写转换方法

- toLowerCase()
- toUpperCase()

## 模式匹配方法

- match()

- search()
- replace()
- split()

```js
// match()
let text = 'cat, bat, sat, fat';
let pattern = /.at/g;
text.match(pattern); // ["cat", "bat", "sat", "fat"]

// search()
text.search(/at/); // 1

// replace()
text.replace('at', "ond"); // "cond, bat, sat, fat"
text.replace(/at/g, "ond"); // "cond, bond, sond, fond"

// split()
text.split(','); // ["cat", " bat", " sat", " fat"]
text.split(',', 2); // ["cat", " bat"]
text.split(/,| /); // ["cat", "", "bat", "", "sat", "", "fat"]
```

## localCompare()方法

## fromCharCode()方法 

接收一个或多个字符的编码，然后将它们转换成一个字符串。

```js
String.fromCharCode(104, 101, 108, 108, 111); // 'hello'
```



