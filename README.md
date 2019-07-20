![](https://i.postimg.cc/4xhqKLQj/image.png)

# 服务端开发实践与工程架构

在[某熊的技术之路指北 ☯](https://github.com/wx-chevalier/Developer-Zero-To-Mastery)中我们介绍过，本系列[服务端架构与实践](https://github.com/wx-chevalier/Backend-Series)与[深入浅出分布式基础架构](https://github.com/wx-chevalier/Distributed-Infrastructure-Series)这两个系列承载了笔者在泛服务端开发、运维相关工作中的知识沉淀。

# Nav | 导航

您可以通过以下任一方式阅读笔者的系列文章，涵盖了技术资料归纳、编程语言与理论、Web 与大前端、服务端开发与基础架构、云计算与大数据、数据科学与人工智能、产品设计等多个领域：

- 在 Gitbook 中在线浏览，直接点击链接即可前往对应的 Gitbook 浏览。

| [Awesome Lists](https://ngte-al.gitbook.io/i/) | [Awesome CheatSheets](https://ngte-ac.gitbook.io/i/) | [Awesome Interviews](https://github.com/wx-chevalier/Developer-Zero-To-Mastery/tree/master/Interview) | [Awesome RoadMaps](https://github.com/wx-chevalier/Developer-Zero-To-Mastery/tree/master/RoadMap) | [Awesome-CS-Books-Warehouse](https://github.com/wx-chevalier/Awesome-CS-Books-Warehouse) |
| ---------------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |


| [编程语言理论](https://ngte-pl.gitbook.io/i/) | [Java 实战](https://ngte-pl.gitbook.io/i/go/go) | [JavaScript 实战](https://ngte-pl.gitbook.io/i/javascript/javascript) | [Go 实战](https://ngte-pl.gitbook.io/i/go/go) | [Python 实战](https://ngte-pl.gitbook.io/i/python/python) | [Rust 实战](https://ngte-pl.gitbook.io/i/rust/rust) |
| --------------------------------------------- | ----------------------------------------------- | --------------------------------------------------------------------- | --------------------------------------------- | --------------------------------------------------------- | --------------------------------------------------- |


| [软件工程、算法与架构](https://ngte-se.gitbook.io/i/) | [现代 Web 开发基础与工程实践](https://ngte-web.gitbook.io/i/) | [大前端混合开发与数据可视化](https://ngte-fe.gitbook.io/i/) | [服务端开发模式与高可用架构](https://ngte-be.gitbook.io/i/) | [分布式基础架构](https://ngte-infras.gitbook.io/i/) | [数据科学，人工智能与深度学习](https://ngte-aidl.gitbook.io/i/) | [产品设计与用户体验](https://ngte-pd.gitbook.io/i/) |
| ----------------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- | --------------------------------------------------- | --------------------------------------------------------------- | --------------------------------------------------- |


- 前往 [xCompass https://wx-chevalier.github.io](https://wx-chevalier.github.io/home/#/search) 交互式地检索、查找需要的文章/链接/书籍/课程，或者关注微信公众号：某熊的技术之路。

![](https://i.postimg.cc/3RVYtbsv/image.png)

- 在下文的 [MATRIX 文章与代码矩阵 https://github.com/wx-chevalier/Developer-Zero-To-Mastery](https://github.com/wx-chevalier/Developer-Zero-To-Mastery) 中查看文章与项目的源代码。

| [服务端应用程序开发基础](./服务端基础) | [微服务与云原生](./微服务与云原生) | [深入浅出 Node.js 全栈架构](./Node) | [Spring Boot 5 与 Spring Cloud 微服务实践](./Spring) | [DevOps 与 SRE 实战](./DevOps) | [信息安全与渗透测试必知必会](./信息安全与渗透测试) | [测试与高可用保障](./测试与高可用保障) |
| -------------------------------------- | ---------------------------------- | ----------------------------------- | ---------------------------------------------------- | ------------------------------ | -------------------------------------------------- | -------------------------------------- |


# Preface | 前言

过去数十年间，信息技术的浪潮深刻地改变了这个社会的通信、交流与协作模式，我们熟知的互联网也经历了基于流量点击赢利的单方面信息发布的 Web 1.0 业务模式，转变为由用户主导而生成内容的 Web 2.0 业务模式；在可见的将来随着 3D 相关技术的落地，互联网应用系统所需处理的访问量和数据量必然会再次爆发性增长。并发的增加对我们的后端架构提出了巨大的挑战，要求我们的系统弹性可扩展。常见的系统扩展包括了垂直伸缩与水平伸缩，前者只能通过增加服务器的配置有限度地提升系统的处理能力，而水平伸缩能够仅通过增减服务器数量相应地提升和降低系统的吞吐量；这种分布式系统架构，在理论上为吞吐量的提升提供了无限的可能。因此，用于搭建互联网应用的服务器也渐渐放弃了昂贵的小型机，转而采用大量的廉价 PC 服务器。

![](https://i.postimg.cc/YS9Y9xYy/image.png)

在分布式系统的背景下，企业架构也由早期的单体式应用架构渐渐转为更加灵活的分布式应用架构，经历了单体分层架构、SOA 服务化架构、微服务架构、云原生架构等不同架构模式的变迁。后端开发不再局限于单一技术栈，并且为了应对更加庞大的集群规模，单纯的分布式系统已经难于驾驭，因此技术圈开启了一个概念爆发的时代：SOA、DevOps、容器、CI/CD、微服务、Service Mesh 等概念层出不穷，而 Docker、Kubernetes、Mesos、Spring Cloud、gRPC、Istio 等一系列产品的出现，标志着云时代已真正到来。

本系列着眼于服务端应用层的相关内容，包含了服务端基础、微服务与云原生、DevOps、测试与高可用保障等内容。

[![image.png](https://i.postimg.cc/Y2vPQ05k/image.png)](https://postimg.cc/G91zCL3S)

- UI 交互层：Windows UI、PC Web UI、移动 App UI、微信小程序 UI、摄像头视觉识别人机界面、语音交互人机界面；

- 应用层：无数碎片化的、场景化的前台业务应用，如零售、采购、招聘、报销等；

- 业务中台：协同平台（工作流引擎/消息引擎等）、应用组件（聚合支付/电子发票/电子合同/自动报税等等）、集成开发平台/定制开发平台/实施平台/运维平台，如零售中台、人力中台、财务中台等；

- 数据与算法中台：数据库/NOSQL 数据库、大数据计算平台/数据仓库数据湖/可视化、人工智能 NLP/机器学习、大数据技术平台（如 Hadoop 和 Spark）、AI 技术引擎；

- 中间件：微服务中间件、面向对象技术/组件技术/SOA、服务中间件/微服务中间件技术；

- 基础设施层：云计算 IaaS（服务器、存储、网络、文件系统）、云 IaaS 平台；

![mindmap](https://tva1.sinaimg.cn/large/007DFXDhgy1g4jlvqqryvj30u014o4qp.jpg)

# 版权

<img src="https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg"/><img src="https://parg.co/bDm" />

笔者所有文章遵循 [知识共享 署名-非商业性使用-禁止演绎 4.0 国际许可协议](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh)，欢迎转载，尊重版权。如果觉得本系列对你有所帮助，欢迎给我家布丁买点狗粮(支付宝扫码)~

![default](https://i.postimg.cc/y1QXgJ6f/image.png)
