# 实现reduce函数

```js
Array.prototype.myReduce = function(fn, base) {
  let arr = this.slice(); // 拷贝原数组
  if (base) {arr.unshift(base);}
  
  let index = this.length - arr.length + 1; // 初始值为0
  while (arr.length > 2) {
    let now = fn.call(null, arr[0], arr[1], index, arr);
    arr.splice(0, 2, now); // 删除旧值，压入新值
    index++;
  }
  
  let res = fn.call(null, arr[0], arr[1], index, arr);
  return res;
}

// 测试
let arr = [1, 2, 3, 4, 5];
arr.myReduce((a, b) => a+b, 10);
```

