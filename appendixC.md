# 附录C 不推荐使用的语法
## for … in …
这个语法是为了方便的做迭代，但用它来迭代数组会出现意想不到的结果。不过我们一直选择使用Array.forEach()或者Array.map()。因此不推荐使用for … in …，甚至，我们连for都不用。

## 原型和原型链
用class。或者更进一步，干脆全部函数式编程。尽量少用class，尽量不要用继承，更不要继承标准对象（Array等）。这样一来，原型和原型链的语法也就不用再使用了。

## Symbol
据说symbol是发明出来解决私有成员的问题的。但我们可以通过闭包来模拟私有成员。且新语法直接支持私有成员。因此不推荐使用symbol。

## Generator
用async/await。

## Arguments
用rest参数。

## 增强类型
Javascript允许你给已有类型增加方法。甚至连内置类型都可以增加方法。不要使用这个功能。不要给已有类型增加方法。更不要给内置类型增加方法。这会让接手你程序的人感到困惑。

[上一课](appendixB.md) &#124; [目录](README.md) &#124; [下一课](appendixD.md)
