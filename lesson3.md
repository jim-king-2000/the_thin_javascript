# 第三课 package.json
一般一个工程有不少依赖包。如果每次运行之前都要一个一个npm install的话，那实在太麻烦了。因此，node/npm使用了一个配置文件来管理这些工程信息。注意，这只能在node.js中使用。在网页端，包使用`<script src='…'>`元素来导入。

## 创建package.json
使用`npm init`命令就可以创建一个空白的package.json。一般所有选项全部默认，因此命令敲完之后，一路回车即可。如果不想交互，就直接敲`npm init -y`。

## 安装某个依赖包
使用`npm install --save shelljs`来安装依赖包。参数"--save"会把依赖包写入package.json的dependencies字段。

## 安装所有依赖包
如果在没有安装过依赖包的机器上，使用`npm install`就可以安装package.json的dependencies字段里的所有依赖包。

## 运行工程
在package.json里的script字段里，添加一行：
```json
"start": "node main.mjs"
```
有了这一行，就可以使用命令`npm start`来启动工程了。

## 总结
node/npm一般使用package.json来管理工程项目。有了它之后，所有node的工程的启动命令都是一样的：`npm install&&npm start`。

命令install可以简写成i，因此上面的`npm install`都可以换成`npm i`。一般推荐在脚本里用全名，因为可读性好；而自己敲命令，则可以敲简写，能够节省不少时间。

[上一课](lesson2.md) &#124; [目录](README.md) &#124; [下一课](lesson4.md)
