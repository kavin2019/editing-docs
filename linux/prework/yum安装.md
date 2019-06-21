## YUM安装

> 在linux系统最小安装包中，YUM可能是默认没有被安装的，所以需要手动安装。linux系统下的软件包rpm安装时，软件包之间的依赖有可能非常的复杂，如果手动rpm安装，需要先安装好依赖，这是一个费时费力而且容易出错的方式。YUM的出现，简化了安装过程，在软件安装过程中会自动检测 、下载和安装依赖。

### YUM最简单的安装方式

* 从[官网]([http://yum.baseurl.org/](http://yum.baseurl.org/))下载yum包，如：[yum-3.4.3.tar.gz](http://yum.baseurl.org/download/3.4/yum-3.4.3.tar.gz)

* 解压缩安装包

  ```shell
  tar -xvf yum-3.4.3.tar.gz
  ```

  

* 运行安装

  ```shell
  touch /etc/ yum.conf
  
  cd yum-3.4.3
  yummain.py install yum
  ```

  

* 更新系统

  ```shell
  yum check-update
  yum update
  yum clean al
  ```

* 配置yum源

  * 进入yum源配置目录下

    ```shell
    $ cd /etc/yum.repos.d
    ```

  * 备份原来的镜像源配置

    ```shell
    $ mv CentOS-Base.repo  CentOS-Base.repo.backup
    ```

  * 下载新的yum源配置
    下载163的yum源和ali的yum源配置文件：

    1. centos7

       ```shell
       curl http://mirrors.163.com/.help/CentOS7-Base-163.repo > CentOS7-Base-163.repo
       ```

    2. centos6

       ```shell
       curl http://mirrors.163.com/.help/CentOS6-Base-163.repo > CentOS6-Base-163.repo
       ```

    3. centos5

       ```shell
       curl http://mirrors.163.com/.help/CentOS5-Base-163.repo > CentOS5-Base-163.repo
       ```
    
  下载aliyun的yum源配置文件：
    
    ```shell
    centos7:curl http://mirrors.aliyun.com/repo/Centos-7.repo > CentOS-Base.repo
    centos6:curl http://mirrors.aliyun.com/repo/Centos-6.repo > CentOS-Base.repo
    centos5:curl http://mirrors.aliyun.com/repo/Centos-5.repo > CentOS-Base.repo
    ```
    
    通过`rpm -ivh`加入新的yum源，例如：
    
    ```shell
    #默认nginx安装包在yum源中是不存在的，需要将epel或者nginx官网的源加入到yum配置中。
    $ rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
    #然后通过 yum repolist可以查看到
    $ yum repolist
    Loaded plugins: fastestmirror, ovl
    Loading mirror speeds from cached hostfile
     * base: mirror.rise.ph
     * extras: mirror.rise.ph
     * updates: mirror-hk.koddos.net
    nginx | 2.9 kB  00:00:00     
    nginx/x86_64/primary_db  |  46 kB  00:00:01     
    repo id                 repo name          status
    base/7/x86_64          CentOS-7 - Base      10019
    extras/7/x86_64        CentOS-7 - Extras    417
    nginx/x86_64           nginx repo           152
    updates/7/x86_64       CentOS-7 - Updates    2089
    repolist: 12677
    ```
    
    
    
  * 运行yum makecache生成缓存

    ```shell
    $ yum clean all
    $ yum makecache
    ```

  * 查看yum源

    ```shell
    $ yum update -y 
    ```

  * yun源配置简单说明：

    ```shell
    [base]                   //软件源的名称，将被yum取得并识别
    name=CentOS-$releasever - Base  //定义软件仓库的名称,没实际作用
    mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
    //mirrorlist指定镜像服务器的地址列表
    #baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
    //baseurl指定一个源的镜像服务器地址,这里加了#表示注释了yum将忽略这一行
    priority=1            //设置源优先级,1到99的正整数，数值越小越优先
    enabled=0             //这个选项表示是否启用repo中定义的这个源，0为禁用，1为启用
    gpgcheck=1          //这个选项表示是否要对下载的rpm包进行gpg的校验，来确定rpm包的来源是有效和安全的，跟下面的gpgkey成对使用
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6          //定义用于校验的gpg密钥
    ```

    

  * 

* yum源查看

  ```shell
      # 显示yum源信息
      yum repolist enabled    #显示启用的yum源信息
      yum repolist disabled    #显示禁用的yum源信息
      yum repolist all    #显示所有yum源信息
  ```

### yum命令

* 查看所有yum源上的安装包：`yum list all`

* 查看有所有通过yum安装的包：

  ```shell
  $ yum list installed
  #查看是否已经安装某个包,例如 gcc
  $ yum list installed | grep gcc 
  ```

  

* 

### 依赖

yum是有pyth实现的工具，所以在安装yum前需要确定已经存在pyth环境。如果没有需要安装。

* 查看python是否安装

  ```shell
  $ python --version
  ```

* 如果不存在，则可以通过源码安装的方式安装

  * 下载python安装包

    ```shell
    $ curl http://www.python.org/ftp/python/2.4.3/Python-2.4.3.tar.bz2 Python-2.4.3.tar.bz2 
    ```

  * 解压缩

    ```shell
    $ tar jxvf Python-2.4.3.tar.bz2
    ```

  * 编译安装

    ```shell
    $ cd Ptyhon-2.4.3
    $ ./configure
    $ make 
    $ make install
    ```

* 此外需要安装python-urlgrabber，python-pycurl，yum-metadata-parser

  ```shell
  $ curl http://tel.mirrors.163.com/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-8.el6.noarch.rpm > python-urlgrabber-3.9.1-8.el6.noarch.rpm
  
  $ curl http://tel.mirrors.163.com/centos/6/os/x86_64/Packages/python-pycurl-7.19.0-8.el6.x86_64.rpm > python-pycurl-7.19.0-8.el6.x86_64.rpm
  
  $ curl http://tel.mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm > yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
  
  $ rpm -ivh python-urlgrabber-3.9.1-8.el6.noarch.rpm python-pycurl-7.19.0-8.el6.x86_64.rpm yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
  ```

* 测试Python是否安装成功`Python --version`