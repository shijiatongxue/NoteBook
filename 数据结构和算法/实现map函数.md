# 实现map函数

```js
Array.prototype.myMap = function(fn, ctx) {
    let arr = this;
    let res = [];
    for (let i=0, len=arr.length; i < len; i++) {
        let now = fn.call(ctx, arr[i], i, arr);
        res.push(now);
    }
    return res;
}

// 测试
let arr = [1, 2, 3];
arr.myMap((x) => x*2); // [2, 4, 6]
```

