# 第十一课 神奇的this
Javascript并不是传统意义上的面向对象语言。它的几乎所有实体都是对象，数组是对象，字符串是对象，甚至连类也是对象。它也没有真正的继承的语法，它是通过原型链来模拟的继承。因此，如果我们曾经学习过面向对象语言（如C++，Java，C#等），我们可能会对Javascript的this关键字的行为十分的吃惊。因为它在很多时候，与传统的面向对象语言有着十分巨大的差异。如果没有掌握这些差异，在编写程序的时候就会遇到一些十分诡异的情况。本章就来分析这些差异。

由于我们在实践中一律开启严格模式，因此在这里我们只讨论严格模式下的this的行为。非严格模式本章不讨论，也不建议读者使用。Javascript既能在浏览器中运行，也能够在服务器端(node.js)运行。我们先来看一下服务器端的情况，然后再看一下浏览器端的情况。

## 服务器端node.js
### 全局域
我们可以在node.js里运行以下代码。
```javascript
console.log(this);
```
我们会看到，程序的输出是undefined。这是因为标准规定，在服务器端，全局域的this的值为undefined。

### 普通函数
我们现在打印一个普通全局函数中的this。
```javascript
function func() {
  console.log(this);
}
func();
```
我们仍然得到undefined。同样，这也是标准规定，在服务器端，全局函数域中的this的值也为undefined。

注意，普通函数会产生一个函数域，它会屏蔽掉外部域的this。
```javascript
class X {
  func = () => {
     console.log(this); // line 1
     (function() { console.log(this) })(); // line 2
  }
}
```
在line 1，this的值不为undefined（具体值见下文分析）。本来在line 2，this的值应与在line 1时相同。但由于我们在line 2增加了一个匿名函数。不管在line 2这一行的this的值为何，在这个匿名函数内部，this的值就是undefined。如果不希望这样的行为，可以使用箭头表达式（见下文分析）。
	
### 对象中的函数
我们现在在对象中定义一个成员函数。
```javascript
const obj = {
  func: function() {
    console.log(this)
  }
}
obj.func();
```
运行上面的程序，我们会看到输出是{ func: [Function: func] }。也就是说，在对象的成员函数中，this指向该对象本身。在上面的程序中，输出就是对象obj的值。但是，遗憾的是，只有使用成员函数语法调用函数，this的值才是对象本身。如果我们换一种调用方式，就会得到不一样的结果。
```javascript
const obj = {
  func: function() {
    console.log(this)
  }
}
const f = obj.func;
f();
```
当我们用面向对象语言的思路去揣测结果的时候，我们以为我们会得到跟上一个程序一样的输出。然而令人惊讶的是，我们得到的竟然是undefined。这是因为，javascript语法规定，obj.func的值是一个普通函数，而在一个普通函数里，this的值为undefined。而成员函数调用语法，obj.func()，竟然只是一个语法糖，它被执行引擎在后台悄悄的转换成了obj.func.call(obj)。这样的语法规定，让无数javascript新手在传递回调函数的时候折戟沉沙。每次我们写出<button onClick={obj.func}>Submit</button>的时候，都是灾难的开始。解决的办法是使用bind。
```javascript
const f = obj.func.bind(obj);
f();

<button onClick={obj.func.bind(obj)}>Submit</button>
```
注意：在Javascript中，没有所谓的“普通函数”与“成员函数”之分。它只有一种函数，就是普通函数。因此，当我们定义一个成员函数（无论使用哪种语法）的时候，我们定义的是一个普通函数。当我们读取函数属性的时候(const f = obj.func;)，得到的也是一个普通函数。只有当我们使用"."来调用成员函数的时候，Javascript引擎会自动的把这个成员函数内的this绑定到"."前面的对象。这是引擎对`.func()`操作符(和`['func']()`)做的特殊处理（语法糖，使得代码简洁一些。），但其并不影响函数的任何性质。
	
### 箭头表达式
当我们使用箭头表达式替换匿名函数的时候，竟然又产生了另一片天地。
```javascript
const obj = {
  func: () => {
    console.log(this)
  }
}
const f = obj.func.bind(obj);
f();
```
我们已经被obj.func是普通函数这件事折磨的死去活来，于是在上面的程序里，我们刻意使用了bind。当我们天真的以为我们能够打印出obj的时候，现实却又一次无情的打脸。我们再一次得到了undefined。这是因为，函数（包括匿名函数）有自己的域，不同域里面的this可以指向不同的值。而箭头表达式没有自己的域。因此，箭头表达式中的this的值，就是父域中this的值。在上面的程序中，this的父域就是全局域，而全局域的this值为undefined。因此，在对象中定义成员函数，尽量不要用箭头表达式，否则this的值就不是调用对象的值。
	
### 类成员函数
在类的成员函数中，this的值与面向对象语言是一样的。
```javascript
class X {
  func() {
    console.log(this);
  }
}
const x = new X();
x.func();
```
这里，我们打印出来的this就是x对象。同样，这里的x.func()是语法糖。如果我们使用x.func，我们仍然会得到意想不到的结果。因为，在Javascript语言中，并没有类这个东西，类仍然是一个对象。
```javascript
class X {
  func() {
    console.log(this);
  }
}
const x = new X();
const f = x.func;
f();
```
这样，我们还是会得到undefined。原因在上面已经解释过了，x.func会被认为是一个普通函数。我们如果要解决这个问题，仍然可以使用bind。
```javascript
const f = x.func.bind(x);
f();
```
这样，this的值就变成了对象x。但是，在ES6的语法中，还有一种写法能够解决该问题。
```javascript
class X {
  func = () => {
    console.log(this);
  }
}
const x = new X();
const f = x.func;
f();
```
这样，this的值也变成了对象x。这种语法叫做“public class fields syntax”。当func是回调函数的时候，我们首选这种写法。注意，这里必须使用箭头表达式，不可以使用匿名函数。
	
### 类静态成员函数
当我们以面向对象语言的语法来类比时，我们会认为我们会得到undefined。因为，在面向对象语言中，静态函数没有this指针。
```javascript
class X {
  static func() {
    console.log(this);
  }
}
X.func();
```
但是，打印的结果是[Function: X]。它代表了class X的构造函数。我们可以通过object.constructor来获取该对象的构造函数。因此，上面的程序与下面这个程序等价。
```javascript
class X {
  func() {
    console.log(this.constructor)
  }
}
const x = new X();
x.func();
```
在类的静态函数中，this的值竟然不是undefined。这真是令人惊讶。这也充分说明了，Javascript并不是传统的面向对象语言。而它的this，也不是面向对象语言中的this指针。
	
## 浏览器端
在浏览器端，this的值在两种情况下与服务器端有所不同。这两种情况分别是全局域（和函数域）和DOM event handler。在其余情况下，this的值与服务器端的规则相同。

### 全局域（和函数域）
我们打开浏览器，按下F12打开调试器窗口，选择“Console”，然后在console中输入以下代码：

    console.log(this);

浏览器返回的是Window {parent: Window, opener: null, top: Window, length: 1, frames: Window, …}。这表明，在浏览器中，全局域里的this有一个值，它的值是Window。这个值属于浏览器DOM规范，在此不再赘述。

但是，在module里，它的值是undefined。
```javascript
<script type='module'>
  console.log(this); // undefined
</script>
```
### DOM Event handler
我们编写一个index.html，内容如下。然后用浏览器打开，点击按钮，看一下控制台的输出。
```HTML
<!DOCTYPE html>
<html>
  <head>
    <title>test this in event handler</title>
  </head>
  <body>
    <button onclick='console.log(this)'>click me</button>
  </body>
</html>
```
单击按钮，我们看到控制台的输出为`<button onclick="console.log(this)">click me</button>`。DOM规范规定，inline event handler中的全局this的值就是注册了该消息的元素，在本例中，就是body里面的那个button。注意：这里的this必须在全局域。如果在函数域中，则this的值是Window。

如果不是inline event handler，则this的值与event.currentTarget的值是相同的，他们都指向了处理该消息的元素。
	
## 总结
| | Node | Browser |
| --- | --- | --- |
| 全局域(和函数域) | undefined |	Window |
| 对象域|	object |	object |
| 箭头表达式	| 父域中的this |	父域中的this |
| 类成员函数域 |	object |	object |
| 类静态函数域 |	constructor |	constructor |
| DOM event handler |	- |	Event.currentTarget |
	
## 参考资料
[this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)

[上一课](lesson10.md) &#124; [目录](README.md) &#124; [下一课](lesson12.md)
