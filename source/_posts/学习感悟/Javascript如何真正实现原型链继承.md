---
title: Javascript如何真正实现原型链继承
date: 2016-9-29 21:32:17
tags: [JavaScript,翻译]
description: work
cover: /images/1.jpg
categories: 学习感悟
---
**本文由我翻译在众成翻译平台，[文章地址](http://www.zcfy.cc/article/javascript-how-prototypal-inheritance-really-works-1337.html)**


在网上的很多地方我们可以得知javascript是基于原型链继承的，其实Javascript只提供一种特殊的方法来实现原型链继承，就是通过new操作。但是很多解读都令人难以理解，这篇文章旨在说明到底什么是原型链继承以及如何在Javascript中真正地运用它。
<!--more-->
### 原型链继承的定义
当你读到有关于Javascript原型链继承的内容时，经常看到这样的定义：
 
> 当对象存取一个属性时，Javascript会沿着原型链向上查询直到找到要存取的属性名（或原型链顶端）[Javascript Garden](http://bonsaiden.github.com/JavaScript-Garden/#object.prototype)

许多Javascript实践运用 [__proto__](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/proto) 属性来表示原型链上的下一个对象，我们会在下文看看\_\_proto__和prototype之间有什么区别。

**提示**: \_\_proto__不是一种标准，不应该将它运用在你的实际代码里，它在文章只是用来解释Javascript如何实现继承。

下面的代码展示了javascript引擎是如何获取属性的：

```
function getProperty(obj, prop) {
  if (obj.hasOwnProperty(prop))
    return obj[prop]

  else if (obj.__proto__ !== null)
    return getProperty(obj.__proto__, prop)

  else
    return undefined
}
```

我们举个通常的栗子：一个2D的点，一个点有两个坐标值x和y，以及一个print方法。

根据前面原型链继承的定义，我们将构造一个对象表示点，拥有三个属性x，y，print，为了创建一个新的点，我们只需要让新的对象的\_\_proto__设为Point：

```
var Point = {
  x: 0,
  y: 0,
  print: function () { console.log(this.x, this.y); }
};

var p = {x: 10, y: 20, __proto__: Point};
p.print(); // 10 20
```

### Javascript原型链继承的奇特之处

令人疑惑的是，每一个教别人Javascript原型链继承的人都不用上面的代码，他们会使用如下代码：

```
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype = {
  print: function () { console.log(this.x, this.y); }
};

var p = new Point(10, 20);
p.print(); // 10 20
```

这和之前的代码完全不同，Point是一个函数，用到prototype属性，new操作符，这是什么意思？

#### new是如何运作的
[Brendan Eich](http://brendaneich.com/) 希望Javascript看起来更像传统的面向对象语言，比如Java 和C++，因此用new操作符来创建一个类的实例，于是他给Javascript添加了new操作符。

*   C++中有关于构造的概念，是用来初始化实例属性，因此new操作符必须指向一个函数。
*   我们需要在一些地方用到对象的方法，由于我们使用的是一门原型语言，可将方法放在函数的原型属性上。

new操作符创建函数F和参数arguments : new F(arguments...)，进行简单的三步操作：

1.  **创建一个实例**，它是一个空对象，其\_\_proto\_\_属性被设为F.prototype。
2.  **实例初始化**，将函数F的参数传递到实例上，F的this(上下文)被设为指向实例。
3.  **返回实例**。

现在我们明白new操作符做了什么，我们可以在Javascript中实践它。

```
     function New (f) {
/*1*/  var n = { '__proto__': f.prototype };
       return function () {
/*2*/    f.apply(n, arguments);
/*3*/    return n;
       };
     }
```

一个小测试看看它如何运行。

```
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype = {
  print: function () { console.log(this.x, this.y); }
};

var p1 = new Point(10, 20);
p1.print(); // 10 20
console.log(p1 instanceof Point); // true

var p2 = New (Point)(10, 20);
p2.print(); // 10 20
console.log(p2 instanceof Point); // true
```

### Javascript真正的原型链继承

在[Javascript specifications](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-262.pdf) 只给了我们一种基于new操作符的实现方法 ,但是Douglas Crockford 还是找到一种利用new操作符实现原型继承的方法，他写了一个[Object.create](http://javascript.crockford.com/prototypal.html)函数。


```
Object.create = function (parent) {
  function F() {}
  F.prototype = parent;
  return new F();
};
```

这看起来非常奇怪，但它的原理其实非常简单。将创建的一个新对象的prototype属性设置为你想要继承的对象，如果允许使用\_\_proto\_\_，那么可以改写为：

```
Object.create = function (parent) {
  return { '__proto__': parent };
};
```

下面就是用真正的原型链继承来实现的Point例子的继承过程。
 
```
var Point = {
  x: 0,
  y: 0,
  print: function () { console.log(this.x, this.y); }
};

var p = Object.create(Point);
p.x = 10;
p.y = 20;
p.print(); // 10 20
```

### 结论

我们已经知道了什么是原型链继承以及Javascript怎样通过一种特殊的方式实现它。

但是，这种原型链继承方法(Object.create and \_\_proto\_\_) 有一些缺点：

*   **没有标准**: \_\_proto\_\_不是标准甚至是被反对的. 包括通用的Object.create和Douglas Crockford实践都没有确定的标准形式。
*   **没有优化**: Object.create比起new操作方式显得更加笨重，这一点被证明于[10 times slower](http://jsperf.com/object-create-vs-crockford-vs-jorge-vs-constructor/16).

#### 参考阅读：

*   [Douglas Crockford: Prototypal Inheritance](http://javascript.crockford.com/prototypal.html)
*   [MDC Documentation: __proto__](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/proto)
*   [John Resig: getPrototypeOf](http://ejohn.org/blog/objectgetprototypeof/)
*   [Javascript Garden: Object.prototype](http://bonsaiden.github.com/JavaScript-Garden/#object.prototype)
*   [Dmitry Shoshnikov: OOP: ECMAScript Implementation](http://dmitrysoshnikov.com/ecmascript/chapter-7-2-oop-ecmascript-implementation/)
*   [Angus Croll: Understanding Javascript prototypes](http://javascriptweblog.wordpress.com/2010/06/07/understanding-javascript-prototypes/)
*   [Yehuda Katz: Understanding JavaScript Function Invocation and “this”](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)