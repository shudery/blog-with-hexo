---
title: ES5和ES6中对于继承的实现方法
date: 2016-07-23
tags: [JavaScript]
categories: 笔记
---

在 ES5 继承的实现非常有趣的，由于没有传统面向对象类的概念，Javascript 利用原型链的特性来实现继承，这其中有很多的属性指向和需要注意的地方。
原型链的特点和实现已经在之前的一篇整理说过了，就是通过将子类构造函数的原型作为父类构造函数的实例，这样就连通了子类-子类原型-父类，原型链的特点就是逐层查找，从子类开始一直往上直到所有对象的原型 Object.prototype，找到属性方法之后就会停止查找，所以下层的属性方法会覆盖上层。

一个基本的基于原型链的继承过程大概是这样的：

```
//先来个父类，带些属性
function Super(){
    this.flag = true;
}
//为了提高复用性，方法绑定在父类原型属性上
Super.prototype.getFlag = function(){
    return this.flag;
}
//来个子类
function Sub(){
    this.subFlag = false;
}
//实现继承
Sub.prototype = new Super;
//给子类添加子类特有的方法，注意顺序要在继承之后
Sub.prototype.getSubFlag = function(){
    return this.subFlag;
}
//构造实例
var es5 = new Sub;
```

原型链实现的继承主要有几个问题：
1、本来我们为了构造函数属性的封装私有性，方法的复用性，提倡将属性声明在构造函数内，而将方法绑定在原型对象上，但是现在子类的原型是父类的一个实例，自然父类的属性就变成子类原型的属性了；
这就会带来一个问题，我们知道构造函数的原型属性在所有构造的实例中是共享的，所以原型中属性的改变会反应到所有的实例上，这就违背了我们想要属性私有化的初衷；
2、创建子类的实例时，不能向父类的构造函数传递参数

```
function Super(){
    this.flag = true;
}
function Sub(){
   this.subFlag = false;
}
Sub.prototype = new Super;
var obj = new Sub();
obj.flag = flase;  /* 修改之后，由于是原型上的属性，之后创建的所有实例都会受到影响 */
var obj_2 = new Sub();
console.log(obj.flag)  //false；
```

为了解决以上两个问题，有一个叫借用构造函数的方法
只需要在子类构造函数内部使用 apply 或者 call 来调用父类的函数即可在实现属性继承的同时，又能传递参数，又能让实例不互相影响

```
function Super(){
    this.flag = true;
}
function Sub(){
    Super.call(this);  /* 如果父类可以需要接收参数，这里也可以直接传递 */
};
var obj = new Sub();
obj.flag = flase;
var obj_2 = new Sub();
console.log(obj_2.flag)  //依然是true，不会相互影响
```

结合借用构造函数和原型链的方法，可以实现比较完美的继承方法，可以称为组合继承：

```
function Super(){
    this.flag = true;
}
Super.prototype.getFlag = function(){
    return this.flag;     /* 继承方法 */
}
function Sub(){
    this.subFlag = flase
    Super.call(this)    /* 继承属性 */
}
Sub.prototype = new Super;
var obj = new Sub();
Super.prototype.getSubFlag = function(){
    return this.flag;
}
```

这里还有个小问题，Sub.prototype = new Super; 会导致 Sub.prototype 的 constructor 指向 Super;
然而 constructor 的定义是要指向原型属性对应的构造函数的，Sub.prototype 是 Sub 构造函数的原型，所以应该添加一句纠正：
Sub.prototype.constructor = Sub;

看完 ES5 的实现，再来看看 ES6 的继承实现方法，其内部其实也是 ES5 组合继承的方式，通过 call 借用构造函数，在 A 类构造函数中调用相关属性，再用原型链的连接实现方法的继承

```
class B extends A {
  constructor() {
    return A.call(this);  //继承属性
  }
}
A.prototype = new B;  //继承方法
```

ES6 封装了 class，extends 关键字来实现继承，内部的实现原理其实依然是基于上面所讲的原型链，不过进过一层封装后，Javascript 的继承得以更加简洁优雅地实现

```
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); /* 等同于parent.constructor(x, y) */
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 等同于parent.toString()
  }
}
```

通过 constructor 来定义构造函数，用 super 调用父类的属性方法

ES6 中 Class 充当了 ES5 中，构造函数在继承实现过程中的作用
同样有原型属性 prototype，以及在 ES5 中用来指向构造函数原型的`__proto__`属性，这个属性在 ES6 中的指向有一些主动的修改。
一个继承语句同时存在两条继承链：一条实现属性继承，一条实现方法继承。

```
class A extends B {}
A.__proto__ === B;  /* 继承属性 */
A.prototype.__proto__ === B.prototype;  //继承方法
```

ES6 的子类的`__proto__`是父类，子类的原型的`__proto__`是父类的原型
第二条继承链理解起来没有什么问题，对应到 ES5 中的 A.prototype = new B;A.prototype 作为 B 构造的实例，指向构造函数 B 的原型 B.prototype，
但是在 ES5 中 A.`__proto__`是指向 Function.prototype 的，因为每一个构造函数其实都是 Function 这个对象构造的，ES6 中子类的`__proto__`指向父类可以实现属性的继承，在 ES5 中在没有用借用继承的时候由于父类属性被子类原型继承，所有的子类实例实际上都是同一个属性引用。
在 ES6 中实现了子类继承父类属性，在构造实例的时候会直接拿到子类的属性，不需要查找到原型属性上面去，ES6 新的静态方法和静态属性（只能在构造函数上访问）也是通过这样类的直接继承来实现，至于普通复用方法还是放到原型链上，道理和实现和 ES5 是一样的。
此外我认为这里修改 A.`__proto__`的指向是有意区分 ES6 中继承和实例化，同时建立子类和父类直接的关系，ES5 的子类的构造函数通过子类的原型与父类的构造函数连接，不存在直接的关系；
可以这么说，在 ES5 继承和构造实例，ES6 构造实例的时候可以理解`__proto__`原型指针是用来指向构造函数的原型的，但是在 ES6 继承中，`__proto__`指继承自哪个类或原型，在 A 继承 B 之后，构造一个实例 var obj = new A; 会发现它所有的属性指向都是和 ES5 一致的。

有个有趣的地方：ES6 继承是在父类创建 this 对象，在子类 constructor 中来修饰父类的 this，ES5 是在子类创建 this，将父类的属性方法绑定到子类，由于原生的构造函数（Function，Array 等）没有 this，子类无法通过 call/apply(this)获得其内部属性，所以在 ES5 无法继承，ES6 实现后可以为原生构造函数封装一些有趣的接口，比方说阮一峰老师的这个给 Array 实例封装一个版本记录和回滚的方法：

```
class VersionedArray extends Array {
  constructor() {
    super();
    this.history = [[]];
  }
  commit() {
    this.history.push(this.slice());
  }
  revert() {
    this.splice(0, this.length, ...this.history[this.history.length - 1]);
  }
}
var x = new VersionedArray();
x.push(1);
x.push(2);
x // [1, 2]
x.history // [[]]
x.commit();
x.history // [[], [1, 2]]
x.push(3);
x // [1, 2, 3]
x.revert();
x // [1, 2]
```

JS 继承小结：
ES5 最经典的继承方法是用组合继承的方式，原型链继承方法，借用函数继承属性，ES6 也是基于这样的方式，但是封装了更优雅简洁的 api，让 Javascript 越来越强大，修改了一些属性指向，规范了继承的操作，区分开了继承实现和实例构造，此外 ES6 继承还能实现更多的继承需求和场景。
