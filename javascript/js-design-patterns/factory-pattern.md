#工厂模式
> Factory pattern

##知识点一：定义
定义一个工厂方法（生产者）返回一个实例对象（产品），最终让一个子类（实际生产者）去完成实例化（生产产品）。工厂模式甚至允许不使用一个构造函数，就可以提供一个通用接口来创建对象。

##知识点二：具体例子

![image](/assest/javascript-factory-method.jpg)

```javascript
//定义工厂
function Factory() {
    this.createEmployee = function (type) {
        var employee;
 
        if (type === "fulltime") {
            employee = new FullTime();
        } else if (type === "parttime") {
            employee = new PartTime();
        } else if (type === "temporary") {
            employee = new Temporary();
        } else if (type === "contractor") {
            employee = new Contractor();
        }
 
        employee.type = type;
 
        employee.say = function () {
            log.add(this.type + ": rate " + this.hourly + "/hour");
        }
 
        //生成ConcreteProduct实例
        return employee;
    }
}

//定义ConcreteProduct产品类
var FullTime = function () {
    this.hourly = "$12";
};
 
var PartTime = function () {
    this.hourly = "$11";
};
 
var Temporary = function () {
    this.hourly = "$10";
};
 
var Contractor = function () {
    this.hourly = "$15";
};
 
// log helper
var log = (function () {
    var log = "";
 
    return {
        add: function (msg) { log += msg + "\n"; },
        show: function () { alert(log); log = ""; }
    }
})();
 
function run() {
    var employees = [];
    var factory = new Factory();
    
    employees.push(factory.createEmployee("fulltime"));
    employees.push(factory.createEmployee("parttime"));
    employees.push(factory.createEmployee("temporary"));
    employees.push(factory.createEmployee("contractor"));
    
    for (var i = 0, len = employees.length; i < len; i++) {
        employees[i].say();
    }
 
    log.show();
}
```

###例子分析

***Creator***
工厂，例子中的Factory
1.  此工厂实例可以生成不同种类的产品
1.  实现了一个工厂方法(factoryMethod)返回产品

***AbstractProduct***
抽象产品，例子中没使用
1.  声明产品的统一接口

***ConcreteProduct***
具体产品，JavaScript没有此概念
1.  被生产出来的产品
1.  所有的产品都会含有统一的接口(相同的属性或者方法)

假设有一个工厂，可以生成不同种类的工人。每一个工人有不同的时薪。createEmployee方法就是一个工厂方法。试想象一些客户端就是客户，按照不同的需要添加参数，通过调用createEmployee进行下单。工厂就会内部去实现创造一个工人实例出来。需要注意的是，由于JavaScript不支持抽象类概念，这些FullTime、PartTime等子类需要一个统一的接口（属性或者方法，例如本事例的this.hourly）。当不同种类的工人创建出来，工厂还会统一实现一些方法，例如（employee.type、employee.say）。

##知识点三：特点
对以下使用场景特别有用&优点：
1.  当对象或组件设置比较复杂
1.  需要根据不同的环境生产不同的实例
1.  需要处理很多具有相同属性的小型对象或组件

什么情况下不应该使用&缺点：
1.  如果要实现的具体产品不具有统一的接口，使用此模式会带来很多不必要的复杂性。建议使用显式构造函数，减少不必要开销。
1.  由于对象创建的过程隐藏在接口之后抽象出来，单元测试可能会带来不便，这取决于创建过程的复杂性

#抽象工厂（Abstract Factory）模式

##知识点一：定义
提供一个接口（抽象工厂类）用来新建一些相似的有关联的或者互相依赖的对象（产品族），而不是通过一个明确指定的具体类

##知识点二：具体例子
```javascript
// Types.js - Constructors used behind the scenes
 
// A constructor for defining new cars
class Vehicle {
  constructor(){
    this.steering = 'left'
  }
}

class Car extends Vehicle{
 constructor(options){
      // some defaults
      this.doors = options.doors || 4;
      this.state = options.state || "brand new";
      this.color = options.color || "silver";
  }
}
 
// A constructor for defining new trucks
class Truck extends Vehicle{
  constructor(options){
      this.state = options.state || "used";
      this.wheelSize = options.wheelSize || "large";
      this.color = options.color || "blue";
  }
}

var abstractVehicleFactory = (function () {
 
  // Storage for our vehicle types
  var types = {};
 
  return {
      getVehicle: function ( type, customizations ) {
          var _vehicle = types[type];
 
          return (_vehicle ? new _vehicle(customizations) : null);
      },
 
      registerVehicle: function ( type, _vehicle ) {
          var proto = Vehicle.prototype;
 
          // only register classes that fulfill the vehicle contract
          if ( proto.drive && proto.breakDown ) {
              types[type] = _vehicle;
          }
 
          return abstractVehicleFactory;
      }
  };
})();
 
 
// Usage:
 
abstractVehicleFactory.registerVehicle( "car", Car );
abstractVehicleFactory.registerVehicle( "truck", Truck );
 
// Instantiate a new car based on the abstract vehicle type
var car = abstractVehicleFactory.getVehicle( "car", {
            color: "lime green",
            state: "like new" } );
 
// Instantiate a new truck in a similar manner
var truck = abstractVehicleFactory.getVehicle( "truck", {
            wheelSize: "medium",
            color: "neon yellow" } );
```
###例子分析

***AbstractFactory***
抽象工厂：abstractVehicleFactory
1.  声明一个接口用于生产产品

***concreteFactory***
具体工厂：registerVehicle方法
1.  根据条件生产产品

***Products***
产品类：Car、Truck
1.  提供要生产产品的接口给工厂

***AbstractProduct***
抽象产品类：Vehicle
1.  为产品族声明接口
