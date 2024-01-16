# 第一课 入门
1. ## 字面常量
	与C语系的语法类似，Javascript支持以下几种类型的字面常量。
	
	数字字面量：
	
		1, 1e6, 0xfe, 0b11111111, 0o377, 0.1
	布尔字面量：
	
		true, false
	字符串：
	
		'javascript', "programming"
	其它：
	
		null, undefined

1. ## 变量
	Javascript语法里面有两个定义变量的关键字。一个是let，用来定义变量；另一个是const，用来定义常量。一般而言，推荐全部使用const。因为javascript的编程范式之一就是尽量不要修改变量。
	```javascript
	let a = 3;
	const str0 = 'The thin javascript';
	```
	如果变量没有初始化，那么它的值将会是undefined。
	在老的javascript语法中，使用的是var关键字来定义变量。但是由于var设计失误太多，因此现在基本上已经被弃用。

1. ## 字符串
	单引号或者双引号引起来的内容就是字符串。转义字符跟C语系完全一致。在ES6中，新增了一种叫string template的语法。String template以反引号定界，以${}把javascript表达式（注意：不可以是语句）引入其中，其结果就是表达式的值。
	```javascript
	const a = 3;
	const b = 2;
	const str1 = `The result of ${a} + ${b} is ${a + b}.`; // The result of 3 + 2 is 5.
	```
	String template也支持多行字符串。
	```javascript
	const str = `
	  <html>
	    <body>
	      The thin javascript.
	    </body>
	  </html>
	`;
	```

1. ## 操作符
	+，-，*，/，%，()，&，|，!，>，<，>=，<=，?:与C语系语言完全相同。幂操作符使用**。&&，||，==，!=与C语系语言略有区别，详情请见第四课。

1. ## 语句和表达式
	if…else…，switch…case…, for…，while…等跟C语系语言完全相同。语句末尾推荐以分号结尾，虽然不加分号也可以。表达式语法也与C语系语言基本一致。

1. ## 函数
	关键字function用来定义函数。关键字return用来返回。除了不用写返回类型，参数也不用定义类型之外，其它方面跟C语系语言完全一致。
	```javascript
	function add(x, y) {
	  return x + y;
	}
	```
	调用函数的时候，实参可以比形参少。少掉的实参将会自动用undefined来填充。实参也可以比形参多，多出来的实参就会被忽略。
	```javascript
	func(1); // 等价于func(1, undefined);
	func(); //等价于func(undefined, undefined)
	func(1, 2, 3); // 等价于func(1, 2)
	```
	函数可以有默认参数。
	```javascript
	function func(x, y = 3) {
	  return x + y;
	}
	func(1, 2); // 1 + 2
	func(1); // 1 + 3
	```
	函数可以有一个返回值，使用return来返回。如果return后面没有值，或者根本就没有return，那么该函数返回undefined。

1. ## 打印
	`console.log(str)`用来执行打印功能。
	```javascript
	const a = 3;
	const b = 2;
	console.log(`The result of ${a} + ${b} is ${a + b}.`); // The result of 3 + 2 is 5.
	```

1. ## 注释
	注释与C语系语言完全一样。单行注释使用`//`，多行注释使用`/**/`。

1. ## 异常
	Javascript使用throw来抛异常，使用try … catch … finally …来处理异常。异常的抛出，传播和处理与C++/Java/C#等语言非常相似。

1. ## 实践
	1. 打开<https://www.nodejs.org>，下载node.js安装包。
	1. 新建子目录，创建main.js文件，写入代码。
	1. 进入子目录，运行"node main.js"执行程序。

[上一课](lesson0.md) &#124; [目录](README.md) &#124; [下一课](lesson2.md)
