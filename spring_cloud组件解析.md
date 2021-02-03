# spring_cloud 组件和注解解析

## 注解使用

### @EnableTransactionManagement

[参考资料]: https://blog.csdn.net/u010963948/article/details/79208328
[源码解析]: https://blog.csdn.net/liuqiang211/article/details/88415721

```java
import com.cic.manage.filter.ContextFilter;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.ImportAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration;
import org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;


//在启动类上使用EnableTransactionManagement是开启事务的支持
@EnableTransactionManagement
@EnableDiscoveryClient
@EnableFeignClients(basePackages= {"com.cic.manage.feign"})
@SpringBootApplication(exclude = {MongoAutoConfiguration.class, MongoDataAutoConfiguration.class})
public class DataManageApplication {

    public static void main(String[] args) {
        SpringApplication.run(DataManageApplication.class, args);
    }

    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new ContextFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }

    @Bean
    public CorsFilter corsFilter() {
        final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        final CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true); // 允许cookies跨域
        config.addAllowedOrigin("*");// #允许向该服务器提交请求的URI，*表示全部允许，在SpringMVC中，如果设成*，会自动转成当前请求头中的Origin
        config.addAllowedHeader("*");// #允许访问的头信息,*表示全部
        config.setMaxAge(18000L);// 预检请求的缓存时间（秒），即在这个时间段里，对于相同的跨域请求不会再预检了
        config.addAllowedMethod("OPTIONS");// 允许提交请求的方法，*表示全部允许
        config.addAllowedMethod("HEAD");
        config.addAllowedMethod("GET");// 允许Get的请求方法
        config.addAllowedMethod("PUT");
        config.addAllowedMethod("POST");
        config.addAllowedMethod("DELETE");
        config.addAllowedMethod("PATCH");
        source.registerCorsConfiguration("/**", config);
        return new CorsFilter(source);
    }

}
```





## eureka

[参考资料]: https://www.cnblogs.com/jing99/p/11576133.html

### service服务端配置文档

```yml
spring:
  application:
    name: eureka-service
  freemarker:
    template-loader-path: classpath:/templates/
    prefer-file-system-access: false

#服务端
eureka:
  instance:
    hostname: 127.0.0.1
#  server:
#    enable-self-preservation: false
#    eviction-interval-timer-in-ms: 5000
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

### client客户端的配置文档

```yml
server:
  address: 0.0.0.0
#  生产环境
#  port: 8081
#  测试环境
  port: 7081

  sessionTimeout: 30000
  tomcat:
    uri-encoding: UTF-8
  connection-timeout: 18000000

# ***************** 属性文件配置 开始 ******************************** #
system:
  properties:
    files: application-service.properties,application-app.properties

# ***************** spring配置 开始 ******************************** #
spring:
  cloud:
    inetutils:
      timeout-seconds: 5
  application:
    name: course-service
  profiles:
    active: datasource-dev,common,feign
#    active: datasource-prod,common,feign

# ***************** eureka服务配置 开始 ******************************** #
#客户端
eureka:
  client:
    service-url:
#      生产环境
#      defaultZone: http://127.0.0.1:8761/eureka/
#      测试环境
      defaultZone: http://127.0.0.1:7761/eureka/
  instance:
    prefer-ip-address: true
```



## Nacos

新一代的微服务管理中间件(阿里巴巴)

[参考资料]: https://blog.csdn.net/u010046908/article/details/85260717

### @EnableDiscoveryClient 

```txt
在启动类上添加@EnableDiscoveryClient 开启服务发现支持
```



```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    <version>0.2.1.RELEASE</version>
</dependency>
```



```yml
spring:
  application:
    name: gss-data-manage
#    name: gss-data-manage
  cloud:
    nacos:
    #服务注册
      discovery:
        register-enabled: true
        server-addr: 127.0.0.1:8848
```



```java
@EnableTransactionManagement
@EnableDiscoveryClient
@EnableFeignClients(basePackages= {"com.cic.manage.feign"})
@SpringBootApplication(exclude = {MongoAutoConfiguration.class, MongoDataAutoConfiguration.class})
public class DataManageApplication {

    public static void main(String[] args) {
        SpringApplication.run(DataManageApplication.class, args);
    }
}
```



<font color='yellow'>eureka和Nacos都是微服务的管理组件,在微服务注册之后都是采用feign的方式进行微服务调用</font>



## zuul

[参考资料]: https://blog.csdn.net/qq_29233293/article/details/84891865

### zuul配置文档

```yml
zuul:
  sensitive-headers:
  ignored-headers: Access-Control-Allow-Origin,Access-Control-Allow-Credentials
  routes:
    course-service:
      path: /course/**
      strip-prefix: false
    system-service:
      path: /system/**
      strip-prefix: false
    order-service:
      path: /order/**
      strip-prefix: false
    xywechat-service:
      path: /xyWeChat/**
      strip-prefix: false
  servlet-path: /
  host:
    max-total-connections: 100000
    max-per-route-connections: 100000
    
management:
  security:
    enabled: false
# ***************** 服务超时配置 开始 ******************************** #
course-service:
  ribbon:
    ConnectTimeout: 300000
    ReadTimeout: 500000
    MaxTotalHttpConnections: 500000
    MaxConnectionsPerHost: 100000
    #设置负载策略
    #RandomRule：随机选择一个server（推荐，每个Server配置一样时）
    #BestAvailabl：选择一个最小的并发请求的server，逐个考察Server，如果Server被tripped了，则忽略
    #RoundRobinRule： 轮询index，选择index对应位置的server
    #WeighedResponseTimeRule：如果部分Server强，则选择
    NFLoacBalancerRuleClassName: com.netflix.loadbalancer.RandomRule

system-service:
  ribbon:
    ConnectTimeout: 300000
    ReadTimeout: 500000
    MaxTotalHttpConnections: 500000
    MaxConnectionsPerHost: 100000

order-service:
  ribbon:
    ConnectTimeout: 300000
    ReadTimeout: 500000
    MaxTotalHttpConnections: 500000
    MaxConnectionsPerHost: 100000

xywechat-service:
  ribbon:
    ConnectTimeout: 300000
    ReadTimeout: 500000
    MaxTotalHttpConnections: 500000
    MaxConnectionsPerHost: 100000

# **************** 负载超时配置 ******************************** #
ribbon:
  ReadTimeout: 500000
  SocketTimeout: 300000
  ConnectTimeout: 300000

hystrix:
  command:
    default:
      execution:
      #hystrix 的隔离策略#
        isolation:
          thread:
          #超时时间#
            timeoutInMilliseconds: 800000
```



## hystrix

[参考资料]: https://www.cnblogs.com/duanxz/p/9681470.html

```txt
hystrix.command.default.execution.isolation中的隔离策略分为两种:
thread:线程池隔离
semaphore:信号量隔离
```



```yml
hystrix:
  command:
    default:
      execution:
      #hysrix 的隔离策略#
        isolation:
          strategy: SEMAPHORE
```































