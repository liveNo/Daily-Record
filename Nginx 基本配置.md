# Nginx 基础知识点整理

### Nginx的优势
* 更快
	* 正常情况下，单次请求响应更快
	* 高峰期，Nginx可以比其他Web服务器更快地响应请求
* 高扩展性
	* 由多个不同功能、不同层次、不同类型且耦合度极低的模块组成，可以专注模块自身
* 高可靠性
	* 源自Nginx的优秀设计，一个master管理多个worker，每个worker进程相对独立。
* 低内存消耗
	* 一般情况下，10000个非活跃的HTTP Keep-Alive 连接在Nginx中仅消耗2.5MB的内存
* 单机支持10万以上的并发连接
	* 非常重要的一个特性。理论上，Nginx支持的并发连接上限取决于内存。
* 热部署
	* Master管理进程与worker工作进程的分离设计，可以在不停止服务进行升级，更新配置，切换日志
* 最自由的BSD许可协议

### Nginx进程间的关系

- Nginx 支持单进程服务（即master进程提供服务）
	> Nginx 在配置文件里提供 `master_process on|off`来修改Nginx的运行方式，默认为`on`，为master-worker运行。关闭后，master 就不会fork出worker子进程来处理请求，而是直接使用master自身来处理请求，一般使用这种方式只是为了方便跟踪调试Nginx，并不用在实际生产环境中。

- Nginx 提供master-worker配置启动多进程服务
![](./resource/nginx_master.png)
> Nginx运行在该模式下， Master进程并不会参与处理请求，只用来管理真正提供服务的worker进程，并提供启动服务、停止服务、重载配置文件、平滑升级等命令。一般来说，master拥有的权限要大于或等于worker权限，这样便于管理worker进程。另外，多个worker进程处理方式可以提高服务的健壮性。

### Nginx 应该了解的基本配置
1. 是否以守护进程的方式运行Nginx。设置`daemon on|off`，默认为`on`。毫无疑问，Nginx是需要以守护进程的方式来支持的，之所以提供这种模式，是为了方便开发者跟踪调试Nginx，这样大大方便了gdb调试跟进fork出的子进程的烦琐。
2. 是否以 master/worker方式运行。在上面已经提到过了
3. error 日志设置。 设置 `error_log /path/file level`。error日志是定位服务出现问题最直接的一个手段，也是必不可少的一个设置。它默认是`logs/error.log error`，level 是日志的输出级别，包括`debug`、`info`、`notice`、`warn`、`error`、`crit`、`alert`、`emerg`八个级别，从左至右依次增大。当设定一个级别的时候，大于或等于该级别的日志都会输出到文件中。**注意：**如果日志级别是debug的话，必须在configure 时加入--with-debug配置项。
4. 嵌入其他配置文件。使用`include /path/file`嵌入到当前的nginx.conf中，file参数的值可以是一个明确的文件名，也可以含有通配符*的文件名。
5. pid文件的路径。设置`pid /path/file`，默认是`pid logs/nginx.pid`，这是一个保存master进程ID的文件，默认是与configure编译时参数`--pid-path`所指定路径一致，可随时修改，但要确保Nginx有权在创建pid文件，它影响着Nginx是否可以运行。
6. Nginx worker 进程运行的用户及用户级。设置`user nobody nobody`，在执行configure 编译时，可使用`--user=nobody`和`--group=nobody`设置。该设置会在master进程启动时，用于运行fork出来的worker进程。
7. Nginx worker的进程个数。 设置`worker_processes number`，默认为`1`，我们都知道worker进程数量会直接影响性能。一般情况下，我们会worker进程数量会与CPU核心数设置一致。
8. lock 文件的设置。 设置`lock_file /paht/file`，默认`lock_file logs/nginx.lock`，这个可能跟accept锁有些千丝万缕的关系，我们就不在这里展开赘述了。
9. 事件模型的选择。模型包括`kqueue|rtsig|epoll|/dev/poll|select|poll|eventport`，nginx会自动选择比较合适的模型，Linux用户来说，一般会使用epoll，它的性能比较高。
10. 每个worker的最大连接数。用于定义每个worker进程可以同时处理的最大连接数。

### Nginx的worker进程数量为什么要与CPU核心数量一致呢？
Nginx的一个worker进程可以同时处理的请求数只受限于内存的大小，而在架构设计上，不同的worker进程之间处理并发请求时几乎没有同步锁的限制，worker进程通常不会进入休息睡眠状态，所以，当Nginx上的进程数与CPU核心数相等时，进程间切换的代价是最小的。每个worker进程都是单线程的进程，多worker进程可以充分利用多核系统架构，如果worker进程数多于CPU内核数，那么会增大进程间切换带来的消耗（Linux是抢占式内核）。