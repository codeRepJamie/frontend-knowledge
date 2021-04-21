# 布局

## 实现垂直水平居中
### 已知宽度高度 position: absolute
```css
.wrapper{
  position: relative;
}
.box{
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
}
```

### 未知宽度高度 transform
```css
.wrapper{
  position: relative;
}
.box{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%)
}
```

### 定义高度 transform
```css
.wrapper{
  display: flex;
  justify-content: center;
  align-items: center;
}
.box{
  height: 100px;
}
```

### 未知宽度高度 table
```css
.wrapper{
  display: table;
}
.box{
  display: table-cell;
  vertical-align: middle;
}
```
