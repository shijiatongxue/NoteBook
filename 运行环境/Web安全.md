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

```js
// 诱导用户点击如下照片
<img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">
```

防范策略为：

- 检查Referer字段
- 添加校验Token

校验Token不保存在Cookie中，攻击者无法伪造。这种数据通常是窗体的一个数据项，服务器将其生成并附加在窗体中，其内容是一个伪随机数。当客户通过窗体提交请求时，这个随机数也提交上去以供校验。

---

参考：

现代前端技术解析

[CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/index.html)

[维基百科]([https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0](https://zh.wikipedia.org/wiki/跨站请求伪造))