# css3-background

css3-background 模块包括背景和边框。

## 半透明边框

> RGBA/HSLA 颜色

```css
border: 10px solid hsla(0,0%,100%,.5);
background: white;
background-clip: padding-box;
```

## 多重边框

> box-shadow

box-shadow 支持逗号分隔语法，可以创建任意数量的投影。

```css
background: white;
box-shadow: 0 0 0 10px red, 0 0 0 30px blue;
```

唯一需要注意的是，box-shadow 是层次叠加的，第一层投影位于最顶层，依次类推。

## 灵活的背景定位

> background-position

```css
background: url(code-pirate.svg)
						no-repeat bottom right #58a;
background-postion: right 20px bottom 10px;
```

> background-origin

默认情况下，background-position 是以 padding-box 为参考的。background-origin 可以改变这种行为。

```css
padding: 10px;
background: url(code-pirate.svg) no-repeat #58a 
						bottom right;
background-origin: content-box;
```

> calc()方案

```css
background: url(code-pirate.svg) no-repeat;
background-position: calc(100% - 20px) calc(100% - 10px);
```

不要忘记在 calc 内部 + 和 - 两侧各加一个空白字符，否则会产生解析错误。

## 边框内圆角

> outline，box-shadow

两个事实：描边不会跟着元素的圆角走，但 box-shadow 会

```css
background: tan;
border-radius: .8rem;
padding: 1em;
box-shadow: 0 0 0 .6em #655;
outline: .6em solid #655;
```

## 条纹背景

> background-size

```css
background: linear-gradient(45deg, red 25%, blue 0, blue 50%, red 0, red 75%, blue 0);
background-size: 42px 42px;
```

---

参考：CSS揭秘



