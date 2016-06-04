---
title: javascript的对象构造和原型链继承
date: 2016-04-14 17:55:58
tags: [javascript,原型链,继承]
description: javascript的对象构造和原型链继承
---
这两天仔细地学习了JS的创建对象以及继承的方法，结合红宝石书整理了下笔记。
（红宝石这里讲了非常多的模式，看第一遍觉得还蛮乱的）
Javascript作为一种动态的面向对象语言，本身却没有类的概念和方法，
在创建对象和继承方面有很多有趣的实现和方法
<!--more-->
## 1.原始绑定和字面量表示：
**优点**：简洁方便
**问题**：使用对象字面量的方法创建的对象，若重复创建会产生大量的重复代码
```
var o = new Object();
o.name = shudery;
o.skill = function(){console.log('sayHello')};
//这是比较老的方法，一般用下面这种简单粗暴的，直接字面量创建

var obj = {
    sex = man,
    skill = function(){console.log('tucao')} 
    ...
}
```
## 2.工厂模式：
抽象创建具体对象的过程
**具体**：用函数来封装对象，然后调用特定接口创建对象
**优点和问题**：解决代码重复性的问题，但是没有解决对象识别问题
```
function createObj(name){
    var obj= {
        name : name ,
        skill : function(){console.log('tucao')}
    };
    return obj;
}
var obj_1 = createObj('shudery');
var obj_2 = createObj('Lin'); 
//Object类型的对象
```
## 3.构造函数模式：
使用`new`操作符，没有显式创建对象
**优点**：解决了代码识别问题，可以将它构造的实例标示为特定类型
**问题**：多次复用的方法需要在每个实例上重新创建一遍，浪费内存
```
function Myobj(name){
        this.name = name,
        this.skill= function(){console.log('tucao')}   
}
var obj_1 =  new Myobj('shudery');
var obj_2 =  new Myobj('Lin');
//对象类型名称为myObj，解决了识别的问题，用下面方法可以验证
console.log(obj_1.constructor === Myobj && obj_2 instanceof Myobj);//true
```
这里如果怕遗漏new关键字，可以在`myObj`函数里头显示地返回一个对象，如下：
```
function Myobj(name){
        var obj={};
        obj.name=name,
        obj.method= function(){
        console.log('tucao')
         }
    return obj;
}
var obj_1 = Myobj('shudery');
var obj_2 = new Myobj('shudery');
```
如果这么做`obj_1`的`constructor`指向的是`Object`
这样就和工程模式一样不能识别对象，
但是功能上两个对象相同，这也被叫寄生构造函数模式，一般不用它
此外在构造函数模式下如果没有引用this和new，那就是稳妥构造函数模式，
一般用在安全的环境

## 4.原型模式：
在构造函数模式下如果直接提取出方法函数到全局环境下，
在方法变多时容易污染命名空间，
此外不利于我们自定义的引用类型的封装。
我们可以用JS的一大特色原型属性来挂载属性方法。
**优点**：我们可以将复用的方法绑定到每一个构造函数定义的原型属性prototype上来，这样所有的对象实例都可以共享这个挂在这个原型下的属性和方法。
**问题**：实例失去本身的特点和封装，公用一套属性和方法
```
function Myobj(){
       
}
Myobj.prototype.name ='shudery';
Myobj.prototype.age = '22';
Myobj.prototype.method = function(){
    console.log('tucao')
}
var obj =  new Myobj();
```
也可用对象字面量形式，不过要注意顺序，字面量的形式相当于重写整个对象，
构造函数原型的`constructor`属性（指向对象类型）会被重写为`Object`,
而不是`Myobj`了
```
function Myobj(){
       
}
var obj =  new Myobj();//先实例化
Myobj.prototype={
//重写后这个原型和Myobj这个构造函数就没有关系了
    name: 'shudery',
    age:'22',
    method : function(){
    console.log('tucao')
    }
};
obj.name;//undefined，联系被切断了
```
如果在重写之后实例化一个对象，虽然还存在联系，
但是`obj`实例就不是`Myobj`类型了
不在乎对象识别问题的话，也可以直接这样重写，仍能调用obj.name等属性和方法

## 5.终极模式：构造函数+原型模式
 最为常用的创建对象的方式，结合两种模式的优点，
 实现对象属性的封装和对象方法的复用，
平衡了对象的独立性和多态复用节省内存的问题。
```
function Myobj(name,age){
    this.name =name;
    this.age = age
}
Myobj.prototype.method = function(){
    console.log('tucao')
}
var obj =  new Myobj('shudery',22);
```
如此一来对象就拥有自定义的属性和可共享复用的方法啦
有句话这样说，当我们把程序中变化的部分封装好之后，剩下的就是稳定可复用的了
这也是很多多种设计模式里推崇的，放到创建对象的方法里也是一样的做法。

说完对象的创建，接下来是继承~
JS是没有类的关键字和概念的，它的继承是基于原型链的，类似上面的原型模式
我们先简单了解下原型链：

![](https://raw.githubusercontent.com/shudery/public/master/clipboard.png)

每个构造函数都有一个原型对象，构造的实例有一个指向原型对象的指针，
如果我们把父类的实例赋值给原型对象，那么子类的原型对象就是父类的实例， 
那么这个实例还是有一个指向父类原型的指针，
这些指针连起了子类和父类的实例（子类构造函数的原型），就是原型链。

对象的方法和属性会循着原型链进行访问，直到查找到相应的属性和方法名，
或者到达原型链的终点`Object.prototype`,这也就实现了继承










