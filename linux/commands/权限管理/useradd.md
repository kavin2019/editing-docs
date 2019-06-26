## useradd添加用户

### 简介

useradd命令用于linux系统创建新的用户，创建账号后，需要使用`passwd`命令设置账号的密码。实际用户信息建立在配置文件:`/etc/passwd`中

后续删除用户使用`userdel`。

### 格式

```
useradd [OPTIONS] <参数>
```

#### 选项

```shell
-c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中；
-d<登入目录>：指定用户登入时的启始目录；
-D：变更预设值；
-e<有效期限>：指定帐号的有效期限；
-f<缓冲天数>：指定在密码过期后多少天即关闭该帐号；
-g<群组>：指定用户所属的群组；
-G<群组>：指定用户所属的附加群组；
-m：自动建立用户的登入目录；
-M：不要自动建立用户的登入目录；
-n：取消建立以用户名称为名的群组；
-r：建立系统帐号；
-s<shell>：指定用户登入后所使用的shell；
-u<uid>：指定用户id。
PS:使用`-u`指定用户id时，id尽量大于500，因为0-500之间保留给系统建立一些特殊用户，例如bin、mail这样的系统账号。
```

#### 参数

用户名



### 实例

新建用户并加入用户组

```shell
$ useradd demo -g demo -d ~/demo -c "for practise"
$ cat /etc/passwd | grep demo
demo:x:1000:1000:for practise:/root/demo:/bin/bash
```

新建用户并指定uid

```shell
$ useradd demo1 -u 1001 
$ cat /etc/passwd | grep demo1
demo1:x:1001:1001::/home/demo1:/bin/bash
```

