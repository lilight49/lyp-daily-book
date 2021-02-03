# nexus安装和使用

[参考资料]: https://blog.csdn.net/wanghuan1191/article/details/79104751

## cmd 命令启动

``` text
进入nexus.exe 文件所在位置
cd \light\install_app\nexus\nexus-3.5.0-02\bin

启动
nexus.exe /run

访问地址:
localhost:8081
```



## 端口号修改

![](D:\photo\裁剪图\捕获.PNG)

```properties
# 端口号配置
application-port=8081 
application-host=0.0.0.0
nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-requestlog.xml
nexus-context-path=/

# Nexus section
nexus-edition=nexus-pro-edition
nexus-features=\
 nexus-pro-feature
```



## 配置信息

[参考资料]: https://www.cnblogs.com/endv/p/11204704.html



