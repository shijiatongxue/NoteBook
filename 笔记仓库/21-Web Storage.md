# 概述
Storage接口用于脚本在浏览器保存数据。两个对象部署了这个接口：window.sessionStorage和window.localStorage。

- sessionStorage保存的数据用于浏览器的一次会话（session），当会话结束（通常是窗口关闭），数据被清空；

- localStorage保存的数据长期存在，下一次访问该网站的时候，网页可以直接读取以前保存的数据。除了保存期限的长短不同，这两个对象的其他方面都一致。

这个接口很像Cookie的加强版，能够使用大得多的存储空间。目前，每个域名的存储上限视浏览器而定，Chrome是2.5MB，Firefox和Opera是5MB，IE是10MB。另外，它们也受同域限制。某个网页存入的数据，只有同域下的网页才能读取，如果跨域会报错。

# 属性和方法
- Storage.length：返回保存的数据项个数；
- Storage.setItem(k, v)：设置或更新键值对，没有返回值；
- Storage.getItem(k)：读取数据，没有则返回null；
- Storage.removeStorage(k)：清除某个键对应的键值；
- Storage.clear()：清除所有保存的数据，返回undefined；
- Storage.key(num)：传入一个数字，取出对应位置的键。


# 与Cookie的区别
相同点：同是客户端存储的数据缓存。

不同点：

（1）是否发送到服务器端：Web Storage不会将数据发送到服务器段，不会造成带宽的浪费；

（2）存储大小不同：Cookie大小为4KB，Web Storage则可以达到5MB；

（3）有效期限不同：Cookie在过期之前一直有效，即使关闭对话；sessionStorage在对话结束后清除；localStorage，数据永久存储；

（4）作用域不同：Cookie在不同浏览器之间，同源页面可以共享；localStorage不在不同浏览器的同源页面共享；

---
参考：阮一峰