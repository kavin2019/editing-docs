## id查看用户的UID和GID

### 简介

id命令可以显示真实有效的用户的用户ID(UID)和群组ID(GID)。

UID是对一个用户唯一的身份标识。GID则对应多个群组ID。

### 格式

```shell
id [OPTIONS] [用户名]
```

#### OPTIONS

```
-g或--group 　 显示用户所属群组的ID。
-G或--groups   显示用户所属附加群组的ID。
-n或--name 　  显示用户，所属群组或附加群组的名称。
-r或--real 　  显示实际ID。
-u或--user 　  显示用户ID。
-help 　       显示帮助。
-version 　    显示版本信息。
```

#### 用户名

指定要查看的用户名，如果省略则默认为当前登录的用户。查看当前登录的用户名使用`whoami`命令。

### 实例

```shell
$ id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
```

