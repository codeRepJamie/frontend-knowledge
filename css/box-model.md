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
- dom.style.width/height(只能取内联样式，可读可写)
- dom.currentStyle.width/height (获取运行时元素的样式，IE支持，非W3C标准)
- getComputedStyle(dom).width/height(获取运行时元素的样式，W3C标准，只读)
- getBoundingClintRect().width/height(返回元素的大小及其相对于视口的位置，W3C标准)
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
行内格式化上下文(Inline Formatting Contexts)
- 行内框（inline box）
- 每个行内元素会形成一个行内框，行内框是浏览器渲染模型中的一个概念，无法显示出来，在没有其他因素（padding等）影响的时候，行内框等于内容区域
- 行框（line box）
- 行框是指文本行的一个虚拟的矩形框，是是浏览器渲染模型中的一个概念
- 行框高度等于本行内所有元素中行内框最大的值，当有多行内容时，每行都会有自己的行框。

规则
- 行内元素从容器的顶端开始，一个接一个地水平排布。
- 水平的padding，margin，border对行内元素有效，但垂直的padding，margin，border对行内元素无效，不影响其高度
- 一个水平行中的行内框组成了行框
- 行框的高度始终容下所有的行内框，并只与行高有关，行内框小于行框时，行内框垂直位置由vertical属性决定
- 行框的宽度由包含块和浮动元素决定：当不存在浮动元素时，行框的宽度等于包含块的内容区域的宽度
- 当存在浮动元素时，因为行内元素会环绕浮动元素布局的特性，行框的宽度会比包含块的内容区域的宽度小
- 当几个行内框在水平方向上无法放入一个行框时，行框将会被分割为几个行框，padding，margin，border在分割处没有视觉效果，如果行框无法分割（当单词过长或设定不换行），则会溢出。
## GFC

## 清除浮动方法
- div加height (写死高度)
- 结尾加div clear:both (麻烦每次多加元素)
- 父级定义伪类:after 伪类添加clear:both和zoom (推荐)
- 父级定义overflow:hidden(BFC，里面元素不能突出)
- 父级定义浮动属性(BFC,可能父元素也会造成问题)
- 结尾处添加br标签:clear:both(麻烦)
