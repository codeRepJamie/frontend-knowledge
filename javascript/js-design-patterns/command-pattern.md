#命令模式
> Command Pattern

##知识点一：定义
1.  把一系列方法调用、请求或操作封装到单一对象，根据不同的请求对客户端进行参数化或传递可执行的方法调用。
1.  典型的Command模式需要有一个接口，接口中有一个统一的方法(excute/run)，这就是"将命令/请求封装为对象"。

##知识点二：具体例子

![image](/assest/javascript-command.jpg)

***Client***
客户端，执行run方法
1.  引用Receiver对象

***Receiver***
接收者，Calculator对象
1.  知道怎样操作相关的命令
1.  （有时候）维护一个执行命令历史

***Command***
命令类，Command类
1.  负责根据怎样的动作，执行怎样的命令
command模式，最主要与抽象类思想有关，作为一个抽象类，定义一个接口，不一定为它所有成员函数提供实现。它只作为一个类基，派生出其他类（AddCommand、SubCommand等）。实现缺失功能的派生类称为具体命令类(Concrete Command)。

***Invoker***
调用者，具体执行calculator.execute(new AddCommand(100));
1.  询问执行请求

```javascript
function add(x, y) { return x + y; }
function sub(x, y) { return x - y; }
function mul(x, y) { return x * y; }
function div(x, y) { return x / y; }

//Command 
var Command = function (execute, undo, value) {
    this.execute = execute;
    this.undo = undo;
    this.value = value;
}

//Concrete Command  
var AddCommand = function (value) {
    return new Command(add, sub, value);
};
 
var SubCommand = function (value) {
    return new Command(sub, add, value);
};
 
var MulCommand = function (value) {
    return new Command(mul, div, value);
};
 
var DivCommand = function (value) {
    return new Command(div, mul, value);
};

// Receiver
var Calculator = function () {
    //执行结果
    var current = 0;
    //执行历史
    var commands = [];
 
    function action(command) {
        var name = command.execute.name;
        return name.charAt(0).toUpperCase() + name.slice(1);
    }
 
    return {
        execute: function (command) {
            current = command.execute(current, command.value);
            commands.push(command);
            log.add(action(command) + ": " + command.value);
        },
 
        undo: function () {
            var command = commands.pop();
            current = command.undo(current, command.value);
            log.add("Undo " + action(command) + ": " + command.value);
        },
 
        getCurrentValue: function () {
            return current;
        }
    }
}
 
// log helper
 
var log = (function () {
    var log = "";
 
    return {
        add: function (msg) { log += msg + "\n"; },
        show: function () { console.log(log); log = ""; }
    }
})();

//Client 
function run() {
    var calculator = new Calculator();
 
    // issue commands
 
    calculator.execute(new AddCommand(100));
    calculator.execute(new SubCommand(24));
    calculator.execute(new MulCommand(6));
    calculator.execute(new DivCommand(2));
 
    // reverse last two commands
 
    calculator.undo();
    calculator.undo();
 
    log.add("\nValue: " + calculator.getCurrentValue());
    log.show();
}

run();
```
##知识点三：特点
设计思想：分离职责，包括执行命令、发布命令委托给不同的对象。使用一个简单的命令把具体命令（Concrete Command）和调用方法（receiver的execute方法）绑定在一起。

优点：
1.  具有相同接口的Command子对象（Concrete Command）可以根据需要轻松交互
1.  扩展/维护子对象（Concrete Command）变得非常容易，几乎不用改大量代码
1.  你可以创建一个具有队列系统的命令队列，例如上面的的命令具有撤销命令功能

缺点：
1.  必须要使用很多类目和对象才能完成目标，开发者必须要小心正确地开发这些类
1.  每一个独立的命令都是一个具体命令（Concrete Command）类，这样会增加大量的类去实现方法和维护

