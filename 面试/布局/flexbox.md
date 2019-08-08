# flexbox

# 背景
flexbox布局模块时W3C在2017年8月提出，旨在提供更高效的布局、对齐和分配容器中项目之间的空间，即使是它们大小不确定或者是动态变化。

其主要思想就是让容器有改变其中项目的宽高的能力，以更好地填充可用空间（适应各种显示设备和屏幕大小）。

Note：flex布局最适合应用程序组件和小规模布局，而grid布局更适合大规模布局。

# 基础和技术
flex布局包括两大元素，container和item。

## flex container的属性

- display
它为所有直接子元素启用flex上下文。

```css
.container {
  display: flex; /* or inline-flex */
}
```

- flex-direction
控制item的流向。

```css
.container {
  /* default: row */
  flex-direction: row | row-reverse | colume | colume-reverse;
}
```

- flex-wrap
默认情况下，container会尝试在一行容下所有item。通过flex-wrap可以设置换行。

```css
.container {
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

- flex-flow
这个属性是以上两种属性的简写。

```css
  flex-flow: <flex-direction> || <flex-wrap>
```

- justify-content
我们经常会使用它水平居中。

```css
.container {
  justify-content: center;
}
```

另外，它还可以设置flex-start，flex-end，space-between，space-around，space-evenly。

- align-items
我们经常会使用它垂直居中项目。

```css
.container {
  align-items: center;
}
```
另外，它也可以设置flex-start，flex-end，stretch，baseline。

- align-content
它规定多行项目在垂直方向的分布关系。

它可以设置为：flex-start，flex-end，center，stretch，space-between，space-around。

由于控制的是多行之间的关系，所以只有一行项目时这个属性没有效果。


## flex item的属性
- order
它可以设置为整数，控制项目的显示顺序。

- flex-grow 
- flex-shrink
- flex-basis

- flex
这个属性是上面三个属性的简写。
关于这三个参数，请参考之前写的[博客](https://shijiatongxue.github.io/blog/2019/8/netease.html)。

```css
.item {
  flex: none | <flex-grow> <flex-shink>? || <flex-basis>;
}
```
其中第二个参数和第三个参数可以省略。flex的默认值为0 1 auto。

- align-self
单个项目可以使用这个属性可以覆盖align-items属性。

它的属性取值与align-items相同。

Note：float，clear，vertical-align对flex item是没有影响的。