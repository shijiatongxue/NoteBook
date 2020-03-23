# filter-effects

## 毛玻璃效果

```css
body, main::before {
  background: url(tiger.jpg) 0 / cover fixed;
}

main {
  position: relative;
  background: hsla(0,0%,100%,.3);
  overflow: hidden;
}

main::before {
  content: '';
  postion: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  filter: blur(20px);
  margin: -30px;
}
```

