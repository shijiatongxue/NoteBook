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

### 例子

定义 30s 刷新时间

```html
<meta http-equiv="refresh" content="30">
```

