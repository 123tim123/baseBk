---
title: android定时缓存
tags: [android,数据]
---
# 定时缓存
> 给大家介绍一个比好好用轻量级缓存工具Acache,它类似javaEE的ehcache使用非常方便，可以设置自动过期时间


```
ACache mCache = ACache.get(this);
mCache.put("test_key1", "test value");
mCache.put("test_key2", "test value", 10);//保存10秒，如果超过10秒去获取这个key，将为null
mCache.put("test_key3", "test value", 2 * ACache.TIME_DAY);//保存两天，如果超过两天去获取这个key，将为nul
```

> 使用起来就和使用map累死，key-value，第三个参数为是时间参数。  
> 如果设置了时间参数，则数据会将在超过该时间后自动清除  
> 如果没有设置时间参数，则数据会永久保存  
> Acache可以完全代替sharedpreferences 使用，简介方便高效

### [Acache 下载文件](../utils/ACache.java)
