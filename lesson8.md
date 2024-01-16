# 第八课 函数进阶一
1. ## 闭包(closure)
    与Java和C#等语言类似，函数可以访问函数父域的变量。这种语法叫做闭包(closure)。
    ```javascript
    const a = 'The thin javascript';
    function func() {
      console.log(a); // 访问全局变量a
    }
    func(); // 'The thin javascript'
    ```
    父域的父域中的变量也能够访问。
    ```javascript
    const a = 'The thin javascript';
    function f1() {
      const b = 'The local variable';
      function f2() {
        console.log(a); // 访问全局变量a
        console.log(b); // 访问f1的局部变量b
      }
      f2();
    }
    f1();
    ```

1. ## 利用闭包实现私有成员
    在面向对象的语言里，我们经常这样写。
    ```java
    // Java
    class A {
      A(int y) {
        x = y;
      }
    
      print() {
        System.out.println(x);
      }
    
      private int x;
    };
    ```
    在javascript的世界里，我们用闭包来模拟私有成员。
    ```javascript
    // javascript
    class A {
      constructor(y) {
        const x = y;
        this.print = () => console.log(x); // 局部变量x是私有成员
      }
    };
    ```
    在构造函数里定义的局部变量x，就是私有成员函数。它能够在构造函数退出之后仍然存在。这个局部变量x，能够被定义在构造函数里的箭头表达式访问，但在类外是不可能被访问到的，因此它很类似面向对象语言里的私有成员变量。由于构造函数的参数y也属于局部变量定义，因此，实际上我们连x都不需要。
    ```javascript
    // javascript
    class A {
      constructor(y) {
        this.print = () => console.log(y); // 局部变量y就是私有成员。
      }
    };
    ```
    这样写有一个缺点，就是无法使用函数定义的语法来定义需要访问私有成员的成员函数。因此有一种说法是把这种需要访问私有变量然后外部能访问的函数叫做特权函数(privileged function)。而不需要访问私有变量，且外部也能访问的函数叫做公有函数(public function)。那么私有函数(private function)如何实现呢？当然跟局部变量类似，就是定义在构造函数里的普通函数。公有函数只能调用特权函数。特权函数能访问私有函数和私有变量。私有函数也能访问私有变量。
    ```javascript
    // javascript
    class A {
      constructor(y) {
        function calc() {
          // 私有成员函数，可以访问私有成员变量。
          return y + 1;
        }
        this.print = () => console.log(y + calc(y)); // 特权成员函数，可以访问私有成员变量和私有成员函数
      }
    
      log() {
        // 公有成员函数，只能调用特权函数。
        this.print();
        console.log();
      }
    };
    ```

1. ## 返回函数的函数
    Javascript的世界崇尚的是函数式编程。因此能够不用类的地方，是尽量不会用类的。函数才是语言的第一公民。我们使用返回函数的函数来模拟类。我们可以用函数式编程把上面的类重写一下。
    ```javascript
    function A(y) {
      // 函数A返回了一个匿名函数
      return function() {
        console.log(y); // 匿名函数使用闭包来访问“私有成员变量”y
      }
    }
    
    const print = A(3); // print是一个“成员函数”，A是一个“构造函数”
    print(); // 3
    ```
    如果用箭头表达式的话，可以这样写。
    ```javascript
    const A = y => { return () => console.log(y); };
    ```
    由于return只有一行，而单行表达式的值默认就是return的值，因此return和花括号可以一并都省了。
    ```javascript
    const A = y => () => console.log(y);
    ```
    使用箭头表达式，可以把代码变得非常短小。但同样也增加了理解的难度。但在函数式编程里，这样的连着两个箭头的箭头表达式满天飞。
    
    我们在其它语言里经常容易看见bind函数。bind函数的作用是，给两个参数的函数的第二个函数绑上一个固定的参数，这样就生成出一个单参的函数。比如：
    ```javascript
    const add3 = bind(add, 3); // add有两个参数，bind生成的add3把3绑定在了第二个参数上
    add3(2); // 2 + 3
    ```
    这样的bind函数在javascript中的实现就比较简洁。
    ```javascript
    const bind = (func, x) => y => func(x, y);
    ```
    等我们学习了后面的章节，我们就知道绑定任意多个参数的bind该如何实现。

[上一课](lesson7.md) &#124; [目录](README.md) &#124; [下一课](lesson9.md)
