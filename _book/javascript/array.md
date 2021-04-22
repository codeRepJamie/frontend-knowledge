# 数组相关方法


## 基本方法
```js
var fruits = ['Apple', 'Banana'];

// 添加数组末尾 - push
fruits.push('Orange'); // => 3
// 删除数组末尾 - pop
fruits.pop(); // => 'Orange'

// 删除数组最前面（头部）的元素
fruits.shift(); // => 'Apple'
// 添加元素到数组的头部
fruits.unshift('Strawberry') // => 2

// 找出某个元素在数组中的索引
fruits.indexOf('Banana'); // => 1
// 删除第二个元素
fruits.splice(1, 1); // ["Banana"]
// 复制数组
fruits.slice() // ['Strawberry']
```

## slice
```js
// 特性：slice 不会修改原数组

const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];
// 空参数
animals.slice(); // => 复制数组 ['ant', 'bison', 'camel', 'duck', 'elephant']

// start 开始索引为正数 从0开始, 从指定地方（包含）截取到最后
animals.slice(1) // => ['bison', 'camel', 'duck', 'elephant']
// start 开始索引为负数 （start换算animals.length-2) 倒数第二个元素到包含最后一个元素截取
animals.slice(-2) // => ['duck', 'elephant']
// start 超出数组数量范围，返回空数组
animals.slice(5) // => []

// start end正数 从start位置开始到end位置之前（不含end）的元素
animals.slice(1,3) // => ['bison', 'camel']
// start负数 end正数 相当于 animals.slice(3,4)（start换算animals.length-2)  ，截取失败为空数组
animals.slice(-2,4) // => ['duck']
// start负数 end负数 相当于 animals.slice(3,4)（start换算animals.length-2)（end换算animals.length-1)，截取失败为空数组
animals.slice(-2,-1) // => ['duck']
// end 大于数组的长度, 截取到最后
animals.slice(-2,5) //  => ['duck', 'elephant']
```

## sort
- 此函数会直接修改数组，返回排序好的原数组，不进行复制
```js
// 默认升序排列
[5,1,2,3,4].sort() // => [1,2,3,4,5]

// compareFunction 判断函数
arr.sort(compareFunction)
// compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前
// 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
// 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
```