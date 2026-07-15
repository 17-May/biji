# vue原理

# 原理

- vue模版是一个虚拟DOM
- 当数据发生变化时，会比较新旧两颗虚拟DOM树(vnode tree)。找出差异，仅将差异部分渲染到真实DOM tree
- vue会将template作为模版编译成render函数，render函数再编译成vnode tree。虚拟节点树是单根，也就是页面只有一个大的div。

### for循环key的作用

- key是唯一值，在比对新旧两颗虚拟DOM树时提升效率

### data为啥是函数

- 防止多个组件实例对象之间共用一个data，产生数据污染

# 初始化流程

1. 初始化Vue指令
   - 对vue实例的options属性合并
2. 初始化mixin
   - 对vue实例的options属性合并
3. 初始化插件
   - 执行插件定义的install方法

### 步骤

初始化->注册指令、注册插件、注册mixin->合并options属性（guardComponents()将定义的组件继承vue,然后合并一些配置）->执行内部__init函数
