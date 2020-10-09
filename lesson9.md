# 第九课 函数进阶二
## 对象中的函数
对象中可以定义函数。无论我们是在对象中直接定义函数或者我们定义一个类的方法，然后用该类实例化一个对象，在本质上都是一样的。因此，以下两种写法是等价的。

写法一，直接在对象中定义函数。
```javascript
const obj = {
  func: () => {
    console.log('Object method.');
  }
};
obj.func();
```
写法二，定义类方法。
```javascript
class X {
  func() {
    console.log('Object method.');
  }
}
const obj = new X();
obj.func();
```
但是当我们在对象中的函数里访问this的时候，结果可能会让C语系的程序员们大吃一惊。
```javascript
class X {
  constructor() {
    this.x = 'abc';
  }
  func() {
    console.log(this.x);
  }
}
const obj = new X();
obj.func();
const callback = obj.func;
callback();
```
结果如下：
```javascript
abc
TypeError: Cannot read property 'x' of undefined
    at func (file:///D:/test-projects/test/app.mjs:7:22)
    at file:///D:/test-projects/test/app.mjs:13:1
    at ModuleJob.run (internal/modules/esm/module_job.js:95:12)
```
我们发现，当我们调用obj.func()的时候，this.x的值可以被正常打印出来。但是当我们把obj.func的值赋给变量callback，然后再通过callback来调用成员函数的时候，this就变成了undefined，于是访问this.x的时候就报错了。

为什么会这样呢？这是因为，在Javascript中，obj.func()是经过特殊处理的，当使用这种语法调用成员函数的时候，this会被自动绑定到obj对象上。但是，当我们使用obj.func去取值的时候，返回的就是一个普通函数。在这个普通函数里，this是没有被绑定的。这是Javascript与其它面向对象语言最大的不同。这样的语法特殊性，经常会导致在回调函数里，我们一不小心传入了this.func，然后就出错了。
```javascript
export default () => <Button onClick={obj.func} />; 
// 回调函数传入了obj.func，这是一个普通函数，在该函数中的this是undefined。
```
## Function.bind
如果我们需要解决上面的问题，我们当然可以传入一个箭头表达式，然后在箭头表达式中调用obj.func()。

解决方案一，使用箭头表达式。
```javascript
export default () => <Button onClick={() => obj.func()} />; 
```
只要不是用obj.func来取值，则this自然就绑定到obj上。

解决方案二，就是使用Function.bind方法。
```javascript
export default () => <Button onClick={obj.func.bind(obj)} />;
```
Function.bind方法的第一个参数，就是this的值。后续的参数，就是被绑定的函数参数的实参。Function.bind的返回值就是绑定了所有这些参数的函数。
```javascript
const f = obj.func.bind(obj2, a, b, c);
f(d, e, f); // 等价于obj2.func(a, b, c, d, e, f);
```
我们看到，虽然我们用的是obj.func.bind，但由于第一个参数（也就是this）被绑定成了obj2，那么调用f其实等价于obj2.func(…);

Function.bind是使用得最为广泛的功能之一。在我们编写前端的时候，bind几乎必定出现。在React代码中，最常见的写法就是在构造函数中为回调绑定好this，然后再传给控件。
```javascript
class Sidebar extends Component {
  constructor(props) {
    super(porps);
    this.onClick = this.onClick.bind(this);
  }

  onClick() {
    …
  }

  render() {
    return <Button onClick={this.onClick} />;
  }
}
```
还有一个常用的技巧就是编写一个带状态的回调函数。
```javascript
class Sidebar extends Component {
  constructor(props) {
    super(porps);
  }

  onSelect(persion, e) {
    person.enabled = e.target.checked;
  }

  render() {
    return (
      <div>
        {this.props.people.map(person => <Checkbox
          label={person.name}
          isChecked={person.enabled}
          onChange={this.onSelect.bind(this, person)} />)}
      </div>
    );
  }
}
```
这个组件有一个people属性，其内容是一个数组。数组里的每一个元素都是person。在这里我们使用了map函数，将people数组映射成一个复选框数组。每一个复选框的label就是person.name。当我们去点击其中的一个复选框的时候，onChange回调函数会被触发，参数为一个事件对象，该事件对象里面包含了复选框的状态（选中或未选中）。控件会使用onChange(event)的语法来触发回调。我们为每一个复选框对象的onChange都绑定了this和person。因此，调用onChange(event)就相当于调用this.onSelect(persion, event)。这样以来，onChange回调就变成了带状态的函数。用户点哪个复选框，对之应的person对象的enabled成员就改变了。

解决方案三，可以使用Javascript的新语法“属性初始化器”。
```javascript
class Page extends Component {
  constructor(props) {
    super(props);
  }

  onClick = () => {
    this.setState({ ... });
  }

  render() {
    return <Button onClick={this.onClick} />;
  }
}
```

[上一课](lesson8.md) &#124; [目录](README.md) &#124; [下一课](lesson10.md)
