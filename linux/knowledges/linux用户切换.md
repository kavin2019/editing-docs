# linux用户切换

***

> 日常操作中，为了系统安全，在多用户系统中通常使用的都是普通用户，权限会有很大的限制。但是在一些情况是需要一些命令的权限来执行操作时，需要切换到root用户或者其它有权限的用户。

## su-命令

su命令为switch user的简写
格式：

```
su -l username //-l 为--longin的简写，通常l可以省略，所以也可这样写：
su - username //如果不指定username，默认登录root
```



## su和su -

两者都是切换用户，主要区别在于

1. su切换用户后会变更工作目录和环境变量，如普通用户user1切换到root，目录会从/home/user1切换到/root
2. su - 切换用户工作目录和环境变量也会保持不变

## sudo

sudo命令时在用户不需要知道其它用户（包括root）时不需要知道其它用户的密码，而执行其它用户具有权限的操作。

sudo是一种权限管理机制，依赖配置文件/etc/sudoers,其定义了授权那个用户或群组可以通过sudo执行哪些命令。

格式：su -u username command
当执行root权限时，-u username可以省略。su cat /etc/shadows

sudoers文件：

```
...
## This file must be edited with the 'visudo' command.//这个文件只能使用visudo命令编辑
...
## Next comes the main part: which users can run what software on 
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
## 	user	MACHINE=COMMANDS  //授权格式
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere 
root	ALL=(ALL) 	ALL 
//对应说明：
# root	       | ALL=(ALL) 	     | ALL
# 授权给那个用户 | 哪台机器登录=(能够切换的用户) | 授权哪些命令

## Allows members of the 'sys' group to run networking, software, 
## service management apps and more.
//授权给用户组
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel	ALL=(ALL)	ALL

## Same thing without a password
# %wheel	ALL=(ALL)	NOPASSWD: ALL //最后设置wheel分组下的用户可以执行所有命令，而且不需要自身密码

## Allows members of the users group to mount and unmount the 
## cdrom as root
# %users  ALL=/sbin/mount /mnt/cdrom, !/sbin/umount /mnt/cdrom //!表示不能使用什么命令

## Allows members of the users group to shutdown this system
# %users  localhost=/sbin/shutdown -h now

例如：
admin all=(root)  !/usr/bin/password, /usr/bin/password [A-Za-z]*, !/usr/bin/password root
      所有登录的机器 = 只能切换root用户  不能单独执行password，可以执行passwrod [A-Za-z]*，不能执行pasword root
```

