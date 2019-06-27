# Promise的含义
Promise是异步社区编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理且更前大。

所谓Promise，简单来说就是一个容器，里面保存着某个未来才会结束的**事件的结果**。从语法上来说，Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

Promise对象的两个特点：
- 对象的状态不受外界影响。Promise对象代表一个异步操作，有3种状态：Pending（进行中）、Fulfilled（已成功）和Rejected（已失败）。只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变就不会再变，任何时候都可以得到这个结果。Promise对象的状态改变有两种可能：从Pending变为Fulfilled和从Pending变为Rejected。只要这两种情况发生，状态就凝固了，不会再变，而是一直保持这个结果，这时就称为Resolved（已定型）。就算改变已经发生，再对Promise对象添加回调函数，也会立即得到这个结果。这与事件完全不同，事件的特点是，如果错过了它，再去监听是得不到结果的。

# 基本用法
ES6规定，Promise对象是一个构造函数，用来生成Promise对象。

Promise实例生成后，可以用then方法分别指定Resolved状态和Rejected状态的回调函数。
```js
funtion timeout(ms){
    return new Promise((resolve, reject) => {
        setTimeOut(resolve, ms, 'done');
    });
}

timeout(100).then((value) => {
    console.log(value);
});
```
上面的代码中，timeout方法返回一个Promise实例，表示一段时间以后才会发生的结果。过了指定的时间以后，Promise实例的状态变为Resolved，就会触发then方法绑定的回调函数。

回调函数将在当前脚本所有同步任务执行完成后才会执行。

/未完待续/