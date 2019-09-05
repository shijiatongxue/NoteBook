# CORS

## 简介

CORS通信与同源的Ajax通信没有差别，代码完全一样。浏览器一旦发现Ajax请求跨域，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。

**实现CORS的关键是服务器，只要服务器实现了CORS接口，就可以跨域通信。**

Firefox3.5+、Safari4+、Chrome、iOS版Safari和Android平台中的Webkit都通过XMLHttpRequest对象实现了对CORS的原生支持。

## 两种请求

浏览器将CORS请求分为两类：简单请求和非简单请求。

只要同时满足以下两大条件，就属于简单请求：

1. 请求方法是以下三种方法之一：

- HEAD
- GET
- POST

2. HTTP的头信息不超出以下几种字段：

- Accept
- Accept-Language
- Content-Language
- Last-Event-ID
- Content-Type：只限于三个值，`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`

凡是不同时满足上面两个条件，就属于非简单请求。

## 简单请求

对于简单请求，浏览器直接发出CORS请求。具体来说，就是在头信息之中，增加一个Origin字段。

```http
GET /cors HTTP/1.1

Origin: http://api.bob.com
```

如果Origin指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含 ```Access-Control-Allow-Origin``` 字段，就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。

如果Origin指定的域名在许可范围内，服务器多出的头信息字段如下：

```http
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```

- Access-Control-Allow-Origin

该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个*，表示接受任务域名的请求。

- Access-Control-Allow-Credentials

该字段可选。它是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为true表示服务器明确许可，Cookie可以包含在请求中，一起发送给服务器。如果不需要发送，直接删除该字段即可。

- Access-Control-Expose-Headers

该字段可选。CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。如果想要拿到其他字段，就需要在Access-Control-Expose-Headers里面指定。上面的例子指定，getResponseHeader(‘FooBar’)可以返回FooBar字段的值。

## withCredential属性

CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，需要做以下配置：

- 服务器同意接受

```http
Access-Control-Allow-Credentials: true
```

- 开发者请求配置

```js
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```

如果不需要传送Cookie：

```js
xhr.withCredentials = false;
```

需要注意的是，如果要发送Cookie，Access-Control-Allow-Origin就不能设置为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源策略，只有用服务器域名设置的Cookie才会上传。

## 非简单请求

非简单请求时那种对服务器有特殊要求的请求，比如请求方法是PUT或DELETE，或者Content-Type字段的类型是application/json。

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为预检请求。

浏览器先询问服务器，当前网页所在域名是否在服务器的许可名单中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式XMLHttpRequest请求，否则就报错。

对于一个请求：

```js
var url = 'http://api.alice.com/cors';
var xhr = new XMLHttpRequest();
xhr.open('PUT', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();
```

浏览器发现这是一个非简单请求，就自动发出一个OPTIONS请求，要求服务器确认可以这样请求，下面是这个OPTIONS请求的HTTP头信息。

```http
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
```

除了Origin字段，OPTIONS请求的头信息包括两个特殊字段：

- Access-Control-Request-Method

这个字段是必须的，用来列出浏览器的CORS请求会用到哪些HTTP方法上。

- Access-Control-Request-Headers

这个字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段。

## 预检请求的回应

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

上面最关键的是Access-Control-Allow-Origin字段，表示http://api.bob.com可以请求数据。若该字段是星号，表示同意任意跨域请求。

如果浏览器否定了预检请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器会认定，服务器不同意预检请求，因此触发一个错误。

```js
XMLHttpRequest cannot load http://api.alice.com.
Origin http://api.bob.com is not allowed by Access-Control-Allow-Origin.
```

## 浏览器的正常请求和回应

一旦服务器通过了预检请求，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个Origin头信息字段，服务器的回应也会有一个Access-Control-Origin头信息字段。

## 总结

CORS与JSONP的使用目的相同，但是比JSONP更强大。

JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

---

参考：

[阮一峰](http://www.ruanyifeng.com/blog/2016/04/cors.html)

[Maldivee](https://segmentfault.com/a/1190000013223031)

JavaScript高级程序设计