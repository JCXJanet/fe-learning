---
title: 前端校招-----设计模式
date: 2021-02-15 21:59:09
tags: 设计模式
---

## 1、单例模式

定义：是保证一个类只有一个实例，并且提供一个访问它的全局访问点。

需求：一些对象我们往往只需要一个，比如线程池、全局缓存、浏览器中的window对象、登录浮窗等。

优点：

1、可以用来划分命名空间，减少全局变量的数量 

2、可以被实例化，且实例化一次，再次实例化生成的也是第一个实例

```
class CreateUser{
    constructor(name){
        this.name = name;
        this.getName();
    }
    getName(){
        return this.name;
    }
}
var  ProxyMode = (function(){
    var instance = null;
    return function(name){
        if(!instance){
            instance = new CreateUser(name);
        }
        return instance;
    }
})();
var a= new ProxyMode('aaa');//CreateUser {name: "aaa"}
var b= new ProxyMode('bbb');//CreateUser {name: "aaa"}结果都是第一个对象
```



##  2、观察者模式

定义：对象间的一种一对多的依赖关系。

需求：当一个对象的状态发生变化时，所有依赖于他的对象都将得到通知。

优点：时间上的解耦，对象之间的解耦。



```
var targetObj = {
    name:'aa'
}
var targetObj2 = {
    name:'aa'
}
// 定义值改变时的处理函数（观察者）
function observer(oldVal, newVal) {
    // 其他处理逻辑...
    targetObj2.name = newVal
    console.info(newVal);
}

// 定义name属性及其set和get方法（name属性为被观察者）
Object.defineProperty(targetObj, 'name', {
    enumerable: true,
    configurable: true,
    get: function() {
        return name;
    },
    set: function(val) {
        observer(name, val)
        name = val
    }
});

targetObj.name = '11';
targetObj.name = '22';
console.log(targetObj2.name)
```



## 3、发布者/订阅者模式

**在网上看到一个对发布者/订阅者模式与观察者模式，比较精辟的总结：**

**有中间商赚差价的是发布者/订阅者模式，无中间商赚差价的是观察者模式。**

```
function Publish(){
    let s =[];
    this.add= function(sMember){
        let isExist = s.some(function(item){
            if(item == sMember){
                return true;
            }else{
                return false;
            }
        })
        if(!isExist){
            s.push(sMember);
        }
    }
    this.send = function(data){
        s.forEach(function(fn){
            fn(data);
        })
        return this;
    }
}

let a = function(data){
    console.log(data);
}
let b = function(data){
    console.log(data);
}
let p = new Publish();
p.add(a);
p.add(b);
p.send('sadasdsa');
```



## 4、工厂模式：

定义：将其成员对象的实例化推迟到子类来实现的类。

需求：创建对象的流程赋值的时候，比如依赖于很多设置文件等 ；处理大量具有相同属性的小对象；注：不能滥用

优点：不暴露创建对象的具体逻辑，而是将将逻辑封装在一个函数中。   

```
function createObject(name ,age){
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.run = function (){
         return this.name + this.age;
    }
    return obj;
}
var a = createObject('lao',22);
```

## 5、模块模式

```
var Person = (function() {
    var name = 'xxx'
    function sayName() {
        console.log(name)
    }
    return{
        name: name,
        sayName: sayName
    }
})()
```



## 6、代理模式

使用ES6中的proxy实现

```
const handler = {
    get: function() {
        console.log('doing');
    }
}

const initialObj = {
    id: 1,
    name: 'cc'
}

const proxiedObj = new Proxy(initialObj, handler);

console.log(proxiedObj.name);
```



## 7、策略模式

策略模式的本意将算法的使用与算法的实现分离开来，避免多重判断调用哪些算法。适用于有多个判断分支的场景，如解决表单验证的问题。

```
// 对于vip客户
function vipPrice() {
    this.discount = 0.5;
}
 
vipPrice.prototype.getPrice = function(price) {
　　return price * this.discount;
}
// 对于老客户
function oldPrice() {
    this.discount = 0.3;
}
 
oldPrice.prototype.getPrice = function(price) {
    return price * this.discount;
}
// 对于普通客户
function Price() {
    this.discount = 1;
}
 
Price.prototype.getPrice = function(price) {
    return price ;
}

// 上下文，对于客户端的使用
function Context() {
    this.name = '';
    this.strategy = null;
    this.price = 0;
}
 
Context.prototype.set = function(name, strategy, price) {
    this.name = name;
    this.strategy = strategy;
    this.price = price;
}
Context.prototype.getResult = function() {
    console.log(this.name + ' 的结账价为: ' + this.strategy.getPrice(this.price));
}

var context = new Context();
var vip = new vipPrice();
context.set ('vip客户', vip, 200);
context.getResult();   // vip客户 的结账价为: 100

var old = new oldPrice();
context.set ('老客户', old, 200);
context.getResult();  // 老客户 的结账价为: 60

var Price = new Price();
context.set ('普通客户', Price, 200);
context.getResult();  // 普通客户 的结账价为: 200
```



接下去的几种模式设计到继承这方面

## 8、构造函数模式

```
  function Car(name, year, country){
    this.name = name;
    this.year = year;
    this.country = country;
  }
  Car.prototype.sayName = function(){
    return this.name;
  }
  var mycar = new Car("BMW", 2017, "Germany");
  console.log(mycar);
```

## 9、混合模式

```
function Parent(name){
    this.name = name;
}
Parent.protoType.sayName = function(){
    console.log('parent name:',this.name);
}
Parent.protoType.doSomeThing = function(){
    console.log('parent doSomeThing');
}
function Child(name,fatherName){
    Parent.call(this,faterName);
    this.name = name;
}
Child.protoType = Parent.protoType;
Child.protoType.constructor = Child;
Child.protoType.sayNmae =  function(){
    console.log('child name:',this.name);
}
var child = new Child('son');
child.sayName();
child.doSomeThing();
//通过原型链实现对象属性和方法的继承，而通过构造函数来实现实例属性的继承。
```



## 10、中间者模式

```
function Parent(name){
    this.name = name;
}
Parent.protoType.sayName = function(){
    console.log('parent name:',this.name);
}
Parent.protoType.doSomeThing = function(){
    console.log('parent doSomeThing');
}
function Child(name,fatherName){
    Parent.call(this,faterName);
    this.name = name;
}
function create(proto){
    function F(){}
    F.proto=proto;
    return new F();
}
Child.protoType = create(Parent.protoType);
Child.protoType.sayNmae =  function(){
    console.log('child name:',this.name);
}
Child.protoType.constructor = Child;
//用F（）{}去替代父类执行构造函数
```



参考：https://www.jianshu.com/p/4f3014fb8b8b

https://www.cnblogs.com/smlp/p/9776789.html

https://www.jianshu.com/p/5a3af723ad84