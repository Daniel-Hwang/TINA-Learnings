# 🛠️ 系统与 API 管理
# 🛠️ System & API Management

## ⚠️ 处理 API 频率限制 (429)
## ⚠️ Handling API Rate Limits (429)

### 📝 场景 (Scenario)
尝试对 ClawHub 等云端 API 进行高频操作（例如批量安装大量新技能）时。
When attempting high-frequency operations (e.g., batch-installing many new skills) on cloud APIs like ClawHub.

### 🔍 问题 (Problem)
API 返回 `429 Too Many Requests` 错误，表明当前 IP 已超过允许的请求阈值。如果继续发送请求，只会延长被封禁的时间。
The API returns a `429 Too Many Requests` error, indicating the current IP has exceeded the allowed request threshold. Continuing to send requests will only prolong the lockout period.

### ✅ 最终解决方案：冷却观察与分步执行策略 (Final Solution: The "Cool-down & Task Splitting" Strategy)
1.  **立即停止 (Immediate Cessation)**：一旦检测到 429 错误，应立即停止所有自动化请求。
    The moment a 429 error is detected, all automated requests must be stopped immediately.
2.  **冷却观察 (Cool-down Observation)**：让系统进入“休息期”（例如几小时），等待服务器端的频率限制器重置。
    Allow the system to enter a "cool-down" period (e.g., several hours) to let the server-side rate limiter reset.
3.  **分步拆解 (Task Splitting)**：不要尝试一次性处理庞大的任务，而是将其拆解为多个细小的、独立的“短跑”任务（例如每次处理 100 封邮件），确保每个进程都能在系统设定的超时阈值内完成。
    Instead of tackling massive tasks all at once, break them down into small, discrete "sprints" (e.g., processing 100 emails at a time) to ensure each process completes within the system's timeout threshold.

---
*创建日期 (Created): 2026-04-07*
