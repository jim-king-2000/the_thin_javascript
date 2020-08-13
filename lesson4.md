# 第四课 与众不同的语法
Javascript有一些与其它语言不一样的地方。忽视这些地方有可能会成为初学者的灾难。

## Falsy values
Javascript有以下falsy values，它们在if语句里面都会被认为是false。

    0, NaN, ''和""(空字符串), false, null, undefined

由于符号!的作用是取反，并且它的返回值一定是true/false。因此，如果我们想把falsy value转换为true/false的时候，我们可以使用两个取反符号。
```javascript
const empty = '';
console.log(!!empty); // false
```
## &&和||
这两个表达式与其它语言一样，都有短路功能。但与其它语言不一样的是，它们的返回值不是boolean。操作符&&的逻辑是，如果第一个表达式的值是falsy value，则返回这个value，否则返回第二个表达式的值。
```javascript
0 && 1 // 0
2 && 1 // 1
'' && 'abc' // ''，空字符串
'123' && 'abc' // 'abc'
```
操作符&&经常被用来做显示/隐藏的开关。由于在很多场合(例如：React)只支持表达式，而不能使用语句，因此这时&&成了if的完美替代品。
```javascript
const showName = true;
const name = 'John';
return <Text>{showName && name}</Text>;
```
在上面的例子中，我们可以用变量showName来控制是否显示name（React不显示falsy值）。由于在花括号中只能写表达式，因此if语句无法出现在其中。这时用&&是唯一的选择。

而操作符||的逻辑是，如果第一个表达式的值是falsy value，则返回第二个表达式的值，否则就返回第一个表达式的值。
```javascript
0 || 1 // 1
2 || 1 // 2
'' || 'abc' // 'abc'
'123' || 'abc' // '123'
```
因此，操作符||经常被用来设置默认参数。但后来javascript增加了默认参数语法，因此操作符用来做默认参数的做法已经过时了。
```javascript
function f(x) {
  const p = x || 3; // 把3设置为默认参数
  console.log(p);
}
f(1); // 1
f(); // 3
```
虽然||不再用来做默认参数，但可以用来简化对空值的判断。
```javascript
let title = getTitle();
if（title === '') title = 'the thin javascript';
```
等价于
```javascript
const title = getTitle() || 'the thin javascript';
```
## ===和!==
由于在javascript中，比较操作符==是会做类型转换的，因此会导致以下神奇的结果。
```javascript
0 == '' // true
0 == '0' // true
null == undefined // true
false == '0' // true
```
所以永远不要使用==。同样中招的还有!=，因此也永远不要使用。想要做比较，请使用===和!==。如果不幸忘记了这一条，等待你的将会是冗长的调试时间。

## +
二元操作符+既是加法操作符，又是字符串连接符。只有两个操作数都是number的时候，它才做加法。其它情况下，该操作符会将两个操作符全部转换为string，然后做连接。使用该操作符的时候，千万注意类型，确保两边全部是number或者全部是string。

操作符+也能作为一元操作符。它表示数学中的正号。在javascript中，由于存在隐式转换，因此一元操作符+可以用来把字符串数字转换成真正的数字。
```javascript
const x = +"345";
console.log(x, typeof x); // 345 'number'
```
