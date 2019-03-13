---
title: 分布式锁之Redis实现
categories: 
  - 分布式
tags:
  - Java
  - Redis
  - 分布式锁
date: 2018-10-08 13:12:38
---

> 分布式锁的Reids实现，包含新旧两个版本

<!-- more -->

# 分布式锁之Redis实现
## 旧版实现
&emsp;&emsp;实现思路：使用jedis.setnx()命令实现加锁，其中key是锁，value是锁的过期时间。
1. 通过setnx()方法尝试加锁，如果当前锁不存在，返回加锁成功。
2. 如果锁已经存在则获取锁的过期时间，和当前时间比较，如果锁已经过期，则设置新的过期时间，并校验是否当前线程请求锁，若是则返回加锁成功。

```java
public static boolean tryLock(String lockKey, int expireTime) {
    String expireStr = String.valueOf(System.currentTimeMillis() + expireTime);
    // 如果当前锁不存在，返回加锁成功
    if (jedis.setnx(lockKey, expiresStr) == 1) {
        return true;
    }
    // 如果锁存在，获取锁的过期时间
    String currentValue = jedis.get(lockKey);
    if (currentValue != null && Long.parseLong(currentValue) < System.currentTimeMillis()) {
        // 锁已过期，获取上一个锁的过期时间，并设置现在锁的过期时间
        String oldValue = jedis.getSet(lockKey, expireStr);
        // 考虑多线程并发的情况，只有一个线程的设置值和当前值相同，它才有权利加锁
        if (oldValue != null && oldValue.equals(currentValue)) {
            return true;
        }
    }
    // 其他情况，一律返回加锁失败
    return false;
}
```

&emsp;&emsp;但是这种做法存在如下问题：
1. 由于是客户端自己生成过期时间，所以需要强制要求分布式下每个客户端的时间必须同步。 
2. 当锁过期的时候，如果多个客户端同时执行jedis.getSet()方法，那么虽然最终只有一个客户端可以加锁，但是这个客户端的锁的过期时间可能被其他客户端覆盖。
3. 锁不具备拥有者标识，即任何客户端都可以解锁。


## 新版实现
&emsp;&emsp;更为严谨的做法如下：
```java
public class RedisDistributeLock {
    @Autowired
    public Jedis jedis;
    
    private static final String LOCK_SUCCESS = "OK";
    private static final String SET_IF_NOT_EXIST = "NX";
    private static final String SET_WITH_EXPIRE_TIME = "PX";
    private static final Long RELEASE_SUCCESS = 1L;

	/**
     * 尝试获取分布式锁
     * 注意: 获取到的锁不可重入
     *
     * @param lockKey 锁
     * @param requestId 请求标识
     * @param expireTime 超期时间
     * @return 是否获取成功
     */
    @Override
    public boolean lock(String lockKey, String requestId, int expireTime) {
        try (Jedis jedis = getResource()) {
            String result = jedis.set(lockKey, requestId, SET_IF_NOT_EXIST, SET_WITH_EXPIRE_TIME, expireTime);
            if (LOCK_SUCCESS.equals(result)) {
                return true;
            }
        } catch (Exception ex) {
            throw ex;
        }
        return false;
    }

    /**
     * 释放分布式锁
     * 如果锁不存在，返回true
     * 如果锁存在，但是请求标识不对，返回失败
     * 如果锁存在，请求标识正确，返回成功
     *
     * @param lockKey 锁
     * @param requestId 请求标识
     * @return 是否释放成功
     */
    @Override
    public boolean unlock(String lockKey, String requestId) {
        String script = "if (redis.call('EXISTS', KEYS[1]) ~= 1) then return 1 elseif (redis.call('get', KEYS[1]) "
                        + "== ARGV[1]) then return redis.call('del', KEYS[1]) else return 0 end";
        try (Jedis jedis = getResource()) {
            Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
            if (RELEASE_SUCCESS.equals(result)) {
                return true;
            }
        } catch (Exception ex) {
            throw ex;
        }
        return false;
    }
}
```

- 第一个为key，使用key来当锁，因为key是唯一的。
- 第二个为value，传的是requestId，客户端的唯一标志。
- 第三个为nxxx，传NX意思是SET IF NOT EXIST，即当key不存在时，进行set操作；若key已经存在，则不做任何操作；
- 第四个为expx，传PX意思是给这个key加一个过期的设置，具体时间由第五个参数决定。
- 第五个为time，与第四个参数相呼应，代表key的过期时间。

&emsp;&emsp;加锁用了新版的set()方法，通过requestId解决了分布式下不同客户端时间不统一问题，通过超期时间解决了多次getset覆盖问题，通过解锁时判断requestId解决了任何客户端都可以解锁问题。
&emsp;&emsp;解锁的第一行代码，是一个简单的Lua脚本代码，第二行代码，将Lua代码传到jedis.eval()方法里，并使参数KEYS[1]赋值为lockKey，ARGV[1]赋值为requestId。eval()方法是将Lua代码交给Redis服务端执行。

> 这段Lua代码的功能：首先获取锁对应的value值，检查是否与requestId相等，如果相等则删除锁（解锁）。在eval命令执行Lua代码的时候，Lua代码将被当成一个命令去执行，并且直到eval命令执行完成，Redis才会执行其他命令。

&emsp;&emsp;注意一种不合适的解锁方法：
```java
public static void releaseLock(String lockKey, String requestId) {
    // 判断加锁与解锁是不是同一个客户端
    if (requestId.equals(jedis.get(lockKey))) {
        // 若在此时，这把锁突然不是这个客户端的，则会误解锁
        jedis.del(lockKey);
    }
}
```
&emsp;&emsp;这种方式的问题在于如果调用jedis.del()方法的时候，这把锁已经不属于当前客户端的时候会解除他人加的锁。比如客户端A加锁，一段时间之后客户端A解锁，在执行jedis.del()之前，锁突然过期了，此时客户端B尝试加锁成功，然后客户端A再执行del()方法，则将客户端B的锁给解除了。
