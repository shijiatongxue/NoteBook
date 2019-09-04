# CORS

CORS通信与同源的Ajax通信没有差别，代码完全一样。浏览器一旦发现Ajax请求跨域，就会自动添加一些附加的头信息，有时还会多出一次附件的请求，但用户不会有感觉。

**实现CORS的关键是服务器，只要服务器实现了CORS接口，就可以跨域通信。**

Firefox3.5+、Safari4+、Chrome、iOS版Safari和Android平台中的Webkit都通过XMLHttpReques对象实现了对CORS的原生支持。

浏览器在尝试打开不同来源的资源时，**无需额外编写代码就可以触发这个行为。**

```js
// 浏览器请求
var xhr = XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4) {
  	if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
    	alert(xhr.responseText);
    }
    else {
    	alert('Request was unsuccessful: ' + xhr.status)
    }
  }
}
xhr.open('get', 'http://www.somewhere-else.com/page/', true);
xhr.send(null);
```

```js
// 服务器配置
app.use(function(req,res,next){
  //只允许8080访问
   res.header('Access-Control-Allow-Origin','http://localhost:8080');

  //服务允许客户端发的方法
  res.header('Access-Control-Allow-Methods','GET,POST,DELETE,PUT');
  //服务器允许的请求头
  res.header('Access-Control-Allow-Headers','Content-Type,Accept');
  //跨域携带cookie  允许客户端把cookie发过来
  res.header('Access-Control-Allow-Credentials','true');
  //如果请求的方法是OPTIONS,那么意味着客户端只要响应头，直接结束响应即可
  if(req.method == 'OPTIONS'){
    res.end();
  }else{
    next();
  }
});
app.listen(3000);
```

跨域XHR对象的限制：

- 不能使用setRequestHeader()设置自定义头部
- 不能发送和接受Cookie
- 调用getAllResponseHeaders()方法总会返回空字符串

---

参考：

[Maldivee](https://segmentfault.com/a/1190000013223031)

JavaScript高级程序设计