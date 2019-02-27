redis

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