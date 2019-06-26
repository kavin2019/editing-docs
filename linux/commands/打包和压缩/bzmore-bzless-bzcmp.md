##bzip2压缩包的辅助命令bzmore bzless bzcmp

### bzmore
#### 简介
bzmore用于分页查看.bz2压缩包内容
#### 格式
```shell
bzmore 压缩包
```

###bzless
#### 简介
bzless也是用于分页查看.bz2压缩包，但是功能比bzmore强大
#### 格式

```shell
bzless 压缩包
```



###bzcmp

#### 简介

bzcmp用于比较两个压缩包之间的内容差别,等价于先解压压缩包，然后使用命令cmp进行比较

格式

```shell
bzcmp file1 file2
#file1和file2都是压缩包
```

