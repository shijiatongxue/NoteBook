Class可以通过extends关键字实现继承。

```js
class Point{
}

class ColorPoint extends Point{
    constructor(x, y, color){
        super(x, y);
        this.color = color;
    }

    toString(){
        return this.color + ' ' + super.toString();
    }
}
```