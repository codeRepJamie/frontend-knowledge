# 对象操作

## 创建对象方法

### 对象字面量
```js
var a = {b:1}
```
### JSON转义
```js
var a = JSON.parse("{\"b\":1}")
```
### 用Function模拟构造函数
```js
function Person(){
  this.name='Mark'
  this.work = function(){
    alert(this.name+' a')
  }
}
var person = new Person();
person.work()
```
### 用工厂方法
```js
var person = new Object();
person.name = 'Mark'
person.work = function(){
  alert(person.name+' a')
}
person.work()
```
### 用原型方法
```js
function Person(){};
person.prototype.name = 'Mark'
person.prototype.work = function(){
  alert(person.name+' a')
}
var person = new Person()
person.work()
```
### 用混合方法
```js
function Person(){
  this.name = 'Mark'
};
person.prototype.work = function(){
  alert(person.name+' a')
}
var person = new Person()
person.work()
```
### 用原型方法
```js
var a = Object.create({b:1})
```