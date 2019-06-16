# zookeeper


## 命令
1. 创建
```
create [-s] [-e] path data acl
```

-s -e :分别表示顺序节点和临时节点，缺省时默认永久节点
path :必须不能以/结尾，且不能没有父节点直接创建子节点
acl:权限控制

由于ZooKeeper中规定了所有非叶子节点必须为**持久节点**

2. 读取
```
ls path [watch]
```

path:指定数据节点路径

. get
```
get path [watch]
```

3. 更新
```
set path data [version]
```

version:版本设定

4. 删除
```
delete path [version]
```

