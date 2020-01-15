# css3-ui

## 切角

#### 方形切角

```css
background: #58a; /* 回退方案 */
background: linear-gradient(135deg, transparent 15px, #58a 0) top left,
			linear-gradient(-135deg, transparent 15px, #58a 0) top right,
			linear-gradient(-45deg, transparent 15px, #58a 0) bottom right,
			linear-gradient(45deg, transparent 15px, #58a 0) bottom left;
background-size: 50% 50%;
background-repeat: no-repeat;
```

#### 弧形切角

```css
background: #58a; /* 回退方案 */
background: radial-gradient(circle at top left, transparent 15px, #58a 0) top left,
			radial-gradient(circle at top right, transparent 15px, #58a 0) top right,
			radial-gradient(circle at bottom right, transparent 15px, #58a 0) bottom right,
			radial-gradient(circle at bottom left, transparent 15px, #58a 0) bottom left;
background-size: 50% 50%;
background-repeat: no-repeat;
```

