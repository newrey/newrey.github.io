---
layout: post
title: Spring.io 源码架构分析
published: true
---

针对spring.io背后的架构原理，抛砖引玉，主要解决以下问题

### 求推荐几个SSH或者springMVC的实例项目源码

### 谁有关于SpringMvc 比较不错的项目

### 如何具备模块设计架构能力，独立完成模块设计和实现

文章不追求高大上，用朴实描述将你实际开发中的问题一一拎清。

- 主角：[sagan](https://github.com/spring-io/sagan "sagan")
- 描述：spring.io 开源项目，即Spring官方网站的项目,其代表了业界的较高水准。不论是初学者还是在ssh/ssi架构下耕作的工程师，都可以从中受益。
- 关键词：Spring MVC, hibernate, spring data jpa, Jade, AOP, Gradle

## 源码本地启动
```
git clone https://github.com/spring-io/sagan.git
cd sagan
gradlew bootRun
```
Spring Boot带着项目飞：[Spring Boot详情](http://projects.spring.io/spring-boot/ "Spring Boot详情") 


## 项目目录结构
```
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
```

通过settings.gradle可以看出，四个核心模块 'sagan-common', 'sagan-site', 'sagan-indexer', 'sagan-client'

## 为什么这么分？
分析出的原因：
1. 解耦模块，便于协作。
_使程序员关注1-2个文件夹，随着项目进展，甚至无需检出/编译/运行 不相干模块；以单元测试用例，接口做交付。_

2. 便于部署和分布式集群。
_client可部署cdn，也可构建时拷贝到site;common+indexer做单点部署，独立于site，以避免多点部署的重复任务；common+site做分布式部署，处理并发请求业务，做负载均衡更自由；数据仓库、搜索引擎独立部署，也可使用云托管。_


## sagan-common
- 没有README.md 是一个遗憾。
- 纯JAVA，业务数据结构定义，数据库关系映射，Bean。MVC中的Module

包含业务功能模块：
```
.
|-- blog  [blog](http://spring.io/blog "sprio.io blog module")  文件夹下主要是定义
|   |-- support 该子目录偏重业务逻辑
|-- guides [guides](http://spring.io/guides "sprio.io guides module")
|-- projects [projects](http://spring.io/projects "sprio.io projects module")
|-- serach 搜索功能
|-- support 

```
- Post/PostCategory/PostFormat数据字段：使用了数据绑定机制进行有效性验证，如@NotEmpty  @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm")
- 相关Service：BlogService、MarkDown相关处理Service:从Interface定义到实现implements类...以@Service 标记，Service之间存在互相引用关系。
- JPA：PostRepository 持久化应用Spring Data 封装方案

按业务功能划分与按层划分对比：
```
.
|-- service
|   |-- impl
|   |   |-- DefaultBlogServiceImpl
|   |   |-- DefaultGuidesServiceImpl
|   |   |-- DefaultProjectsServiceImpl
|   |-- BlogService
|   |-- GuidesService
|   |-- ProjectsService
|-- dao
|   |-- impl
|   |   |-- DefaultBlogDaoImpl
|   |   |-- DefaultGuidesDaoImpl
|   |   |-- DefaultProjectsDaoImpl
|   |-- BlogDao
|   |-- GuidesDao
|   |-- ProjectsDao
```
曾在项目中以上述层结构划分，该方案对比按业务划分的不足点已经很明显，如：
1. PostSummary放service/impl下是否合理？有没有必要为PostSummary写interface？
2. Blog使用新系统，本系统内无需保留时，哪些属于Blog的业务范畴？
3. Dao已经被Spring Data封装好了，还有必要再写一遍？

## sagan-site
涉及到
- MVC中的View 和 Control

@Controller @RequestMapping为页面入口，获取数据，调用“sagan-common” 做数据处理，return 给resources/templates展示结果

- SecurityConfig.java

从build.gradle引入spring security模块对/admin Controller进行拦截
- 搜索引擎服务器Eleasticsearch独立，未连接会导致搜索服务出错
- HealthCheck\RewriteFilter 的拦截

## sagan-client
到前端部分，该部分涉及到页面动态展示效果，如点击，图片展示

- gulp
- jspm
- npm

前端框架和工具繁多，各具特色，此处不展开

## sagan-indexer
Elasticsearch 和数据处理任务

涉及到对页面定期抓取，数据结构化，数据归档，索引处理等任务。

这块是在site\client完善后重点突破的方向，大数据处理、数据挖掘的拓深，数据价值从中体现。项目中这块处理的是基础部分。

## 总结

进一步对项目研究，除了优秀的模块结构划分，细节上的深度处理，足够全面的测试用例，开发运维的全盘考虑，是该项目优秀的关键点。

技术标杆已立于此，实际操作中，工作量与开发团队可利用资源的矛盾、技术点的优先级，何时推动？3个人的团队该怎么去做、30人的团队该怎么去做项目，是考验架构师/项目经理的所在。