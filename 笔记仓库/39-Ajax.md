# 概述
Ajax技术的核心是XMLHttpRequest对象。XHR为向服务器发送请求和解析服务响应提供了流程的接口。

所谓异步是指，用户点击之后，不必刷新页面也能获得新数据。

# XMLHttpRequest对象
同步请求：
```js
var xhr = new XMLHttpRequest();
xhr.open('get', 'example.html', false);
xhr.send(null);  // 不需要发送数据则传入null

// 收到响应
if((xhr.status>=200 && xhr.status < 300) || xhr.status == 304){
    alert(xhr.responseText);
}
else{
    alert('Request was unsuccessful: ' + xhr.status);
}
```
异步请求：
```js
// 异步
var xhr = new XMLHTTPRequest();
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4){
        if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
            alert(xhr.respnseText);
        }
        else{
            alert('Request was unsuccessful: ' + xhr.status);
        }
    }
}
// true代表是异步操作
xhr.open('get', 'example.txt', true);
xhr.send(null);
```

readyState:
- 0：未初始化。尚未调用open()方法。
- 1：启动。已经调用open()方法，但尚未调用send()方法。
- 2：发送。已经调用send()方法，但尚未接收到响应。
- 3：接收。已经接收到部分响应数据。
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

# 