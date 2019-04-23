redis

## 过期机制
redis淘汰过期key分为主动方式和被动方式：
1. 主动方式：当客户端尝试访问他时，key被发现并主动的过期
2. 每隔10秒测试随机 20个Keys检测过期，然后删除过期key，如果存在多余25%的keys过期重复1操作

## 过期时间
Keys  的过期时间使用是Unix 时间戳存储的，即使实例不可用，时间也在流失
例如：设置1000秒过期，服务器时间设置为未来2000秒，则redis认为key过期

通过redis命令可以实现key过期，超时后只有对key执行DEL，set或者getset是才会清除，**即所有改变key的值的操作都会使他清除**。
**rename 操作超时时间会转移到新的Key**


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
