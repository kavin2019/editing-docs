# Mac系统下安装docker

## 下载

### 下载地址1：[docker hub官网](https://hub.docker.com/editions/community/docker-ce-desktop-mac) 
  	docker的官网，但是服务器在国外，国内被墙后下载速度很慢，建议使用下面的地址下载。
### 下载地址2: [docker中文官网](https://www.kancloud.cn/jingyucloud/docker/306050)



## Docer版本

按Docker应用的类型划分：

* Docker CE版本（社区版本）免费
* Docker EE版本 （企业版本） 收费

按Docker发布的版本划分：

* Stable： 稳定版本，Docker官网每年六月分发布，功能稳定。
* Edge：最新版本，具有很多新的功能，但是系统稳定性较差。适合学习研究使用



## 安装

1. 安装过程很简单，双击`dmg`文件开始
2. 然后将docker鲸鱼图表加入到application中

![](images/docker_install.png)

3. 双击应用中的Docker.app图标，启动Docker。

4. Docker启动会要求输入账号密码授权。需要特权访问才能安装到Docker应用程序的网络组件和链接

5. 单击状态栏的🐳图标，可以唤出docker的菜单，preferences可对docker进行设置。

6. 通过终端验证docker是否安装成功

   ```
   //docker enginner版本
   jamesde-iMac:~ james$ docker --version
   Docker version 18.09.2, build 6247962
   //docker-compase版本
   jamesde-iMac:~ james$ docker-compose --version
   docker-compose version 1.23.2, build 1110ad01
   //docker-machine的版本
   jamesde-iMac:~ james$ docker-machine --version
   docker-machine version 0.16.1, build cce350d7
   ```



## Docker卸载脚本

```shell
#!/bin/bash

# Uninstall Script

if [ "$(which boot2docker)" == "" ]; then
	echo "boot2docker does not exist on your machine!"
	exit 1
fi

if [ "${USER}" != "root" ]; then
	echo "$0 must be run as root!"
	exit 2
fi

echo "Stopping boot2docker processes..."
boot2docker stop && boot2docker delete

echo "Removing boot2docker executable..."
rm -f /usr/local/bin/boot2docker

echo "Removing boot2docker ISO and socket files..."
rm -rf ~/.boot2docker
rm -rf /usr/local/share/boot2docker

echo "Removing boot2docker SSH keys..."
rm -f ~/.ssh/id_boot2docker*

echo "Removing boot2docker OSX files..."
rm -f /private/var/db/receipts/io.boot2docker.*
rm -f /private/var/db/receipts/io.boot2dockeriso.*

echo "Removing Docker executable..."
rm -f /usr/local/bin/docker

echo "All Done!"
```

