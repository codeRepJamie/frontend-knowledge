# 数据类型数据转换

## 基本数据类型
一种既非对象也无方法的数据(MDN)，它们的值直接存储在变量访问的位置(栈储存)，所以一个原始值只能对应一个变量
- string 字符串
- number 数字
- boolean 布尔值
- undefined 未定义
- null 空
- symbol 唯一符号值

## 引用数据类型
一种 object 类型并且可以有自己的方法（而且继承Object的原型方法），引用值允许多个变量访问，所以一个引用值可以对应多个变量(堆储存)，反之，一个变量改变这个引用值，可能会引发其他变量改变（因为都是同一个内存地址）
- Object 对象

## typeof运算符结果
```js
 console.log(typeof '123') // => 'string'
 console.log(typeof 123) // => 'number'
 console.log(typeof true) // => 'boolean'
 console.log(typeof undefined) // => 'undefined'
 console.log(typeof {}) // => 'object' 引用类型
 console.log(typeof function(){}) // => 'function' 引用类型(特殊)
 console.log(typeof Symbol('')) // => 'symbol'

 //奇怪结果
 // null 值会返回 "Object"
 console.log(typeof null) // 'object'
```
## ==运算结果
- undefined和null与任何有意义的值比较返回的都是false, 相反他们之间包括自身比较结果都为true
- NaN与所有值都不相等，包括自身
- 所有相同类型的数据比较，都不会发生隐式转换
- String与Boolean的值参与比较，String与Boolean的值都会隐式转换成数字，再比较 如: '1' == 1 和 '1' == true
- object值参与比较时（非对象与对象比较），该尝试调用.toString() 和 .valueOf() 方法，将参数 A 转换为原始值（Primitive），再比较
- 对象与对象比较时，结果与===的结果一样，内存地址不一样的对象不相等
- +0 == -0 结果为true

## ===运算结果
 - undefined和null与任何有意义的值比较返回的都是false, 相反他们之间包括自身比较结果都为true
 - 只要是类型不相同，都判断为不相等
 - NaN与所有值都不相等，包括自身
 - +0 == -0 结果为true

## Object.is(value)结果
 - 只要是类型不相同，都判断为不相等
 - NaN与所有值都不相等，但 Object.is(NaN,NaN) 为true
 - +0 == -0 结果为false
## 强制类型转换情况
 - 字符串运算 100 + '1'
 - ==运算符(非null/undefined) ==号两边都会发生隐式转换  例如 100 == '100', [] == true
 - if运算符 if(a){}
 - 逻辑运算符 10 && 0
 - 取反 !1

## 判断数据类型方法
### 基本类型： string、number、boolean、undefined、symbol
```js
/* instanceof 是用来判断new XXX出来的非字面量数值 */
typeof value === 'string' || value instanceof String // 前者结果=> "string"
typeof value === 'number' || value instanceof Number // 前者结果=> "number"
typeof value === 'boolean' || value instanceof Boolean // 前者结果=> "boolean"
typeof value === 'undefined' //=> "undefined"
typeof value === 'symbol' //=> "symbol"
```

### 基本类型： null
```js
// 由于
console.log(typeof null); //=> "object"
// 所以只能用
value === null 
//或 
value == null
```

### 特殊类型：Function
```js
// Function实际属于object（引用类型），但是可以使用typefo判断
typeof value === 'function'
```

### 特殊类型：纯对象
```js
// 利用constructor判断该对象的构造函数是否为对象构造函数本身(前提类有定义构造函数)
value.constructor === Object
```

### 类型：数组
```js
Array.isArray(value)
// 或
Object.prototype.toString.call(value) === '[object Array]'
```