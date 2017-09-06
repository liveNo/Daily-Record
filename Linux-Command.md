# Linux-Command 命令小记
> 日常生活常用到的Linux命令组合小记

1. 清除所有文件的执行权限 不影响目录
	
	```
	find . ! -type d -exec chmod -x {}\; 
	```
2. 找出当前系统内存使用量较高的进程
	
	```
	ps aux | sort -rnk 4 | head -20
	```
输出的第4列就是内存的耗用百分比。最后一列就是相对应的进程。
3. 同理， 找出系统CPU使用量较高的进程

	```
	ps aux | sort -rnk 3 | header -20
	```
	
	由2、3可以发现，sort命令后的3、4其实代表着第3列进程排序、第4列进程排序。
4. 持续ping并将结果记录到日志
	
	```
	ping example.com | awk '{print $0 " " strftime("%Y-%m-%d %H:%M:%S", systime())}' >> /tmp/example.log
	```
5. 查看tcp连接状态
	> 指定查看端口的tcp连接状态， 有利于分析连接是否释放，或者攻击时进行状态分析
	
	```
	netstat -nat | grep 80 | awk '{print $6}' | sort | uniq -c | sort -rn
	```
	
6、查看80端口请求数最高的前20个ip