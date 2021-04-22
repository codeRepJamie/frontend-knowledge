# 术语表

## 变量提升（hoisting）
- js有变量提升和函数提升，指的是用 var声明变量 或用 function 函数名（）{} 声明的，会在 js预解析 阶段提升到顶端
- es6的let  和 const 不会提升
- 相同作用域时声明变量而不赋值则还是以前的值， 而子作用域声明不赋值则函数内该值为undefined，因为声明了私有变量

## TDZ（“暂时性死区”）
在区块作用域绑定早于声明语句时的状况时，所使用的专用术语。
- let/const是使用区块作用域；var是使用函数作用域
- 在let/const声明之前就访问对应的变量与常量，会抛出ReferenceError错误；但在var声明之前就访问对应的变量，则会得到undefined

## [标准内置对象分类](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)
指的是JavaScript标准内建对象包括一些属性、方法及基本对象。即不依赖宿主环境的对象。此对象也不能直接在js调用，仅可以在宿主环境下的全局作用域中调用。标准内置对象不应与global对象混淆。
## global对象
global对象可以在全局作用域里通过使用this访问到（但只有在 ECMAScript 5 的非严格模式下才可以，在严格模式下得到的是 undefined）。在浏览器环境中，这个全局对象就是 window 对象。全局作用域中的其他对象则可由用户的脚本创建，或由宿主程序提供。global对象 = 标准内置对象 + 用户的脚本创建 + 宿主程序提供（浏览器BOM）
## JavaScript全局属性
- Infinity 代表正的无穷大的数值。
- NaN 指示某个值是不是数字值。
- undefined 指示未定义的值。
- globalThis 全局属性 globalThis 包含全局的 this 值，类似于全局对象（global object）

## JavaScript全局函数
### uri编码类
- encodeURI()	把字符串编码为 URI。
- decodeURI()	解码某个编码的 URI。
- encodeURIComponent()	把字符串编码为 URI 组件。
- decodeURIComponent()	解码一个编码的 URI 组件。
### 字符串转换数字
- parseFloat()	解析一个字符串并返回一个浮点数。 
```js
parseFloat(string);
```
  - 参数string, 要被解析的值。如果参数不是一个字符串，则将其转换为字符串(使用  ToString 抽象操作)。此处会发生隐式转换,例如浮点数。如果参数字符串的第一个非(+/-)字符不能被解析成为数字,则 parseFloat 返回 NaN


- parseInt()	解析一个字符串并返回一个整数。
```js
parseInt(string, radix);
```
  - 参数string, 要被解析的值。如果参数不是一个字符串，则将其转换为字符串(使用  ToString 抽象操作)。此处会发生隐式转换,例如浮点数。如果参数字符串的第一个非(+/-)字符不能被解析成为数字,则 parseFloat 返回 NaN
  - 参数radix, 从 2 到 36，表示字符串的基数。例如指定 16 表示被解析值是十六进制数。请注意，10不是默认值！'0'开头的字符串会有一定机会解析成8进制。
### 判断
- isFinite()	检查某个值是否为有穷大的数。
- isNaN()	检查某个值是否是数字。
### 脚本执行
- eval()	计算 JavaScript 字符串，并把它作为脚本代码来执行。


### 对象值转换(有争议，应该属于基本对象)
- String()	把对象的值转换为字符串。
- Number()	把对象的值转换为数字。
### 字符串编码(已废弃)
- escape()	对字符串进行编码。
- unescape()	对由 escape() 编码的字符串进行解码。

### 标准内置对象分类，注意！！
- setTimeout/setInterval 不属于JavaScript标准内建对象，
在浏览器宿主上，此属于浏览器实现功能，正确的写法应该是window.setTimeout/setInterval
在node.js宿主上, 此属于timer(定时器)提供，又因为这是node.js内建global对象方法，调用此函数不用声明引入timer，故此很容易会造成混乱


