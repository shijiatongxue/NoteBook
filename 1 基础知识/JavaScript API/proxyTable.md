# proxyTable

在Vue中，经常会用到proxyTable。

它是个Node服务器，是浏览器HTTP代理，会把浏览器的请求进行转发。

使用的原理是：后端请求不受跨域限制。

```js
proxy: {
  '/api': {
   target: 'http://211.69.197.4:8080',
   changeOrigin: true,
   pathRewrite: {
     '^/api': '/'
   }
}
```

---

参考：

[http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware#options)

