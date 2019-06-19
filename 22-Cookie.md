Cookie由网景创造，旨在维持客户端状态。它是在客户端存储数据的一种选项。

# 创建Cookie
一个cookie的创建分为三个步骤：

- 服务器对请求的响应报文添加Set-Cookie字段；

```http
HTTP/1.1 200 OK
...
Set-Cookie: name=value
...
​
...
```

- 浏览器存储这样的会话信息；
- 为每个请求添加Cookie HTTP头将信息发送会服务器。

```http
GET /index.html HTTP/1.1
...
Cookie: name=value
...
​
...
```
# Cookie的构成
Cookie由浏览器保存的以下几块信息构成：

- 名称：不区分大小写。
- 值：值必须经过URL编码。
- 域：说明这个cookie可以在哪个域内使用。
- 路径：如果指定路径则只能在这个路径下使用。
- 失效时间：默认情况下，浏览器会话结束时即将所有cookie删除。
- 安全标志：指定后，cookie只有在使用SSL连接的时候才发送到服务器。

```http
HTTP/1.1 200 OK
...
Content-type: text/html
Set-Cookie: name=value; domain=.github.com; path=/; secure;
...
​
...
```
# Cookie的限制
原因：为了确保cookie只可以被一定的域名使用，因为它本来就是特定的域名产生，不希望被滥用；其次，不占用浏览器太多空间。



每个域最多可以有30~50个cookie，根据浏览器的不同；
cookie的尺寸基本为4K，超过则会被丢掉；