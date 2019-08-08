```js
var getUrlPara = function(){
    var href = window.location.href;
    var args = href.split('?');
    // 如果没有参数，返回空
    if (args[0]===href){
        return "";
    }
    var arr = args[1].split('&');
    var obj = {};
    for (let i = 0, len = arr.length; i < len; i++){
        var arg = arr[i].split('=');
        obj[arg[0]] = arg[1];
    }
    return obj;
}
```