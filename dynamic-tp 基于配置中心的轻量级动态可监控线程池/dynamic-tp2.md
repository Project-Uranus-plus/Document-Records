项目主要功能：

1.实现对运行中线程池参数的动态修改，实时生效

2.实时监控线程池的运行状态，触发设置的报警策略时报警，报警信息推送办公平台

3.定时采集线程池指标数据，配合像 grafana 这种可视化监控平台做大盘监控



一共7个模块：

1.adapter 模块：主要是适配一些第三方组件的线程池管理，目前已经实现的有 SpringBoot 内置的三大 web 容器（Tomcat、Jetty、Undertow）、Dubbo、RocketMq、Hystrix、Grpc 的线程池管理， 后续会接入其他常用组件的线程池管理。

2.common 模块：主要是一些各个模板都会用到的类，解耦依赖，复用代码，大家日常开发中可能也经常会这样做。

3.core 模块：该框架的核心代码都在这个模块里，包括动态调整参数，监控报警，以及串联整个项目流程都在此。

4.example 模块：提供一个简单使用示例，方便使用者参照

5.extension 模块：放一些扩展功能实现，比如基于 redis 的流控扩展、邮件发送扩展、skywalking 上下文传递扩展等

6.logging 模块：用于配置框架内部日志的输出，目前主要用于输出线程池监控指标数据到指定文件

7.starter模块：提供独立功能模块的依赖封装、自动配置等相关。



# 功能特性 ✅

- **代码零侵入**：所有配置都放在配置中心，对业务代码零侵入
- **轻量简单**：基于 SpringBoot 实现，引入 starter，接入只需简单 4 步就可完成，顺利 3 分钟搞定
- **高可扩展**：框架核心功能都提供 SPI 接口供用户自定义个性化实现（配置中心、配置文件解析、通知告警、监控数据采集、任务包装等等）
- **线上大规模应用**：参考[美团线程池实践open in new window](https://tech.meituan.com/2020/04/02/java-pooling-pratice-in-meituan.html)，美团内部已经有该理论成熟的应用经验
- **多平台通知报警**：提供多种报警维度（配置变更通知、活性报警、容量阈值报警、拒绝触发报警、任务执行或等待超时报警），已支持企业微信、钉钉、飞书、邮件报警，同时提供 SPI 接口可自定义扩展实现
- **监控**：定时采集线程池指标数据，支持通过 MicroMeter、JsonLog 日志输出、Endpoint 三种方式，可通过 SPI 接口自定义扩展实现
- **任务增强**：提供任务包装功能，实现 TaskWrapper 接口即可，如 MdcTaskWrapper、TtlTaskWrapper、SwTraceTaskWrapper，可以支持线程池上下文信息传递
- **兼容性**：JUC 普通线程池和 Spring 中的 ThreadPoolTaskExecutor 也可以被框架监控，@Bean 定义时加 @DynamicTp 注解即可
- **可靠性**：框架提供的线程池实现 Spring 生命周期方法，可以在 Spring 容器关闭前尽可能多的处理队列中的任务
- **多模式**：参考 Tomcat 线程池提供了 IO 密集型场景使用的 EagerDtpExecutor 线程池
- **支持多配置中心**：基于主流配置中心实现线程池参数动态调整，实时生效，已支持 Nacos、Apollo、Zookeeper、Consul、Etcd，同时也提供 SPI 接口可自定义扩展实现
- **中间件线程池管理**：集成管理常用第三方组件的线程池，已集成Tomcat、Jetty、Undertow、Dubbo、RocketMq、Hystrix、Grpc 等组件的线程池管理（调参、监控报警）

# 技术架构

![技术架构](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38e4bf71d2c84b7ba67d7059b5432a7e~tplv-k3u1fbpfcp-zoom-1.image)