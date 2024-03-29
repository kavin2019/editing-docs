## who显示目前所有登录系统的用户

### 简介

who命令可以显示所有登录当前系统的用户，得知有哪些用户什么时候登录系统。单独执行who命令会列出登录账号、使用终端、登入时间以及从何处登录。

### 格式

```shell
who [OPTIONS] [参数]
```

#### OPTIONS

```
-H或--heading：显示各栏位的标题信息列；
-i或-u或--idle：显示闲置时间，若该用户在前一分钟之内有进行任何动作，将标示成"."号，如果该用户已超过24小时没有任何动作，则标示出"old"字符串；
-m：此参数的效果和指定"am i"字符串相同；只显示和自己账户相关信息
-q或--count：只显示登入系统的帐号名称和总人数；
-s：此参数将忽略不予处理，仅负责解决who指令其他版本的兼容性问题；
-w或-T或--mesg或--message或--writable：显示用户的信息状态栏；
--help：在线帮助；
--version：显示版本信息。
```

#### 参数

文件名，指定查询文件

### 实例

1. 直接使用who

```shell
$ who
root     pts/0        2019-06-24 16:59 (192.168.254.178)
root     pts/1        2019-06-25 01:29 (192.168.254.104)
```

2. 显示内容的头

   ```shell
   $ who -H
   NAME     LINE         TIME             COMMENT
   root     pts/0        2019-06-24 16:59 (192.168.254.178)
   root     pts/1        2019-06-25 01:29 (192.168.254.104)
   ```

3. 只显示当前账号和当前登录人数

   ```shell
   $ who -q
   root root
   # users=2
   ```

   

4. 