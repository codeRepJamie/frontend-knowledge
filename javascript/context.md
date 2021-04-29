# 执行上下文

## 执行上下文定义
执行上下文（简称上下文）决定了Js执行过程中可以获取哪些变量、函数、数据，一段程序可能被分割成许多不同的上下文，每一个上下文都会绑定一个变量对象（Variable Object）它就像一个容器，用来存储当前上下文中所有已定义或可获取的变量、函数等。位于最顶端或最外层的上下文称为全局上下文（Gobal Execution Context），全局上下文取决于执行环境。

执行上下文有3种
- Global execution context (全局执行上下文)
- Functional execution context (函数执行上下文)
- Eval（eval执行上下文）

## 变量对象（Variable object, VO）
变量对象是与执行上下文相关的数据作用域。它是一个与上下文相关的特殊对象，其中存储了在上下文中定义的变量和函数声明。
因为不同执行上下文下的变量对象稍有不同

## 全局执行上下文 Global execution context
全局对象是预定义的对象，作为 JavaScript 的全局函数和全局属性的占位符。通过使用全局对象，可以访问所有其他所有预定义的对象、函数和属性。
在顶层 JavaScript 代码中，可以用关键字 this 引用全局对象。因为全局对象是作用域链的头，这意味着所有非限定性的变量和函数名都会作为该对象的属性来查询。
例如，当JavaScript 代码引用 parseInt() 函数时，它引用的是全局对象的 parseInt 属性。全局对象是作用域链的头，还意味着在顶层 JavaScript 代码中声明的所有变量都将成为全局对象的属性。
```js
// 浏览器全局对象 this就是 window
console.log(this); // Window{ ... }
```
全局对象是由 Object 构造函数实例化的一个对象
```js
console.log(this instanceof Object);
```
全局上下文下作为全局变量的宿主
```js
var a = 1;
console.log(this.a);
```
```js
this === window // ture
```
总结：全局上下文中的变量对象就是全局对象

全局执行上下文创建阶段流程：
- 创建定义域链(Scope chain)
- 创建变量对象VO（variables, functions and arguments）
  - 会赋值对应预设值
- 设置this的值 window(浏览器)

## 函数上下文 Functional execution context
在函数上下文中，我们用活动对象(activation object, AO)来表示变量对象。

执行上下文的代码会分成两个阶段进行处理：分析和执行，我们也可以叫做：
- 进入执行上下文
- 代码执行

### 进入执行上下文（创建阶段）
  当进入执行上下文时，这时候还没有执行代码，

Arguments Objects 属性有：
- callee：指向当前函数的引用
- length： 真正传递的参数的个数
- properties-indexes：就是函数的参数值(按参数列表从左到右排列)

- 变量对象(AO)会包括：
  - Arguments Objects 
  - 函数的所有形参 (如果是函数上下文) 
    - 由名称和对应值组成的一个变量对象的属性被创建
    - 没有实参，属性值设为 undefined
  - 函数声明 (Function Declaration, FD);
    - 由名称和对应值（函数对象(function-object)）组成一个变量对象的属性被创建
    - 如果上一级变量对象已经存在相同名称的属性，则当前声明的函数完全替换这个属性
  - 变量声明 (var, Variable Declaration);
    - 由名称和对应值（undefined）组成一个变量对象的属性被创建；
    - 如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性(内部的变量不会影响外部的变量)

函数上下文都有三个重要的属性:
- 变量对象（Variable object）
- 作用域链（Scope chain）
- this (js只能访问)

除了这三个属性之外，根据实现的需要，Execution Context还可以有一些附加属性（例如 在哪里调用--调用栈、调用方式）（变量对象和作用域链这两个属性对象是JS引擎实现，不能直接用代码直接访问，只有this可以访问）

创建阶段流程：
- 创建定义域链(Scope chain)
- 创建变量对象AO（variables, functions and arguments）
  - 有实参的，会赋值对应的形参，其余都设置为undefined
  - 会优先处理functions声明与赋值，其次再声明varibles在赋值，如果无实参，则赋值undefined
- 设置this的值

```js
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};
  b = 3;
}
foo(1);
```
```js
AO = {
    arguments: {
        0: 1,
        callee: reference to function foo(){},
        length: 1
    },
    a: 1,
    b: undefined,
    c: reference to function c(){},
    d: undefined
}
```
### 在代码执行阶段
代码执行后AO就会更改
在代码执行阶段，会顺序执行代码，根据代码，修改变量对象的值
```js
AO = {
    arguments: {
        0: 1,
        callee: reference to function foo(){},
        length: 1
    },
    a: 1,
    b: 3,
    c: reference to function c(){},
    d: reference to FunctionExpression "d"
}
```
