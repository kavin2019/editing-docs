# node之NVM

***

## NVM简介

NVM（node）

## NVM安装

* linux下安装

* 

  从官网下载
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```
或
```
  wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```
vim ~/.bashrc 写入下面代码
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

NVM安装完成！

* windows下安装
首先最重要的是：一定要卸载已安装的 NodeJS，否则会发生冲突。然后下载 nvm-windows 最新安装包，直接安装即可。（windows下的版本和mac/linux的版本不是同一个，所以其中命令可能会有不同）

* IMac下安装

  mac下安装和linux大体相似，只是在编辑文件为 `~/.zshrc`

## NVM使用
* 安装node
```
nvm install [version] //安装指定版本的node，如version为4.2.2，如果是4.2，则自动安装4.2.x中最新的版本
nvm install node //安装最新版本
nvm install unstable //安装最新不稳定版本
nvm install stable //安装最新稳定版本
nvm ls-remote //列出远程服务器上所有可用版本
windows下使用：
nvm ls available

```

* 切换node环境
每当安装一个新版本的（最后安装）的node，全局环境就会把它设为默认的环境。如果要切换环境，使用命令`nvm use`
```
nvm use [version] //切换到指定的版本
nvm use node //使用最新版本
nvm alias awesome-version 4.2.2 //为版本命名，后面就可以使用别名切换
nvm unalias awesome-version //删除别名
nvm current //显示当前版本
```

* 列出所有安装实例
```
nvm ls
```

* 其它命令

```
nvm run 4.2.2 --version //直接运行特定版本的 Node
nvm exec 4.2.2 node --version //在当前终端的子进程中运行特定版本的 Node
nvm which 4.2.2 //确认某个版本Node的路径
nvm install iojs-v3.2.0 //安装 Node 的其他实现，例如 iojs（一个基于 ES6 的 Node 实现，现在已经和 Node 合并）
nvm reinstall-packages <version> ## 在当前版本 node 环境下，重新全局安装指定版本号的 npm 包。即在安装node环境是不安装自带版本的npm而使用已有的npm
```