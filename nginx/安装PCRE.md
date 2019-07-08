## PCRE安装

> 使用源码安装NGINX前，linux环境需要已经安装PCRE，否则在进行`./configure`时会提示错误

### 为什么需要PCRE
pcre模块为nginx提供了重写机制，如果不需要重写模块，在`./configure`时指定参数`--without-pcre`忽略。

### 源码安装PCRE

* 进入到`/usr/local/src

  ```shell
  $ cd /usr/local/src
  ```

* 下载PCRE源码包

  ```shell
   $ curl https://netix.dl.sourceforge.net/project/pcre/pcre/8.40/pcre-8.40.tar.gz > pcre-8.40.tar.gz
  ```

* 解压缩源码包

  ```shell
  $ tar -xvf pcre-8.40.tar.gz
  #进入源码包
  $ cd pcre-8.40
  ```

* 执行编译安装

  ```shell
  $ ./configure
  $ make
  $ make install
  ```

* 查看安装版本

  ```shell
  $ pcre-config --version
  ```

### 错误

* 未安装GCC-C++

  ```shell
  configure: error: You need a C++ compiler for C++ support.
  ```

  解决方案：使用yum安装GCC-C++

  ```shell
  $ yum install -y gcc-c++
  ```

  

### YUM安装

```shell
$ yum install -y pcre pcre-devel
```

