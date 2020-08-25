# 附录E NPM使用指南
NPM是Node.js的包管理工具。它有着很多的功能。在这里，我们对这些功能和相应的命令做一个简要的介绍。

## 常用命令
+ `npm init -y`

在当前目录下创建初始的package.json文件。

+ `npm i`

安装package.json内的dependencies和devDependencies中指定的package。

+ `npm i pkg`

安装某个package，并将其写入package.json内的dependencies中。

+ `npm i -g pkg`

将某个package安装到全局。全局package中的命令可以直接在console的任意路径下调用。

+ `npm ls pkg`

查看某个package。

+ `npm up`

升级package.json中的package。

## 次常用命令
+ `npm config set registry https://registry.npm.taobao.org`

设置淘宝源

+ `npm config delete registry`

删除源

+ `npm config get registry`

查看源

+ `npm config [get|set|delete] proxy`

[查看|设置|删除]代理服务器
