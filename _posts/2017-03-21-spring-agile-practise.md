本文抛砖引玉，主要解决以下问题

## 求推荐几个SSH或者springMVC的实例项目源码
## 谁有关于SpringMvc 比较不错的项目
## 如何具备模块设计架构能力，独立完成模块设计和实现

文章不追求高大上，用最朴实描述将你实际开发中的问题一一拎清。

主角：[sagan](https://github.com/spring-io/sagan "sagan")
描述：spring.io 开源项目，即Spring官方网站的项目,其代表了业界的较高水准。不论是初学者还是在ssh/ssi架构下耕作的工程师，都可以从中受益。
关键词：Spring MVC, hibernate, spring data jpa, Jade, AOP, Gradle

源码本地启动
```
git clone https://github.com/spring-io/sagan.git
cd sagan
gradlew bootRun
```

项目目录结构
.
|-- .settings eclipse配置
|-- gradle gradle构建相关
|-- sagan-client 整站web静态资源，前端
|-- sagan-common 业务数据结构，包含blog、guides、projects、search 及通用工具集
|-- sagan-indexer index构建，一个SpringBootApplication,和site分两个进程分别启动
|-- sagan-site web应用入口，涉及mvc rest为入口的逻辑分发和security
|-- style 开发IDE代码格式统一配置
|-- util 开发过程用到的批处理shell脚本，如做数据重置
|-- wiki 项目wiki页，git子模块

通过settings.gradle可以看出，四个核心模块 'sagan-common', 'sagan-site', 'sagan-indexer', 'sagan-client'

##为什么这么分？
我分析出的原因：
1.解耦模块，便于协作。
使程序员关注1-2个文件夹，随着项目进展，甚至无需检出/编译/运行 不相干模块。
以单元测试用例，接口做交付。
2.便于部署和分布式集群。
client可部署cdn，也可构建时拷贝到site;common+indexer做单点部署，独立于site，以避免多点部署的重复任务；common+site做分布式部署，处理并发请求业务，做负载均衡更自由；数据仓库、搜索引擎独立部署，也可使用云托管。


