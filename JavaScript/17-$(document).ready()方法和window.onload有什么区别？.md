# window.onload
- 执行时机：必须等待网页的所有内容加载完毕后才能执行；
- 编写个数：重复会覆盖之前的事件；
# \$(document).ready()
- 执行时机：网页中的所有DOM元素绘制完毕后就执行，可能DOM关联的东西并没有加载完；
- 编写个数：可以添加多个；
- 简写：\$(document).ready(function(){})可以简写为\$(function(){})