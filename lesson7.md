# 第七课 对象与JSON
1. ## 对象
    在javascript中，对象就是花括号中的一组数据。数据条目之间用逗号隔开，最后一个条目的逗号可有可无。每一个数据条目的格式是key: value。
    ```javascript
    const empty = {}; // 空对象
    const obj = {
      height: 20,
      weight: 5,
      elements: [
        1, 3, 5
      ],
      description: 'this is an object'
    };
    ```
    这里的key，虽然没有用引号引起来，但它就是一个字符串。这个字符串必须满足标识符的定义，不能有特殊字符。如果key含有特殊字符，则必须用引号。
    ```javascript
    const obj = {
      'Content-Type': 'application/json'
    }
    ```
    对象也可以嵌套。
    ```javascript
    const obj = {
      subobj: {
        length: 10,
        description: 'this is a subobject'
      }
    };
    ```
    对象成员还可以是函数。
    ```javascript
    const obj = {
      func: function() {
      }
    };
    ```
    如果属性的名称在变量里，那么我们可以使用中括号来定义属性。
    ```javascript
    const property = 'weight';
    const obj = {
      [property]: 5
    }; // { weight: 5 }
    ```
    定义了对象之后，我们就可以用dot操作符来访问对象。所有的字段都是外部可访问的。在javascript中，没有私有字段的概念。后面我们会看到，我们将使用closure来模拟所谓的私有字段。
    ```javascript
    obj.height; // 20
    obj.elements; // [1, 3, 5]
    obj.subobj.length; // 10
    ```
    如果给一个未定义的字段赋值，那么对象就会新增这个字段（和值）。
    ```javascript
    const empty = {};
    empty.x = 10; // empty = { x: 10 }
    ```
    如果对象的属性在变量里，或者key含有特殊字符，那我们可以用中括号来访问。
    ```javascript
    let property = 'weight';
    obj[property]; // 5
    obj['Content-Type']; // 'application/json'
    ```
    我们一般用这种方法定义纯数据对象。至于内部有函数的对象，我们一般使用类。

1. ## 对象语法的扩展
    我们常常需要把同名变量赋值给同名成员。在老的javascript中，我们只能这样写。
    ```javascript
    const name = 'The thin javascript';
    const type = 'book';
    const obj = {
      name: name,
      type: type
    }; // { name: 'The thin javascript', type: 'book' }
    ```
    在新的javascript中，可以简化为：
    ```javascript
    const obj = {
      name,
      type
    }; // { name: 'The thin javascript', type: 'book' }
    ```

1. ## 类
    Javascript语言本身没有类的概念，它只有对象的概念。因此，在javascript中，类就是创建对象的对象。关键字class用来定义类。关键字constructor用来定义类的构造函数。
    ```javascript
    class Color {
      constructor(r, g, b) {
        this.red = r;
        this.green = g;
        this.blue = b;
      }
    };
    let somecolor = new Color(1, 3, 5);
    somecolor.red = 10;
    somecolor.green = 20;
    somecolor.blue = 30;
    ```
    类内部的字段使用this关键字来使用。当某个字段还不存在的时候，第一次为其赋值，就会创建这个字段。在上面的例子中，class Color创建了red，green和blue三个字段，同样，这样定义的字段全部都是外部可见的。

1. ## 方法
    除了构造函数，我们还可以为class添加更多的方法以及静态方法。在调用的时候，静态方法使用类名来调用，普通方法使用对象名来调用。
    ```javascript
    class Color {
      constructor(r, g, b) {
        this.red = r;
        this.green = g;
        this.blue = b;
      }
    
      print() {
        console.log(`red=${this.red}, green=${this.green}, blue=${this.blue}`);
      }
    
      static desc() {
        console.log('This is class Color.');
      }
    };
    
    let somecolor = new Color(1, 3, 5);
    somecolor.print(); // 调用方法
    Color.desc(); // 调用静态方法
    ```
    我们会发现，定义类成员函数，不需要使用function关键字。同样的语法也可以在对象中使用。比如，下面的程序，在一个对象中定义了一个成员函数。
    ```javascript
    const x = {
      func() {
        console.log('abc')
      }
    }
    ```
    上述写法与下面的写法在语义上完全相同。
    ```javascript
    const obj = {
      func: function() {
      }
    };
    ```
    由于方法也是对象，因此我们还可以这样定义方法。
    ```javascript
    class Color {
      constructor(r, g, b) {
        this.red = r;
        this.green = g;
        this.blue = b;
        this.print = () => console.log(`red=${this.red}, green=${this.green}, blue=${this.blue}`);
      }
    };
    ```

1. ## 属性方法
  使用get/set可以添加属性方法。这一般用的不太多。在实际项目中，全部使用方法就可以了。
  ```javascript
  class Color {
    constructor(r, g, b) {
      this.red = r;
      this.green = g;
      this.blue = b;
  
      this.print = () => console.log(`red=${this.red}, green=${this.green}, blue=${this.blue}`);
    }
  
    get Red() {
      return this.red;
    }
  
    set Red(value) {
      this.red = value;
    }
  };
  
  let somecolor = new Color(1, 3, 5);
  somecolor.Red = 20;
  ```

1. ## Public class fields
    Javascript的新语法public class fields可以简化类的成员函数和成员变量的定义。实例如下：
    ```javascript
    class A {
      x = 0; // public instance member
      func = () => console.log('a.func()'); // public instance method
      static s_func = () => console.log('A.func()') // public static method
    }
    ```
    我们看到，使用该语法可以直接做成员的初始化，省去了编写繁琐的构造函数的过程。

1. ## Private class fields
    Javascript原本不支持私有成员。所有的成员全部都是公有的。虽然一直有把下划线开头的成员来当作私有成员的惯例，但这仅仅是一个惯例。在语法级并不支持，也就是说，以下划线开头的成员，在对象/类的外部仍然是可访问的。
    新语法private class fields在语法级支持私有成员。所有的私有成员都以'#'开头。在对象/类外部使用'#'开头的变量，会触发错误。
    ```javascript
    class A {
      #x = 0; // private instance member
      #func = () => console.log('a.func()'); // private instance method
      static #s_func = () => console.log('A.func()') // private static method
    }
    ```

1. ## 继承
    关键字extends用来实现继承。关键字super用来实现调用父类的方法。
    ```javascript
    class Color4 extends Color {
      constructor(r, g, b, a) {
        super(r, g, b); // 调用父类构造函数
        this.alpha = a;
      }
    
      print() {
        super.print(); // 调用父类方法
        console.log(`alpha=${this.alpha}`);
      }
    };
    ```
    在现实世界中，javascript的编程风格为函数式编程。因此面向对象的各种东西相对使用得不多。所以，在实际的javascript项目中，继承用得并不多。

1. ## 对象与引用
    与Java或者C#相同，javascript定义出来的对象是唯一的。但多个变量可以指向这个对象，因此javascript的对象变量就类似于引用（或者指针，只是用的时候不需要dereference）。
    ```javascript
    const a = { x: 10, y: 20 };
    const b = { x: 10, y: 20 };
    const c = a;
    console.log(a === b); // false
    console.log(a === c); // true
    a.y = 100;
    console.log(c.y); // 100
    ```
    我们能看到，虽然a和b对象里面的内容都相同，但它们是两个不同的对象。而a和c都指向了同一个对象，因此修改了a.y，c.y也受到了影响。

1. ## JSON
    在互联网的世界里，API的数据格式基本上已经被JSON统治了。JSON简单，高效，学习成本低廉。相比之下，XML就复杂很多，因此基本上也没人用XML了。不知道未来会不会连web也放弃HTML（HTML的语法借鉴了XML），投向JSON的怀抱。
    简单点说，JSON就是一个定义对象的字符串。
    ```json
    '{ "length: 30", "speed": 20 }'
    '{
      "coordinate": {
        "latitude": 132.876,
        "longitude": 32.764
      },
      "color": {
        "r": 10,
        "g": 15,
        "b":150,
        "a": 50
      },
      "cities": [
        "Shanghai", "Nanjing", "L.A."
      ]
    }'
    ```
    发现了吧？是不是跟javascript对象的定义很像？没错，JSON就是"JavaScript Object Notation"的缩写，它就是使用javascript定义对象的格式来定义字符串数据的。在程序中，我们使用两个简单的API，JSON.stringify和JSON.parse就可以方便的在javascript对象和JSON字符串之间做转换。
    ```javascript
    const v = {
      length: 30,
      speed: 20
    };
    JSON.stringify(v); // '{ "length": 30, "speed": 20 }'
    const s = '{ "x": 10, "y": 20 }';
    JSON.parse(s); // { x: 10, y: 30 }
    ```

[上一课](lesson6.md) &#124; [目录](README.md) &#124; [下一课](lesson8.md)
