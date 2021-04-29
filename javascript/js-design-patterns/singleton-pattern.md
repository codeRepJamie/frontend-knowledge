#单例模式
> Observer Pattern

##知识点一：定义
单例模式限制类的实例化次数只能为一次，浏览器的window对象就是一个单例，著名的jQuery库使用的也是Singleton单例模式

###最简单的单例模式[^1]
以对象字面量创建对象的方式在JS开发中很常见。上面的对象是一个处理时间的工具库, 以对象字面量的方式来封装了一些方法处理时间格式。全局只暴露了一个timeTool对象, 在需要使用时, 只需要采用timeTool.getISODate()调用即可。

####特点
    优点：访问简单，容易创建
    缺点：
    1.  一旦该对象十分复杂，那么创建对象本身就需要一定的耗时，耗内存
    1.  不支持私有变量和私有方法

```javascript
let timeTool = {
  name: '处理时间工具库',
  getISODate: function() {},
  getUTCDate: function() {}
}
```

###惰性单例模式
只有在需要使用单例时候才实例化单例，称为惰性单例。这样声明单例时候可以占用比较小的内存。
```javascript
var mySingleton = (function () {
 
  // Instance stores a reference to the Singleton
  var instance;
 
  function init() {
 
    // Singleton
 
    // Private methods and variables
    function privateMethod(){
        console.log( "I am private" );
    }
 
    var privateVariable = "Im also private";
 
    var privateRandomNumber = Math.random();
 
    return {
 
      // Public methods and variables
      publicMethod: function () {
        console.log( "The public can see me!" );
      },
 
      publicProperty: "I am also public",
 
      getRandomNumber: function() {
        return privateRandomNumber;
      }
 
    };
 
  }
 
  return function(){
 
    // Get the Singleton instance if one exists
    // or create one if it doesn't
    if ( !instance ) {
      instance = init();
    }
          
    return instance;
 
  };
 
})();
 
var myBadSingleton = (function () {
 
  // Instance stores a reference to the Singleton
  var instance;
 
  function init() {
 
    // Singleton
 
    var privateRandomNumber = Math.random();
 
    return {
 
      getRandomNumber: function() {
        return privateRandomNumber;
      }
 
    };
 
  }
 
  return {
 
    // Always create a new Singleton instance
    getInstance: function () {
 
      instance = new init();
 
      return instance;
    }
 
  };
 
})();
 
 
// Usage:
 
var singleA = mySingleton();
var singleB = mySingleton();
console.log( singleA.getRandomNumber() === singleB.getRandomNumber() ); // true
 
var badSingleA = myBadSingleton.getInstance();
var badSingleB = myBadSingleton.getInstance();
console.log( badSingleA.getRandomNumber() !== badSingleB.getRandomNumber() ); // true
 
// Note: as we are working with random numbers, there is a
// mathematical possibility both numbers will be the same,
// however unlikely. The above example should otherwise still
// be valid.
// 这里还有一个缺点：客户端使用时候需要特别注明（研究代码）才知道getInstance是唯一的访问入口，而MyBadSingleton只需要调用函数就可以获取单例
}
```

##知识点二：一个单例必须要符合以下两点
1.  该类只能有个一个实例，客户端[^2]只能够通过一个众所周知的访问入口访问
1.  该实例可以通过子类扩展，客户端应该无需更改任何代码就可以使用该子类扩展的实例

##知识点三: 单例扩展
针对第二点，上面例子getInstance更改为
```javascript
class BasicSingleton {
  // ...
}

class FooSingleton extends BasicSingleton {
  // ...
}

var mySingleton = (function () {
  var instance;
  
  function isFoo() {
    // ...
  }
  
  return function () {
 
      if ( instance == null ) {
          if ( isFoo() ) {
             instance = new FooSingleton();
          } else {
             instance = new BasicSingleton();
          }
      }
      return instance;
  }
})();
```

##知识点四: 静态实例（class实例出来的object）与 Singleton的区别，什么时候使用Singleton更有优势？

1.  命名空间（避免使用全局变量/方法）

> 一个项目常常不只一个程序员进行开发和维护, 然后一个程序员很难去弄清楚另一个程序员暴露在的项目中的全局变量和方法。如果将变量和方法都暴露在全局中, 变量冲突是在所难免的。[^3]

```javascript
//开发者A写了一大段js代码
let devA = {
  addNumber() { }
}

//开发者B开始写js代码
let devB = {
  add: ''
}

//A重新维护该js代码
devA.addNumber();
```

1.  管理模块

> 一个项目有不同的库实现不同的功能，例如ajax请求，使用单例模式可以用来管理代码库中的各个模块

##知识点五: 使用ES6创建单例模式
1.  使用class声明[^4]
1.  使用ES6 module模式下 export 一个类的实例也可以实现单例模式(非惰性)

```javascript
class MyClass{
  //...
}

exports defaul new MyClass()
```

[^1]:  [摘自知乎李棠辉的文章《从ES6重新认识JavaScript设计模式(一): 单例模式》](https://zhuanlan.zhihu.com/p/34754447)
[^2]:  消费者，业务代码
[^3]:  [摘自知乎李棠辉的文章《从ES6重新认识JavaScript设计模式(一): 单例模式》](https://zhuanlan.zhihu.com/p/34754447)
[^4]:  [请查看知乎李棠辉的文章《从ES6重新认识JavaScript设计模式(一): 单例模式》](https://zhuanlan.zhihu.com/p/34754447)