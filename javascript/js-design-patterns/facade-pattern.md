#外观模式
> Facade pattern

##知识点一：定义
提供一个唯一的接口与子系统进行交互。外观模式定义了一个高层次的接口，隐藏其底层的复杂性。
在jQuery库中会经常看到此模式，把复杂的底层使用一个高层次的接口管理，暴露给客户端使用。当使用jQuery的$(el)、css()或$(el).animate()方法时。实际上这就是使用facade模式，一种简单的接口，客户方不必知道或者调用很多jQuery核心的内部方法，让jQuery容易使用。

##知识点二：具体例子

![image](/assest/javascript-facade.jpg)

***Facade***
外观模式，Mortgage类
1.  知道哪一些子系统需要加载/请求
1.  代理客户端去请求获取正确的子系统对象

***Sub Systems***
子系统，Bank, Credit, Background 等类
1.  实现和展示具体的子系统功能
1.  不关心外观模式是怎样引用它的

```javascript
var Mortgage = function(name) {
    this.name = name;
};
 
Mortgage.prototype = {
 
    applyFor: function(amount) {
        // access multiple subsystems...
        var result = "approved";
        if (!new Bank().verify(this.name, amount)) {
            result = "denied";
        } else if (!new Credit().get(this.name)) {
            result = "denied";
        } else if (!new Background().check(this.name)) {
            result = "denied";
        }
        return this.name + " has been " + result +
               " for a " + amount + " mortgage";
    }
};
 
var Bank = function() {
    this.verify = function(name, amount) {
        // complex logic ...
        return true;
    }
};
 
var Credit = function() {
    this.get = function(name) {
        // complex logic ...
        return true;
    }
};
 
var Background = function() {
    this.check = function(name) {
        // complex logic ...
        return true;
    }
};
 
function run() {
    var mortgage = new Mortgage("Joan Templeton");
    var result = mortgage.applyFor("$100,000");
 
    alert(result);
}
```

##知识点三：特点
适合场景：
1.  大型而且复杂的系统
1.  整合大量复杂而且互相引用的代码模块

优点：
1.  让代码库更容易使用
1.  增加代码可读性
1.  代码库内部可以减少依赖外部代码，因为多数的代码使用facade模式，让开发系统时候有更多弹性
1.  可以包装一些设计得比较差的API接口成为设计良好的API
缺点：
1.  性能问题，facade提供的抽象接口，其实现的隐性成本一般很难发现。例如：jQuery的 $('#id')与getElementById()之间就存在很大的性能差距。因为$('#id')实现会使用一个叫Sizzle的选择引擎。所以考虑到facade抽象出来的方法有其隐性成本，在设计时候要试着了解任何性能成本，并确认是否有必要抽象。


