#观察者模式
> Observer Pattern

##实现一个小需求
1.  实现一个可以不断生成checkbox（check）的button(addNewObserver)
1.  实现一个checkbox(controlCheckbox)，可以控制页面上所有addNewObserver生成的check的状态

##知识点一：定义
观察者模式用于建立一种对象与对象之间的依赖关系，一个对象发生改变时将自动通知其他对象，其他对象将相应作出反应。

***Subject*** （目标）
维护一个观察者列表，可以添加删除任意一个观察者

***Observer*** （观察者）
负责提供一个接口（当目标状态发生改变时候，具体要怎样做）

***ConcreteSubject*** （具体目标）
主要有两个职责：
1.  储存当前ConcreteSubject的状态
1.  状态发生改变时，向Observer发出通知

***ConcreteObserver*** （具体观察者）
主要有两个职责：
1.  储存一个指向ConcreteSubject的引用
1.  实现Observe的更新接口，实现自身状态和目标状态保持一致

##知识点二：具体实行代码
```html
<body>
  <button id="addBtn">增加CheckBox</button>
  <input type="checkbox" id="handle"></input>
  <div id="checkboxWrapper"></div>
</body>
```
```js
// 事件池
class ObserverList {
  constructor() {
    this.list = []
  }
  count() {
    return this.list.length
  }
  add(obj) {
    this.list.push(obj)
  }
  get(index) {
    return this.list[index]
  }
}
//抽象目标
class Subject {
  constructor() {
    this.observers = new ObserverList()
  }
  addObserver(observer) {
    this.observers.add(observer);
  }
  notify(context) {
    var observerCount = this.observers.count();
    for (var i = 0; i < observerCount; i++) {
      this.observers.get(i).update(context);
    }
  }
}
//抽象观察者
class Observer {
  constructor(fn) {
    this.update = fn
  }
}

const addBtn = document.getElementById('addBtn')
let handle = document.getElementById('handle')
const checkboxWrapper = document.getElementById('checkboxWrapper')

addBtn.onclick = addNewObserver
handle.onclick = changeHandle
//具体目标绑定
handle.subject = new Subject()

// 具体观察者绑定
function addNewObserver() {
  let check = document.createElement('input')
  check.type = 'checkbox'
  check.observer = new Observer((value) => {
    // 广播事件后的回调函数
    check.checked = value
  })
  handle.subject.addObserver(check.observer)
  checkboxWrapper.appendChild(check)
}
function changeHandle() {
  // 目标广播事件
  handle.subject.notify(handle.checked);
}
```

####分析代码
***Subject*** （目标）
即是当前的Subject类。它内置observers属性，透过它来负责管理一个ObserverList实例。它拥有添加（Subject.prototype.addObserver）、删除（Subject.prototype.removeObserver）一个观察者，通知观察者（Subject.prototype.notify）的能力。

***Observer*** （观察者）
即是Observer类，提供了状态改变时获得通知对象（check），提供一个名为update的接口

***ConcreteSubject*** （具体目标）
即本例里面的controlCheckbox，案例中职责分别为：
1.  透过执行extend( controlCheckbox, new Subject() );保存了目标实例
1.  当它被点击时候，向Observer发出通知，执行了controlCheckbox.notify( controlCheckbox.checked );

***ConcreteObserver*** （具体观察者）
即本例里面的check，案例中职责分别为：
1.  透过执行controlCheckbox.addObserver(check) 储存一个指向ConcreteSubject的引用
1.  通过覆写 check.update 实现自身状态和目标状态保持一致

##知识点三：发布订阅模式
发布订阅模式类似观察者模式，但该模式会使用一个主题/事件通道（ topic/event channel）。此通道介于希望接收通知（订阅者）对象与激活事件（发布者）对象之间。此通知系统可以自定义特定事件/传递自定义参数（包括订阅者需要的值）。这样设计主要为了避免订阅者与发布者之间产生依赖关系。

中心思想是促进代码松散耦合（loose coupling）。通过订阅另一个对象的特定任务或活动，当活动发生时候获得通知，而不是通过单个对象直接调用其他对象的方法。

此模式适合在JavaScript生态系统，ECMAScript大多数的实现都是事件驱动。特别是浏览器环境下DOM使用事件作为主要的交互API。

一般的发布订阅模式，含有publish（发布）、subscribe（订阅）、unsubscribe（取消订阅）的功能

以下使用jQuery为例
```javascript
// Publish
$( el ).trigger( "/login", [{username:"test", userData:"test"}] );
// Subscribe
$( el ).on( "/login", function( event ){...} );
// Unsubscribe
$( el ).off( "/login" );
```

##知识点四：观察者模式 vs 订阅模式
相同点：
1.  具有相同的思想，都是考虑应用的不同部分，他们的驱动关系
1.  促进代码松散耦合，把应用程序分解成更小的块，提高代码复用

不同点：
观察者模式：维护多个相关对象，不需要把各个类紧密耦合。例如一个对象要通知其他对象，无需在这些对象上做太多兼容。

优点：
1.  促进代码松散耦合，把应用程序分解成更小的块，提高代码复用
1.  很容易扩展代码
1.  观察者与目标/发布者与订阅者之间动态关系，提供很大的灵活性
1.  容易测试

缺点：
1.  发布订阅模式的中间通道由于没有告诉系统信息传递的结果状态/执行结果，例如订阅者执行报错，发布者本身是不能知道
1.  随着订阅者和发布者的数量增加，信息也会随着猛增，滥用此模式会增加系统崩溃风险
1.  更改订阅者和发布者的关系会导致问题，由于大家都不认识对方
1.  内存泄漏风险，当观察者不存在，如果不手动删除，观察者会一直存在于观察者列表中

###知识点五：订阅模式实现[^1]
```javascript

//主题/事件通道（ topic/event channel）
var makePubSub = function () {
	var callbacks = {},
        publish = function (){
            //Turn arguments object into real array
            var args = Array.prototype.slice.call(arguments, 0);
    
            //Extract the event name which is the first entry
            var ev = args.shift();

            //Return if callbacks object doesn't contain 
	    //any entry for event
            var list, i, l;
            if(!callbacks[ev]) { return this; }
            list = callbacks[ev];
    
            //Invoke the callbacks, passing in the rest of parameters
            for (i=0, l=list.length; i<l; i=i+1){
                list[i].apply(this, args);
            }

            return this;
        },
        subscribe = function (ev, callback){
	    //Check if ev is already registered
	    // If it isn't create an array entry for it
            if(!callbacks[ev]){
                callbacks[ev] = [];
            }
            callbacks[ev].push(callback);
            return this;
        };

	return {pub: publish, sub: subscribe};
};

test = makePubSub()
test.sub('alert', function(){console.log('hello');})
test.pub('alert')
```

[^1]:  [Design Patterns: PubSub Explained](https://abdulapopoola.com/2013/03/12/design-patterns-pub-sub-explained/)
