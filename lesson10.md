# 第十课 高级语法糖
1. ## Rest参数
    我们经常会遇到函数需要处理变参的情况。在C语言中，printf就是变参函数的典型。在javascript语言中，我们使用rest参数来处理变参。
    ```javascript
    function func(...param) {
      console.log(param);
    }
    
    func(); // []
    func(1); // [1]
    func(1, 2); // [1, 2]
    func(1, 2, 3); // [1, 2, 3]
    ```
    我们用三个点的前缀来表示rest参数。Rest参数会把所有的实参都变成一个数组的元素，然后把这个数组初始化给这个参数。

1. ## Spread操作
    Spread操作与rest正好相反。Rest是把一堆参数变成一个数组。Spread是把一个数组变成一堆参数。
    ```javascript
    Math.max(...[1, 2, 3]);
    ```
    等价于
    ```javascript
    Math.max(1, 2, 3);
    ```
    在上一章，我们讲到了bind。但目前的这个bind只能绑定单个参数。
    ```javascript
    const bind = (func, y) => x => func(x, y);
    const max3 = bind(Math.max, 2, 3); // 参数3就被忽略了
    console.log(max3(0)); // 2。但其实3才是最大值
    ```
    使用spread/rest，就能完美的解决这个问题。
    ```javascript
    const bind = (func, ...y) => (...x) => func(...x, ...y);
    ```
    这样，bind就能绑定任意多个参数了。
    
    同样，对象也可以spread。
    ```javascript
    const obj1 = {
      b: 2,
      c: 3
    };
    const obj2 = {
      a: 1,
      ...obj1
    }; // { a: 1, b: 2, c: 3 }
    ```

1. ## Destructuring
    有时候我们需要从数组中提取数据。比如：
    ```javascript
    const list = [1, 2];
    const list0 = list[0];
    const list1 = list[1];
    ```
    现在有更方便的destructuring语法来简化这样的操作。
    ```javascript
    const [list0, list1] = [1, 2]; // list0 = 1, list1 = 2
    ```
    对象也支持destructuring。
    ```javascript
    const obj = {
      x: 10,
      y: 20
    };
    const { x, y } = obj; // x = 10, y = 20
    ```
    注意，在对象destructuring中，变量名必须与对象的成员名一致。
    
    Destructuring可以和rest一起使用。
    ```javascript
    const [a, b, ...c] = [0, 1, 2, 3, 4, 5]; // a = 0, b = 1, c = [2, 3, 4, 5]
    ```

1. ## Nullish coalescing operator (??)
    假如我们需要在表格中显示一个数字，比如当前温度。当温度值不存在（为null或undefined）时，我们显示'-'。这时，我们可能会这样写：
    ```javascript
    const temp = 0;
    console.log(temp || '-');
    ```
    我们惊讶的发现，上述程序的运行结果是'-'。也就是说，合法的温度值，0度，显示不出来了。这时因为在javascript中，数字0被认为是falsy，因此`||`操作符会获取后一个表达式的值。
    
    如果我们希望能够显示0，我们可以使用nullish coalescing operator。它只有在值为null或者undefined的情况下，才会判断为false。
    ```javascript
    const temp = 0;
    console.log(temp ?? '-');
    ```
    这时，运行结果就是0。

1. ## Optional chaining operator
    我们经常会获取一连串的子对象。如下程序所示：
    ```javascript
    const x = {};
    console.log(x.a.b.c);
    ```
    这时，我们会得到一个报错，并且程序退出。
    ```
    TypeError: Cannot read property 'b' of undefined
    ```
    然而在实际工程中，我们更希望当子对象不存在的时候返回undefined。于是我们只能这样做。
    ```javascript
    const x = {};
    console.log(x.a && x.a.b && x.a.b.c);
    ```
    使用最新的语法糖，optional chaining operator，可以有效的缩短程序。
    ```javascript
    const x = {};
    console.log(x?.a?.b?.c);
    ```

[上一课](lesson9.md) &#124; [目录](README.md) &#124; [下一课](lesson11.md)
