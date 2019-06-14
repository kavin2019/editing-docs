# 利用SCP传输文件

***

在linux下一般使用scp命令来通过ssh传输文件。

1. 从linux服务器上下载文件

   > scp *username@servername:/path/filename*    /path[/filename]
   >
   > *username@servername:/path/filename* :是远程服务器上的路径和文件名
   >
   > /path[/filename]: 是本地的路径，filename是可选的，可以下载的时候重命名。

    例如scp root@192.168.0.101:/var/www/test.txt  把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）

2. 从本机上传到linux服务器

   >scp */path/filename*    username@servername:/path[/filename]
   >
   > */path/filename* :本地地址和文件名
   >
   > username@servername:/path[/filename]： 服务器上的地址和文件名
   
   例如scp /var/www/test.php  root@192.168.0.101:/var/www/  把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中

3、从服务器下载整个目录

> scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir（本地目录）

例如:scp -r root@192.168.0.101:/var/www/test  /var/www/  

4、上传目录到服务器

> scp  -r local_dir username@servername:remote_dir

例如：scp -r test  root@192.168.0.101:/var/www/   把当前目录下的test目录上传到服务器的/var/www/ 目录