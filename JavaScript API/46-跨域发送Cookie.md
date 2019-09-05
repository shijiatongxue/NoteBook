# 跨域发送Cookie

客户端：

```
xhr.withCredentials = true
```

服务器：

```
Access-Control-Allow-Credentials: true
```

只有同时设置了这两项，即可允许跨域请求携带Cookie。

注意，设置了Access-Control-Allow-Credentials就不能设置Access-Control-Allow-Origin=‘*’了。

这时候：

- Access-Control-Allow-Origin

不允许使用通配符，只能指定单一域名，否则浏览器还是会挡住跨域请求。



