---
title: JavaScript如何真正实现原型链继承
date: 2016-8-20
tags: [JavaScript]
categories: 翻译
---

**本文由我翻译在[众成](http://www.zcfy.cc/claim)翻译平台，[文章地址](http://www.zcfy.cc/article/javascript-how-prototypal-inheritance-really-works-1337.html)**

在网上的很多地方我们可以得知 javascript 是基于原型链继承的，其实 Javascript 只提供一种特殊的方法来实现原型链继承，就是通过 new 操作。但是很多解读都令人难以理解，这篇文章旨在说明到底什么是原型链继承以及如何在 Javascript 中真正地运用它。

### 原型链继承的定义

当你读到有关于 Javascript 原型链继承的内容时，经常看到这样的定义：

> 当对象存取一个属性时，Javascript 会沿着原型链向上查询直到找到要存取的属性名（或原型链顶端）[Javascript Garden](http://bonsaiden.github.com/JavaScript-Garden/#object.prototype)

许多 Javascript 实践运用 [**proto**](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/proto)  属性来表示原型链上的下一个对象，我们会在下文看看\_\_proto\_\_和 prototype 之间有什么区别。

**提示**: \_\_proto\_\_不是一种标准，不应该将它运用在你的实际代码里，它在文章只是用来解释 Javascript 如何实现继承。

下面的代码展示了 javascript 引擎是如何获取属性的：

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

我们举个通常的栗子：一个 2D 的点，一个点有两个坐标值 x 和 y，以及一个 print 方法。

根据前面原型链继承的定义，我们将构造一个对象表示点，拥有三个属性 x，y，print，为了创建一个新的点，我们只需要让新的对象的\_\_proto\_\_设为 Point：

```
var Point = {
  x: 0,
  y: 0,
  print: function () { console.log(this.x, this.y); }
};
var p = {x: 10, y: 20, __proto__: Point};
p.print(); // 10 20
```

### Javascript 原型链继承的奇特之处

令人疑惑的是，每一个教别人 Javascript 原型链继承的人都不用上面的代码，他们会使用如下代码：

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

这和之前的代码完全不同，Point 是一个函数，用到 prototype 属性，new 操作符，这是什么意思？

#### new 是如何运作的

[Brendan Eich](http://brendaneich.com/)  希望 Javascript 看起来更像传统的面向对象语言，比如 Java 和 C++，因此用 new 操作符来创建一个类的实例，于是他给 Javascript 添加了 new 操作符。

- C++中有关于构造的概念，是用来初始化实例属性，因此 new 操作符必须指向一个函数。
- 我们需要在一些地方用到对象的方法，由于我们使用的是一门原型语言，可将方法放在函数的原型属性上。

new 操作符创建函数 F 和参数 arguments : new F(arguments...)，进行简单的三步操作：

1.  **创建一个实例**，它是一个空对象，其\_\_proto\_\_属性被设为 F.prototype。
2.  **实例初始化**，将函数 F 的参数传递到实例上，F 的 this(上下文)被设为指向实例。
3.  **返回实例**。

现在我们明白 new 操作符做了什么，我们可以在 Javascript 中实践它。

```
function New (f) {
 var n = { '__proto__': f.prototype };
 return function () {
   f.apply(n, arguments);
   return n;
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

### Javascript 真正的原型链继承

在[Javascript specifications](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-262.pdf)  只给了我们一种基于 new 操作符的实现方法 ,但是 Douglas Crockford 还是找到一种利用 new 操作符实现原型继承的方法，他写了一个[Object.create](http://javascript.crockford.com/prototypal.html)函数。

```
Object.create = function (parent) {
  function F() {}
  F.prototype = parent;
  return new F();
};
```

这看起来非常奇怪，但它的原理其实非常简单。将创建的一个新对象的 prototype 属性设置为你想要继承的对象，如果允许使用\_\_proto\_\_，那么可以改写为：

```
Object.create = function (parent) {
  return { '__proto__': parent };
};
```

下面就是用真正的原型链继承来实现的 Point 例子的继承过程。

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

我们已经知道了什么是原型链继承以及 Javascript 怎样通过一种特殊的方式实现它。

但是，这种原型链继承方法(Object.create and \_\_proto\_\_) 有一些缺点：

- **没有标准**: \_\_proto\_\_不是标准甚至是被反对的. 包括通用的 Object.create 和 Douglas Crockford 实践都没有确定的标准形式。
- **没有优化**: Object.create 比起 new 操作方式显得更加笨重，这一点被证明于[10 times slower](http://jsperf.com/object-create-vs-crockford-vs-jorge-vs-constructor/16).

#### 参考阅读：

- [Douglas Crockford: Prototypal Inheritance](http://javascript.crockford.com/prototypal.html)
- [MDC Documentation: **proto**](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/proto)
- [John Resig: getPrototypeOf](http://ejohn.org/blog/objectgetprototypeof/)
- [Javascript Garden: Object.prototype](http://bonsaiden.github.com/JavaScript-Garden/#object.prototype)
- [Dmitry Shoshnikov: OOP: ECMAScript Implementation](http://dmitrysoshnikov.com/ecmascript/chapter-7-2-oop-ecmascript-implementation/)
- [Angus Croll: Understanding Javascript prototypes](http://javascriptweblog.wordpress.com/2010/06/07/understanding-javascript-prototypes/)
- [Yehuda Katz: Understanding JavaScript Function Invocation and “this”](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)
