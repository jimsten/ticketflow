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

<img width="1390" height="1276" alt="image" src="https://github.com/user-attachments/assets/fc2f9795-752f-4f6e-a4cd-30ae65779122" />



