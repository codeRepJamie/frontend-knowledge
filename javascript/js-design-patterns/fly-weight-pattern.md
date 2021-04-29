#享元模式
> Flyweight pattern

##知识点一：定义
Flyweight 模式通过与一系列相关对象共享尽可能多的数据，减少应用程序中的内存使用。

##知识点二：具体例子
![image](/assest/javascript-flyweight.jpg)

```javascript
function Flyweight (make, model, processor) {
    this.make = make;
    this.model = model;
    this.processor = processor;
};
 
var FlyWeightFactory = (function () {
    var flyweights = {};
 
    return {
 
        get: function (make, model, processor) {
            if (!flyweights[make + model]) {
                flyweights[make + model] = 
                    new Flyweight(make, model, processor);
            }
            return flyweights[make + model];
        },
 
        getCount: function () {
            var count = 0;
            for (var f in flyweights) count++;
            return count;
        }
    }
})();
 
function ComputerCollection () {
    var computers = {};
    var count = 0;
 
    return {
        add: function (make, model, processor, memory, tag) {
            computers[tag] = 
                new Computer(make, model, processor, memory, tag);
            count++;
        },
 
        get: function (tag) {
            return computers[tag];
        },
 
        getCount: function () {
            return count;
        }
    };
}
 
var Computer = function (make, model, processor, memory, tag) {
    this.flyweight = FlyWeightFactory.get(make, model, processor);
    this.memory = memory;
    this.tag = tag;
    this.getMake = function () {
        return this.flyweight.make;
    }
    // ...
}
 
// log helper
 
var log = (function () {
    var log = "";
 
    return {
        add: function (msg) { log += msg + "\n"; },
        show: function () { console.log(log); log = ""; }
    }
})();
 
function run() {
    var computers = new ComputerCollection();
    
    computers.add("Dell", "Studio XPS", "Intel", "5G", "Y755P");
    computers.add("Dell", "Studio XPS", "Intel", "6G", "X997T");
    computers.add("Dell", "Studio XPS", "Intel", "2G", "U8U80");
    computers.add("Dell", "Studio XPS", "Intel", "2G", "NT777");
    computers.add("Dell", "Studio XPS", "Intel", "2G", "0J88A");
    computers.add("HP", "Envy", "Intel", "4G", "CNU883701");
    computers.add("HP", "Envy", "Intel", "2G", "TXU003283");
 
    log.add("Computers: " + computers.getCount());
    log.add("Flyweights: " + FlyWeightFactory.getCount());
    log.show();
    // Computers: 7
    // Flyweights: 2
}
run();
```

###例子分析

这个例子里面,具有相同的内部数据对象（"Dell", "Studio XPS", "Intel"和"HP", "Envy", "Intel", "4G"）可以被一个由FlyWeightFactory方法创建一个单一共享对象，优点是可以极大减少储存实例数据所需要的内存，这样新的实例只需要保存与现有Flyweight对象不同是数据即可。

享元模式有个概念要知道就是“内部和外部。对象中的内部方法，可能需要内部信息，没有它程序就绝对没法正常运行。但外部信息是可以被删除或者存储在外部的。以上面例子为例内部信息为make、model、processor，一般储存在享元，外部信息为memory、tag、getMake方法。


***Client***
客户端，例子中的Computer类
1.  调用FlyweightFactory获取flyweight对象

***FlyweightFactory***
享元工厂，例子中的 FlyweightFactory
1.  生产和管理Flyweight对象，
1.  一旦请求，如果flyweight对象不存在，就会实现一个具体享元（Flyweight）对象
1.  负责保存最新生成出来的flyweight对象，应付未来的请求

***Flyweight***
享元，例子中的 Flyweight类
1.  维护一个分享到所有应用程序的固有数据
