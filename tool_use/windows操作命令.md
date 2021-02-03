## 根据端口杀死进程

[参考资料]:https://blog.csdn.net/Andi_c/article/details/80926133



```bat
# 根据端口号查询进程
netstat -ano | findstr 端口号

#根据进程id强制关闭进程
taskkill -PID 进程号 -f
```

