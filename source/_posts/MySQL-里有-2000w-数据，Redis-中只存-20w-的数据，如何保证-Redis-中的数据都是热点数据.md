---
title: MySQL 里有 2000w 数据，Redis 中只存 20w 的数据，如何保证 Redis 中的数据都是热点数据?
date: 2023-02-06 17:42:37
tags: [Redis, 中间件]
categories: 面试
description: redis的数据淘汰策略。
---

Redis存储在内存中的数据升到配置大小时，就进行数据淘汰

使用 allkeys-lru 策略，从数据集（server.db[i].dict）中挑选最近最少使用的数据优先淘汰，即可满足保存热点数据

<!--more-->

## 补充：
Redis 提供 6 种数据淘汰策略：

1.  **volatile-lru（least recently used）**：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
2.  **volatile-ttl**：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
3.  **volatile-random**：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
4.  **allkeys-lru（least recently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是最常用的）
5.  **allkeys-random**：从数据集（server.db[i].dict）中任意选择数据淘汰
6.  **no-eviction**：禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！

4.0 版本后增加以下两种：

1.  **volatile-lfu（least frequently used）**：从已设置过期时间的数据集（server.db[i].expires）中挑选最不经常使用的数据淘汰
2.  **allkeys-lfu（least frequently used）**：当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的 key

