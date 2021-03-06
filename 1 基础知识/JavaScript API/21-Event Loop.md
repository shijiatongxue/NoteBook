# Event Loop

![内存](./stack-queue.png)

## 栈

函数调用形成了一个栈帧。帧种包括了函数的参数和局部变量。

## 堆

对象被分配在一个堆中，即用以表示一大块非结构化的内存区域。

## 队列

一个JavaScript在运行时包含了一个待处理的消息队列。每一个消息都关联着一个用以处理这个消息的函数。

在事件循环期间的某个时刻，运行时从最先进入队列的消息开始处理队列中的消息。为此，这个消息会被移出队列，并作为输入参数调用与之相关联的函数。

函数处理会一直进行到执行栈再次为空为止；然后事件循环将会处理队列中的下一个消息。

## 任务类型

- 同步任务
- 异步任务

同步任务：在主线程排序执行的任务，只有前一个任务执行完毕，才能执行后一个任务；

异步任务：不进入主线程，而进入任务队列（task queue）的任务。只有任务队列通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

![主线程和任务队列](./task.jpg)

图来自阮一峰博客。

> 只要主线程空了，就会读取任务队列，这就是JavaScript的运行机制。这个过程会不断重复。

## 事件和回调函数

任务队列是一个事件的队列，IO设备完成一项任务，就在任务队列中添加一个事件，表示相关的异步任务可以进入执行栈了。主线程读取任务队列，就是读取里面有哪些事件。

任务队列中的事件，除了IO设备的事件外，还包括一些用户产生的事件（如点击、滚动）。只要指定过回调函数，这些事件发生时就会进入任务队列，等待主线程读取。

所谓回调函数，就是那些会被主线程挂起来的代码。异步任务必须执行回调函数，当主线程开始执行异步任务，就是执行对应的回调函数。

主线程的读取过程基本是自动的，只要执行栈一清空，任务队列上第一位的事件就自动进入主线程。但是，由于存在定时器功能，主线程首先会检查一下执行时间，某些事件只有到了规定的事件，才能返回主线程。

## 事件循环

![事件循环](./event-loop.png)

图来自Philip Roberts的演讲[《Help, I'm stuck in an event-loop》](http://vimeo.com/96425312)

上图中，主线程运行的嘶吼，产生堆和栈，栈中的代码调用各种外部API，它们在任务队列中加入各种事件。只要栈中的代码执行完毕，主线程就会读取任务队列，依次执行那些事件所对应的回调函数。

## 定时器

除了放置异步任务事件，任务队列还可以放置定时事件，即指定某些代码在多少事件之后执行。

```js
setTimeout(function(){console.log(1);}, 0);
console.log(2);

// 2
// 1
```

上面代码执行结果总是2,1，因为只有在执行完第二行之后，系统才会去执行任务队列中的回调函数。

## 事件循环的特点

- 执行至完成

每一个消息完整地执行后，其他消息才会被执行。

- 添加消息

点击一个附带点击事件处理函数的元素会添加一个消息，其他事件类似。

函数setTimeout函数接收两个参数：待加入队列的消息和一个延迟。这个延迟代表了消息被实际加入到队列的最小延迟时间。如果队列中没有其他消息，在这段时间过去之后，消息会被马上处理。但是，如果有其他消息，setTimeout消息必须等待其他消息处理完。

- 永不阻塞

JavaScript永不阻塞。处理I/O通常通过事件和回调来执行，所以当一个应用正等待一个XHR请求返回时，它仍然可以处理其他事情，比如用户输入。

## 宏任务与微任务

规范中规定task分为两大类，分别是macro task和micro task，并且每个macro task结束后，都要清空所有的micro task。

```js
for (macroTask of macroTaskQueue) {
    // 1. Handle current MACRO-TASK
    handleMacroTask();
      
    // 2. Handle all MICRO-TASK
    for (microTask of microTaskQueue) {
        handleMicroTask(microTask);
    }
}
```

- 宏任务：setTimeout、MessageChannel、postMessage、setImmediate
- 微任务：MutationObserver和Promise.then。

---

参考：

MDN

[阮一峰](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[Vue.js技术揭秘]([https://ustbhuangyi.github.io/vue-analysis/reactive/next-tick.html#js-%E8%BF%90%E8%A1%8C%E6%9C%BA%E5%88%B6](https://ustbhuangyi.github.io/vue-analysis/reactive/next-tick.html#js-运行机制))