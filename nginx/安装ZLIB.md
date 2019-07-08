## ZLIB安装

> zlib为压缩包工具，为nginx压缩模块提供支持，如果linux环境中没有事先安装zlib，在进行·`./configure/`时会提示错误。

### 源码包安装

* [官网](https://zlib.net/)下载源码包

  ```shell
  $ curl https://zlib.net/zlib-1.2.11.tar.gz > zlib-1.2.11.tar.gz
  ```

  

* 解压源码包

  ```shell
  $ tar -xvf zlib-1.2.11.tar.gz 
  $ cd zlib-1.2.11
  ```

* 编译安装

  ```shell
  $ ./configure
  $ make 
  $ makeinstall
  ```

  

* 检查安装

### 错误

* 未安装gcc
  如果没有安装gcc，那么会提示下面的错误：

  >Checking for gcc...
  >
  >Compiler error reporting is too harsh for ./configure (perhaps remove -Werror).
  >
  >** ./configure aborting.

  解决方案：

  安装gcc即可，一般通过yum直接安装最新的版本即可

  ```shell
  #yum从yum源安装
  $ yum install -y gcc
  #检查是否安装成功
  $ gcc --version
  ```

  

* 未安装make命令
  如果没有安装make，提示一下错误

  > bash: make: command not found

  解决方案：

  通过yum安装make

  ```shell
  $ yum install -y make
  #查看安装是否成功
  $ make --version
  ```

  

* 

### YUM安装

```shell
$ yum install -y zlib zlib-devel
```

