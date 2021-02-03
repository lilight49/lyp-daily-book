# redis使用lock锁

```java
public static String getAccessToken(int tryCount) {//使用递归循环的次数
        // 获取AccessToken
        AccessToken accessToken = (AccessToken) getRedisClient().get(RedisConsts.WX_MINI_ACCESS_TOKEN);
        try {
            if (accessToken == null) {
                //获取lock锁
                long lock = getRedisClient().setnx(RedisConsts.WX_MINI_ACCESS_TOKEN_LOCK, RedisConsts.WX_MINI_ACCESS_TOKEN_LOCK);
                if (lock > 0) {
                    //防止死锁,获取到锁同时获取到accessToken后不立即返回，accessToken可能为空
                    getRedisClient().expireString(RedisConsts.WX_MINI_ACCESS_TOKEN_LOCK, 5);
                    accessToken = getAccessTokenByApi();

                    //删除锁
                    getRedisClient().delStrings(RedisConsts.WX_MINI_ACCESS_TOKEN_LOCK);
                } else if (lock < 1 && tryCount < 3) {
                    //未获取到锁，则等待，然后重试
                    Thread.sleep(500L);
                    return getAccessToken(tryCount++);
                } else {
                    //重试3次后仍未获取到锁，直接返回null
                    return null;
                }
            }
        } catch (Exception e) {
            log.error("\r\n ***********获取accessToken出错：{}", ExceptionUtils.getFullStackTrace(e));
        }
        return accessToken == null ? null : accessToken.getToken();
    }
```



## 封装的Redisclient操作redis库

```java
//jedis 操作redis数据库不设置过期时间时，即为永不过期。redis的删除策略不会清除没有过期时间的
public long setnx(String key, String value) {
        Jedis jedis = null;

        long result = -1;

        try {
            jedis = getJedis();
            result = jedis.setnx(key, value);//调用jedis的操作数据库  result:设置成功，返回 1 。设置失败，返回 0 
        } catch (Exception e) {
            logger.error("[Redis setnx 调用失败]" + ExceptionUtils.getFullStackTrace(e));
        } finally {
            releaseJedisInstance(jedis);
        }
        return result;
    }
```



## jedis操作Redis数据库

```java
    public Long setnx(String key, String value) {
        this.checkIsInMultiOrPipeline();//校验数据是否存在
        this.client.setnx(key, value);//添加数据
        return this.client.getIntegerReply();//返回Redis的响应
    }
```



## Redis分布式锁-基于springboot的RedisTemplate实现

```java
private static final Long SUCCESS = 1L;
 
    /**
     * 获取锁
     * @param lockKey
     * @param value
     * @param expireTime：单位-秒
     * @return
     */
    public static boolean getLock(String lockKey, String value, int expireTime){
        boolean ret = false;
        try{
            String script = "if redis.call('setNx',KEYS[1],ARGV[1]) then if redis.call('get',KEYS[1])==ARGV[1] then return redis.call('expire',KEYS[1],ARGV[2]) else return 0 end end";
 
            RedisScript<String> redisScript = new DefaultRedisScript<>(script, String.class);
 
            Object result = redisTemplate.execute(redisScript, Collections.singletonList(lockKey),value,expireTime);
 
            if(SUCCESS.equals(result)){
                return true;
            }
 
        }catch(Exception e){
 
        }
        return ret;
    }
 
    /**
     * 释放锁
     * @param lockKey
     * @param value
     * @return
     */
    public static boolean releaseLock(String lockKey, String value){
 
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
 
        RedisScript<String> redisScript = new DefaultRedisScript<>(script, String.class);
 
        Object result = redisTemplate.execute(redisScript, Collections.singletonList(lockKey),value);
        if(SUCCESS.equals(result)) {
            return true;
        }
 
        return false;
    }

————————————————
版权声明：本文为CSDN博主「long2010110」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/long2010110/java/article/details/82911168
```







[LUA脚本参考资料]: https://www.cnblogs.com/52fhy/p/9786720.html

```java
package com.project.common.core.utils.redis;

import com.project.common.core.utils.StringUtil;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.data.redis.core.RedisCallback;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisCluster;
import redis.clients.jedis.JedisCommands;

import javax.annotation.Resource;
import javax.security.auth.callback.Callback;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

/**
 * Redis分布式锁
 * @author light
 * @date 2020-06-30 11:08
 **/
@Component
public class RedisDistributedLock {

    private final Logger log = LoggerFactory.getLogger(RedisDistributedLock.class);

    @Resource
    private RedisTemplate<String, Object> redisTemplate;

    //设置的LUA脚本
    public static final String UNLOCK_LUA;

    static{
        StringBuilder lockBulider = new StringBuilder();
        //lua脚本
        lockBulider.append("if redis.call(\"get\",KEYS[1] == ARGV[1]) ");
        lockBulider.append("then ");
        lockBulider.append("    return redis.call(\"del\",KEYS[1]) ");
        lockBulider.append("else ");
        lockBulider.append("    return 0 ");
        lockBulider.append("end ");

        UNLOCK_LUA = lockBulider.toString();
    }

    /**
     * 设置lock锁
     * @author light
     * @date 2020-06-30 11:32
     * @params
     * @param key       lock锁key
     * @param expire    过期时间
     * @return boolean
    **/
    public boolean setLock(String key, long expire){
        try{
            RedisCallback<String> callback = (connection) -> {
                JedisCommands commands = (JedisCommands) connection.getNativeConnection();
                String uuid = UUID.randomUUID().toString();

                return commands.set(key, uuid, "NX", "PX", expire);
            };

            String result = redisTemplate.execute(callback);

            return !StringUtil.isEmpty(result);
        }catch (Exception e){
            log.error("set redis occured an exception", e);
        }

        return false;
    }

    /**
     * 获取lock锁
     * @author light
     * @date 2020-06-30 11:50
     * @params
     * @param key
     * @return java.lang.String
    **/
    public String get(String key){
        try {
            RedisCallback<String> callback = (connection) -> {
              JedisCommands commands = (JedisCommands) connection.getNativeConnection();

              return commands.get(key);
            };

            return redisTemplate.execute(callback);
        }catch (Exception e){
            log.error("get redis occured an exception", e);
        }

        return "";
    }

    /**
     *
     * 释放lock锁
     * @author light
     * @date 2020-06-30 13:14
     * @params
     * @param key
     * @param requestId 通过get()方法回去的值
     * @return boolean
    **/
    public boolean releaseLock(String key, String requestId){
        try {
            List<String> keys = new ArrayList<>();
            keys.add(key);

            List<String> requestIds = new ArrayList<>();
            requestIds.add(requestId);

            RedisCallback<Long> callback = (connection) -> {
                Object nativeConnection = connection.getNativeConnection();

                //区分集群和单机
                //集群模式

                if(nativeConnection instanceof JedisCluster){
                    return  (Long) ((JedisCluster) nativeConnection).eval(UNLOCK_LUA, keys, requestIds);
                }

                //单机模式
                if(nativeConnection instanceof Jedis){
                    return (Long) ((Jedis) nativeConnection).eval(UNLOCK_LUA, keys, requestIds);
                }

                return 0L;
            };

            Long result = redisTemplate.execute(callback);

            return result != null && result > 0;
        }catch (Exception e){
            log.error("release reids loc occured an exception", e);
        }finally {
            // 清楚ThreadLock中的数据,避免内存溢出
//            lockFlag.remove;
        }

        return false;
    }



















}

```

































