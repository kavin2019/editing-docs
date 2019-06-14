这些都是典型的使用GNU的AUTOCONF和AUTOMAKE产生的程序的安装步骤

## 基本信息

* ./configure  用于检测安装平台的目标特征和环境。

* make  用来编译，它从makefile读取指令，然后编译

* make install  安装，它也从makefile读取指令，然后将编译后的文件安装到指定目录

## 详解

1. configure命令
   这一步主要是生成 Makefile文件，为下一步的编译做准备，你可以通过在 configure 后加上参数来对安装进行控制，比如:`./configure –prefix=/usr `意思是将该软件安装在 `/usr `下面，执行文件就会安装在` /usr/bin `（而不是默认的 /usr/local/bin),资源文件就会安装在` /usr/share`（而不是默认的`/usr/local/share`）。同时一些软件的配置文件你可以通过指定 –sys-config= 参数进行设定。有一些软件还可以加上 –with、–enable、–without、–disable 等等参数对编译加以控制，你可以通过允许 ./configure –help 察看详细的说明帮助。

2. make命令
   这一步就是编译源代码
   如果遇到错误：“make *** 没有指明目标并且找不到 makefile”。就是没有找到Makefile，解决方法是重新执行`./configure `生成makefile，然后再make

3. makeinstall命令

   这条命令来进行安装（需要你有 root 权限，因为要向系统写入文件）。命令执行前可能通过执行make check 或 make test 来进行一些安装前的测试。

   PS：个人理解是将编译后的文件（如可执行程序、资源文件、配置文件等）安装makefile中的配置移动到相应的位置。

4. 拓展

   * make clean：清除编译产生的可执行文件及目标文件(object file，*.o)。

   * make distclean：除了清除可执行文件和目标文件外，把configure所产生的Makefile也清除掉。
   * make dist：将程序和相关的档案包装成一个压缩文件以供发布。执行完在目录下会产生一个以PACKAGE-VERSION.tar.gz为名称的文件。 PACKAGE和VERSION这两个变数是根据configure.in文件中AM_INIT_AUTOMAKE(PACKAGE，VERSION)的定义。在此范例中会产生test-1.0.tar.gz的档案
   * make distcheck：和make dist类似，但是加入检查包装后的压缩文件是否正常。这个目标除了把程序和相关文件包装成tar.gz文件外，还会自动把这个压缩文件解开，执行 configure，并且进行make all （等价make）的动作，确认编译无误后，会显示这个tar.gz文件可供发布了。这个检查非常有用，检查过关的包，基本上可以给任何一个具备GNU开发环境-的人去重新编译。

5. 