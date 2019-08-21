# Vuex

![Vuex](./vuex.png)

## State

### 单一状态树

Vuex使用单一状态树，用一个对象就包含了全部的应用层级状态。

### 在Vue组件中获得Vuex状态

由于Vuex的状态存储是响应式的，从store示例中读取状态最简单的方法就是在计算属性中返回某个状态：

```js
const Counter = {
  template: `<div>{{ count }}</div>`
  computed: {
  	count () {
    	return store.state.count
    }
	}
}
```

每当 ```store.state.count``` 变化时，都会重新求取计算属性，并且触发更新相关的DOM。

然而，这种模式导致组件依赖全局状态单例。在模块化的构建系统中，在每个需要使用state的组件中需要频繁地导入，并且在测试组件时需要模拟状态。

Vuex通过 ```store``` 选项，提供了一种机制将状态从根组件“注入”到每个子组件中（需调用 ```Vue.use(Vuex)```）：

```js
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给"store"选项，这可以吧store的实例注入到所有的子组件。
  store,
  components: {Counter},
  template: `
		<div class="app">
			<counter></counter>
		</div>
	`
})
```

子组件访问：

```js
const Counter = {
  template: `<div> {{ count }} </div>`,
  computed: {
  	count () {
    	return this.$store.state.count
    }
  }
}
```

### mapState 辅助函数

当一个组件需要获取多个状态时，将这些状态都声明为计算属性有些冗余。我们可以使用mapState辅助函数帮助我们生成计算属性：

```ls
import { mapState } from 'vuex'

export default {
	computed: mapState({
		count: state => state.count,
		// 传字符串参数'count'等同于`state => state.count`
		countAlias: 'count',
		// 获取局部状态不能使用箭头函数，因为它没有this
		countPlusLocalState (state) {
			return state.count + this.localCount
		}
	})
}
```

当映射的计算属性和名称与state子节点的名称相同时，可以给mapState传一个字符串数组：

```js
computed: mapState([
  // 映射this.count为store.state.count
  'count'
])
```

### 对象展开运算符

将mapState于局部计算属性混合使用（提案中）：

```js
computed: {
  localComputed () {},
  ...mapState({})
}
```

## Getter

对store中的state进行计算，可以认为是store的计算属性。就像计算属性一样，getter的返回值会根据它的依赖缓存起来，且只有当它的依赖发生了改变才会被重新计算。

Getter接受state作为其第一个参数：

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

### 属性访问Getter

```js
store.getters.doneTodos
```

Getters也可以接受其他getter作为第二个参数：

```js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

在**任何组件使用**它：

```
computed: {
	doneTodosCount () {
		return this.$store.getters.doneTodosCount
	}
}
```

### 方法访问Getter

通过让getter返回一个函数，来实现给getter传参。

```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```

```js
store.getters.getTodayById(2)
```

注意：通过方法访问时，每次都会去调用，而不会缓存结果。

### mapGetters辅助函数

mapGetters辅助函数仅仅是将store中的getter映射到局部计算属性：

```js
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

如果想给getter属性另取一个名字，使用对象形式：

```js
mapGetters({
  // 把`this.doneCount`映射为`this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

## Mutation

更改Vuex的store状态的唯一方法是提交mutation。每个mutation都有一个字符串事件类型和一个回调函数。

```js
const store = new Vuex.store({
  state: {
 		count: 1
  },
  mutations: {
  	increment (state) {
    	state.count++
    }
  }
})
```

```js
// 调用mutation handler
store.commit('increment')
```

### 提交载荷

可以传入额外的参数，它叫做mutation的载荷（payload）：

```js
mutations: {
  	increment (state, n) {
    	state.count += n;
    }
}
```

```js
store.commit('increment', 10)
```

大多数情况下，载荷应该是一个对象：

```js
mutations: {
  increment (state, payload) {
  	state.count += payload.amount
  }
}
```

```js
store.commit('increment', {amount: 10})
```

### 对象风格提交

```js
store.commit({
  type: 'increment',
  amount: 10
})
```

```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

### 给对象添加新属性

```js
Vue.set(obj, 'newProp', 123)

// 或
state.obj = {...state.obj, newProp: 123}
```

### 使用常量替代事件类型

让你的代码合作者对整个APP包含的mutation一目了然：

```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```

```js
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

### Mutation必须是同步函数

每一条mutation被记录，devtools都需要捕捉到前一状态和后一状态的快照。然而， 异步函数中的回调让着不可能完成。

### 在组件中提交Mutaion

- this.$store.commit(‘xxx’)
- mapMutations辅助函数将组件中的methods映射为store.commit调用

```js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

## Action

处理异步。

注册一个简单的action：

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

可以通过context.commit调用一个mutation或通过context.state和context.getters来获取state和getters。

使用参数解构简化代码：

```js
actions: {
  increment( {commit} ){
  	commit('increment')
  }
}
```

### 分发Action

```js
store.dispath('increment')
```

Action不受约束，可以在内部进行异步操作，不需要异步的时候，直接使用Mutation就是了：

```js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

传入变量：

```js
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

### 在组件中分发Action

- this.$store.dispath(‘xxx’)
- mapActions辅助函数将methos映射为store.dispath

```js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

### 组合Action

store.dispath可以处理被触发的action的处理函数返回的Promise，并且store.dispath仍旧返回Promise：

```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

使用：

```js
store.dispatch('actionA').then(() => {
  // ...
})
```

在另外一个action使用：

```js
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

结合aync/await：

```js
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```

## Module

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

### 模块的局部状态

对于模块内部的mutation和getter，接收的第一个参数是模块局部的状态对象。

```js
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```

对于模块内部的action，局部状态通过context.state暴露出来，根节点状态则为context.rootState：

```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的getter，根节点状态会作为第三个参数暴露出来：

```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

## 项目结构

需要遵守的规则：

1. 应用层级的状态应该集中到单个store对象中
2. 提交mutation是更改状态的唯一办法，并且这个过程是同步的
3. 异步逻辑都应该封装到action里面

对于一个大型应用，参考目录如下：

```sh
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── mutations.js      # 根级别的 mutation
    └── modules
        ├── cart.js       # 购物车模块
        └── products.js   # 产品模块
```







