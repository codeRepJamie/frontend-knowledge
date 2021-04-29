#混合模式
> Mixin pattern

##知识点一：定义
在JavaScript中，可以把继承（Mixin）看作扩展收集功能的方式。例如，一个新对象我们可以定义其原型，可以从中继承/重写其他对象的属性，而且可以为任意数量的的对象实例定义属性，促进函数复用。

##知识点二：具体例子
```javascript

//使用类继承实现mixin
class moduleA {
  constructor(){
    this.name = 'moduleA';
    this.city = 'guangzhou';
  }
  getName(){
    return this.name
  }
  say(){
    return 'hi, i\'m '+ this.name
  }
}

//moduleB做的就是在moduleA基础上做扩充，而且可以在say方法上做修改
class moduleB extends moduleA{
  constructor(){
    super();    
    this.age = 12;
    this.city = 'shenzhen';
  }
  getAge(){
    return this.age
  }
  say(){
    const origin = super.say();
    return 'hi, i\'m the new methods, the origin is say:'+ origin
  }
}

const module = new moduleB()
console.log(module.say());

//使用对象继承实现mixin

const fooA = {
  a:1,
  b:2
}

const fooB = Object.create(fooA,{c:3});
console.log(fooB);
```
##知识点三：特点
优点：
1.  有助于减少系统中重复的功能及增加函数复用
缺点：
1.  有观点认为，功能注入原型可能导致原型污染和函数起源不确定性，但可以编写详细的文档把函数来源困惑写明白
