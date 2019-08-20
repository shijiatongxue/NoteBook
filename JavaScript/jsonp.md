# JSONP

JSONP是JSON with padding的简写，JSONP看起来与JSON差不多，只不过是被包含在函数调用中的JSON。

```js
callback({ "name": "Shi"});
```

JSONP由两部分组成：回调函数和数据。回调函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON数据。

```http
// 一个典型的JSONP请求
http:freegeoip.net/json/?callback=handleResponse
```

JSONP是通过动态script元素来使用的，使用时可以为src属性指定一个跨域URL。script和img元素类似，都有能力不受限制地从其他域加载资源。因为JSONP是有效的JavaScript代码，所以在请求完成后，即在JSONP响应加载到页面以后，就会立即执行。

```js
// 一个例子
function handleResponse(response){
    console.log(response.city, response.region_name);
}

var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResonse";
document.body.insertBefore(script, document.body.firstChild);
```

参考：JavaScript高级程序设计