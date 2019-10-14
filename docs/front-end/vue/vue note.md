# vue2.x 

``` js 中被定义为 falsy (即， 除了false，0，“”，null，undefined和NaN 外)
```

## 疑问
1. 自定义事件到底是否区分大小写？v-on 和@有区别吗？亲测事件名称区分大小写

## lodash 



## 

## class 与style 

### 在组件中使用class，不会覆盖模板中存已存在的类（class)
[官方文档](https://cn.vuejs.org/v2/guide/class-and-style.html)

使用v-bind:class|style 内容时，value值必须是model类型，即是{{xxx}}类型内容

```
1.
<my-component v-bind:class="{ active: isActive }"></my-component>

2.
new Vue({
data:{
    isActive：true
}
})

```

一处代码是html使用，在二出出现flume，isActive 必须是data内对象


## 数组更新检测
数组可以对常规js的数组函数检测数据：
- push
- pop
- reverse
- shift
- unshift
- splice
- sort

### 不检测方法
- filter
- concat
-  slice
以上方法不检测数组响应式。但是由于是结构相同可以直接替换原数组达到响应式效果

除以上情况以外，一下两种特殊情况操作数组也不会发生响应式变化
1. items[indxe]=xxx
2. imtes.length=newLength
针对以上两种情况可以使用**Vue.set(arrays,index,newVuel)** 和 Array.prototype.splice。
给数组设定新长度，也可以通过splice方法实现响应式

[官方说明](https://cn.vuejs.org/v2/guide/list.html#%E6%95%B0%E7%BB%84%E6%9B%B4%E6%96%B0%E6%A3%80%E6%B5%8B)

## 对象更新检测注意事项
对象新增字段是直接使用obj.newFilde时vue是无法响应式。
只能通过以下语法完成
```
//method 1
Vue.set(obj,key,vaule);

//method 2  vm.$set 实例方法，它只是全局 Vue.set 的别名：
vm.$set(obj,key,vauel)
```

同时添加多个字段：
```
this.obj=Object.assign({},this.obj,{
    newFiled:vue,
    newFiled2:vue2
    .....
    })
```

[官方说明](https://cn.vuejs.org/v2/guide/list.html#%E5%AF%B9%E8%B1%A1%E6%9B%B4%E6%94%B9%E6%A3%80%E6%B5%8B%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)

## v-for&组件
### 在组件里使用 v-for，必须有key：v-bind:key指令

### v-for 数据要传递到组件中必须通过props。
原有：
1. 组件有自己独立作用域
2. 降低数组和组件的耦合度

## 事件处理
### v-on 支持方法调用方式

#### 支持js代码调用
```
<div id="app">
 <button v-on:click="counter+=1">add</button>
</div>
```

#### 支持方法名称调用
主要用于无参调用方法
```
<button v-on:click="clickEvnet"></button>
```


#### 支持内联js调用
主要支持含有参数调用
```
<button v-on:click="clickeEvent(param1,param2)"></button>
```

### 通过keyup支持按键事件监听
[官方文档](https://cn.vuejs.org/v2/guide/events.html)

### 2.1 支持系统修饰键
系统修饰键：
1.ctrl
2.alt
3.shift
4.meta（操作系统键：windows 下 windows键）

> 使用修饰键时必须是在修饰键按下的状态，即只有按住修饰键时释放其他按键才会触发keyup.key

### 组件

#### 驼峰 vs kebab-case
> HTML 特性是不区分大小写的。所以，当使用的不是字符串模板时，camelCase (驼峰式命名) 的 prop 需要转换为相对应的 kebab-case (短横线分隔式命名)：

> 事件名称不会自动转换大小写，且必须完全匹配。不存在camelCase 转换 kebab-case 的情况。v-on事件名称会自动全部转成小写，html大小写不明感
建议组件自定义事件名用kebab-case 方式名称 

#### 数据单向流
在父子级组件中，数据流是单向，只能通过prop从父级流向子级。如果希望通过prop来定义子级的初始化数据最好使用prop赋值局部变量

> js中对象和数组引用是指向同一个内存地址(引用类型)，如果在子级中操作prop的对象或者数组可能会破坏父级数据对象

### 自定义组件

#### 自定义组件中使用 v-model
vuejs v-model语法是语法糖效果，下面两段代码一致

```html
<self-component v-model="text" ></self-component>
```

```html
<self-component v-bind:value="text" v-on:input="text=$event"></self-component>
```

> 即:v-model 其实是指向子组件prop名为value的，且通过触发input事件向父级抛送。父级处理input事件
这也是为什么第二段代码使用`v-on:input="text=$event"`

但是，使用`v-model`语法有默认前提条件:
1. 子组件props 必须value
2. 子组件必须抛送input事件给父级：`v-on:input='$emit('input',$event)'` 

> $emit('input',$event) 这个只是demo极简代码写法，可以根据实际应用写更复杂

>2.2+ 增加 model模块 扩展v-model
可以通过设置model改变默认的props的名称和事件名称

```apple js
model:{
    props:'selfProp',
    event:'selfEvent'
}
```



#### 简写函数
在只想bind和update时触发异常可用这昂处理
```
Vue.directvie('directive',function(el,binding){
  //todosomething
})
```

# vue-router

## 基础标签
> 注入路由成功，可以通过this.$router 或者$router访问当前路由对象

1. 路由导航 通过传入to 熟悉导入到指定路由
```
<router-link to="path"> name</router-link>
```
2. 路由出口。路由匹配到的组件在这里渲染
```
<router-view></router-view>
```

## 嵌套路由

嵌套路由可在模板中渲染

>嵌套子路由不能以`/`开头，否则被认为是root目录
[官方文档](https://router.vuejs.org/zh-cn/essentials/nested-routes.html)

## 编程导航

可以通过$route.push(...) 实现路由效果，同理<router-link to="></router-link>

> 相同路由不同参数需要使用 beforeRouteUpdate 来响应这个变化

>注意：如果提供了 path，params 会被忽略

## 路由命名
1. 路由对象提供name属性，可以通过name路由
```
<router-link :to="{name:'name',params:{paramName:paramVaue}}"></router-link>
```
> 注：这里router-link 标签是:to 和路径不同多一个冒号。配置参数的时候注意如果是非数字需要引号包起来

2. 对于嵌套路由使用命名方式路由时，如果**选择父级名称路由不会渲染子集路由**，如果需要渲染子集路由，则直接:to后面写入子集的路由名称.

## 命名视图
即在标签router-view 属性中设置name字段。当需要在同级中展示多个视图且不是嵌套时候，需要命名视图。例如布局页面。默认name='default'。
一个视图使用一个组件渲染，对于多个视图在一个路由中就需要多个组件渲染.代码模板：
```
new VueRouter({
routes:[
    {
    path:'',
    components:{
        default:componentName1,
        viewName2:componentName2,
        ......
        }
    }
]
})

```
## 路由组件传参
在组件中使用$route会使组件和路由耦合度高，不利于组件复用
如果路由对象props设置为true,则route.params将设置为组件属性

props 支持三种类型：字符串，对象，函数模式
字符串
```
props:['id']
```
对象:
```
props:{xx:xxx}
```
函数：
```
props:(route)=>{}
```

## 导航守卫
> 参数或者查询改变不会触发导航守卫

# issues

### vue data 三种写法区别
1.
```
data:{
 field1:'',
 ....
 fieldn:''
}

```

2.
```
data(){
    return {}
}
```

3.
```
data:function(){
    return {

    }
}
```

### template 相互切换模板使用相同元素
不明白这里为什么是相同元素，没有设定name和id
[官方文档](https://cn.vuejs.org/v2/guide/conditional.html)

### 事件修饰符
事件修饰符作用

### 字符串模板定义界定是什么

### props在路由中函数模式，参数route是什么意思

### route history模式

# answer

#### 三种data写法

第三种写法即data必须是函数这样写法主要是在**组件(component)必须要求**，这样做目的为了防止组件共享一个data.必须保障组件是独立
