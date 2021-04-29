#装饰者模式
> Decorator pattern

##知识点一：定义
装饰者模式提供了对一个对象动态添加额外临时的功能职责（属性和方法）。注意的是装饰者添加的功能，对于原本的对象来说并不是必要的，否则，该功能应该添加到对象原型中。
装饰者模式一般用于修改现有系统，希望为对象添加额外功能，而不需要修改体层代码。
一些类库例如JQuery，也有使用类似模式。jQuery.extend()方法就是用来扩展jQuery内置对象，可以认为是装饰者模式一种实现。

##知识点二：具体例子
![image](/assest/javascript-decorator.jpg)

```javascript

var User = function(name) {
    this.name = name;
 
    this.say = function() {
        log.add("User: " + this.name);
    };
}
 
var DecoratedUser = function(user, street, city) {
    this.user = user;
    this.street = street;
    this.city = city;
 
    this.say = function() {
        log.add("Decorated User: " + this.user.name + ", " +
                   this.street + ", " + this.city);
    };
    return this;
}
 
// logging helper
 
var log = (function() {
    var log = "";
 
    return {
        add: function(msg) { log += msg + "\n"; },
        show: function() { console.log(log); log = ""; }
    }
})();
 
function run() {
 
    var user = new User("Kelly");
    user.say(); //User: Kelly
 
    var decorated = new DecoratedUser(user, "Broadway", "New York");
    decorated.say(); //Decorated User: Kelly, Broadway, New York
 
    log.show(); 
}
run();
```

###例子分析

例子中，User类中实例被装饰者类DecoratedUser装饰了（增强了）。实例对象添加了一个地址属性。原来的属性方法则保持不变（this.name）

***Client***
客户端，例子中的run
1.  负责实例化装饰者类

***Component***
目标组件，例子中的 User类
1.  需要添加额外功能的类

***Decorator***
目标组件，例子中的 DecoratedUser类
1.  包裹目标组件，引用目标组件实例
1.  定义符合目标组件接口的接口（定义额外参数）
1.  实现额外功能（添加成员）

##知识点三：特点
优点：
1.  相当灵活，旧的代码如果需要添加新的行为，可以在上面进行包装或者“装饰”，不用修改原来的底层代码
1.  不必依靠大量子类实现一些功能
缺点：
1.  如果滥用，会极大复杂化程序框架，解决方法应使用大量注解和文档说明，同时要限制装饰者数量

#抽象装饰者（Abstract Decorator）模式

装饰者模式增强版。可以把多种功能相似装饰者抽象出来成为一个类基，称为抽象装饰者。抽象装饰者本身也可以装饰原有目标组件。

##知识点一：具体例子
```javascript

class User {
  constructor(name){
    this.name = name;
    this.classType = 'normal';
  }
  say(){
    log.add("User: " + this.name+ "; classType:" + this.classType);
  }
}

class UserVip extends User {
  constructor(name){
    super(name);
    this.classType = 'VIP';
  }
}

class UserDecorator {
  constructor(user,street){
    this.user = user;
    this.street = street;
  }
  getStreet(){
    return " Decorated User Street is: "+this.street
  }
  say(){
      const user = this.user;
      log.add("User: " + user.name+ "; classType:" + user.classType + this.getStreet());
  }
}

class CaseUserDecorator extends UserDecorator{
  constructor(user, street, city){
     super(user, street);
     this.city = city;
  }
  getCity(){
    return " caseDecorated User City is: "+this.city
  }
  say(){
    const user = this.user;
    log.add("User: " + user.name+ "; classType:" + user.classType + this.getStreet() + '; ' + this.getCity());
  }
}

// logging helper
 
var log = (function() {
    var log = "";
 
    return {
        add: function(msg) { log += msg + "\n"; },
        show: function() { console.log(log); log = ""; }
    }
})();
 
function run() {
    var user = new User("Mike");
    user.say(); //User: Mike; classType:normal
  
    var decoratedUser = new UserDecorator(user, "Broadway");
    decoratedUser.say(); // User: Mike; classType:normal Decorated User Street is: Broadway
 
    var userVip = new UserVip("Kelly");
    userVip.say(); //User: Kelly; classType:VIP
 
    var decoratedVipUser = new CaseUserDecorator(userVip, "Broadway", "New York");
    decoratedVipUser.say(); // User: Kelly; classType:VIP Decorated User Street is: Broadway;  caseDecorated User City is: New York
 
    log.show();
}
run();
```