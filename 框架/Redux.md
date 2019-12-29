# 上手

## 安装

```bash
# npm
npm install redux --save

# yarn 
yarn add redux
```

## 辅助插件

- Redux Toolkit

## 示例

Redux 由唯一的 store 组成，它保存整个应用的 state 。改变 state 树需要发射 action，action 是一个对象，它描述了发生了什么。把这个对象转化到 state 树，需要用到 reducer。

核心要素：

- state
- action
- reducer

```javascript
import { creatState } from 'redux';

// 创建 store 的时候需要把 reducer 传进去
let store = createState(counter);

// reducer
function counter(state=0, action) {
    switch (action.type) {
        case 'INCREMENT':
          return state + 1
        case 'DECREMENT':
          return state - 1
        default:
          return state
      }
}

// 组件通过 subscribe 方法订阅 state tree 的变化
store.subscribe(() => console.log(store.getState()))


// action
store.dispatch({type: 'INCREMENT'})
```

# 三项原则

## store 全局唯一

## state 只读

改变 state 的唯一方式就是发射一个 action，用一个对象描述事件内容。

## reducer 是纯函数

由于是纯函数，你不能直接修改 state。一开始你可以写一个 reducer，随着项目扩大，可以把 reducer 拆分成小的 reducer。

```javascript
// reducer1
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

// reducer2
function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}
import { combineReducers, createStore } from 'redux'
// combined
const reducer = combineReducers({ visibilityFilter, todos })
const store = createStore(reducer)
```







