# Redis基础学习笔记（一）

<!--more-->

# Redis 的八种数据结构

## String 字符串

:one: 支持二进制（可存储图片或序列化对象），单个最大512MB。

:two: 一个Key对应一个Value，并且Set之后会覆盖之前所存储的值。

**基本语法**：

```shell
set key value
getrange key start end # 字符串值的子字符（单个字符）
exists key # 判断存在返回1，不存在则返回0
append key value # 追加到value到原值之后
strlen key # 查看字符串长度
incr key 10 # 自增1   incrby key 10 自增10
decr key # 自减1      decrby key 10 自减10
setrange key start vakue # 在Start位置替换为value
setnx key value #如果键存在，则不设置值
mset k1 v1 k2 v2 #设置多个键值
mget k1 k2 #获取多个值
msetnx k1 v1 k2 v2 #要么都成功设置，要么都失败
getset key value #如果已存在Key，先显示旧value再进行替换，如果没有则直接写入
mset a:1:name Scan # 效果如下图所示
```

![VeryCapture_20240710192658](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202407101948289.jpg)

**键值过期**：

```shell
ttl key  #查询过期时间 -1永不过期  -2已过期
expire key time #设置key的过期时间，前提是Key已存在
setex key time value #设置key的过期时间，可以同时设置键值
```



## Hash对象

:information_source: 是一个String类型的field和value的映射表，适合当对象

```shell
hset key field value # 设置单个属性
hmset key field value field value # 设置多个属性
hget  hgetall #一个是查询单个 一个是查询所有
hdel key field #删除hash中指定的值
hlen key #获取长度
hexists key field #判断值是否存在
hvals  #获取所有值 获取所有键
hkey  #获取所有键
hincrby key field 值 #自增自减操作  填数字
hsetnx #存在则不添加
```



## 无序集合

:information_source: 元素没有重复的，都是单个的

```shell
sadd key 元素 #可添加多个元素
scard key #查看元素长度（个数）
smembers key #查看元素
srem key member #移除指定元素（可多个）
srandmember key num #随机抽取num个元素，不填写个数默认抽取一个并且不显示序号
spop key #删除随机元素
smove key newkey value #启动value到newkey
```

```shell
#交集 并集 差集
sdiff k1 k2 # 求差集： k1有的元素、k2中却没有的元素
sinter k1 k2 # 求交集： k1,k2都有的元素
sunion k1 k2 k3# 求并集(包括重复的)： k1 k2 k3 合起来的元素
```

## 有序集合

```shell
zadd key score value #添加值、score值
zrange key #查询值
zrangebyscore key -inf +inf #根据value的score值从小到大排列
zrevrabge key #根据value的score值从大到小排列
zrangebyscore key -inf +inf withscores #查询所有的值包括key
zcard key #查询集合里的元素个数
zrem key 元素# 移除指定元素、可多个
zcount key min max #如果在min 或者 max 前添加 （  则为闭区间
```

>同一个元素可以有多个分数，每个分数对应一个不同的排序位置





## 列表

```shell
lpush key value #左插入数据（可以多个）
rpush key value #右插入数据（可以多个）
lpop #从最左边移除数据
rpop #从最右边移除数据
lrange key start end  #end为-1时 查所有
lindex key 0 #查询指定下标元素
llen key #获取指定列表的元素长度
lrem key 数量 value #当数量为0时，删除所有value
ltrim key start end #截取元素，下标从0开始
rpoplpush key newkey #移除key的最后一个元素，到newkey里
lset key 下标值 newvalue #更新/替换 下标所在的元素 为 newvalue
linsert key after/before value newvalue # 在key的value之前或者之后 添加newvalue
```



## 地理位置

:information_source: 有效的**经度**从-180度到180度，有效的**纬度**从-85.05112878度到85.05112878度

:information_source: 本质上是**有序**集合数据类型，官方做了二次包装

``` shell
geoadd 索引值 经度 纬度 地点  #geoadd city 经度 维度 "漂亮国"
geopos city Beijing Shanghai #查看指定城市的经纬度信息，支持多个
geodis city Beijing Shanghai (可加单位 默认单位米) # 计算两点距离


georadius city 经度 纬度 距离 (可加单位 默认单位米) # 查询指定坐标范围的城市
		可加参数：1.withcoord # 返回范围内城市的元素坐标
				2.withdist #返回与中心店的距离
				3.count #限制返回多少个城市数量
georadiusbymember city 地点 距离 (可加单位 默认单位米) #查询指定城市地点的范围地点城市
```



## 位图(Bitmap)

:information_source: 只有 **0**  和  **1**   两 个 状 态

```shell
setbit key offset 0/1  #设置值为0或1
getbit key offset #查询offset是否为1
bitcount key #默认查询多少个是1
```

> 在上下学、在线状态的场景下实用。

## 基数(数据集)

:information_source: 占用的内存是固定的，2^64个元素，相当于只需要12kb的内存即可。效率极高！

```shell
pfadd key element #添加数据集
pfcount key #统计数据集的元素个数
pfmegre k1 k2 #合并并且去重
```


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/redis%E5%9F%BA%E7%A1%80%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E4%B8%80/  

