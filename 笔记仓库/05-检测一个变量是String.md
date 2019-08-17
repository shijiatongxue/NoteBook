# typeof
```js
var isString = function(obj){
    return typeof obj === 'string';
}
```
# constructor
```js
var isString = function(obj){
    return obj.constructor === String;
}
```
# Object.prototype.toString()
```js
var isString = function(obj){
    return Object.prototype.toString.call(obj) === '[object String]';
}
```