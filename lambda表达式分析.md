# lambda表达式 analyze

[参考资料]:https://www.cnblogs.com/CarpenterLee/p/6729368.html



##  interface = (int a, int b) ->{ return a+ b}

```java
//只有一个接口
public interface RedisCallback<T> {
    @Nullable
    T doInRedis(RedisConnection var1) throws DataAccessException;
}
```



```java
//方式1
//对上述接口的实现方式
 RedisCallback<String> callback = (RedisConnection connection/* 传入的参数 */) -> {
   //对接口的具体实现方法
                JedisCommands commands = (JedisCommands) connection.getNativeConnection();
                String uuid = UUID.randomUUID().toString();

                return commands.set(key, uuid, "NX", "PX", expire);
            };


//方式2
//具体的接口实现使用lamda表达式
RedisCallback<String> callback = (connection) -> {
  //对接口的具体实现方法
                JedisCommands commands = (JedisCommands) connection.getNativeConnection();
                String uuid = UUID.randomUUID().toString();

                return commands.set(key, uuid, "NX", "PX", expire);
            };
```



## lambda表达式解析

[参考资料]:https://blog.csdn.net/majishushu/article/details/81123633



























