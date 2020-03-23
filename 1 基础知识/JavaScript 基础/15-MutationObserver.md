# MutationObserver

该接口提供了监视DOM树改变的能力。是DOM3 Events规范的一部分。

DOM的任何变动，如节点增减、属性变动、文本内容变动，这个API都可以得到通知。

## 构造函数

- MutationObserver()

创建并返回一个新的MutationObserver，它会在指定的DOM发生变化时被调用。

## 方法

- observer()

配置MutationObserver在DOM更改匹配给定选项时，通过其回调函数开始接收通知。

- disconnect()

阻止MutationObserver实例继续接收通知，直到再次调用其observe()方法，该观察者对象包括的回调函数都不会再被调用。

- takeRecords()

从MutationObserver的通知队列中删除所有待处理的通知，并将它们返回到MutationRecord对象的新的Array中。

## 示例

```html
<input type="text" value="123">
  
<script>
  // 创建一个MutationObserver实例
  let observer = new MutationObserver(callback);
  // 调用observer方法开始监听
  observer.observe(dom, options);
  
  // 停止监听
  observer.disconnect();
<script>
```

options可以设置监听的内容：

```js
let options = {
  attributes: true,
  childList: true,
  subtree: true,
  characterData: true,
  attributeFilter: ['class']
}
```

实例传入一个回调函数，是为了触发事件之后调用这个方法：

```js
let callback = function(mutations) {
  mutations.forEach((mutation) => {
  	if (mutation.type == 'childList') {
    	console.log('child node changed.');
    }
    else if (mutation.type == 'attributes') {
    	console.log(`${mutation.attributeName} changed.`);
    }
    
  });
}
```

---

参考：

MDN

黑马教程

