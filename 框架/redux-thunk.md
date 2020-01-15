# redux-thunk

Redux 的 Thunk 中间件

## 为什么使用

利用基本的 Redux，你可以通过 dispatch action 来执行简单的同步代码。这个中间件拓展了 store 的能力，可以让你在 store 写异步代码。

## 动机

Redux Thunk 可以让你的 action creator 返回一个函数而不是一个 action。thunk 可以延迟 action 的 dispatch，或在条件为真时执行 dispatch。它返回的函数接收 `dispatch` 和 `getState` 作为参数。

```javascript
const INCREMENT_COUNTER = 'increment_counter'

function increment() {
  return {
    type: INCREMENT_COUNTER
  }
}

function incrementAsync() {
  return (dispacth) => {
    setTimeout(() => {
      dispatch(increment())
    }, 1000)
  }
}
```

执行条件 dispatch：

```javascript
function incrementIfOdd() {
  return (dispacth, getState) => {
    const { counter } = getState();
    
    if (counter % 2 === 0) {
      return;
    }
    
    dispatch(increment())
  }
}
```

## 什么是 thunk

thunk 是一个封装表达式以延迟计算的函数。

```javascript
let x = 1 + 2

// foo is a thunk!
let foo = () => { 1 + 2}
```

## 安装

```bash
npm install redux-thunk
```

使用 Redux Thunk，需要引入 `applyMiddleware`：

```javascript
import { createStore, applyMiddle } from 'redux'
import thunk from 'thunk'
import rootReducer from './reducers/index'

const store = createStore(rootReducer, applyMiddleware(thunki))
```





---

参考：[github](https://github.com/reduxjs/redux-thunk)