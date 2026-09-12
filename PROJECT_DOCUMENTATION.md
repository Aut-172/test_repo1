# 个人书评网站项目文档

文档版本：v0.1（基于仓库当前代码）  
更新时间：2026-09-12  
项目状态：基础骨架已搭建，业务功能待开发

## 1. 项目概述

本项目是一个面向个人使用的书评网站，目标是集中管理阅读过的书籍，发布个人书评，并以清晰的方式展示书籍信息、评分和阅读观点。项目采用 Spring Boot 单体架构，前后端是否分离、数据存储方案和是否开放注册将在业务实现前确认。

需要特别说明：当前仓库还没有书籍、书评、用户等领域代码，也没有 Controller、Service、Repository 或数据库迁移脚本。本文档中的“规划”章节是建议方案，不代表已实现能力。

## 2. 当前实现范围

### 已实现

- Spring Boot 应用入口：`com.aut172.testrepo1.TestRepo1Application`。
- Maven 项目构建配置，项目坐标为 `com.aut172:test_repo1:0.0.1-SNAPSHOT`。
- Spring Boot Web 依赖，可作为后续 HTTP 接口的基础。
- `contextLoads` 启动上下文测试。
- GitHub Actions 工作流，在 push 和 pull request 时使用 JDK 24 执行 `mvn test`。
- 应用配置 `spring.application.name=test_repo1`。

### 暂未实现

- 书籍和书评的增删改查。
- 用户登录、权限和评论/点赞等互动功能。
- 数据库、ORM、数据库迁移和数据初始化。
- 前端页面、静态资源或 API 文档（OpenAPI/Swagger）。
- 生产环境配置、日志采集、监控和备份策略。

## 3. 技术栈与版本

| 类别 | 当前选择 |
| --- | --- |
| 语言 | Java 24 |
| Web 框架 | Spring Boot 3.5.5 |
| Web 能力 | `spring-boot-starter-web`（内嵌 Web 容器） |
| 构建工具 | Maven |
| 测试 | Spring Boot Test、JUnit 5 |
| 持续集成 | GitHub Actions + Eclipse Temurin JDK 24 |
| 持久化 | 尚未选择 |

版本选择与仓库中的 `pom.xml`、IDE 项目设置及 CI 配置保持一致。升级 Java 或 Spring Boot 时，应同步更新 `pom.xml`、`.github/workflows/maven.yml` 和本文档。

## 4. 代码结构

```text
.
├─ pom.xml                              # Maven 与依赖配置
├─ README.md                            # 项目入口说明
├─ PROJECT_DOCUMENTATION.md             # 本项目文档
├─ PROJECT_DECISIONS.md                 # 初始脚手架决策记录
├─ .github/workflows/maven.yml           # CI：执行测试
└─ src
   ├─ main
   │  ├─ java/com/aut172/testrepo1
   │  │  └─ TestRepo1Application.java   # 应用启动类
   │  └─ resources/application.properties
   └─ test/java/com/aut172/testrepo1
      └─ TestRepo1ApplicationTests.java # 上下文测试
```

业务代码落地后，建议按职责拆分为 `book`、`review`、`user` 等领域包，每个领域内部采用 `controller`、`service`、`repository`、`domain`（或 `entity`）分层，避免所有功能堆积在启动包下。

## 5. 本地开发

### 前置条件

1. 安装并配置 JDK 24，确认 `java -version` 输出为 24。
2. 安装 Maven 3.9 或更高版本，确认 `mvn -version` 使用的是同一 JDK。
3. 在项目根目录执行命令。

### 常用命令

```bash
# 编译并运行测试
mvn test

# 打包（跳过测试可按需使用 -DskipTests，不建议作为提交前验证）
mvn package

# 启动开发服务器
mvn spring-boot:run
```

默认端口为 Spring Boot 的 `8080`。如需临时变更，可使用：

```bash
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8081"
```

当前没有业务端点，启动成功只能说明 Spring 上下文可加载；后续增加 Controller 后，应补充端到端或 MockMvc 测试。

## 6. 建议的业务方案（待确认）

### 6.1 核心领域

- **Book（书籍）**：书名、作者、ISBN、封面、简介、出版信息和标签。
- **Review（书评）**：关联书籍，包含标题、正文、评分、阅读日期、发布状态和创建/更新时间。
- **User（用户）**：首期可仅保留管理员/作者；若开放注册，再扩展角色、密码哈希和账户状态。
- **Tag（标签）**：用于按主题、类型或阅读场景筛选书籍。

首期建议优先实现“公开书评浏览 + 管理员维护书籍和书评”，把注册、点赞、访客评论等高风险或非核心能力后置。

### 6.2 建议的接口草案

接口路径仅为规划，实施时需根据是否前后端分离和认证方案调整。

| 方法 | 路径 | 用途 | 访问范围 |
| --- | --- | --- | --- |
| GET | `/api/books` | 分页、搜索、筛选书籍 | 公开 |
| GET | `/api/books/{id}` | 查看书籍详情及关联书评 | 公开 |
| GET | `/api/reviews` | 分页查看已发布书评 | 公开 |
| GET | `/api/reviews/{id}` | 查看书评详情 | 公开 |
| POST | `/api/books` | 新增书籍 | 管理员 |
| PUT | `/api/books/{id}` | 修改书籍 | 管理员 |
| POST | `/api/reviews` | 创建书评草稿或发布书评 | 管理员 |
| PUT | `/api/reviews/{id}` | 修改书评 | 管理员 |
| DELETE | `/api/reviews/{id}` | 删除或软删除书评 | 管理员 |

统一返回结构、错误码、分页字段和认证方式应在接口开发前形成单独的 API 约定，避免前后端联调时反复修改。

### 6.3 建议的数据模型

最低可行版本可包含以下表：

- `books`：书籍基础信息，`isbn`（如有）建立唯一约束。
- `reviews`：书评正文、评分（建议 1–5 的整数或明确精度的小数）、发布状态及时间字段。
- `tags`：标签名称及规范化名称。
- `book_tags`：书籍与标签的多对多关联。

所有表建议使用明确的主键、创建时间和更新时间；删除策略（物理删除或软删除）需在引入外键关系前确定。数据库类型建议优先考虑 PostgreSQL 或 MySQL，最终选择取决于部署环境。

## 7. 分层与质量约定（建议）

- Controller 只负责参数校验、协议转换和状态码，不承载业务规则。
- Service 负责用例编排、事务边界和权限校验。
- Repository 负责持久化访问，禁止在 Controller 中直接操作数据库。
- 对外响应使用 DTO，避免直接暴露实体和内部字段。
- 对分页、排序、搜索关键字设置上限，避免无界查询。
- 书评正文需要进行长度限制和输出编码；若支持 Markdown/HTML，必须采用白名单过滤。
- 密码只存储强哈希结果（如 Argon2 或 BCrypt），日志中不得记录密码、Token 等敏感信息。
- 新增业务功能时同时补充单元测试和 Web 层测试；提交前至少执行 `mvn test`。

## 8. 配置与环境

当前仅配置应用名，没有数据库或密钥配置。引入外部依赖后，建议按环境拆分：

- `application.properties`：安全的默认值和通用配置。
- `application-dev.properties`：本地开发配置，不提交真实密码。
- `application-prod.properties`：生产参数，通过环境变量或密钥服务注入。

数据库密码、JWT 密钥、第三方服务 Token 等不得写入 Git；应通过环境变量、CI Secret 或部署平台 Secret 管理。

## 9. CI/CD 与发布

现有 `.github/workflows/maven.yml` 会在 push 和 pull request 触发，使用 Temurin JDK 24 和 Maven 缓存执行 `mvn --batch-mode test`。当前 CI 只验证测试，不负责构建镜像或部署。

建议的发布门槛：

1. CI 测试通过。
2. 数据库迁移脚本可重复执行并经过备份/回滚演练。
3. 生产配置通过 Secret 注入，未把敏感信息提交到仓库。
4. 明确运行方式（可执行 JAR、容器或平台服务）及健康检查地址。

## 10. 迭代路线

1. **MVP**：确定数据库和认证边界，实现书籍/书评 CRUD、公开列表和详情、输入校验及基础测试。
2. **可用性**：增加搜索、标签筛选、分页、草稿/发布状态和 Markdown 安全渲染。
3. **体验**：补充响应式页面、封面上传或对象存储、SEO 元信息和 RSS/站点地图（如需要）。
4. **运维**：增加 Actuator 健康检查、结构化日志、备份与恢复流程、容器化和生产 CI/CD。

## 11. 待确认事项

在开始业务开发前，请确认以下决策：

- 是否仅单人管理员写作，还是开放多用户注册？
- 前端采用服务端模板（如 Thymeleaf）还是独立 SPA？
- 数据库选择 PostgreSQL、MySQL，还是先使用 H2 进行原型开发？
- 书评评分采用 1–5 整数还是允许半星/小数？
- 是否需要访客评论、点赞、收藏、导入 ISBN/豆瓣信息等功能？
- 生产部署目标和域名/HTTPS 方案是什么？

确认这些范围后，再补充实体、接口契约、迁移脚本和部署文件，可以减少返工。

