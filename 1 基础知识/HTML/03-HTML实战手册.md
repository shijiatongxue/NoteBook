# HTML 实战手册

## HTML 文件语言编码

HTML 的 `lang` 属性可以用来声明网页或网页其中部分的语言。它用来协助搜索引擎和浏览器。

W3C 推荐在 \<html\> 标签的 `lang` 属性上声明网页的首选语言。

```html
<html lang="en">
    ...
</html>
```

在 XHTML，应该这样写：

```html
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">
...
</html>
```

ISO 639-1 定义了语言的缩写。

在 HTML 和 XHTML 你可以使用 `lang` 和 `xml:lang` 属性。

| 语言         | ISO 码  |
| ------------ | ------- |
| 中文         | zh      |
| 中文（简体） | zh-Hans |
| 中文（繁体） | zh-Hant |
| 英文         | en      |

相关链接：[W3C](https://www.w3schools.com/tags/ref_language_codes.asp)

## HTML \<meta\> 标签

\<meta\> 标签显示 HTML 文件的元信息。它不会显示在屏幕上，但是会被机器解析。

它一般用来提供页面的解释、关键词、作者、最后修改和其他元数据。

它可以被浏览器（如何显示和重载页面）、搜索引擎（关键字）和其他网络服务使用。

HTML5 引入了一种方法，可以让设计者通过 \<meta\> 标签决定用户的视窗大小。

### 注意事项

- \<meta\> 标签总是在 \<head\> 标签内使用。
- 元数据总是以键值对呈现。
- 如果 `name` 或 `http-equiv` 被定义，`content` 属性**必须**被定义。如果两者都没有被定义，则 `content` **不可**被定义。

**设置视窗**

`viewport` 元素让浏览器控制页面的维度和缩放比例。

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

其中，`width=device-width` 让网页的宽度等于屏幕的宽度；

`initial-scale=1.0` 指定网页首次加载的缩放比例。

```html
<meta name="viewport" content="width=1226">
```

content 也可以指定宽度为固定值。

### 例子

定义 30s 刷新时间

```html
<meta http-equiv="refresh" content="30">
```

关于 http-equiv 也可以控制缓存，缓存优先级低于响应头的缓存设置。[How do we control web page caching, across all browsers?](https://stackoverflow.com/questions/49547/how-do-we-control-web-page-caching-across-all-browsers) 不建议强制在 meta 部分设置缓存策略，最好通过响应头设置。

## HTML \<link\> 标签

\<link\> 定义文档与外部资源的关系，最常见的用途是链接外部样式表。

此元素只能用于 head 部分，可以出现任何次数。

### 属性

| 属性  | 值                                                           | 描述                                      |
| ----- | ------------------------------------------------------------ | ----------------------------------------- |
| href  | URL                                                          | 规定被链接文档的位置                      |
| rel   | icon<br />stylesheet<br />prefetch<br />preload<br />dns-prefetch<br />preconnect | 规定文档与被链接文档的关系                |
| sizes | heightxwidth <br />any                                       | 规定被链接资源的尺寸，仅适用于 rel="icon" |
| type  | MIME_type                                                    | 规定文档的 MIME 类型                      |

### preload 和 prefetch

preload：尽快下载并缓存文档，下载完毕后不会执行，等待需要的时候立即执行。它的作用是：加载完页面，需要立即使用的资源。

prefetch：尽量下载并缓存文档，它的优先级较低，不会影响页面的执行。它的作用是：当你在其他页面需要这个资源时，可以预先缓存下来。

### dns-prefetch

```html
<link rel="dns-prefetch" href="https://fonts.gstatic.com/" >
```

它的作用是在请求之前提前进行外域的 dns 解析。当你的网站需要外域的资源时，建议把 dns-prefetch 写在 head 头部。

建议：

1. 只需预取跨域的域名，不要指向自己的域。因为用户输入网址之后，已经解析了网站的地址。
2. 考虑与 `preconnect` 成对使用。因为 `dns-prefetch` 只负责解析，`preconnect` 于服务器建立 TCP 连接，如果对方是 HTTPS 协议，还会执行 TSL 握手。同时使用这两个会降低跨域的访问时间。

```html
<link rel="preconnect" href="https://fonts.gstatic.com/" crossorigin>
<link rel="dns-prefetch" href="https://fonts.gstatic.com/">
```

> 然而如果与太多的外域建立连接，进行 preconnect 会适得其反。preconnect 适合用在最重要的几个连接上。对于其他连接，只需要 dns-prefetch 进行 DNS 预解析就可以了。

[HTML <link> 标签](https://www.w3school.com.cn/tags/tag_link.asp)

[preload 和 prefetch](https://stackoverflow.com/a/60295034/10289937)

[dns-prefetch](https://developer.mozilla.org/en-US/docs/Web/Performance/dns-prefetch)