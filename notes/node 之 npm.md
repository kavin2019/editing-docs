# node 之 npm

***

## 简介

> 通常称为node包管理器(node package manager)。顾名思义，它的主要功能就是管理node包，包括：安装、卸载、更新、查看、搜索、发布等

## 使用

* npm install之间不同安装模式的区别

```
npm install packageName //本地安装，安装到项目目录下，不在package.json中写入依赖。本地安装只有在本目录使用
`npm install packageName -g //全局安装，安装在Node安装目录下的node_modules下。但是会在/usr/local/bin中创建链接文件。所以本地安装后也可以通过创建链接文件来实现全局的效果
`npm install packageName --save //安装到项目目录下，并在package.json文件的dependencies中写入依赖，简写为-S`
`npm install packageName --save-dev //安装到项目目录下，并在package.json文件的devDependencies中写入依赖，简写为-D
```

* npm install安装文件版本策略

```
npm install pkg //安装pkg的最新版本
npm install pkg@1.9.2 /安装1.9.2的指定版本
```

  

* 查看已经安装的模块

  命令：npm ls  [modulename...]

```
npm ls //查看所有已安装的模块
npm ls sequelize epxress //查看具体的模块。可以指定多个模块
```



* 卸载安装包

```
npm uninstall pkg
npm uninstall pkg@1.9.2
如果需要从dependences中删除依赖，需要加入选项 --save
如果需要从devDependences中删除依赖，需要加入选项 --save-dev
```



* 更新安装包

```
npm update pkg
npm outdate --depth=0 //查看有最新版本的依赖
```



* 使用npm init创建package.config文件

```
npm init
```



* 如果出现 `npm err! Error: connect ECONNREFUSED 127.0.0.1:8087 `

```
npm config set proxy null
```

