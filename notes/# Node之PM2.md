# Node之PM2

***
## 什么是PM2
> PM2是一个自带负载均衡的进程管理器

## 为什么使用PM2
* 持久化
	使用PM2可以持久化应用，在机器崩溃和重启时自动启动
* 进程管理
  所有的应用都被守护，在后台持续运行
* 日志管理
	所有的应用日志都会保存在服务器中
* 零配置负载均衡服务器
	pm2通过创建多个共享相同服务器端口的子进程来扩展您的应用，这样您可以在停机时间为零的情况下重启您的应用
* 终端内监控
	在终端中监控您的应用以检查您的应用运行状况（CPU使用情况，内存使用，请求/分钟以及更多）
* 使用ssh轻松部署
	自动完成部署，不需一对一的ssh服务器连接

## 还有哪些类似插件
* forever
* supervisor
* nohup（比较笨拙）

##怎么使用PM2
* 安装
	使用`npm`安装
	
	> npm install pm2 -g //安装
	> pm2 update // 更新 
	> npm install pm2@lastest -g //安装最新的pm2，也是更新
	> pm2 uninstall pm2 //卸载pm2
	> 安装cli自动完成(???)
	> pm2 completion install
	>
	> pm2 completion uninstall //卸载
	> 
	> pm2 --help //查看帮助
* 管理进程列表
	> pm2 start app.js //启动并将应用加入到进程管理列表中
	> 
	> > 默认进程名没有.js后缀，如（app.js进程名app）；
	> > 如需修改进程名可以使用--name或-n
	> > 如果需要监控文件修改，在修改后自动部署而不用重启应用。使用选项 --watch；默认不开启的
	> > --watch [paths] //同时可以指定哪些目录下的修改被监视
	> > --ignore-watch <folders|files> //指定哪些文件和目录不用监视
	> > ***注意：启用 --watch时，您必须使用 pm2 stop --watch <app_name>来停止该进程，因为一般的停止不会使观测停止***
	>
	> pm2 ls //查看进程列表
	>
	> pm2 delete app//关闭并从列表中移除应用
	> > pm2 delete <id | name>  [<id | name> ... ] //可以移除一个或多个
	> > pm2 delete all //关闭并清空整个个列表
	
	**`ps：上述进程管理操作还支持正则表达式，如：pm2 restart /app*/`**
	
	* 管理应用进程
	
	  将进程加入到管理列表后，可以对进程进行操作
	
	> pm2 stop <id | name> [<id | name> ... ] //	关闭进程，但是不从列表中移除
	> > pm2 stop all //关闭所有进程，但不从列表移除
	> pm2 restart <id | name>  [<id | name> ... ]//重启进程
	> > pm2 restart all //重启列表中所有进程
	> pm2 start <id | name>  [<id | name> ... ]// 启动进程
	> > pm2 start all //启动列表中所有的进程
	>
	> pm2 show <id | name> //查看单独进程的具体信息
* 查看日志
> pm2 logs  [id | name] //查看进程的日志，如
> >0|microlet | GET /stylesheets/style.css 304 0.325 ms - -
>   0|microlet | GET / 304 0.530 ms - -
>   0|microlet | GET /stylesheets/style.css 304 0.275 ms - -
>   0|microlet | GET / 304 0.541 ms - -
>   0|microlet | GET /stylesheets/style.css 304 0.307 ms - -
>   0|microlet | GET / 304 0.604 ms - -
>   0|microlet | GET /stylesheets/style.css 304 0.396 ms - -
>   0|microlet | GET / 304 0.589 ms - -
>   0|microlet | GET /stylesheets/style.css 304 0.297 ms - -
>   0|microlet | GET / 304 0.504 ms - -
>   0|microlet | GET /stylesheets/style.css 304 0.282 ms - -
>   0|microlet | GET / 200 15.987 ms - 213
>   0|microlet | GET /stylesheets/style.css 304 1.560 ms - -
>   0|microlet | GET / 200 19.650 ms - 225
>   0|microlet | GET /stylesheets/style.css 304 1.747 ms - -
> --lines <n> 可显示最近的n条日志
> --json 以json的格式显示
>
> Pm2 flush //清空日志
>pm2 reloadlogs //重新加载日志
> 

* 集群化
集群化可以将你的应用自动扩展到所有可用的cpu，无需更改代码
>  在使用负载均衡之前，需要确保应用是无状态的（即没有本地数据的存储，如session/websocket连接，以及会话内存及相关内容）
要以集群的方式启动，加参数 -i <n | max>
> pm2 start <id | name>  -i <n | max>
> >  如：pm2 start app -i 4
> >  pm2 start app -i MAX //自动检测可用cpu数量
> 使用reload来实现无中断式重新启动
> > pm2 reload <id | name>

* 生态系统文件
  当在多个服务器上部署或使用多个cli参数时，生态系统文件是最好的替代方案。生态系统文件的目的是集中应用的选项和环境变量
  1. 初始化
  	> pm2 init //自动生成一个文件模版,根目录下的ecosystem.config.js
  	> ```
  	> module.exports = {
    >  apps : [{
    >    name: "app",
    >    script: "./app.js",
    >    env: {
    >      NODE_ENV: "development",
    >    },
    >    env_production: {
    >      NODE_ENV: "production",
    >    }
    >  }]
    > }
  	> 
  	> ```
  	
  	 生态文件属性描述：[详细字段描述参考](<https://pm2.io/doc/zh/runtime/reference/ecosystem-file/>)
  2. 使用生态系统文件
  进行常规操作时传递的参数不再是某个进程名或id，而是生态系统文件名称；操作会一次执行apps中所有的应用程序（通过app的json对象中的script）
  	
  	> pm2 start ecosystem.config.js
  	> pm2 restart ecosystem.config.js
  	> pm2 stop ecosystem.config.js
  	> pm2 reload ecosystem.config.js
  	> > 也可以单独指定文件中应用
  	> > pmw start ecosystem.config.js --only <name>
  3. 环境变量
  可以多次进行申明,每个条目必须遵照此格式 env_<environment-name>,如上面ecosystem.config.js文件中有两个环境env和env_production；
  > pm2 start ecosystem.config.js --env production //指定应用开始的环境，对应使用env_production
  >
  > PS：一旦应用启动就如流程，进程环境加载后就不能修改。因此，如果重启具有不同环境或具有新生态系统文件的进程列表，进程环境不会更改，此行为是为了确保应用重启的一致性

  ​	如果要强制更新，使用 —update-env

  	> pm2 restart ecosystem.config.js —update-env //更新使用的环境下的修改（如环境变量进行了修改）
  	>
  	> pm2 restart ecosystem.config.js —env production —update-env //更改流程环境

  * 进程管理
  保存和重新生成进程列表
  > pm2 save //保存当前的进程列表到硬盘
  > pm2 resurrect //恢复硬盘中的进程列表
  > PS:进程列表保存在 $HOME/.pm2/dump.pm2
	> pm2 kill //直接杀死进程列表中所有进程
  监控进程资源

  > pm2 monit

* 日志管理
日志文件配置，系统默认日志文件保存路径：$HOME/.pm2/logs
为日志文件指定配置
```
module.exports = {
  apps: [{
      name: 'app',
      script: 'app.js',
      output: './out.log', //普通日志
      error: './error.log', //错误日志
	    log: './combined.outerr.log', //结合了 output 和 error, 默认是禁用的
	    //merge_logs: true,//集群下的日志都合并到一个文件中
    }]
}
````
如果要将日志分为多个小文件而不是一个整体大文件，需要使用循环日志logrotate：
> pm2 install pm2-logrotate
> [具体配置](https://github.com/keymetrics/pm2-logrotate)

禁用日志
```
module.exports = {
  apps: [{
      name: 'app',
      script: 'app.js',
      output: '/dev/null', //禁用普通日志
      error: '/dev/null', //禁用错误日志
    }]
}
```

* 启动挂钩
启动挂钩的目的是保存您的进程列表，并在计算机重启甚至出现意外时将其恢复。
每个操作系统都有一个特定的工具来处理启动挂钩：pm2提供了一种简单的方法来生成和配置它们。
> pm2 startup
$ [PM2] 您必须以root身份运行此命令。 执行以下命令:
$ sudo su -c "env PATH=$PATH:/home/unitech/.nvm/versions/node/v4.3/bin pm2 startup <distribution> -u <user> --hp <home-path>

禁用挂钩

> pm2 unstartup

重启挂钩

> pm2 unstartup
>
> pm2 startup

* 负载均衡（集群）
1. 运用
	命令行配置集群，指定参数 -i <number-instances>;number-instances 可以是数字，也可以是max（表示系统自动确定可用cpu数量），也可以使用一个负整数。 如果有4个核心，pm2 start -i -1 将传播3个群集
	
	> pm2 start app.js -i 4
	
2. 无状态应用
在集群环境中，必须首先确定应用是无内部状态
内部状态通常是存储在其进程中的一些本地数据。 例如，它可以是一组websocket连接或本地会话内存。 改用Redis或其他数据库来共享进程间的状态。如何快速构建无状态应用，参考[这里](https://12factor.net/)

3. 0秒宕机重启
当您使用 restart时，pm2杀死并重启该进程，所以在短时间内您将无法使用该服务。
通过重载，pm2会一一重启所有进程，并始终保持至少一个进程正在运行

3. 正常开机关机
为了确保所有请求都能在重载中被正确处理，您需要确保您的应用关闭，不留下未答复的请求。
正常关机确保在退出应用并关闭所有外部连接之前处理所有剩余的请求。
参考[官方关机教程帮助](https://pm2.io/doc/zh/runtime/best-practices/graceful-shutdown/)

4. 集群环境变量
NODE_APP_INSTANCE环境变量用于区分群集。
例如，如果您只想在一个群集上运行cronjob，可检查 process.env.NODE_APP_INSTANCE === 0是否成立。
该变量可在生态系统文件中重命名
```
module.exports = {
  apps: [{
    name: "app",
    script: "./app.js",
    instance_var: "INSTANCE_ID",
  }]
}
```
* 通过http部署静态页面
通过cli启动服务
	> pm2 serve <path> <port>
	> 默认值是 current folder 和 8080，您可以直接使用
	> pm2 serve
生态系统文件：
```
module.exports = {
  apps: [{
    name: "static-file",
    script: "serve",
    env: {
      PM2_SERVE_PATH: ".", //静态文件所在的目录
      PM2_SERVE_PORT: 8080, //端口号
    },
  }]
}
```

* SSH部署
[直接参考这里](https://pm2.io/doc/zh/runtime/guide/easy-deploy-with-ssh/)
##不足
* 监控功能，pm2 monit监控cpu和memory使用情况，keymetrics监控更为详细
* 支持集群模式，支持负载均衡，但因采用nodejs的cluster模块实现，仅适用于nodejs进程；

## 使用pm2时的建议

> 1. 定时重启，应用进程运行时间久了或许总会产生一些意料之外的问题，定时可以规避一些不可测的情况；
>
> 2. 最大内存限制，根据观察设定合理内存限制，保证应用异常运行；
>
> 3. 合理min_uptime，min_uptime是应用正常启动的最小持续运行时长，超出此时间则被判定为异常启动；
>
> 4. 设定异常重启延时restart_delay，对于异常情况导致应用停止，设定异常重启延迟可防止应用在不可测情况下不断重启的导致重启次数过多等问题；
>
> 5. 设置异常重启次数，如果应用不断异常重启，并超过一定的限制次数，说明此时的环境长时间处于不可控状态，服务器异常。此时便可停止尝试，发出错误警告通知等
>
> 链接：https://github.com/jawil/blog/issues/7/

## 留下的问题：
* pm2有fork和cluster两种启动模式，之间有什么区别？

## 参考

[官方教程](https://pm2.io/doc/zh/runtime/overview/)

[pm2参数及命令](https://pm2.io/doc/zh/runtime/reference/pm2-cli/)