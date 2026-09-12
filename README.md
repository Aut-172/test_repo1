# 个人书评网站

一个基于 Spring Boot 的单体个人书评网站项目。当前仓库处于基础骨架阶段，已完成应用启动、Maven 构建和持续集成配置；书籍、书评及用户交互等业务功能将在后续迭代中实现。

## 快速开始

### 环境要求

- JDK 24
- Maven 3.9+（当前仓库尚未提交 Maven Wrapper）

### 启动与测试

```bash
mvn spring-boot:run
mvn test
```

应用默认使用 Spring Boot 的内嵌 Web 容器启动，应用名为 `test_repo1`。当前没有业务 Controller，因此启动成功后暂不提供书评页面或 REST 接口。

## 文档

完整的项目现状、技术方案、目录说明、运行方式、接口/数据模型规划和迭代路线见 [PROJECT_DOCUMENTATION.md](PROJECT_DOCUMENTATION.md)。

## 当前技术栈

- Java 24
- Spring Boot 3.5.5
- `spring-boot-starter-web`
- Maven
- JUnit 5 + Spring Boot Test
- GitHub Actions（推送和 Pull Request 自动执行 `mvn test`）

## 许可与状态

项目尚未声明开源许可证。当前版本为内部开发基线，业务功能和部署策略待确认后继续推进。
