# VNode

## 什么是VNode

在Vue.js中，它是一个类，使用它可以实例化不同类型的vnode实例，而不同类型的vnode实例各自表示不同类型的DOM元素。

vnode代表一个真实的DOM元素，所有真实的DOM节点都是使用vnode创建并插入到页面中。

渲染视图的过程是先创建一个vnode，然后再使用vnode去生成真实的DOM元素，最后插入到页面渲染视图。

## VNode的作用

由于每次渲染视图时都是先创建vnode，然后再使用它创建真实DOM插入到页面中，所以可以将上一次渲染视图时所创建的vnode缓存起来，之后每当需要重新渲染视图时，将新建的vnode和上一次缓存的vnode进行对比，查看它们之间有哪些不一样的地方，找出这些不一样的地方并基于此去修改真实的DOM。

> Vue.js目前对状态的侦测策略采用了中等粒度。当状态发生变化时，只通知到组件级别，然后组件内使用虚拟DOM来渲染视图。

也就是说，只要组件使用的众多状态中有一个发生了变化，那么整个组件都要重新渲染。

如果一个组件只有一个节点发生了变化，那么重新渲染整个组件的所有节点，很明显会造成跟大的性能浪费。因此，对vnode进行**缓存**，并将上一次缓存的vnode和当前创建的vnode进行**对比**，**只更新**发生变化的节点就变得尤为重要。这是vnode最重要的一个作用。

## VNode的类型

- 注释节点
- 文本节点
- 元素节点
- 组件节点
- 函数式组件
- 克隆节点

前面介绍vnode是JavaScript中的一个对象，不同类型的vnode之间其实只是属性不同，准确地说是有效属性不同。

### 注释节点

```js
export const createEmptyVNode = text => {
    const node = new VNode()
    node.text = text
    node.isComment = true
    return node
}
```

可以看出，注释节点有两个有效属性——text和isComment，其余属性全是默认的undefined或false。

例如，一个真实的注释节点：

```html
<!-- 注释节点 -->
```

所对应的vode是下面这个样子：

```js
{
    text: "注释节点",
    isComment: true
}
```

### 文本节点

```js
export function createTextNode (val) {
    return new VNode(undefined, undefined, undefined, String(val))
}
```

```js
{
    text: "Hello Shi"
}
```

### 克隆节点

克隆节点是将现有节点的属性复制到新节点中，让新创建的节点和被克隆节点的属性保持一致，从而实现克隆效果。它的作用就是优化静态节点和插槽节点。

以静态节点为例，当组件内的某个状态发生变化后，当前组件会通过虚拟DOM重新渲染视图，静态节点因为它的内容不会改变，所以除了首次渲染需要执行渲染函数获得vnode之外，后续更新不需要执行渲染函数重新生成vnode。因此，这时就会使用创建克隆节点的方法将vnode克隆一份，使用克隆节点进行渲染。这样就不需要重新执行渲染函数生成新的静态节点的vnode，从而提升一定程度的性能。

```js
export function cloneVNode (vnode, deep) {
    
}
```







