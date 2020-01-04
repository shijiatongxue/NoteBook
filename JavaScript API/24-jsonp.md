# JSONP

JSONP 或 JSON-P 是一种通过 `<script>` 标签请求数据的 JavaScript 技术。它于 2005 年被 Bob Ippolito 提出。JSONP 可以绕过同源策略分享数据。

## 原理

由于 `<script>` 属于跨域嵌入类型（见笔记[跨域](https://github.com/shijiatongxue/NoteBook/blob/master/运行环境/跨域.md)），它支持跨域执行。

所以你可以：

1. 在 `a.com` 执行 `<script>` 加载 `b.com` 的脚本。

2. 在返回的消息中嵌入可以执行的脚本，其中带有需要的 JSON 数据。

代码如下：

```html
// a.com

<script>
	function foo(data) {
		alert('收到数据' + data.name);
	}
</script>
<script src="http://localhost:3000?callback=foo"></script>
```

```javascript
// b.com nodejs代码

const http = require('http');
const url = require('url');

http.createServer((req, res) => {
  const data = {
    name: 'hello jsonp'
  }
  const cb = url.parse(req.url, true).query.callback;
  const resData = `${cb}(${JSON.stringify(data)})`;
  res.writeHead(200);
  res.end(resData);
}).listen(3000);
```

## 优缺点

优点：

- 可以很简单的实现跨域
- 兼容性很好，支持古老的浏览器

缺点：

- 只支持 GET 请求
- 无法捕捉 JSONP 请求时的连接异常

---

参考：

JavaScript高级程序设计

[wikipedia](https://en.wikipedia.org/wiki/JSONP)