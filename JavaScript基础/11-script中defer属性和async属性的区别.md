# script中defer属性和async属性的区别

这两个属性都是异步加载脚本，不阻塞HTML的解析和其他资源的加载，但是执行的时间不同，defer是在DOM解析完毕后，DOMContentLoaded事件前执行；async是加载完后立即执行，并且不保证多个脚本的执行顺序。

![](../%5C%E5%9B%BE%E7%A4%BA%5Cdefer.jpg)

## defer

这个布尔属性被设定用来通知浏览器该脚本将在文档完成解析后，触发DOMContentLoaded事件前执行。

## async

该布尔属性指示浏览器是否在允许的情况下异步执行该脚本。

---

参考：[script中defer属性和async属性的区别](https://shijiatongxue.github.io/blog/2019/8/defer-async.html)

