# React

## 特点

- 声明式开发
- 可以与其他框架并存

- 组件化

- 单向数据流

- 视图层框架
- 函数式编程

### 声明式开发

声明式开发：不直接操作DOM。

命令式开发：直接操作DOM，原生或者jQuery。

### 单向数据流

父组件通过属性向子组件传值，子组件无法直接修改父组件的数据；

子组件通过方法向父组件传值。

## 元素渲染

> 元素是构成 React 应用的最小砖块。

元素描述了你在屏幕想看到的内容。

```javascript
const element = <h1>Hello, world</h1>;
```

与浏览器 DOM 元素不同，React 元素是创建开销极小的普通**对象**。React DOM 会负责更新 DOM 来与 React 元素保持一致。

元素与组件不同，组件是由元素构成的。

### 将一个元素渲染为 DOM

```html
<div id="root"></div>
```

想要把一个 React 元素渲染到根 DOM 节点中，只需要把它们一起传入 `ReactDOM.render()`：

```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

## 组件 & Props

组件，从概念上类似于JavaScript函数。它接收任意的入参（即“props”），并返回用于描述页面展示内容的React元素。

### 函数组件与 class 组件

```javascript
function Welcome(i) {
  return <h1>Hello, {props.name}</h1>;
}
```

该函数是一个有效的 React 组件，因为它接收唯一带有数据的"props”对象并返回一个React元素。这类组件被称为“函数组件”。

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上述两个组件在React里是等效的。

### 渲染组件

React元素可以是普通的 DOM 元素也可以是用户自定义的组件：

```javascript
const element = <Welcome name="Sara"/>;
```

当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性转换为单个对象传递给组件，这个对象被称为“props”。

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
const element = <Welcome name="Sara"/>;
ReactDOM.reader(element, document.getElementById('root'));
```

> 注意：组件名称必须以大写字母开头，小写字母开发的组件视为原生 DOM 标签。

### 组合组件

组件可以在其输出中引用其他组件。

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

### 提取组件

将组件拆分为更小的组件。

最初看上去，提取组件可能是一件繁重的工作，但是，在大型应用中，构建可复用组件库是完全值得的。根据经验来看，如果 UI 中有一部分被多次使用，或者组件本身就足够复杂，那么他就是一个可复用组件的候选项。

### Props 的只读性

React 非常灵活，但它有一个严格的规则：

**所有 React 组件都必须像[纯函数](https://en.wikipedia.org/wiki/Pure_function)一样保护它们的 props 不被更改。**

## State & 生命周期

State 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。

```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

### 正确地使用 State

关于 `setState()` 你应用了解的三件事：

1. 不要直接修改 State

直接修改不会重新渲染组件，构造函数是唯一可以给 `this.state` 赋值的地方。

```javascript
// Wrong
this.state.comment = 'Hello';

// Correct
this.setState({comment: 'Hello'});
```

2. State 的更新可能是异步的

处于性能的考虑，React 可能会把多个 `setState()` 调用合并成一个调用。

因为 `this.props` 和 `this.state` 可能会异步更新，所以不要依赖他们的值来更新下一个状态。

例如：此代码可能会无法更新计数器：

```javascript
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment
});
```

要解决这个问题，可以让 `setState()` 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 作为第二个参数：

```javascript
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

或

```javascript
// Correct
this.setState(function(state, props) {
	return {
  	counter: state.counter + props.increment 
  }
});
```

3. State 的更新会被合并

当你调用 `setState()` 的时候，React 会把你提供的对象合并到当前的 state。这里的合并是浅合并。

### 数据是向下流动的

```javascript
// 父组件传值
<FormattedDate date={this.state.date} />

// 子组件通过 props 接收
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>
}
```

## 事件处理

React 元素的事件处理和 DOM 元素的很相似，但是有一点语法的不同：

- React 事件的命名采用小驼峰式，而不是纯小写。
- 使用 JSX 语法时你需要传入一个函数作为事件处理函数，而不是一个字符串。

```javascript
// React事件处理
<button onClick={activateLasers}>
  Activate Laser
</button>
```

在 React 中另一个不同点是你不能通过返回 `false` 的方式阻止默认行为。你必须显式的使用 `preventDefault`。

> 注意：在 JavaScript 中， class 的方法默认不会绑定 `this`。如果你忘记绑定 `this` 并把它传入事件绑定，当你调用这个函数的时候 `this` 的值为 `undefinded`。

### 向事件处理程序传递参数

```javascript
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
// 或
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

## 条件渲染

```javascript
<Fragment>
  {showMyComponent
    ? <MyComponent />
    : <OtherComponent />}
</Fragment>
```

## 列表 & Key

```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

## 表单

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('提交的名字: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          名字:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

## 状态提升

通常，多个组件需要反映相同的变化数据，这时我们建议将共享状态提升到最近的共同父组件中去。

## 组合 vs 继承

React 有十分强大的组合模式。我们推荐使用组合而非继承来实现组件间的代码重用。

## React哲学

- 将设计好的 UI 划分为组件层级

- 单一功能原则
- 先静后动
- 确定 UI State 的最小且完整表示

通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。

- 添加反向数据流

---

参考：[文档](https://zh-hans.reactjs.org/docs/thinking-in-react.html)