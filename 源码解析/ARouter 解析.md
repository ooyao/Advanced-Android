# ARouter介绍

# ARouter 的技术方案

## 两个 SDK

### API SDK

面向运行期、为开发者提供 API 支持

### Compiler SDK

面向编译期

Router Processor：处理路由路径

Interceptor Processor：拦截器

Autowire Processor：自动装配



## 页面自动注册

ARouter 尽可能少的使用反射。自动注册流程如下：

1. 编译期处理被注解标注的类，Compiler SDK 负责完成这个任务。
2. 注解处理器扫描出被`Route`标注的类文件。
3. 按照不同种类的源文件进行分类（如拦截器、Group等）。
4. 按照固定的命名格式生成映射文件。
5. 初始化的时候通过固定包名加载映射文件。



## 加载：分组管理，按需加载

在运行期需要将映射关系加载到内存中，这里为了减少内存压力，提升性能采用了分组管理、按需加载。

每个模块都有一个`root`节点，每个`root`节点都会管理整个模块中的`group`节点，每个`group`节点则包含了该分组下的所有页面信息。

`group`是使用`path`字段来设置的，ARouter 规定`path`至少有两层，如`/group/name`，这里第一层就会被充当 `group`名称。统一模块下相同 `group`的页面会被放在一起存放，当其中某一页面被加载的时候整个`group`将会被初始化。



## 拦截器

拦截器会作用于每次跳转，并按照注册顺序执行。



## InstantRun兼容















































































