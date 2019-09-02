# Fetch

Fetch API提供了一个JavaScript接口，用于访问和操作HTTP管道的部分，例如请求和响应。

注意，fetch规范与jQuery.ajax()主要有两种方式的不同，牢记：

- 当接收到一个代表错误的HTTP状态码时，**从fetch()返回的Promise不会标记为reject**，相反它会将Promise状态标记为resolve，仅当网络故障或请求被阻止时，才会标记为reject。
- 默认情况下，**fetch不会从服务端发送或接收任何cookie**s，如果站点依赖于用户session，则会导致未经认证的请求（要发cookies，必须设置credentials选项）。

## 发送请求

```js
fetch('http://shijia.com/movies.json')
	.then(function(response){
  	// Response使用了Body定义的方法
  	return response.json();
	})
	.then(function(myJson){
  	console.log(myJson);
	})
```

## Headers

```
var content = "Hello World";
var myHeaders = new Headers();
myHeaders.append('Content-type', 'text/plain');
```

也可以传入一个多维数组或对象字面量：

```js
myHeaders = new Headers({
  "Content-Type": "text/plain"
})
```

获取Headers属性：

```js
// has
console.log(myHeaders.has("Content-Type")); // true
// set
myHeaders.set("Content-Type", "text/html"); 
// get
console.log(myHeaders.get("Content-Type")); // "text/html"
// delete
myHeaders.delete("Content-Type");
```

## Response对象

Response实例是在fetch()处理完promise之后返回的。

最常见的response属性有：

- Response.status
- Response.statusText
- Response.ok

## Body

不管是请求还是响应都能够包含body对象，body也可以是以下任意类型的实例。

- ArrayBuffer
- ArrayBufferView
- Blob/File
- string
- URLSearchParams
- FormData

**Body类定义了以下方法（Request和Response实现了这些方法）以获取body内容**，这些方法都会返回一个被解析后的Promise对象和数据。

- arrayBuffer
- blob()
- json()
- text()
- formData()

比起XHR来，这些方法让非文本化的数据使用起来更加简单。

```js
var form = new FormData(document.getElementById('login-form'));
fetch("/login", {
  method: "POST",
  body: form
})
```

request和respnse都会试着自动设置Content-Type。如果没有设置Content-Type值，发送的请求也会自动设置。

## 检测支持情况

通过检测Headers，Request或fetch()是否再Window或Worker域中：

```js
if (self.fetch) {
  // run my fetch request here
}
else {
  // do something with XMLHttpRequest?
}
```

[浏览器兼容性]([https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch#浏览器兼容))

注意：

- 所有的IE浏览器都不支持fetch()方法
- 服务器返回状态吗400或500的时候不会被reject

## 使用Fetch

以下例子来自[zuggs](https://blog.csdn.net/zuggs_/article/details/80775455)，以供参考

```js
// 发送文本
fetch('/xxx/page').then(res => {
        return res.text();
    }).then(res => {
        console.log(res);
    })
```

```js
// 发送JSON
fetch('/xxx', {
        method: 'post',
        body: JSON.stringify({
            username: '',
            password: ''
        })
    });
```

```js
// 发送表单

var form = document.querySelector('form');
    fetch('/xxx', {
        method: 'post',
        body: new FormData(form)
    });
```

```js
// 获取图片

fetch('/xxx').then(res => {
        return res.blob();
    }).then(res => {
        document.querySelector('img').src = URL.createObjectURL(imageBlob);
    })
```

```js
// 上传
var file = document.querySelector('.file')
   var data = new FormData()
   data.append('file', file.files[0])
   fetch('/xxx', {
     method: 'POST',
     body: data
   })
```

---

参考：

MDN

[zuggs](https://blog.csdn.net/zuggs_/article/details/80775455)



