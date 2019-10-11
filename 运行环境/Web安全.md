# Web安全

## XSS

跨站脚本攻击，通常由页面可解析内容的**数据未经处理直接插入到页面**上解析导致的。

- 存储型：前端提交的数据未经处理直接存储到数据库，然后取出后直接插入到页面；
- 反射性：在URL参数注入了可解析的内容。

```html
<!-- 存储型XSS -->
<div>
  {{content}}
</div>

<!-- 渲染后输出 -->
<div>
  <script>
  	alert();
  </script>
</div>
```

```js
// 反射型XSS，后台未经验证，简单地把请求参数插入到返回的HTML中
let name = req.query(['name']);
this.body = `<div>${name}</div>`;
```

它的防范方法是验证输入到页面的所有内容来源数据是否安全，如果含有脚本标签则需要进行转义。

## SQL注入

在服务器未经数据验证就将数据直接拼接到SQL语句中执行。

```js
let id = req.query['id'];
// id = '100 or name=%user%'
let sql = `select * from user while id=${id}`;
let data = exec(sql);
this.body = data;
```

针对SQL注入，需要对传入的id内容进行校验。

## CSRF

![](csrf.jpg)

非法获取用户登录状态，对目标网站进行访问。

上图中，form发起的POST请求不受到同源策略的限制，所以可以任意地使用其他域的Cookie向其他域发送POST请求。而token不同，浏览器不会自动添加到headers里。token一般存在客户端的cookie、sessionStorage或localStorage里。

```js
// 诱导用户点击如下照片
<img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">
```

防范策略为：

- 检查Referer字段
- 添加校验Token

```js
// 生成随机的csrf验证Token，并返回给前端界面
this.session.csrf = md5(Math.random(0, 1).toString()).slice(5, 15);
this.body = yield render('user/login', {
  csrf: ctx.session.csrf;
})

// 提交时验证Token是否与源站的Token相同
let csrf = this.request.body['csrf'];
if (csrf !== this.session.csrf) {
  res = {
		code: 403,
    msg: '不明网站来源提交'
  }
} else {
  // 正常提交后的处理逻辑
}
```

Token传递方式有两种：

```http
GET http://pinduoduo.com/api/xxx
...
Token: xxx
```

或在请求参数中传递：

```js
http://pinduoduo.com/api/xxx?token=xxx
```

前端通过Authorization属性发送token或者把数据放在POST请求体里：

```js
headers: {
  "Authorization": xxx
}
```

---

参考：

现代前端技术解析

[CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/index.html)

[维基百科]([https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0](https://zh.wikipedia.org/wiki/跨站请求伪造))

[HTML特殊字符编码对照表](https://www.jb51.net/onlineread/htmlchar.htm)

[JSON Web Token 入门教程](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)