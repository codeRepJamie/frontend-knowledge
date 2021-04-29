#模块模式
> Module Pattern

##知识点一：模块模式有下列4种方法：
1.  Object字面量模式
1.  Module模式
1.  AMD模式
1.  CommonJS模式
1.  ECMAScript Harmony模式

###Object字面量模式

```javascript
var myObjectLiteral = {
 
    variableKey: variableValue,
 
    functionKey: function () {
      // ...
    }
};
```

####特点
优点：
    1.  简单容易，不使用new，不用实例化
缺点：
    1.  没有作用域概念，只能挂载到全局引用
    1.  所有属性都可以访问，没有私有属性/方法
适用：
    1.  简单设置

###Module模式

最简单的Module模式定义与实现
```javascript
var testModule = (function () {
 
  var counter = 0;
 
  return {
 
    incrementCounter: function () {
      return counter++;
    },
 
    resetCounter: function () {
      console.log( "counter value prior to reset: " + counter );
      counter = 0;
    }
  };
 
})();
 
// Usage:
 
// Increment our counter
testModule.incrementCounter();
 
// Check the counter value and reset
// Outputs: counter value prior to reset: 1
testModule.resetCounter();
```

####特点
优点：
    1.  私有变量完全与全局作用域实现隔离，只能通过内部方法访问（在模块的闭包内）
    1.  公共函数往往已声明，在debug过程中更容易查看哪些函数发生异常

####Module模式变化
混入模式
下面演示如何引入一个第三方库（JQuery、underscore），并且取一个本地别名（可以有效防止命名冲突）
```javascript
// Global module
var myModule = (function ( jQ, _ ) {
 
    function privateMethod1(){
        jQ(".container").html("test");
    }
 
    function privateMethod2(){
      console.log( _.min([10, 5, 100, 2, 1000]) );
    }
 
    return{
        publicMethod: function(){
            privateMethod1();
        }
    };
 
// Pull in jQuery and Underscore
})( jQuery, _ );
 
myModule.publicMethod();
```

####Module模式变化
引出模式
下面演示如何新建一个全局变量，不需要在本模块使用它，并引出到模块外，并支持上面的例子
```javascript
// Global module
var myModule = (function () {
 
  // Module object
  var module = {},
    privateVariable = "Hello World";
 
  function privateMethod() {
    // ...
  }
 
  module.publicProperty = "Foobar";
  module.publicMethod = function () {
    console.log( privateVariable );
  };
 
  return module;
 
})();
```

####特点
优点：
    1.  比object字面量模式更清晰
    1.  支持作用域，可以有私有数据
缺点：
    1.  访问公有成员和私有成员的方法是不同的，一旦需要改变他们的可见性时候，需要改变每一个曾经使用过该成员的地方
    1.  很难访问那些之后在方法里添加的私有成员（？）
    1.  无法为私有成员创建自动化单元测试（因为私有方法不暴露）
    1.  无法轻易扩展私有方法（？私有方法一改，其他关联的公有方法全部都要改）

####Module模式变化（揭示模块）

```javascript
var myRevealingModule = (function () {
 
        var privateCounter = 0;
 
        function privateFunction() {
            privateCounter++;
        }
 
        function publicFunction() {
            publicIncrement();
        }
 
        function publicIncrement() {
            privateFunction();
        }
 
        function publicGetCount(){
          return privateCounter;
        }
 
        // 暴露的公有指针指向到私有函数和属性上
 
       return {
            start: publicFunction,
            increment: publicIncrement,
            count: publicGetCount
        };
 
    })();
 
myRevealingModule.start();
```

####特点
优点：
    1.  该模式可以使语法一致，很容易看出哪些可以公开访问，改善可读性
缺点：
    1.  如果私有函数引用一个公有函数，如果需要扩展（继承）公有函数，此公有函数不能被覆盖。因为私有函数要继续引用原有的公有函数，所以此模式只适用于一般函数使用，不能用于任何继承(或尝试覆盖其方法)。引用了私有变量的公有成员遵守无补丁原则
    
缺点例子：
```javascript
var revealed = (function() {      
  function foo() {
    return baz();
  }

  function bar() {
    return "I am the original bar!";
  }

  // private function always calls bar because it's in the closure
  // and it can't see the "instance" variable
  function baz() {
    return bar();
  }

  return { foo : foo, bar : bar }
})();

var child = Object.create(revealed);

child.bar = function() {
  return "I am the new bar!";
}

// we want this to call the new bar but it doesn't
console.log(child.foo()); // I am the original bar!
```