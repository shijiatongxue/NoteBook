# 同步请求
```js
// 一个简单的同步Ajax请求
var xhr = new XMLHttpRequest();
xhr.open("get", "example.php", false);
xhr.send(null);
```

由于这次请求是同步的，JavaScript代码会等到服务器相应之后再继续执行。在收到响应后，响应的数据会自动填充到XHR对象的属性。

- responseText
- responseXML：如果响应内容类型是“text/xml”或“application/xml”，这个属性中将保存着响应数据的XML DOM文档。
- status
- statusText

```js
// 根据状态码检查返回内容

if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
    console.log(xhr.responstText);
}
else {
    console.log("Request was unsuccessful: " + xhr.status);
}
```

Note：无论内容类型是什么，响应主体的内容都会保存到responseText属性中；而对于非XML数据而言，responseXML属性的值将为null。

# 异步请求
```js
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        // 收到响应报文
    }
    else {
        // 没有收到响应
    }
}
// open的第三个参数为是否异步
xhr.open("get", "example.php", true);
xhr.send(null);
```

readyState的属性可取的值如下：

- 0：代表未初始化。尚未调用open()方法。
- 1：启动。已经调用open()方法，但尚未调用send()方法。
- 2：发送。已经调用send()方法，但尚未接收到响应。
- 3：接收。已经接收到部分响应数据。
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

# 设置头部信息
必须在open()方法之后，send()方法之前调用setRequestHeader()方法。

```js
// xhr.open();
xhr.setRequestHeader("MyHeader", "MyValue");
// xhr.send();
```

获取头部信息可以使用getAllResponseHeaders()方法。

# GET和POST方法
Ajax也可以发送带参数的GET和POST方法。

```js
// GET

function addURLParam(url, name, value) {
    url += (url.indexOf('?') == -1 ? '?' : '&' );
    url += encodeURLComponent(name) + '=' + encodeURIComponent(value);
}

// 添加参数
var url = "example.php";
url = addURLParam(url, "name", "Shi");
url = addURLParam(url, "book", "Professional JavaSctipt");

// 初始化请求
xhr.open("get", url, false);
```

```js
// POST

xhr.open("post", "postexample.php", false);
xhr.setRequest("Content-Type", "application/x-www-form-urlencode");
xhr.send(seralize(form));
```

参考：JavaScript高级程序设计