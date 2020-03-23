# eventBus

```js
// 创建总线

import Vue from 'vue'
export default new Vue()
```

```js
// 引入并发布事件

import eventBus from '../eventBus‘
eventBus.$emit('newVideo', params)
```

```js
// 引入并监听事件

import eventBus from '@/views/curriculum/course/eventBus'
eventBus.$on('newVideo', url => {
  this.playerOptions.sources[0].src = url
})
```

优点：

- 方便非父子组件通信；

- 使用原理简单，代码量少。

缺点：

- 使用同一个Vue实例，容易出现重复触发的场景；

- 对于大型项目，总线没有Vuex使用清晰。

