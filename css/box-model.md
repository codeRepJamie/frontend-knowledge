# 盒子模型

## 盒子模型
在一个文档中，每个元素都被表示为一个矩形的盒子。确定这些盒子的尺寸, 描述以下属性
- conent内容
- padding填充
- border边框
- margin边距

## 标准模型
- display: content-box
- 盒模型大小包含：
- conent内容


## IE模型
- display: broder-box
- 盒模型大小包含：
- padding填充
- padding填充
- border边框

## 获取盒子默认宽高方法
## 块级元素(1)
- 块级元素可以设置宽（width）和高（height），独占一行
- 只出现在body，默认继承父元素高度
- 元素：div ul ol li dl dt dd h1-h6 p...

## 行内元素(1)
- 行内元素不可以设置宽（width）和高（height）和垂直方向外边距(margin)，高度跟随字体行高，宽度跟随内容宽度（可替换元素除外），不独占一行
- 
- 元素 a b span img input strong select...

## BFC
块格式化上下文（block formatting context）
- BFC元素是一个独立的容器，外面的元素不会影响子元素，子元素也不会影响元素外面的元素
- 不同BFC的子元素的垂直方向的外边距边不会发生margin重叠（BFC场景1）
- 计算元素高度，浮动元素会参与计算（BFC场景2）
- 元素与浮动元素不会发生围绕（BFC场景3）
  
BFC元素
- 根元素
- 浮动元素 float不为none
- 绝对定位元素 position: absolute或fixed
- display取值table,table-cell,table-caption,inline-block,flex,inline-flex.
- overflow不为visible

场景
- 消除margin Collapse
- 阻止子元素围绕浮动元素
- 消除元素只有浮动子元素时候，高度为0

## IFC
## GFC

## 清除浮动方法
- div加height (写死高度)
- 结尾加div clear:both (麻烦每次多加元素)
- 父级定义伪类:after 伪类添加clear:both和zoom (推荐)
- 父级定义overflow:hidden(BFC，里面元素不能突出)
- 父级定义浮动属性(BFC,可能父元素也会造成问题)
- 结尾处添加br标签:clear:both(麻烦)
