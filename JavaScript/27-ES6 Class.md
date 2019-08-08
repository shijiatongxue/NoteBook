# 简介
基本上，ES6中的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让对象原型的写法更加清晰，更像面向对象编程的语法而已。
```js
function Point(x, y){
    this.x = x;
    this.y = y;
}

Point.prototype.toString = function(){
    return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```
class写法：
```js
class Point{
    constructor(x, y){
        this.x = x;
        this.y = y;
    }

    toString(){
        return '(' + this.x + ', ' + this.y + ')';
    }
}
```

