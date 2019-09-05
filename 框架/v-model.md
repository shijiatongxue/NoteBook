# v-model

v-model本质上是v-bind和v-on的语法糖。

```html
<input v-model="message"/>

// 等同于
<input :value="message" @input="messge=$event.target.value"/>
```

