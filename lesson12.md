# 第十二课 Promise与异步函数
## 单线程与消息循环
迄今为止，所有的javascript引擎，无论运行在浏览器里还是服务器上，都是单线程的。学习过C或者Java等语言的人都知道，单线程最大的问题是无法并发。一个操作，无论有多久，我们都只能等待它结束之后才能进行下一个操作。这种同步阻塞式的编程模型严重阻碍了语言的运行效率。

但javascript是允许并发的。它的奥秘就在于，它使用的是基于消息的异步编程模型。在这个编程模型中，每当javascript执行一个耗时的操作时，该操作就被放入后台运行。Javascript引擎可以继续执行后面的语句。当这个耗时的操作结束时，消息队列会被写入一条消息。Javascript引擎就不断的从消息队列里面拿出消息，然后运行该消息对应的函数。这种模型也叫响应式编程模型。在这种模式下，单线程也可以并发。而且还没有多线程模型带来的竞争等难以发现和处理的问题。因此在近年来得到了广泛的使用。

在node.js中，如果仅仅使用单线程是无法榨干硬件的性能的。我们可以使用PM2来多开几个进程，那这样就可以充分享受多核CPU的性能了。

## 永不阻塞
由于目前几乎所有javascript运行环境都是单线程异步的，因此当javascript引擎在执行一个阻塞住（如死循环等）的消息相应函数的时候，整个应用都会阻塞。因此，在写javascript函数的时候，永不阻塞几乎成为一条铁律。

## Callback hell
基于消息的异步编程模型最大的特点就是，凡是耗时的操作，都会提供带回调函数的API。
```javascript
import fs from 'fs';
fs.readdir('./', (err, files) => console.log(files));
```
Node.js的绝大部分API都是这样的模式。它们的最后一个参数一定是一个回调函数。当API运行完毕，回调函数会被调用。回调函数的第一个参数一般都是err，如果没有出错，那么err就是undefined。回调函数的第二个参数一般就是API的运行结果，如果API出错，那么这个结果就是undefined。既然所有的操作都是带回调的，那么当我们要顺序执行的时候，问题就来了。
```javascript
// 获取所有文件名并且发出去
fs.readdir('./', (err, files) => {
  console.log(files);
  http_client.post('www.somewhere.com/upload', (err, res) => {
    console.log(res);
  });
});
```
我们只是希望顺序调用2个函数。但基于消息的异步编程模型却强迫我们进入了2层嵌套。当调用的函数一多，程序看起来就是这样的。
```javascript
method1(function(err, result) {
  if (err) {
    throw err;
  }
  method2(function(err, result) {
    if (err) {
      throw err;
    }
    method3(function(err, result) {
      if (err) {
        throw err;
      }
      method4(function(err, result) {
        if (err) {
          throw err;
        }
        method5(result);
      });
    });
  });
});
```

这就是所谓的"callback hell"。在早期编写javascript程序的时候，动不动就十几层深的回调嵌套着实让人望而生畏。不仅如此，回调函数在处理并发的时候也显得束手无策。幸好后来我们有了promise。

## Promise
Promise是一个对象，它代表未完成的操作。在C++语言里，它叫做`future`。在java里，它叫做`CompletableFuture`。而在C#里，它叫做`Task`。Node.js中有一个工具API，叫做`util.promisify`，可以用它来把带回调的API转换成返回`promise`的API。
```javascript
const readdir_async = util.promisify(fs.readdir);
```
系统API `fs.readdir`的函数声明是`fs.readdir(path, function(err, files) {})`。经过`util.promisify`处理之后，`readdir_async`的函数原型就变成了 `Promise { files } readdir_async(path)`。回调函数参数不见了，增加了`promise`类型的返回值。原生API回调函数的第一个参数`err`，变成了`promise`的异常。
我们可以用`Promise.then`为`promise`注册一个成功完成的回调函数。这个回调函数的参数就是`promise`的运行结果。
```javascript
const files_promise = readdir_async('./');
files_promise.then(files => console.log(files));
```
有趣的是，`Promise.then`的返回值也是一个`promise`。
```javascript
const readdir_async = util.promisify(fs.readdir);
const files_promise = readdir_async('./');
const post_promise = files_promise.then(files => http_client.post('www.somewhere.com', files));
post_promise.then(res => console.log(res));
```
因此，我们通常把`Promise.then`串起来使用。前一个`promise`的结果，会作为后一个回调函数的参数。
```javascript
const readdir_async = util.promisify(fs.readdir);
readdir_async('./')
  .then(files => http_client.post('www.somewhere.com', files))
  .then(res => console.log(res));
```
我们必须使用`promise.catch`来处理`promise`异常，这与通常我们习惯的异常传播方式相违背。因此在真实的工程中，我们很少使用`Promise.then`和`Promise.catch`。
```javascript
const readdir_async = util.promisify(fs.readdir);
readdir_async('./')
  .then(files => console.log(files))
  .catch(err => console.log(err));
```
`Promise.all`是实现并发的利器。有了`promise.all`，我们可以同时枚举两个文件夹。
```javascript
const readdir_async = util.promisify(fs.readdir);
const enum_2_dirs_promise = Promise.all([
  readdir_async('./'),
  readdir_async('../')
]);
enum_2_dirs_promise.then(([current_dir_files, parent_dir_files]) =>
  console.log(current_dir_files, parent_dir_files));
```
`Promise.all`的参数是一个数组，数组里面的每一个元素都是`promise`。`Promise.all`负责把这个数组里的所有`promise`合并成一个大的`promise`。当数组中的所有`promise`都运行完毕，这个合成之后的大`promise`的`.then`函数会被调用。所有`promise`的运行结果会组成一个数组，放入`.then`的回调函数的参数中。
`Promise.all`可以与`Array.map`结合在一起，达到很好的效果。
```javascript
// 同时枚举dir_list中的文件夹
const readdir_async = util.promisify(fs.readdir);
const dir_list = ['./', '../', '/'];
Promise.all(dir_list.map(dir => readdir_async(dir))).then(results => console.log(results));
```
在上面的代码中，dir_list.map把路径字符串数组映射成为promise数组，然后传给了Promise.all。

## 异步函数
在真实工程中，我们很少使用`Promise.then`和`Promise.catch`。因为它跟我们传统的异常传播模型相违背。真正大规模使用的是异步函数。
我们使用`async`关键字来定义一个异步函数。
```javascript
async function do_some_async() {
  ...
}
```
异步函数的返回值一定是`promise`。如果我们在异步函数里没有返回`promise`，那么返回值会被自动转换成`promise`。
```javascript
async function do_some_async() {
  return 2;
}
console.log(do_some_async()); // Promise { 2 }
```
在async函数里面，我们可以用`await`关键字来“等待”`promise`结束，或者当`promise`出错的时候，`await`会把`promise`异常`throw`出来。注意，`await`关键字只能出现在`async`函数内。
```javascript
const readdir_async = util.promisify(fs.readdir);
async function do_some_async() {
  const file_list = await readdir_async('./');
  console.log(file_list);
}
do_some_async();
```
注意，这里的`await`并没有真的去等待函数结束。其实，`await`关键字就是`Promise.then`的语法糖。但是这样一来，我们就可以轻易的传播异常了。
```javascript
const readdir_async = util.promisify(fs.readdir);
async function do_some_async() {
  const file_list = await readdir_async('./');
  console.log(file_list);
}

async function caller() {
  try {
    await do_some_async();
  } catch (e) {
    console.log(e);
  }
};
caller();
```
当`readdir_async`抛出异常，我们可以在函数调用端捕获异常。由于使用了异步函数之后，异步编程变得跟同步编程一样简单。而且只要小心避免共享变量，就没有多线程的竞争问题。因此异步函数语法一出现，就立刻得到了广泛的使用。

我们可以把上一小节的例子用异步函数重写一下。
```javascript
const readdir_async = util.promisify(fs.readdir);
async function do_some_async() {
  const dir_list = ['./', '../', '/'];
  const file_list = await Promise.all(dir_list.map(dir => readdir_async(dir)));
  console.log(file_list);
}

async function caller() {
  try {
    await do_some_async();
  } catch (e) {
    console.log(e);
  }
};
caller();
```
异步函数有以下两个注意事项。首先，`await`关键字只能在`async`函数内部使用。如果在顶层使用`await`，会出现错误。
```javascript
await readdir_async('./'); // Oops!
```
注意：在本书编写的过程中，node.js (v13.11.0) 的顶层`await`还是一个实验性的功能。需要用`--harmory-top-level-await`来打开。

我们可以定义一个匿名的异步函数，然后立刻执行它。
```javascript
(async () => await readdir_async('./'))();
```
其次，如果异步函数的返回值本身就是`promise`，则可以不用`await`。
```javascript
async function func() {
  ...;
  return await readdir_async('./'); // 最后一行不需要await
}
```
关键字`return`后面的`await`是不需要的。因为异步函数本身返回`promise`即可。不需要等待`promise`完成之后再返回。
```javascript
async function func() {
  ...;
  return readdir_async('./');
}
```
刚才定义的匿名异步函数，也不需要`await`。
```javascript
(async () => readdir_async('./'))();
```
在真实的项目中，我们基本上不用带回调函数的原始API，也不用`Promise.then`和`Promise.catch`。而是用`util.promisify`函数把带回调的API转换成返回`promise`的API，或者直接使用返回`promise`的新版API，然后就可以开始`async/await`了。

[上一课](lesson11.md) &#124; [目录](README.md) &#124; [下一课](lesson13.md)
