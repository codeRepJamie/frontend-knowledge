# 选择器 

## 简单选择器
- 元素选择器 tagName
- 类选择器 .class
- id选择器 #id
- 通用选择器 *

## 属性选择器
- [attr]含有属性attr
- [attr=val]属性attr等于val值
- [attr~=val]属性attr含有val值（以空格分开）
- [attr|=val]属性attr值为“val”或是以“val-”为前缀,例如 class="zh-CN"之类的
- [attr^=val]属性attr值为val开头
- [attr$=val]属性attr值为val结尾
- [attr*=val]属性attr值包含val

## 常用伪类
- 表单
  - :checked 选中
  - :read-only 只读
  - :valid 有效
  - :enabled 可用的
- 行为
  - :hover 鼠标悬浮ansf
  - :active 鼠标按下
  - :focus 控件获取焦点
  - :visited 已被访问过的
- 选择
  - :first-child 父元素第一个子元素
  - :first-of-type 父元素第一个子元素（指定标签）
  - :last-child 父元素最后一个子元素
  - :last-of-type 父元素最后一个子元素（指定标签）
  - :nth-child(n) 父元素第几个元素（1递增）
  - :nth-last-child(n) 父元素倒数第几个元素（1递增）
  - :nth-of-child(n) 父元素第几个元素（1递增）
  - :nth-last-of-type(n) 父元素倒数第几个元素（1递增，指定标签）
  - :only-child 父元素的唯一子元素
  - :only-of-type 父元素的唯一子元素（指定标签）
  - :not 否定选择器 如 p:not(.fancy)


## 伪元素
- ::after
- ::before

## 组合器
- A,B 满足A和B任意元素
- A B 满足B是A的子节点
- A>B 满足B是A的直接子节点
- A+B 满足B是A的下一个兄弟节点
- A~B 满足B是A之后的兄弟节点（AB都有相同父节点，B在A之后，不一定紧邻）

## 伪类与伪元素区别
- 单冒号（:）用于css伪类 伪类是选择器的一种，它用于选择处于特定状态的元素
- 双冒号（::）用于css伪元素 伪元素以类似方式表现，不过表现得是像你往标记文本中加入全新的HTML元素一样，而不是向现有的元素上应用类

## css优先级计算
- 最后载入的样式为准
- 就近原则，同权情况下定义最近者优先
- 优先级：!important>内联样式>id>class/属性选择器/伪类>tag