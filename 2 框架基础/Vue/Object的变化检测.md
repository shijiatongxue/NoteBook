# Object变化检测

## 如何追踪变化

有两种方法可以检测到变化：

- Object.defineProperty
- Proxy (ES6)

```js
// Object.defineProperty

function defineReactive(data, key, val) {
  Object.defineProperty(data, key, {
  	emumarable: true,
    configurable: true,
   	get: function () {
    	return val       
    },
   	set: function (newVal) {
    	if (val === newVal) {
      	return
      }
      val = newVal
    }
  })
}
```

## 如何依赖收集

思考一下，我们之所以要观察数据，其目的是当数据的属性发生变化时，可以通知哪些曾经使用了该数据的地方。

```html
<template>
	<h1>{{ name }}</h1>
</template>
```

该模板中使用了数据 `name`，所以当它发生变化时，要向使用了它的地方发送通知。

对于上面的问题，先收集依赖，即把数据 `name` 的地方收集起来，然后等属性发生变化时，把之前收集好的依赖循环触发一遍就好了。

> 在getter中收集依赖，在setter中触发依赖。

## 收集依赖在哪里

思考一下，每个key都有一个数组，用来存储当前key的依赖。**假设依赖是一个函数**，保存在 `window.target` 上，现在就可以吧 `defineReactive` 稍微改造一下：

```js
function defineReactive(data, key, val) {
  let dep = [] // 新增
  Object.defineProperty(data, key, {
 		enumerable: true,
    configurable: true,
    get: function () {
    	dep.push(window.target) // 新增
     	return val
    },
    set: function () {
    	if (val === newVal) {
      	return 
      }
      // 新增
      for (let i = 0; i < dep.length; i++) {
     		dep[i](newVal, val)
      }
      val = newVal
    }
  })
}
```

这里我们新增了数组dep，用来存储被收集的依赖。

然后在set被触发时，循环dep以触发收集到的依赖。

然是这样写有点耦合，我们把收集依赖的代码封装到一个 `Dep` 类，它专门帮助我们管理依赖。使用这个类，我们可以收集依赖、删除依赖或者向依赖发送通知等。

```js
export default class Dep {
  constructor () {
  	this.subs = []
  },
	addSub (sub) {
 		this.subs.push(sub)
  },
  removeSub (sub) {
 		remove(this.subs, sub)
  },
  depend () {
  	if (window.target) {
    	this.addSub(window.target)
    }
  },
  notify () {
  	const subs = this.subs.slice()
   	for (let i = 0, l = subs.length; i < l; i++) {
    	subs[i].update()
    }
  }
}

function remove(arr, item) {
	if (arr.length) {
		const index = arr.indexOf(item)
    if (index > -1) {
    	return arr.splice(index, 1)
    }
  }
}
```

之后再改造一下 `defineReactive`：

```js
function defineReactive (data, key, val) {
  let dep = new Dep() // 修改
  Object.defineProperty(data, key, {
 		enumerable: true,
    configurable: true,
   	get: function () {
    	dep.depend() // 修改
      return val
    },
    set: function (newVal) {
    	if (val === newVal) {
      	return 
      }
     	val = newVal
    	dep.notify() // 新增
    }
  })
}
```

此时代码看起来就清晰多了，这也顺便回答了上面的问题，依赖收集到哪儿？收到到 `Dep` 中。

## 依赖是谁

上面的代码中，我们收集的依赖是 `window.target` ，那么到底是什么？我们究竟要收集谁呢？

收集谁，换句话说，就是当属性发生变化后，通知谁。

我们要通知用到数据的地方，而使用这个数据的地方有很多，而且类型还不一样，既有可能是模板，也有可能是用户写的一个watch，这时需要抽象出一个能集中处理这些情况的类。然后，我们在依赖收集阶段只收集这个封装好的类的实例进行，通知也只通知一个。接着，它再负责通知其他地方。它就是 `Watcher` 。

所以，收集谁？` Watcher` ！

## 什么是Watcher*

Watcher是一个中介角色，数据发生变化时通知它，然后它再通知其他地方。

关于Watcher，先看一个经典的使用方式： 

 ```js
// keypath
vm.$watch('a.b.c', function (newVal, oldVal) {
  // 做点什么
})
 ```

这段代码表示当data.a.b.c属性发生变化时，触发第二个参数中的函数。

```js
export default class Watcher {
  constructor (vm, expOrFn, cb) {
  	this.vm = vm
    this.getter = parsePath(expOrFn) // 触发属性的getter方法，读出data.a.b.c的内容
    this.cb = cb
  	this.value = this.get() // 初始化的值
  }

  // get方法有两个功能
  // 1. 初始化从data读取数据
  // 2. 外界从watcher读取数据
  get() {
  	window.target = this // 把watcher实例赋值给window.target
    let value = this.getter.call(this.vm, this.vm)
    window.target = undefined
    return value
  }
  
  // watcher通知外界发生了变化
 	update() {
  	const oldValue = this.value
    this.value = this.get() // 新的值
  	this.cb.call(this.vm, this.value, oldValue)
  }
}
```

```js
/ **
	* 解析简单路径
	*/

const bailRE = /[^\w.$]/
export function parsePath (path) {
  if (bailRE.test(path)) {
  	return
  }
  const segments = path.split('.')
  return function (obj) {
  	for (let i = 0; i < segments.length; i++) {
   		if (!obj) return
      obj = obj[segments[i]]
    }
    return obj
  }
}
```

这段代码可以把**自己**主动添加到data.a.b.c的Dep中去，是不是很神奇？

因为我在 `get` 方法中先把window.target设置成了this，也就是当前watcher实例，然后再读一下data.a.b.c的值，这肯定会触发getter。

## 递归侦测所有key

我们希望把数据中的所有属性（包括子属性）都侦测到，所以要封装一个Observer类。这个类的作用是将一个数据内的所有属性都转换成 `getter/setter` 形式，然后去追踪它们的变化：

```js
/ **
	* Observer类会附加到每一个被侦测的Object上。
 	*/
export class Observer {
  constructor (value) {
  	this.value = value
                      
    if (!Array.isArray(value)) {
    	this.walk(value)
    }
  }
  
  / **
		* walk会将每一个属性都转换为getter/setter的形式来侦测变化
		* 这个方法只有在数据类型为Object时被调用
    */
	walk (obj) {
  	const keys = Object.keys(obj)
   	for (let i = 0; i < keys.length; i++) {
    	defineReactive(obj, keys[i], obj[keys[i]])
    }
  }
}

function defineReactive(data, key, val) {
 	// 新增，递归子属性
  if (typeof val === 'object') {
  	new Observer(val)
  }
  let dep = new Dep()
  Object.defineProperty(date, key, {
  	enumerable: true,
    configurable: true,
    get: function () {
    	dep.depend()
      return val
    },
    set: function (newVal) {
    	if (val === newVal) {
      	return
      }
     	val = newVal
      dep.notify()
    } 
  })
}
```

在上面的代码中，我们定义了Observer类，它用来将一个正常的Object转换成被侦测的object。

然后判断数据的类型，只有Object类型的数据才会调用walk将每一个属性转换成getter/setter的形式来侦测变换。

最后，在defineReactive中新增new Observer(val)来递归子属性，这样我们就可以把data中的所有属性（包括子属性）都转换成getter/setter的形式来侦测变化。

当data中的属性发生变化时，与这个属性对应的依赖就会接收到通知。

也就是说，只要我们将一个object传到Observer中，那么这个object就会变成响应式的object。

## 关于Object的问题

了解了数据的变化是通过getter/setter来追踪的。也正是因为这种追踪方式，有些语法中即便是数据发生了变化，Vue.js也追踪不到。

比如，向object添加属性：

```js
var vm = new Vue({
  el: '#el',
 	template: '#demo',
  methods: {
 		action () {
   		this.obj.name = 'shi'
    }
  },
  data: {
 		obj: {}
  }
})
```

在action方法中，我们在obj上面新增了name属性，Vue.js无法侦测到这个变化，所以不会再向依赖发送通知。

再比如，从obj中删除一个属性：

```js
var vm = new Vue({
  el: '#el',
 	template: '#demo',
  methods: {
 		action () {
   		delete this.obj.name
    }
  },
  data: {
 		obj: {
   		name: 'shi'
    }
  }
})
```

上面删除了obj的name属性，而Vue.js无法侦测到这个变化，所以不会向依赖发送通知。

Vue.js通过 `Object.defineProperty` 来将对象的key转换为getter/setter的形式来追踪变化，但getter/setter只能追踪一个数据是否被修改，无法追踪新增属性和删除属性，所以才会导致上面例子中提到的问题。

为此Vue.js提供了两个API——vm.$set与vm.$delete，后面会介绍它们。

---

学习笔记：深入浅出Vue.js

