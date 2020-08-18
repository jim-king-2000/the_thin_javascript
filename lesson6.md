# 第六课 数据类型与数组
## 数据类型
Javascript有6种原生数据类型，分别是undefined，null，boolean，number，string和symbol；以及2种非原生数据类型，object和array。但是我们在定义变量的时候，并不需要指定类型。一个变量，可以被赋值为任意类型，被赋值多次的时候，类型还可以不一样。
```javascript
let x = undefined;
x = 10;
x = 'the thin javascript';
```
未赋值的变量（包括未赋值的函数参数和无返回值的函数运行结果），如果去获取它的值，会得到undefined。

## 数组
在javascript中，我们用方括号来定义数组。
```javascript
const books = []; // 空数组
const lists = [1, 2, 3];
```
方括号里面可以是变量，甚至可以调用函数。数组元素的类型也可以不一样。
```javascript
const x = true;
const getValue = () => {};
const lists = [1, x, getValue()]; // [1, true, undefined]
```
也可以定义数组的数组（数组元素的长度也不要求相同）以及函数的数组。
```javascript
const lists = [[0, 1, 2], [2, 3], [4, 5]];
const funcs = [x => x, x => 2 * x, x => x + 1];
```
有了数组之后，我们就可以使用下标来访问数组。但是，高手通常不太使用下标。一般都通过数组方法来操作数组。

## 数组方法
在javascript中，数组就是对象。因此，数组可以直接调用数组方法。最常用的数组方法就是遍历，我们使用Array.forEach进行数组的遍历。Array.forEach的参数是一个函数，这个函数有一个参数，将会被传入数组中的元素值。Array.forEach会对于每一个元素调用这个函数。
```javascript
// print array elements
[1, 2, 3, 4, 5].forEach(x => console.log(x));
```
数组还有两个很重要的方法就是Array.map和Array.reduce。Array.map的作用是将一个数组映射成另一个数组。它的参数是一个函数，Array.map会使用这个函数对每一个元素进行计算，然后将结果生成一个新的数组。
```javascript
[1, 2, 3, 4, 5].map(x => 2 * x); // [2, 4, 6, 8, 10]
```
Array.reduce是将数组中所有的元素映射成一个值。它的参数也是一个函数，这个函数有两个参数，第一个参数是一个累计值，第二个参数是当前值。该函数返回的结果将成为下一次调用的累计值。因此，我们可以使用Array.reduce来做数组的求和。
```javascript
[1, 2, 3, 4, 5].reduce((accumulator, current) => accumulator + current); // 15
```
累计值的初始值如果不指定，则为0。如果需要指定的话，可以在Array.reduce的第二个参数中指定。
```javascript
[1, 2, 3, 4, 5].reduce((accumulator, current) => accumulator + current, 10); // 25
```
如果需要求数组中最大元素的值，我们可以这样写：
```javascript
[1, 2, 3, 4, 5].reduce((max, current) => max < current ? current : max); // 5
```
数组的其它方法可以参考：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array

