# 第五课 函数与箭头表达式
## 函数变量
在javascript里，函数就是一个变量。因此，一个add函数可以这样写：
```javascript
// add.mjs
function add(x, y) {
  return x + y;
}
```
也可以这样写：
```javascript
// add.mjs
const add = function(x, y) {
  return x + y;
}
```
这就意味着，一个函数可以被当作参数传到另一个函数里，一个函数也可以作为另一个函数的返回值。

## 匿名函数
我们可以把一个函数传到另一个函数里，以便执行某些操作。
```javascript
// print.mjs
function print(x) {
  console.log(x);
}

[1, 2, 3].forEach(print);
```
但其实在这里，我们并不需要print这个符号。因此我们可以直接把函数定义写进参数里。
```javascript
// print.mjs
[1, 2, 3].forEach(function(x) { console.log(x); });
```
参数里面的函数定义是没有名字的，因此叫做匿名函数。

## 箭头表达式
匿名函数写起来太长，我们有更简单的写法。
```javascript
// print.mjs
[1, 2, 3].forEach((x) => { console.log(x); });
```
箭头表达式就是一个匿名函数的定义，只不过使用了=>替代了关键字function。这样使得代码更加精简。在C++/Java/C#等语言中，类似的语法叫做lambda表达式。

箭头表达式还有一些更加使得代码精简的小技巧：
1. 如果箭头表达式只有一个参数，那么圆括号可以省略。
2. 如果函数体只有一行，那么花括号可以省略，且函数的返回值就是该表达式的值，因此return也可以省略。

因此上面的箭头表达式可以写成这样：
```javascript
// print.mjs
[1, 2, 3].forEach(x => console.log(x));
```
以下的匿名函数和箭头表达式等价：
```javascript
function (x) {
  return x + 1;
}

x => x + 1;
```
定义一个命名函数也可以写成这样：
```javascript
const calc = x => x + 1;
```
