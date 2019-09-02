# history和hash

hash前端路由，无刷新

history回去请求接口

Vue是为了实现SPA，需要引入Vue Router。前端路由的核心是，改变视图的同时不会向后端发送请求。

## hash

hash虽然出现在URL中，但不会被包含在HTTP请求中，对后端完全没有影响，因此改变hash不会重新加载页面。

hash模式的原理：在window对象上监听onhashchange事件。

## history

history利用了HTML5 history接口中新增的pushState和replaceState方法。这两个方法应用于浏览器记录栈，在当前已有的go、back、forward基础上，提供了对历史记录修改的能力。知识当它们执行修改时，虽然改变了URL，但浏览器不会立即向后端发送请求。

history模式的原理：hashchnage只能改变#后面的代码片段，history API则给了前端完全的自由，通过在window对象上监听popState()事件。

```js
// pushState()和replaceState()方法接收3个参数：stateObj, title, url

// 设置状态
history.pushState({color: "red"}, "red", "red");

// 监听状态
window.onpopstate = function(event){
    console.log(event.state);
    if(event.state && event.state.color === "red"){
        document.body.style.color = "red";
    }
}

// 改变状态
history.back();
history.forward();
```

---

来自：[西芹儿](https://juejin.im/post/5b31a4f76fb9a00e90018cee)