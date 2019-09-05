redis

## 过期机制
redis将设置过期时间的数据放在一个特殊的字典中，淘汰过期key分为主动方式和被动方式：
1. 主动方式：当客户端尝试访问他时，key被发现并主动的过期
2. 每隔10秒测试随机 20个Keys检测过期，然后删除过期key，如果存在多余25%的keys过期重复1操作

>主动过期注意，在同时删除过多过期数据时可能会导致redis卡顿，主要原因：1.单线程被占用来处理过期数据；2.内存频繁回收内存页也会消耗cpu性能

**解决方案：对于大量过期数据，可以在过期时间上添加随机过期offset，防止同一时间内大量过期数据产生**

### 过期时间
Keys  的过期时间使用是Unix 时间戳存储的，即使实例不可用，时间也在流失
例如：设置1000秒过期，服务器时间设置为未来2000秒，则redis认为key过期

通过redis命令可以实现key过期，超时后只有对key执行DEL，set或者getset是才会清除，**即所有改变key的值的操作都会使他清除**。
**rename 操作超时时间会转移到新的Key**

## 淘汰数据策略（LRU）
当redis内存数据超出物理最大内存时，会与磁盘发生频繁的交换（swap),这样会在影响redis性能，直接导致不能正常使用。为了避免，redis提供maxmemory ，超出则实现各种策略：
1. novicition:停止写请求，删除和读请求正常使用。默认机制
2. volatile-lru:淘汰设置过期时间数据中最近最少使用的数据，未设置过期时间的数据不会被删除;
3. volatile-ttl: ttl值越低的越先被淘汰
4. volatile-random:随机淘汰设置过期数据
5. allkeys-lru: 所有数据最近最少使用删除
6. allkeys-random:所有的数据随机删除

>volatile-xxx 只淘汰设置过期时间的数据，alllkeys-xxx 则是所有数据都可以能被淘汰


## Lua 脚本
自2.6.0版本redis起可以通过`eval` 命令调用Lua脚本
语法：
```
eval scirpt numberkeys key [key...] arg[arg...]
```

redis和Lua之间通过一定规则可以[转化数据类型](http://www.redis.cn/commands/eval.html])
注意事项：
1. lua语法中数字类型只有number类型，在数值转化到redis时浮点数据会丢失小数部分，所以必须通过字符串形式转化lua浮点数据。案例：
```
eval "return 12.332" 0;
```

返回是12
```
eval "return '12.332'" 0;
```

返回是12.332

2. Lua false转化成redis是nil;true转化成redis是1

## 位操作

### setbit
```
SETBIT key offset value
```

**offset 参数必须大于或者等于0，小于2^32(bit映射被限制在512M内)**

###getbit
```
getbit key offset
```
**当offset比字符串长度大，或者key不存在时返回0**

### bitcount
```
bitcount key [start end]
```
? 后面的start 和end 存在疑问：
>setbit bit 0 1
setbit bit 10 1
setbit bit 20 1
bitcount bit     //预期 3
bitcount bit 0 10 //预期2， 但是3

### bitfield
>3.2版本新增功能
后期补充，复杂


## HyperLoglog
HyperLoglog 是可以作为独立统计数据，并且有去重效果，但是存在一定误差（标准误差：0.81%）
HyperLoglog占据12Kb内存空间，