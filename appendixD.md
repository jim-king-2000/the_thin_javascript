# 附录D 常用小技巧
## 类型转换
### 数字字符串转换成数字
+ 第一种方法，使用一元操作符'+'
```javascript
const x = +'53';
```
+ 第二种方法，使用Number()
```javascript
const x = Number('53');
```
### 数字字符串提取数字
在上面的例子中，数字字符串必须是纯数字。若数字字符串是包含其它字符，如'53px'，且我们要提取'px'前的数字，上面的方法就失效了。这时，我们必须使用`Number.parseInt()`或`Number.parseFloat()`。
```javascript
const x = Number.parseInt('53px');
const x = Number.parseFloat('53.4px');
```
### 其它类型转换成布尔
任何类型的变量都可分为truthy和falsy。因此我们可以直接在if语句或者&&表达式中使用任何类型的变量。但是当我们真的想把任何类型的变量转换成布尔类型的时候，我们可以对变量取两次非即可。
```javascript
const x = !!'53.4px';
```
我们也可以使用`Boolean()`。
```javascript
const x = Boolean('53.4px');
```
### 数字转换成字符串
+ 通常情况下，可以使用`toString()`。
```javascript
const x = (53).toString();
```
+ 当我们需要限制小数位个数的时候，我们可以使用`toFixed()`。
```javascript
const x = (53.333333333333333).toFixed(2); // 53.33
```
## 数组
### 数组浅拷贝
```javascript
const shallow_copy = [1, 2, 3].slice();
```
### 数组取最后一个元素的值
数组的`slice()`函数支持负数下标-n，代表从右向左数的第n个位置。我们可以使用`slice(-1)`来访问数组中的最后一个值。
```javascript
const [ x ] = [1, 2, 3].slice(-1); // 3
```
### 数组去重
我们可以根据原始数组创建一个`set`，再根据这个`set`创建一个数组。
```javascript
const x = Array.from(new Set([1, 1, 2, 2, 3, 3]));
```
### 从数组中过滤掉Falsy值
```javascript
const x = [null, undefined, [], {}, 1, 'a'].filter(Boolean);
```
### 数组方法借用
很多HTML对象是array-like对象。他们并不是数组。但偶尔他们也需要使用数组方法。这时候，我们可以使用数组方法借用的小技巧。这个技巧利用了`Function.call()`的this支持array-like对象的特性。
```javascript
function hash() {
  console.log([].join.call(arguments)); // 1,2
}
hash(1, 2);
```
## 对象
### 创建纯对象
一个空对象并不是空的，它包含了内置对象`Object`的方法。如果要创建一个完全空的对象，我们可以使用如下的方法：
```javascript
Object.create(null)
```
### 合并对象
+ 第一种方法，我们可以使用`Object.assign()`。
注意：由于`Object.assign()`的第一个参数会被改变，且作为返回值返回，因此我们通常赋予第一个参数一个新的空对象。这样可以避免修改已有的变量。
```javascript
const x = Object.assign({}, a, b, c);
```
+ 第二种方法，我们可以使用destructuring。
```javascript
const x = {
  ...a,
  ...b,
  ...c,
};
```
## 异步
### 异步函数返回另一个异步函数的值。
当我们编写一个异步函数的时候，它的返回值经常有可能是另一个异步函数的返回值。我们按照直觉，通常会这样写：
```javascript
async function a() {
  ...;
  return await b();
}
```
但是，异步函数的返回值其实本身就是一个promise。因此，我们可以直接将最后一个函数（上边的b函数）返回的promise返回即可。
```javascript
async function a() {
  ...;
  return b();
}
```
### 捕获异步函数的异常。
我们通常用try … catch …来捕获异常。当我们捕获异步函数的异常时，我们通常会写成这样。
```javascript
async function a() {
  try {
    return b();
  } catch (e) {
    ...;
  }
}
```
但是，上面的try … catch …是不会捕获到任何异常的。因此异步函数的异常被封装在promise里，然后被返回到上一层函数了。正确的做法是要加上`await`，`await`会解析出promise里面的异常，然后抛出来。
```javascript
async function a() {
  try {
    return await b();
  } catch (e) {
    ...;
  }
}
```
### 等待多个异步函数全部结束。
我们使用`Promise.all()`来把多个promise聚合成一个大的promise。只有当参数中所有的promise全部resolve，大promise才resolve。大promise resolve之后得到的值，就是里面小promise resolve的值形成的数组。
```javascript
const [r0, r1, r2, r3] = await Promise.all([
  async_func0(),
  async_func1(),
  async_func2(),
  async_func3(),
]);
```