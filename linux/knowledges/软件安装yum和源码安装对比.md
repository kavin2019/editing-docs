## yum安装和源码安装对比

### 难易程度

* yum安装简单，在线安装，不容易出错
* 源码包安装复杂，需要收到下载源码包以及依赖等。好处在于，在自己系统编译安装，更符合系统的性能所以执行效率相应会高。而且有源码的情况下可以修改和定制。

### 安装路径不同

* yum会将软件安装在不同的位置。可以通过`rpm -ql <软件>`来查看安装的位置，例如：

  ```shell
  $ rpm -ql nginx
  /etc/logrotate.d/nginx
  /etc/nginx
  /etc/nginx/conf.d
  /etc/nginx/conf.d/default.conf
  /etc/nginx/fastcgi_params
  /etc/nginx/koi-utf
  /etc/nginx/koi-win
  /etc/nginx/mime.types
  /etc/nginx/modules
  /etc/nginx/nginx.conf
  /etc/nginx/scgi_params
  /etc/nginx/uwsgi_params
  /etc/nginx/win-utf
  /etc/sysconfig/nginx
  /etc/sysconfig/nginx-debug
  /usr/lib/systemd/system/nginx-debug.service
  /usr/lib/systemd/system/nginx.service
  /usr/lib64/nginx
  /usr/lib64/nginx/modules
  /usr/libexec/initscripts/legacy-actions/nginx
  /usr/libexec/initscripts/legacy-actions/nginx/check-reload
  /usr/libexec/initscripts/legacy-actions/nginx/upgrade
  /usr/sbin/nginx
  /usr/sbin/nginx-debug
  /usr/share/doc/nginx-1.16.0
  /usr/share/doc/nginx-1.16.0/COPYRIGHT
  /usr/share/man/man8/nginx.8.gz
  /usr/share/nginx
  /usr/share/nginx/html
  /usr/share/nginx/html/50x.html
  /usr/share/nginx/html/index.html
  /var/cache/nginx
  /var/log/nginx
  ```

  其位置很多，所以在卸载时，只能通过`rpm -e`命令来卸载，否则容易遗漏导致垃圾文件。如果卸载过程中提示依赖问题导致卸载失败，可以使用参数`--no-deps`，这个表示强制卸载，忽略依赖问题。

* 源码安装，其安装后的文件（二进制文件、配置文件、日志文件、库文件及资源文件）都在同一个目录中，所以卸载直接删除这个目录即可。通常在`/usr/local/包名`中，但是不是绝对的，在`./configure`时可以通过`--prefix`改变这个位置。

### 启动方式不同

* yum安装的服务软件，我们可以通过`systemctl start/stop/restart`来管理（centos6及之前通过`services`命令）
* 源码包安装的软件，需要在 `软件安装目录下/sbin/软件可执行程序`来执行。但是通过将`ln`命令将可执行文件在/usr/bin/目录下创建一个链接文件，这样也可以通过`systemctl`管理