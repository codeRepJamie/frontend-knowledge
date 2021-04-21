# 综合问题

## 可替换元素与不可替换元素
## 显示元素与隐藏元素
- 显示元素就是绝大多少能够显示图形的元素
  - css2.1定义为：块级元素（block-level）和行内元素（inline-level，也译作“内联”元素）。
- 元素是隐藏，无任何高度宽度，一般在head元素内
  - 常见有：meta、title、description、keyword、link、style

## 显示元素与隐藏元素

## display:none 与 visibility:hidden;区别
- display:none完全从渲染树消失，不占任何空间
- visibility:hidden 不会让元素从渲染树消失，会占用空间，只有内容是不可见
- display:none非继承属性，子元素是由于父元素不在渲染树中造成
- display:hidden是继承属性，子元素可以设置visble显示
- 修改display会造成文档重排，修改visibility属性只会造成重绘
- 度屏器不会读取display:none;会读取display:hidden内容

## link与@import区别
- link是html方式 @import 是css方式
- link 支持并行下载 @import 过多嵌套导致串行下载，会导致样式暂时失效
- link 可以通过rel="alternate stylesheet"指定候选样式??
- 浏览器对link支持早于@import, @import可对老浏览器隐藏样式

## FOUC
- Flash Of Unstyled Content 用户定义样式表之前浏览器使用默认样式显示文档，用户样式加载渲染之后显示新文档，造成闪烁。
- 解决方法：把样式表放<head>

## CSS可继承元素
## 包含块
## 层叠上下文
## css3新特性
- 新增css选择器
- 圆角 broder-radius
- 阴影和反射 (box-shadow与box-reflect)
- 多列布局 column-count
- 文字阴影 text-shadow
- 线性渐变
- 颜色单位 rgba
- 变形 transform
- 动画 animation keyframe
- 过渡动画 transition

## css合并方法
- 避免使用@import引入多个css文件， 使用工具合并

## css性能优化
- css压缩与合并，Gzip压缩
- css放head文件，不使用@import
- 尽量用缩写，合理使用选择器

## base64优缺点
- 减少HTTP请求
- 耗cpu编码

## marign collaspe
- 相邻两个盒子（兄弟或祖先）外边距结合成一个单独外边距，这种合并称为margin collapse
- 计算规则
- 两个相邻外边距为正数，折叠结果为两者最大值
- 两个相邻外边距为负数，折叠结果为两者绝对值最大值
- 两个相邻外边距为一正一负，折叠结果为两者相加之和

## rgba和opacity区别
- opacity作用于元素与子元素所有内容透明度
- rgba只作用于背景颜色，子元素不继承透明效果
