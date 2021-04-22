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
 - 'string'
 - 'number'
 - 'boolean'
 - 'undefined'
 - 'object' //引用类型
 - 'function'  //引用类型(特殊)
 - 'symbol'
 - 注意 typeof 运算符对于 null 值会返回 "Object"

## 强制类型转换情况
 - 字符串运算 100 + '1'
 - ==运算符 100 == '100'
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
value === null 或 value == null
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