# 网络通信

## tcp udp区别

[参考资料]:https://www.cnblogs.com/fundebug/p/differences-of-tcp-and-udp.html

```txt
tcp/ip中最具代表性的传输层协议,分别是TCP和UDP
```

### 一:tcp/ip网络模型

```txt
tcp/ip是互联网相关的各类协议的总称,比如:TCP,UDP,IP,FTP,HTTP,ICMP,SMTP

tcp/ip模型是互联网的基础,他是一系列网路协议的总称,这些协议可以划分为四层:	1:链路层	2:网络层	3:传输层	4:应用层
1:链路层:负责封装和解封装IP报文,发送和接收ARP/RARP报文等
2:网络层:负责路由以及把分组报文发送给目标网络或主机
3:传输层:负责对报文分组和重组,并已TCP或UDP协议格式封装报文
4:应用层:负责向用户提供应用程序,比如HTTP,FTP,Telnet,DNS,SMTP等
```



| osi七层模型 | tcp/ip概念模型 | 功能                                | tcp/io协议族                  |
| ----------- | -------------- | ----------------------------------- | ----------------------------- |
| 应用层      | 应用层         | 文件传输,电子邮件,文件服务,虚拟终端 | TFTP,HTTP,FTP,SMTP,DNS,Telnet |
| 表示层      | 应用层         | 数据格式,代码转换,数据加密          | 没有协议                      |
| 会话层      | 应用层         | 接触或建立与别的节点的联系          | 没有协议                      |
| 传输层      | 传输层         | 提供端对端的接口                    | TCP,UDP                       |
| 网络层      | 网络层         | 为数据包选择路由                    | IP,ICMP,RIP,OSFF,BCP,IGMP     |
| 数据链路层  | 链路层         | 传输有地址的帧以及错误监测功能      | SLIP,CSLIP,PPP,ARP,RARP,ICMP  |
| 物理层      | 链路层         | 已二进制的形式在物理媒体上传输数据  | ISO2110,IEEE802,,IEEE802.2    |



# http解析



## http 错误代码

[参考资料]: https://blog.csdn.net/chenzhengfeng/article/details/83214263

504: geteway timeout



## http content-type 对照表

[参考资料]:https://blog.csdn.net/xiaoyu19910321/article/details/79279364



# 问题解析

```text
问题描述
request:
1:Accept-Encoding: gzip, deflate, br				   请求方式使用gzip对请求数据进行压缩
2:Content-Type: application/x-www-form-urlencoded		请求方式使用body中表单的提交方式

response:
1:Content-Encoding: gzip 							响应使用gzip进行解析
2:Content-Type: application/octet-stream			响应使用二进制流式数据格式
```



```java
package com.gykj.util;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.NameValuePair;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.io.IOUtils;
import org.apache.commons.lang3.StringUtils;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.lang.reflect.Field;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.List;
import java.util.zip.GZIPInputStream;

/**
 * @Descripriton: TODO
 * @author: liyunpeng
 * @date: 2021:05:28 14:04
 **/
public class SanyiHttpUtil {

    /**
     * @methodName SanyiPostSend
     * @description 三一请求发起
     * @param url:http路径地址
     * @param requestParamClass:参数封装类
     * @author liyunpeng
     * @date 2021/5/28 13:55
     * @return: java.lang.String
     **/
    public static <T> String PostSend(String url, T requestParamClass) {
        PostMethod postMethod = new PostMethod(url);
        postMethod.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        postMethod.setRequestHeader("Accept-Encoding", "gzip, deflate, br");

        NameValuePair[] data = getRequestParams(requestParamClass);
        postMethod.setRequestBody(data);

        HttpClient httpClient = new HttpClient();
        try {
            httpClient.executeMethod(postMethod);
            String result = getResponseParams(postMethod.getResponseBodyAsStream());

            return result;
        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }

    /**
     * @methodName getResponseParams
     * @description TODO    Content-Encoding: gzip Content-Type: application/octet-stream  二进制Gzip数据流解析
     * @param inputStreamClass:数据响应信息流
     * @author liyunpeng
     * @date 2021/5/28 13:52
     * @return: java.lang.String
     **/
    public static <T> String getResponseParams(T inputStreamClass) {
        try {
            InputStream input = (InputStream) inputStreamClass;
            //接收的二进制流
            byte[] resBytess = IOUtils.toByteArray(input);

            //解压gzip流
            GZIPInputStream gzipInputStream = new GZIPInputStream(new ByteArrayInputStream(resBytess));

            return new String(IOUtils.toByteArray(gzipInputStream), StandardCharsets.UTF_8);
        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }

    /**
     * @methodName getRequestParams
     * @description body form表单参数封装
     * @param requestParamClass:参数封装泛型类
     * @author liyunpeng
     * @date 2021/5/28 11:12
     * @return: org.apache.commons.httpclient.NameValuePair[]
     **/
    public static <T> NameValuePair[] getRequestParams(T requestParamClass) {
        List<NameValuePair> list = new ArrayList<>();

        Field[] requestParamClassFailds = requestParamClass.getClass().getDeclaredFields();
        for (Field requestParamClassFaild : requestParamClassFailds) {
            requestParamClassFaild.setAccessible(true);
            try {
                if (null == requestParamClassFaild.get(requestParamClass)) {
                    continue;
                }

                String value = requestParamClassFaild.get(requestParamClass).toString();
                String type = requestParamClassFaild.getType().getName();

                if (type.equals(String.class.getTypeName())) {//String
                    if (StringUtils.isBlank(value)) {
                        continue;
                    }
                }else if(type.equals(int.class.getTypeName())){//int
                    if (Integer.parseInt(value) == 0) {
                        continue;
                    }
                }else if(type.equals(List.class.getTypeName())){//list
                    if ("[]".equals(value)){
                        continue;
                    }
                }

                NameValuePair nameValuePair = new NameValuePair(requestParamClassFaild.getName(), value);
                list.add(nameValuePair);

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        NameValuePair[] data = list.toArray(new NameValuePair[list.size()]);

        return data;
    }
}

```

