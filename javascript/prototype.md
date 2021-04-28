# 原型与原型链

## 创建对象几种方法
1. 字面量 var obj = {name:'abc'}
2. 构造形式 var obj = new Object({name:'abc'})
3. 使用显式构造函数 
   - var M = function(){this.name = 'o2' } 
   - var obj = new M();
4. 使用ES6对象的内置方法
   - const obj = Object.create({name:'o3'})

## instanceof原理
- 实例对象的__proto__是否与构造函数的prototype是否引用同一个内存地址（obj.__proto__ === F.prototype）
- 遍历实例对象的[[prototype]]原型链上找到改构造函数的prototype
- 结论instanceof不能完全证明一个实例对象是该构造函数直接创建的

## 原型与原型链
- 原型
  - 一个对象，通过原型可以实现对象的属性继承。JavaScript的对象中都包含了一个" [[Prototype]]"内部属性，这个属性所对应的就是该对象的原型。
  - "[[Prototype]]"作为对象的内部属性，是不能被直接访问的。所以为了方便查看一个对象的原型，Firefox和Chrome中提供了"__proto__"这个非标准（不是所有浏览器都支持）的访问器。ECMA引入了标准对象原型访问器"Object.getPrototypeOf(object)"
- 原型链
  - 如果一个对象没有找到需要的属性和方法引用，js引擎就会继续在[[prototype]]关联的对象上进行查找，如果后者也没有找到需要的引用就会继续查找它的[[prototype]],以此类推。这一系列的对象的链接被称为“原型链”。
  - 当查找一个对象的属性时，JavaScript 会向上遍历原型链，直到找到给定名称的属性为止，到查找到达原型链的顶部（也就是 "Object.prototype"）， 如果仍然没有找到指定的属性，就会返回 undefined。
  - "hasOwnProperty"是"Object.prototype"的一个方法，该方法能判断一个对象是否包含自定义属性而不是原型链上的属性

## new操作流程
1. 创建一个空对象，并且 this 变量引用该对象，同时实例原型还继承了该构造函数的原型对象prototype
2. 新创建的对象由 this 所引用，可以在构造函数内，通过this往上面添加属性与方法
3. 隐式返回this
   - 如果显式返回基本数据类型，结果与隐式返回this相同
   - 如果显式返回引用数据类型，new F()结果会返回该数据

## 更改__proto__指向方式
1. 字面量方式
   ```js
   var a = {}
   // 相当于 
   // a.__proto__ = Object.prototype
   ```
2. 构造器方式
   ```js
   var A = function(){};
   var a = new A();
   // 相当于 
   // a.__proto__ = A.prototype
   ```
3. Object.create方式
   ```js
   var a1={} 
   var a2 = Object.create(a1)
   // 相当于
   // a2.__proto__ = a1;
   ```

## js继承方式
### 构造继承
```js
function Child () {
  Parent.call(this);
}
// 缺点
// 不能继承父类的原型
```
### 原型链继承
```js
Child.prototype = new Parent();
// 缺点
// 导致父类的构造函数体内的属性,只能通过原型链访问，不是实例的自己的属性
// 实例与实例之间会因为共享同一个原型对象，当操作父类的属性，会透过原型链影响了其他的实例
```
### 构造+实例混合继承
```js
function Child () {Parent.call(this)};
Child.prototype = new Parent();
// 缺点
// 父类构造函数执行了两次！
// 由于执行两次，子类的原型会含有父类的构造函数属性，变相多了冗余属性
```


### 构造+原型继承
```js
function Child () {Parent.call(this)};
Child.prototype = Parent.prototype;
// 缺点
// 由于子类的prototype和父类prototype绑为同一个内存地址引用，修改子类的prototype会同时影响父类的prototype！
// 实例的constructor属性不属于Child;
```

### 构造+拷贝继承
```js
function Child () {Parent.call(this)};
Child.prototype.constructor = Child
Child.prototype = Object.create(Parent.prototype);
// 缺点
// 需要创建一个新对象然后把旧对象Child.prototype抛弃，不能直接修改已有默认对象
// 需要手动指定constructor属性
```
### 构造+指定prototype继承
```js
function Child () {Parent.call(this)};
Object.setPrototypeOf(Child.prototype,Parent.prototype);
// 需要ES6支持
```
### 标准继承
```js
class Child extends Parent(){
  constructor(){
    super()
  }
}
```