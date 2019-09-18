

理清Java混乱的日志体系

	一次偶然的机会，在一个项目工程中发现很多日志jar包，期初以为都要加入classpath，结果发现jar冲突，弄了好久才解决问题，为了知其所以然，系统的学习了一下Java日志体系，才发现原来简单的log.info() 当中水这么深，果然是有人的地方就有江湖，技术框架也不例外。



日志体系历史

道听途说了一段历史，据说是造成了日志体系混乱的原因

1. log4j（作者Ceki Gülcü）出来时就等到了广泛的应用（注意这里是直接使用），是Java日志事实上的标准，并成为了Apache的项目
2. Apache要求把log4j并入到JDK，SUN拒绝，并在jdk1.4版本后增加了JUL（java.util.logging）
3. 毕竟是JDK自带的，JUL也有很多人用。同时还有其他日志组件，如SimpleLog等。这时如果有人想换成其他日志组件，如log4j换成JUL，因为api完全不同，就需要改动代码。
4. Apache见此，开发了JCL（Jakarta Commons Logging），即commons-logging-xx.jar。它只提供一套通用的日志接口api，并不提供日志的实现。很好的设计原则嘛，依赖抽象而非实现。这样应用程序可以在运行时选择自己想要的日志实现组件。
5. 这样看上去也挺美好的，但是log4j的作者觉得JCL不好用，自己开发出slf4j，它跟JCL类似，本身不替供日志具体实现，只对外提供接口或门面。目的就是为了替代JCL。同时，还开发出logback，一个比log4j拥有更高性能的组件，目的是为了替代log4j。
6. Apache参考了logback,并做了一系列优化，推出了log4j2



日志体系组成



各部分调用关系

画了一张图，下图说明了各个组织之前的调用关系





举个小例子，比如客户端用slf4j做门面，用log4j2做日志实现的案例

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    
    import java.io.IOException;
    
    public class Test1 {
    
        public static void main(String[] args) throws IOException {
    
            Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld1");
            logger.info("Hello world.");
            logger.debug("Hello world.");
            logger.error("Hello world.");
        }
    }

这段代码调用必须需要三个组成部分，slf4j日志门面，slf4j到log4j2的日志绑定器以及具体的log4j2日志框架

slf4j日志门面：slf4j-api-1.7.5.jar

slf4j到log4j2的日志绑定器：log4j-slf4j-impl-2.1.jar

具体的log4j2日志框架：log4j-core-2.1.jar、log4j-api-2.1.jar



日志框架

	具体的日志实现，包括log4j,log4j2,jul,locback,其他

日志门面

	提供统一的日志调用接口，面向抽象编程，客户端直接调用门面接口获取日志类,jcl(commons-logging)、slf4j(Simple Logging Facade for JAVA)

绑定器

 客户端调用日志门面接口API获取日志类，需要日志门面返回实际的日志实现类，而日志门面本身不提供日志实现，需要绑定到具体的日志框架，绑定后返回对应日志框架的日志实现类。

 

下表是具体的绑定实现方案



桥接器

	为了让SLF4J承接不同的日志框架（日志框架转化为SLF4J），因而产生了桥接器,可以实现让当前项目中采用的其他日志框架转换为SLF4J

 

下表是具体的桥接实现方案





同时SLF4j也可以转换回其他日志框架,具体桥接方案如下表





总结

网上找的一张图，很好的反映了日志体系中日志门面-日志绑定器-日志框架实现-日志桥接器的关系图：

 

留个小目标，后续如果有时间还将学习一下log4j2和slf4j源码

参考资料：

SLF4J官网

Java 日志框架解析(上) - 历史演进


