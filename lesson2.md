## 第二课 模块
## 导入与导出
当我们定义一个函数的时候，我们常常会把函数写在另外的文件里。比如这样：
```javascript
// add.js
function sum(a, b) {
  return a + b;
}
// main.js
sum(1, 2);
```
直接这样写是不能工作的。如果要实现函数的定义方与调用方不在一个文件里，就需要使用import/export关键字。原生支持import/export，需要文件名扩展为mjs。通常，我们使用export来导出变量。
```javascript
//add.mjs
function sum(a, b) {
  return a + b;
}
export { sum };
```
或者把导出与定义写在一起。
```javascript
//add.mjs
export function sum(a, b) {
  return a + b;
}
```
然后，就可以在别的文件里导入然后使用了。导入的语法就类似于C语系的#include。
```javascript
//main.mjs
import { sum } from './add.mjs';
sum(1, 2);
```
注意，我们的源文件扩展名全部为mjs，且export花括号里的符号名与import花括号里的符号名称必须完全一样。

如果我们的库文件的扩展名是标准的，比如*.js等，那么from后面的扩展名可以省略。但扩展名mjs不可以省略。

每一个库文件可以有一个默认导出。
```javascript
export default sum;
```
导入默认导出，则不需要花括号。
```javascript
import sum from './add.mjs';
```
## 第三方库
第三方库就是第三方已定义的库，一般托管在github上，使用npm命令下载到本地。在这里，我们介绍一下运行标准命令行的库shelljs。

shelljs的介绍可参考：<https://github.com/shelljs/shelljs>

我们使用下面的命令来下载依赖包。

`npm i shelljs`

然后我们就可以在代码中import并调用shelljs的功能。
```javascript
//main.mjs
import shelljs from 'shelljs';
shelljs.exec('ls -l *.mjs');
```
注意：
1. 第三方库的名字前面不需要加路径。
2. 这里的import后面的符号也不需要加花括号。

## Javascript的模块标准
Javascript语言历史悠久。在最早期，这门语言是没有模块的。代码只能全部写在一起。到了后来渐渐开始有了对模块的需求。于是在漫长的发展过程中，出现了各种各样的模块标准。比较常用的有：CommonJS, AMD, CMD和ES Module等。我们上面介绍的就是最新的ES Module。不过上述语法仅限于在node.js中使用。浏览器的ES Module语法与上述语法略有不同。不过好在我们现在都是用前端框架来生成页面，很少直接在浏览器中使用ES Module了。
