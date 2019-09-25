单页面应用的路由细节。

将组件映射到路由，然后告诉Vue Router在哪里渲染它们。

# 1 起步

## HTML

```html
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```

## JavaScript

```javascript
// 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

// 1. 定义 (路由) 组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')

// 现在，应用已经启动了！
```

# 2 动态路由匹配

将不同ID的用户，都使用这个组件来渲染。

```js
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

现在，像 /user/foo 和 /user/bar 都将映射到相同的路由。

一个“路径参数”使用冒号标记。当匹配到一个路由时，参数值会被设置到this.$route.params，可以在每个组件内使用。我们可以更新 User 的模板，输出当前用户的ID：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```

## 响应路由参数的变化

当使用路由参数时，例如从 /user/foo 导航到 /user/bar，**原来的组件实例会被复用**。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。**不过，这也意味着组件的生命周期钩子不会再被调用。**

复用组件时，相对路由参数的变化做出响应的化，可以简单地watch $route 对象：

```js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

或使用导航守卫：

```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

## 捕获404

```js
{
  path: '*',
  component: 'NotFoundComponent'
}
```

当使用一个通配符时，$route.params内会自动添加一个名为pathMatch参数。它包含了URL通过通配符被匹配的部分：

```js
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

## 高级路由匹配

[path-to-regexp文档](https://github.com/pillarjs/path-to-regexp#parameters)

# 3 嵌套路由

主页App：

```html
<div id="app">
  <router-view></router-view>
</div>
```

路由：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```



在User组件的模板添加一个<router-view>：

```js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

子路由设置，需要在VueRouter的参数中使用children：

```js
const router = new VueRouter({
  routes: [
  	{path: '/user/:id', component: User,
     children: [
      {
      	path: '',
        component: UserHome
      },
      // 其他子路由
     	{
        // 当/user/:id/profile匹配成功，
        // UserProfile 会被渲染在User的<router-view>中
      	path: 'profile',
        component: UserProfile
      },
      {
      	path: 'posts',
        component: UserPosts
      }
     ]
    }
  ]
})
```

# 4 命名路由

给路由一个name属性：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```

使用：

```js
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```

这跟代码调用router.push()是一回事：

```js
router.push({ name: 'user', params: { userId: 123 }})
```

# 5 命名视图

同时展示多个视图，而不是嵌套。例如创建一个有主内容和侧边栏的视图。

在这种情况下，一个界面拥有多个单独命名的视图，而不是只有一个单独的出口。

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用components配置（带上s）：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

进阶：[嵌套命名视图]([https://router.vuejs.org/zh/guide/essentials/named-views.html#%E5%B5%8C%E5%A5%97%E5%91%BD%E5%90%8D%E8%A7%86%E5%9B%BE](https://router.vuejs.org/zh/guide/essentials/named-views.html#嵌套命名视图))

# 6 重定向和别名

## 重定向

重定向也是通过routes来配置完成，下面是从/a重定向到/b：

```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

重定向的目标也可以是一个命名的路由：

```js
const router = new VueRouter({
  routes: [
  	{ path: '/a', redirect: { name: 'foo'}}
  ]
})
```

甚至是一个方法，动态返回重定向目标：

```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

## 别名

/a的别名是/b，意味着，当用户访问/b时，URL会保持/b，但是路由匹配则为/a，就像用户访问/a一样。

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

# 7 History模式

vue-router默认hash模式——使用URL的hash来模拟一个完整的URL，于是当URL改变时，页面不会重新加载。

如果不想要很丑的hash，我们可以用路由的history模式，这种模式充分利用history.pushState API来完成URL跳转而无须重新加载页面。

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

# 8 数据获取

进入路由后，需要从服务器获取数据。有两种方式可以实现：

- 导航后获取：先完成导航，然后在接下来的组件生命周期钩子中获取数据。在数据获取期间显示“加载中”之类的指示。
- 导航完成之前获取：导航完成前，在路由进入的守卫中获取数据，在数据获取成功后执行导航。

## 导航后获取

假如我们有一个Post组件，需要基于$route.params.id获取文章数据：

```html
<template>
  <div class="post">
    <div class="loading" v-if="loading">
      Loading...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
```

```js
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace getPost with your data fetching util / API wrapper
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

## 导航前获取

在导航转入前获取数据，我们可以在接下来的组件的beforeRouteEnter守卫中获取数据，当数据获取成功后只调用next方法。

```js
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  methods: {
    setData (err, post) {
      if (err) {
        this.error = err.toString()
      } else {
        this.post = post
      }
    }
  }
}
```

这种方法获取数据时，用户会停留在当前的页面，因此建议在数据获取期间，显示一些进度条或者别的提示。如果数据获取失败，同样也有必要展示一些全局的错误提醒。

# 9 滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。vue-router就能做到。

当创建一个Router实例，你可以提供一个scrollBehavior方法:

```js
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return 期望滚动到哪个的位置
  }
})
```

对于所有路由导航，简单地让页面滚动到顶部：

```js
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

模拟原生浏览器表现：

```js
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition
  } else {
    return { x: 0, y: 0 }
  }
}
```

滚动到锚点：

```js
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
    }
  }
}
```

# 10 路由懒加载

[文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

# 11 守卫

官方的说法叫导航守卫，实际上就是发生在路由路径切换的时候，执行的一系列钩子函数。

Vue-router提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的，单个路由独享的，或者组件级的。

## 全局

- beforeEach
- beforeResolve
- aferEach

这三个钩子主要是用于登录验证，也就是路由还没跳转提前告知，以免跳转了再通知就晚了。

### 全局前置守卫——beforeEach

```js
const router = new VueRouter({...})
router.beforeEach((to, from, next) => {
  // ...
})
```

全局导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫resolve完之前一直处于等待中。

每个守卫方法接收三个参数：

- to：Route：即将要进入的目标的路由对象
- from：Route：当前导航正要离开的路由
- next：Function：一定要调用该方法来resolve这个钩子。执行效果依赖next方法调用的参数。
  - next()：进行管道中的下一个钩子。如果钩子执行完了，则导航的状态就是confirmed。
  - next(false)：中断当前的导航。如果浏览器的URL改变了（可能是用户手动或者浏览器后退按钮），那么URL地址会重新回到from路由对应的位置（主要用于验证不通过的处理）。
  - next(‘/’)或者next({path: ‘/’})：跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向next传递任意位置对象，且允许设置注入replace：true、name： ‘home’之类的选项以及然和用在router-link的to属性或router.push中的选项。
  - next(error)：如果传入next参数是一个Error实例，则导航会被终止且该错误会被传递给router.onError()注册过的回调。

**确保要调用next方法，否则钩子就不会被resolved。**

### 全局解析守卫——beforeResolve

> 2.5.0新增

在2.5.0+可以使用router.beforeResolve注册一个全局守卫。这和router.beforeEach类似，区别是在导航被确认（afterEach）之前，同时在所有组件内守卫（beforeEach）和异步路由组件（beforeRouteEnter）被解析之后，解析守卫就被调用。

### 全局后置钩子——aferEach

你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受next函数也不会改变导航本身。

```js
router.afterEach((to, from) => {
  // ...
})
```

## 路由级

你可以在路由配置上直接定义**beforeEnter**守卫：

```js
const router = new VueRouter({
  routes: [
  	{
  		path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
      	// ...
      }
    }
  ]
})
```

这些守卫和全局前置守卫的方法和参数是一样的。

## 组件级

最后，你可以在组件内直接定义以下路由导航守卫：

- beforeRouteEnter
- beforeRouterUpdate（2.2增加）
- beforeRouterLeave

```js
const Foo = {
 	template: `...`,
  beforeRouteEnter (to, from, next) {
  	// 在渲染该组件的对应路由被confirm前调用
  	// 不能获取组件实例this
   	// 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
  	// 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和/foo/2 之间跳转的时候，
   	// 由于会渲染同样的组件，因此组件实例会被调用。而这个钩子就会在这个情况下调用。
    // 可以访问组件实例this
  },
	beforeRouteLeave (to, from, next) {
 		// 导航离开该组件的对应路由时调用
    // 可以访问组件实例this
  }
}
```

这里离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过next(false)来取消。

```js
beforeRouteLeave(to, from, next) {
 	const answer = window.comfirm('Do you really wan to leave? you have unsaved changes!')
  if (answer) {
  	next()
  } else {
		next()
  }
}
```

## 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用离开`beforeRouteLeave`守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。(beforeCreate，created，beforeMount，mounted)
12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数。

## 实例

```js
// 登录

router.beforeEach((to, from, next) => {
  if (isLogin || to.name === 'login') {
 		next()
  } else {
    next({name: 'login'})
  }
}
```



---

参考：

[Vue Router](https://router.vuejs.org/zh/guide/)

Vue.js技术揭秘

[做个俗人](https://www.zhihu.com/people/tuo-yan-zheng-67-99)