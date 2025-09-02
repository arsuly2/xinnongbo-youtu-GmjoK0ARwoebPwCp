距离 Nacos 发布 3.0 版本已经过去几个月了，中间一直在忙其他事情，所以也没来及升级体验。

刚好最近要做新项目，所以准备使用最新版升级一波，这不升级还不知道，一升吓一跳，这变化也太大了吧？

## 1.升级概览

Nacos 3.0 最大的升级是新增了“AI 中心”，将会支持以下功能：

1. **存量 API 可以快速构建 MCP Server**： Nacos 配置存量 API 描述可以 0 代码的构建成 MCP Server，快速跟进 MCP 协议。
2. 动态提示词 Prompt（尚未发布）。
3. 支持 A2A 协议等（尚未发布）。

![1](https://img2024.cnblogs.com/blog/172074/202509/172074-20250902091456852-1743510999.png)

## 2.安全升级

Nacos 2.0 中面临的一个主要的风险就是 Nacos 所有的 HTTP OpenAPI 均通过统一的端口进行暴露，同时使用了统一的鉴权开关，这使得使用者必须在便捷性和安全性中作出取舍，导致在许多部署的环境中可能存在安全风险。

Nacos 3.0 为了解决这个问题，从 Nacos 的部署架构上作出演进，独立控制台部署，拆分鉴权开关，分类 API 并默认开启控制台及管控类 API 的鉴权：

![2](https://img2024.cnblogs.com/blog/172074/202509/172074-20250902091505176-733871408.png)

同时配合配置加密插件，TLS 传输，来实现 Nacos 3.0 的零信任安全架构：

![3](https://img2024.cnblogs.com/blog/172074/202509/172074-20250902091512375-1081038943.png)

## 3.不再支持JDK8

**Nacos 3.0 不再支持 JDK8 环境** ，它将其依赖的 JDK 版本升级至 17，并将 Spring Boot 版本升级至 3.4.1。

## 4.升级遇到的问题

### 4.1 无法启动

因为 Nacos 3.0 升级了安全策略，**取消了 nacos.core.auth.server.identity.key、nacos.core.auth.server.identity.value 和 nacos.core.auth.plugin.nacos.token.secret.key 的默认值，所以当我们使用 Nacos 3.0 启动时会报错**。

解决方案：进入 config/application.properties 配置 nacos.core.auth.server.identity.key、nacos.core.auth.server.identity.value 和 nacos.core.auth.plugin.nacos.token.secret.key 的默认值，**其中 nacos.core.auth.plugin.nacos.token.secret.key 值需要 32 位 Base64 编码值，如果设置不符合规则启动也会报错**。

> nacos.core.auth.plugin.nacos.token.secret.key 为 Nacos 中生成 JWT 的秘钥，之前使用默认值，会导致伪造 JWT Token 的问题，所以去掉了默认值。

### 4.2 访问不了控制台

当我们费了九牛二虎之力启动了 Nacos 之后，发现使用 localhost:8848 不能访问控制台了。

原来是控制台和 Nacos 核心服务分开了，控制台变成了独立的端口号 8080，所以使用新的地址 localhost:8080 才能访问。

这。。。

为了让原来的服务不报错，所以之前的 8848 确实要给 Nacos 核心服务用，所以控制台就得配置新的端口号了，但你设置默认 8080，是怕我调试本地程序太累吗？（默认本地程序的端口是 8080）

## 小结

版本升级有风险，升级需谨慎。你究竟是要更多的功能、更快的性能，还是要稳定性？这需要根据当前业务场景来评估。我是磊哥，咱们明天再见。

> 本文已收录到我的面试小站 [www.javacn.site](https://github.com)，其中包含的内容有：场景题、SpringAI、SpringAIAlibaba、并发编程、MySQL、Redis、Spring、Spring MVC、Spring Boot、Spring Cloud、MyBatis、JVM、设计模式、消息队列、Dify、Coze、AI常见面试题等。

本博客参考[海盗云机场](https://haidaocloud.com)。转载请注明出处！
