# linux命令学习

[参考资料]: https://man.linuxde.net/



## jar包启动

<font color="yellow">nohup表示永久运行	&表示后台启动	>表示重定向到哪里	1:表示stdout标准输出,系统的默认值是1	2:表示stderr标准错误</font>

```sh
nohup java -jar ***.jar > nohup.out 2>&1 &
```



## 查看运行项目占用的pid(进程号)

```sh
ps aux|grep ***.jar	(jar包具体名称)
```



## 查看端口号占用的 pid

```sh
lsof -i :port	(端口号)
```



## 根据pid查询端口号

```sh
netstat -antup|grep pid

# 示例
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      25689/nginx: worker 
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      25689/nginx: worker
```





## 根据运行jar包的PID 查询jar包的绝对目录地址

```sh
ll /porc/PID	(运行jar包的PID)


# 实例
dr-xr-xr-x  2 root root 0 Jul 30 09:26 attr
-rw-r--r--  1 root root 0 Jul 30 09:26 autogroup
-r--------  1 root root 0 Jul 30 09:26 auxv
-r--r--r--  1 root root 0 Jul 18 01:06 cgroup
--w-------  1 root root 0 Jul 30 09:26 clear_refs
# comline 符号是程序运行时输入的命令行命令
-r--r--r--  1 root root 0 Jul 27 22:21 cmdline
-rw-r--r--  1 root root 0 Jul 30 09:26 comm
-rw-r--r--  1 root root 0 Jul 30 09:26 coredump_filter
-r--r--r--  1 root root 0 Jul 30 09:26 cpuset
# cwd 符号链接的进程的运行目录
lrwxrwxrwx  1 root root 0 Jul 28 02:58 cwd -> /root/gss-server/wqht-app-re
# environ 符号记录了进程运行时的环境变量
-r--------  1 root root 0 Jul 30 03:03 environ
# exe 符号链接的是执行程序的绝对路径
lrwxrwxrwx  1 root root 0 Jul 16 15:17 exe -> /usr/local/java/jdk1.8.0_211/bin/java
# fd 目录下是进程打开或使用的文件的符号链接
dr-x------  2 root root 0 Jul 21 19:28 fd
dr-x------  2 root root 0 Jul 28 08:22 fdinfo
-rw-r--r--  1 root root 0 Jul 30 09:26 gid_map
-r--------  1 root root 0 Jul 30 09:26 io
-r--r--r--  1 root root 0 Jul 30 09:26 limits
-rw-r--r--  1 root root 0 Jul 30 09:26 loginuid
dr-x------  2 root root 0 Jul 30 09:26 map_files
-r--r--r--  1 root root 0 Jul 30 09:26 maps
-rw-------  1 root root 0 Jul 30 09:26 mem
-r--r--r--  1 root root 0 Jul 30 09:26 mountinfo
-r--r--r--  1 root root 0 Jul 30 09:26 mounts
-r--------  1 root root 0 Jul 30 09:26 mountstats
dr-xr-xr-x  7 root root 0 Jul 30 09:26 net
dr-x--x--x  2 root root 0 Jul 30 09:26 ns
-r--r--r--  1 root root 0 Jul 30 09:26 numa_maps
-rw-r--r--  1 root root 0 Jul 30 09:26 oom_adj
-r--r--r--  1 root root 0 Jul 30 09:26 oom_score
-rw-r--r--  1 root root 0 Jul 30 09:26 oom_score_adj
-r--r--r--  1 root root 0 Jul 30 09:26 pagemap
-r--r--r--  1 root root 0 Jul 30 09:26 personality
-rw-r--r--  1 root root 0 Jul 30 09:26 projid_map
lrwxrwxrwx  1 root root 0 Jul 28 07:43 root -> /
-rw-r--r--  1 root root 0 Jul 30 09:26 sched
-r--r--r--  1 root root 0 Jul 30 09:26 schedstat
-r--r--r--  1 root root 0 Jul 30 09:26 sessionid
-rw-r--r--  1 root root 0 Jul 30 09:26 setgroups
-r--r--r--  1 root root 0 Jul 30 03:03 smaps
-r--r--r--  1 root root 0 Jul 30 09:26 stack
-r--r--r--  1 root root 0 Jul 16 15:16 stat
-r--r--r--  1 root root 0 Jul 29 10:32 statm
-r--r--r--  1 root root 0 Jul 16 15:16 status
-r--r--r--  1 root root 0 Jul 30 09:26 syscall
dr-xr-xr-x 78 root root 0 Jul 28 07:43 task
-r--r--r--  1 root root 0 Jul 30 09:26 timers
-rw-r--r--  1 root root 0 Jul 30 09:26 uid_map
-r--r--r--  1 root root 0 Jul 30 09:26 wchan

```



## 停止运行jar包服务

```sh
kill -9 PID	(运行jar包的PID)
```



## 追踪日志文件

```sh
tailf ***.out
```



## 日志文件分析

```sh
cat ***.out|grep 'error' > error.out
```



## 查看指定文件的内容

```sh
1、tail date.log               输出文件末尾的内容，默认10行

     tail -20  date.log        输出最后20行的内容

     tail -n -20  date.log    输出倒数第20行到文件末尾的内容

     tail -n +20  date.log   输出第20行到文件末尾的内容

     tail -f date.log            实时监控文件内容增加，默认10行。

2、head date.log           输出文件开头的内容，默认10行
# 输出开头15行的内容
     head -15  date.log     

     head -n +15 date.log 输出开头到第15行的内容

     head -n -15 date.log  输出开头到倒数第15行的内容

3、sed -n "开始行，结束行p" 文件名    

      sed -n '70,75p' date.log             输出第70行到第75行的内容

      sed -n '6p;260,400p; ' 文件名    输出第6行 和 260到400行

      sed -n 5p 文件名                       输出第5行

tail 和 head 加上 -n参数后 都代表输出到指定行数，tail 是指定行数到结尾，head是开头到指定行数

+数字 代表整数第几行， -数字代表倒数第几行
```



## 错误文件筛选方式

```sh
 # -n 代表试下行号 -i 区分大小写
 grep *Service nohup.out -n -i
 # 显示 第1行到5行的内容
 sed -n '1,5p' nohup.out
 # 根据关键字查询日志 每页显示 10条  可以多次grep
 cat -n app.log|grep "error" |more -10
 # 倒着读取日志文件
 tac nohup.out |grep 'sanyi' -n |more -10
```



## grep参数详解

```sh
# 在filename中查找包含keyword的行
grep [keyword] filename

# -n 显示行号
grep [keyword] filename -n

# -i 忽略大小写
grep [keyword] filename -i

# -c 统计次数
grep [keyword] filename -c

# -v 逆行查找输出不符合项
grep [keyword] filename -v
```



## Linux jvm调优

### jvm(缓存区使用率查询)

```sh
jmap -heap 11538

# jmap是一个输出所有内存中的对象功能,甚至可将vm中的head,以二进制输出成文本.打印出某个java进程的内存,所有对象的情况

```



### jstat(监控内存回收)

```sh
jstat -gcutil pid 10s  #没间隔10秒监控一次内存回收情况

jstat -gcutil 139926 10s

# 示例
 S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
 99.98   0.00  76.97   4.76  93.37  90.71     54    1.330     7    1.835    3.164
 99.98   0.00  79.43   4.76  93.37  90.71     54    1.330     7    1.835    3.164
 99.98   0.00  81.19   4.76  93.37  90.71     54    1.330     7    1.835    3.164
 99.98   0.00  82.99   4.76  93.37  90.71     54    1.330     7    1.835    3.164
 99.98   0.00  83.90   4.76  93.37  90.71     54    1.330     7    1.835    3.164
 99.98   0.00  85.26   4.76  93.37  90.71     54    1.330     7    1.835    3.164

# E 代表Eden区的使用率
# O 代表Old区的使用率
# P 代表Permanent区的使用率
# CCS 代表压缩区的使用率
# M 代表MetaspaceSize已使用的占当前容量的百分比
# YGC YoungGC代表Minor GC次数
# YGCT YoungGcTime 代表MInor GC 耗时
# FGC Full GC 代表Full GC 次数
# FGCT Full GC TIme 代表Full GC 耗时
# GCT GC TIme 代表 Minor GC & Full GC 共耗时
```



### jps(JVM运行的进程状态信息)

```sh
jsp [options] [hostid] #如果不指定hostid则默认为当前主机或服务器

# [options] 说明
-q #不输出类名,jar名和传入main方法的参数
-m #输出传入main方法的参数
-l #输出main类或jar的权限名
-v #输出传入JVM的参数

#示例
jps -m -l

1992 /root/nacos2/target/nacos-server.jar --spring.config.location=classpath:/,classpath:/config/,file:./,file:./config/,file:/root/nacos2/conf/ --logging.config=/root/nacos2/conf/nacos-logback.xml nacos.nacos
95498 insurance-server-sys-20200907014303-SNAPSHOT.jar
139661 push-service-1.0-SNAPSHOT.jar


```



### jstack(Java进程内的线程堆栈信息)

```sh
jstack -F pid #检测是否有死锁

#语法格式
jstack [options] pid
jstack [options] executabel core
jstack [options] [server -id @] remote -hostname -or -ip

# [options] 说明
-l #long listings,会打印出额外的锁信息,在发送死锁的同时可以用 jstack -l pid
```



## top(Linux资源统计)

```sh
# 示例
top - 13:47:05 up 2 days,  3:06, 10 users,  load average: 1.80, 2.56, 2.08
# top --任务队列信息
# 系统时间  --13:47:05	 	运行时间  --up 2 days 3:06min 	当前登录用户 --10 users	load average(负载均衡时间) 1分钟,5分钟,15分钟的负载情况load average数据是每隔5秒钟检查一次活跃的进程数，然后按特定算法计算出的数值。如果这个数除以逻辑CPU的数量，结果高于5的时候就表明系统在超负荷运转了
Tasks: 725 total,   1 running, 724 sleeping,   0 stopped,   0 zombie
# tasks --任务(进程)
# 总数 --total  	运行数量 -- running 	休眠数量 -- sleeping 	停止数量: stopped 	僵尸进程 --zombie
%Cpu(s):  3.1 us,  1.3 sy,  0.0 ni, 95.3 id,  0.2 wa,  0.0 hi,  0.1 si,  0.0 st
# cpu状态信息
# us --user space 用户空间占用CPU的百分比		sy --sysctl 内核空间占用CPU的百分比		ni --改变过优先级的进程占用cup的百分比					id --idoit 空闲CPU的百分比		wa --wait io等待占用CPU的百分比		hi --hardware IRQ 硬中断占用CPU的百分比		si --software interrupts 软中断占用CPU的百分比
KiB Mem : 98715536 total,  6331620 free, 74622544 used, 17761372 buff/cache
# Mem --内存信息
# 总数量 --total	未使用数量 --free	已使用数量 --used	缓存的内存量 --buff/cache
KiB Swap: 25165816 total, 24903160 free,   262656 used. 21535580 avail Mem 
# Swap --交换分区信息

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND 
# pid --进程号		user --进程的拥有者	pr --进程优先级		ni --nice值,负值标识高优先级,正值标识低优先级	virt --进程使用的虚拟内存总量,单位:kb,virt = swap + res		res --进程使用过的未被换出的物理内存的大小,单位:kb res = code + data	s --进程状态.D--不可中断的睡眠状态; R--运行 S --睡眠		T --跟踪/停止	Z --僵尸进程
```



## Linux挂载信息

[参考资料]: https://blog.csdn.net/weixin_39525744/article/details/81093003

```sh
df -h /

# 示例
Filesystem      Size  Used Avail Use% Mounted on
/dev/vdb1      1008G  452G  506G  48% /mnt
```



## ll -lh  命令信息解析

```sh
ll -lh
# 信息示例
# 第一个字符 d 代表文件信息,后面的字符三个为一组 rwx(文件所有者具有的权限) rw-(文件所有的用户组的其他用户所具有的权限) rw-(其他用户组的用户所具有的权限) 代表权限信息
drwxrw-rw- 2 root root 4.0K Oct 15 14:48 log
-rwxrw-rw- 1 root root  23K Jul  9 14:34 tght_def.docx
-rw-r--r-- 1 root root 114K Jul  9 14:34 tght_def.xml

#第一个字符所代表的信息
# -: 表示普通文件
# d:表示目录
# l:表示文件链接
# p:表示管理文件
# b:表示块设备文件
# c:表示字符设备文件
# s:表示套接字文件

#后面字符的权限信息
# r:read 	读权限  	使用数字表示为:4
# w:write 	写权限 	使用数字表示为:2
# x:execute 执行权限 	使用数字表示为:1



```



## nginx启动

### 找nginx的路径

[参考资料]:https://blog.csdn.net/erdfty/article/details/89919513

```sh
ps -ef|grep nginx

#示例如下 第三行为nginx的启动信息
nginx    25689 28966  0 14:53 ?        00:00:00 nginx: worker process
root     27412 23912  0 15:21 pts/2    00:00:00 grep --color=auto nginx
root     28966     1  0 Oct14 ?        00:00:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf

#nginx 的执行文件
/usr/sbin/nginx
#nginx 的配置文件
/etc/nginx/nginx.conf
```



### 重新启动nginx

```sh
./ust/sbin/nginx -s reload
```



### 配置文件信息解析

[参考资料]:https://www.runoob.com/w3cnote/nginx-setup-intro.html

```sh
# 运行的用户
user  nginx;
# 工作进程数
worker_processes  1;

#全局日志定义类型,日志从低到高依次为 debug| info | notice | warn | error | crit 
error_log  /var/log/nginx/error.log warn;
#记录主进程的id文件
pid        /var/run/nginx.pid;

#连接上限,单个进程允许的最大连接数量
events {
    worker_connections  1024;
}

#设定http服务器,利用它的反向代理功能提供负载均衡支持
http {
	#文件扩展名与文件类型映射表
    include       /etc/nginx/mime.types;
    #默认的文件类型
    default_type  application/octet-stream;
    
    #日志的格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
	
	#记录那些用户,页面,用户浏览器的IP和其他访问信息
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```



```sh
server {
    listen       80;
    server_name  appehn.agribigdata.com.cn;
    add_header X-Frame-Options SAMEORIGIN;
    location / {
        root   /usr/share/nginx/appehn;
        index  index.html index.htm;
    }   
        
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }   
}   
server {
    listen       443;
    ssl on;      
    ssl_certificate /etc/nginx/ssl/2854574__agribigdata.com.cn.pem;
    ssl_certificate_key /etc/nginx/ssl/2854574__agribigdata.com.cn.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    server_name  appehn.agribigdata.com.cn;
    
    location / {
        root   /usr/share/nginx/appehn;
        index  index.html index.htm;
    }   
    
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}
```



## 快速使用历史命令

[参考资料]:https://blog.csdn.net/qq_37860634/article/details/87365776

```sh
# ctrl + r 快速搜索历史命令

```

<img src="https://raw.githubusercontent.com/lilight49/markdown_photo/master/linux_213132.png" height=200 align="left">





## awk 使用解析

```sh

```























