# 1 数组的增删改查

增删改查就是王道！

## 1.1 创建数组

有两种方式可以创建数组，分别是数组直接量和new()方法。

```js
// 第一种方法
var empty = []; // 没有元素的数组
var promes = [1, 2, 3, 4, 5]; // 有5个值数值的数组
var misc = [1.1, true, "a",]; // 3个不同类型的元素和结尾的逗号

// 第二种方法
var a = new Array(); // 创建一个没有任何元素的空数组
var b = new Array(10); // 指定长度
```

## 1.2 数组元素的添加和删除

```js
// 直接赋值插入元素
a = [];
a[0] = "zero";
a[1] = "one";
// push()方法在数组末尾插入元素
a.push("two");
// 也可以调用unshift()方法在数组的首部插入一个元素

// 用delete运算符删除，删除位置值改为undefined
delete a[1];
// 也可以pop()删除末尾元素，长度减一并返回删除元素的值
// shift()删除头部元素，其他元素向左移动一个位置
```

## 1.3 数组的遍历

使用**for循环**是遍历数组元素最常见的方法：

```js
var keys = Object.keys(o); // 获得o对象属性名组成的数组
var values = [];
for (var i = 0, len = keys.length; i < len, i++){
  var key = keys[i];
  values[i] = o[key];
}
```

如果想跳过null、undefined和不存在的元素，可以加上：

```js
if (!key) continue; // 过滤掉null, unfined，不存在
```

ES5定义了一个**forEach**方法，可以按照所以的顺序按个传递给定义的一个函数：

```js
var data = [1, 2, 3, 4, 5];
var sunOfSquares = 0;
data.forEach(function(x){
  sumOfSquares += x*x;
});
sunOfSquares; // => 55
```

ES6定义了一个**forOf**方法，可以对数组进行遍历，推荐使用这个方法遍历数组。

```js
let data = [1, 2, 3, 4, 5];
for (let value of data){
  console.log(value);
}
```

# 2 数组的方法

## 2.1 join()

```js
var a = [1, 2, 3];
a.join(); // >= "1,2,3" // 默认的分隔符为逗号
a.join(" "); // >= "1 2 3"
a.join(""); // >= "123"
```

## 2.2 reverse()

原地翻转。

```js
var a = [1, 2, 3];
a.reverse().join(); // >= "3,2,1"，并且现在a是[3, 2, 1]
```

## 2.3 sort()

不带参数的时候，是以字母表的顺序进行比较。

```js
var a = [33, 4, 1111, 222];
a.sort(function(a, b){ // 这里使用了匿名函数
  return a-b; // 0表示a=b, -1表示a<b, 1表示a大于b
});
```

## 2.4 concat()

不改变数组本身。

```js
var a = [1, 2, 3];
a.concat(4, 5); // 返回[1, 2, 3, 4, 5]
a.concat([4, 5]); // 返回[1, 2, 3, 4, 5]
a.concat([4, 5], [6, 7]); // 返回[1, 2, 3, 4, 5, 6, 7]
a.concat(4, [5, [6, 7]]); // 返回[1, 2, 3, 4, 5, [6, 7]]
```

## 2.5 slice()

slice(a [, b])不会修改调用的数组。两个参数分别代表起始位置和结束位置，包括a，不包括b。

```js
var a = [1, 2, 3, 4, 5];
a.slice(0, 3); // >= [1. 2, 3]; 
a.slice(3); // >= [4, 5], 省略了第二个参数
a.slice(1, -1); 
a.slice(-3, -2); 
```

## 2.6 splice()

修改调用的数组。数组在调用splice()进行插入和删除之后，会对自己的节点进行移动。它的第一个参数为删除（或插入）的位置，第二个参数指定个数。

```js
var a = [1, 2, 3, 4, 5, 6, 7, 8];
// 删除
a.splice(4); // 返回[5, 6, 7, 8], a是[1, 2, 3, 4]
a.splice(1, 1); // 返回[2], a是[1, 3, 4]
// 插入
a.splice(2, 0, 'a', 'b'); // 返回[], a是[1, 3, 'a', 'b', 4]
// 先删除后插入
a.splice(2, 2, [1, 2], 3); // 返回['a', 'b'], a是[1, 3, [1, 2], 3, 4]
```

## 2.7 push()和pop()& unshift()和shift()

见[链接](https://www.yuque.com/shijiatongxue/web/ty5ud4#8a4b577d)



## 2.8 toString()和toLocaleString()

```js
[1, 2, 3].toString(); // 生成"1,2,3"
["a", "b", "c"].toString(); // 生成"a,b,c"
[1, [2, "c"]].toString(); // 生成"1,2,c"
```

# 3 ESMAScript 5中的数组方法

对于这些个方法，它们大多数的第一个参数接收一个函数，该函数使用三个参数：数组元素、元素的索引和素组本身；第二个参数是可选的，如果有则调用的函数被看作是第二个参数的方法；

## 3.1 forEach()

forEach()方法从头至尾遍历数组，为每个元素调用指定的函数。

```js
var data = [1, 2, 3, 4, 5];
data.forEach(function(v, i, a){a[i] = v + 1;});
data; // => [2, 3, 4, 5, 6]
```

## 3.2 map()

map()返回的是新数组。

```js
a = [1, 2, 3];
b = a.map(function(x){return x*x}); // b是[1, 4, 9]
```

## 3.3 filter()

根据条件的真假判断去留。如果是真，就留下。返回的是新数组。

```js
a = [5, 4, 3, 2, 1];
smallvalues = a.filter(function(x){return x<3}); // [2, 1]
everyother = a.filter(function(i){return i%2==0}); // 返回奇数，[5, 3, 1]
```

## 3.4 every()和some()

对数组的元素进行判断，返回布尔值。

## 3.5 reduce()和reduceRight()

reduce接收两个参数，一个是函数，另一个是初始值。下面第二个求max的操作就没有给定初始值，这时把数组的第一个元素当作初始值。

```js
var a = [1, 2, 3, 4, 5]
var sum = a.reduce(function(x, y){return x+y}, 0); // 注意这个0，也是初始值，第一次是求1+0、
var max = a.reduce(function(x, y){return (x>y) ? x : y}); // 求最大值
```

## 3.6 indexOf()和lastIndexOf()

有返回下标，没有返回-1。可以给定第二个元素，指定开始的搜索位置。

一个有趣的例子👌：

```js
// 在数组中查找所有出现的x，并返回一个包含匹配索引的数组
function findall(a, x){
  var results = [],
      len = a.length,
      pos = 0;
  while(pos<len){
    pos = a.indexOf(x);
    if (pos==-1) break;
    results.push(pos);
    pos = pos + 1;
  }
  return results;
}
```

---

转载：[我的语雀](https://www.yuque.com/shijiatongxue/web/array)