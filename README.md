# TicketFlow / 闪约
Status: docs skeleton ready, backend init next.

### Prerequisites
- JDK 21（IDEA 已配置）
- Maven（项目已带 Wrapper；IDEA 直接导入即可）
- 本地可安装 MySQL、Redis（当前 MVP 不强依赖连接，后续模块会用）

### Run (IntelliJ IDEA)
1. 打开 `ticketflow-app` 模块，配置 **Application** 运行项  
   - Main class: `com.ticketflow.app.TicketflowAppApplication`  
   - Use classpath of module: `ticketflow-app`
2. 运行后控制台看到 `Tomcat started on port 8082`

### Self-check
- 健康检查：`http://localhost:8082/api/health` → 期望 `{"status":"UP"}`
- OpenAPI（JSON 合同，供 Apifox 导入）：`http://localhost:8082/v3/api-docs`

> 说明：springdoc 会在运行时自动扫描 `@RestController` 与 `@*Mapping`，默认在 `/v3/api-docs` 暴露 OpenAPI 3 JSON，无需额外配置。

- POST /api/auth/login → {"token":"demo-token"}
- GET /api/me （需 Authorization: Bearer demo-token）→ {"uid":1}




📁 代码结构 / Code Structure（Feature-first + inner layers）

主启动类放在根包，Spring Boot 会从其所在包向下扫描所有组件；这是官方推荐的布局。We keep the main class in the root package so that Spring Boot’s component scan covers all subpackages. 
Home

com.ticketflow.app
│── TicketflowAppApplication          ← 启动类 / Main application (root package)
│
├── common                            ← 跨功能通用 / Cross-cutting
│   ├── api                           ← 如健康检查 / e.g., health check
│   ├── dto                           ← 通用 DTO（分页、统一错误等）/ shared DTOs
│   ├── exception                     ← 全局异常处理 / global exception handling
│   └── util                          ← 通用工具 / utilities
│
├── auth                              ← 登录与鉴权 / Authentication & Authorization
│   ├── api                           ← 控制器 / Controllers
│   ├── dto                           ← 请求/响应模型 / DTOs
│   ├── service                       ← 业务逻辑 / Services
│   └── repo                          ← 数据访问 / Repositories
│
├── events                            ← 场次管理 / Events (sessions)
│   ├── api ├── dto ├── service └── repo
│
├── reservations                      ← 预约下单 / Reservations (orders)
│   ├── api ├── dto ├── service └── repo
│
├── queue                             ← 排队与异步 / Queue & async
│   ├── redis                         ← Redis Streams（消费者组等）/ Redis Streams
│   └── rabbitmq                      ← RabbitMQ（交换器/队列/监听）/ RabbitMQ
│
└── config                            ← 基础配置（Jackson、CORS、插件等）/ app-wide config


为什么这样组织？/ Why this layout?

高内聚、低耦合：同一功能的 Controller/DTO/Service/Repo 聚在一起，查找与修改路径最短；将来要拆微服务也方便。This is the classic package-by-feature style used by many production apps (e.g., Spring PetClinic demonstrates feature-centric organization ideas). 
GitHub

扫描与约定简洁：主类在根包，@SpringBootApplication 的默认扫描即可覆盖所有子包，无需额外 @ComponentScan。Spring 官方文档推荐把主类放在根包之上。 
Home

可演进到模块化：若后续要做更严格的领域模块边界，可引入 Spring Modulith 的模块约束与文档化能力。This keeps a path open to modular monolith practices. 
Home

🧭 命名约定 / Naming Conventions

DTO：*Request / *Response（如 LoginRequest, LoginResponse）。

Controller：放在 api/；方法路径以功能名为前缀（如 /api/auth/*, /api/events/*）。

Service/Repo：仅对外暴露必要的接口；内部实现可用包可见性降低耦合（package-private）。

通用模型放 common/dto；功能专属模型放各自功能包的 dto/ 中（例如 auth/dto/LoginRequest）。

🚀 运行与自测 / Run & Self-check

运行 / Run：IDEA 选择 TicketflowAppApplication，端口（例如）8082。

健康检查 / Health：GET http://localhost:8082/api/health → {"status":"UP"}。

OpenAPI JSON：http://localhost:8082/v3/api-docs（默认路径，无需额外配置 / default JSON endpoint). 
OpenAPI 3 Library for spring-boot
+1

注：如需修改为其它路径，可用 springdoc.api-docs.path 属性。/ You can change it via springdoc.api-docs.path. 
OpenAPI 3 Library for spring-boot

📌 提交信息 & 分支 / Commits & Branches

分支命名建议 / Branch naming: feat/*, fix/*, chore/*, docs/*

提交信息遵循 Conventional Commits（如 feat(auth): add login endpoint）。

PR 描述可用 Closes #<issue number> 自动关闭对应 Issue。/ Use Closes #<issue> in PR description to auto-close the linked issue on merge.
