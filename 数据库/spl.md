spring 中有一种非常解耦的扩展机制，Spring factories 这种机制实际上是仿照java中的spl扩展机制实现的

### 什么是spl机制
spl的全名为service provider interface 大多数开发人员可能不太熟悉，因为这个是针对厂商或者插件的，在java.util.ServicelLoader 的文档李有比较详细的介绍
简单总结下java spl机制的思想，我们系统里抽象的各个模块，往往有很多不同的实现方案，比如日志模块的方案，xml解析模块，jdbc模块的方案等，面向对象的设计李，我们一般推荐模块之间的基于接口编程,模块之间不对实现类进行硬编码，一旦代码涉及了具体的实现类，就违反了可插拔的原则，如果需要替换一种实现，就需要修改代码，为了实现在模块装配的时候不在程序里动态指明，这就需要一种服务发现机制

java spl 就是提供了这样的一种机制，为某个接口寻找服务的实现的机制，有点类似ioc的思想，就是将装配的控制权转移到程序之外，在模块化设计中这个机制很重要

Spring boot 中的 spl 机制
早Spring boot 中有一种类似的加载机制，它在META-INFO/spring-factories文件中配置接口的实现类的名称，然后在程序中读取这些配置并实例化

这种自定义的spl机制就是SpringBootStarter实现的基础


Spring Factories 