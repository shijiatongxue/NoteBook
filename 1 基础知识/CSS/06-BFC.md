# BFC

## 什么是BFC

BFC(Block Formatting Context)，简单讲，它是提供了一个独立布局的环境，每个BFC都遵守同一套布局规则。

## 什么情况产生BFC

当元素CSS属性设置了下列之一时，即可创建一个BFC:

- <html>根元素；
- float不为none；
- overflow为auto、scroll或hidden；
- display为table-cell、table-caption、inline-block中的任何一个；
- position不为relative或static。

## 它有什么用？

1. 可以包含浮动元素
2. 不被浮动元素覆盖
3. 阻止父子元素的margin折叠

---

参考：

[CSS布局中一个简单的应用BFC的例子](http://www.aliued.cn/2012/12/31/css布局中一个简单的应用bfc的例子.html)

[什么是BFC?](https://juejin.im/post/5a4dbe026fb9a0452207ebe6)

