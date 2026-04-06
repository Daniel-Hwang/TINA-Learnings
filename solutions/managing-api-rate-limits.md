# 🛠️ System & API Management

## ⚠️ Handling 429 Rate Limits

### 📝 Scenario
Attempting to perform high-frequency operations (like batch-installing many new skills) via a cloud API (e.g., ClawHub).

### 🔍 Problem
The API returns `429 Too Many Requests`, indicating that the current IP address has exceeded the allowed request threshold. Continuing to send requests will only prolong the block.

### ✅ Final Solution: The "Cool-down & Observe" Protocol
1.  **Immediate Cessation**: The moment a 429 error is detected, **stop all automated requests immediately**.
2.  **Cool-down Period**: Allow the system to "rest" for a significant period (e.g., several hours) to let the server-side rate limiter reset.
3.  **Incremental Re-entry**: When resuming, do not attempt the same massive batch. Instead, implement a **rate-limited retry mechanism** (e.g., one request every 30-60 seconds).

---

## 🛡️ Avoiding SIGKILL with Task Splitting

### 📝 Scenario
A long-running automation script is repeatedly terminated by the system with a `SIGKILL` signal.

### 🔍 Problem
The system's watchdog mechanism perceives the long-duration, high-resource process as "hung" or "unresponsive" and forcibly terminates it to reclaim resources.

### ✅ Final Solution: The "Sprints" Strategy
Break down the monolithic "marathon" task into a series of "short sprints":
1.  **Decompose the Workflow**: Separate *Scanning*, *Filtering*, and *Downloading* into distinct, lightweight modules.
2.  **Implement Small Batch Processing**: Instead of processing the entire dataset in one loop, process in small, discrete batches (e.g., 100 emails at a time).
3.  **Status Persistence**: Ensure each batch writes its progress to a persistent file (e.g., a JSON or CSV log) so the next "sprint" can pick up exactly where the last one left off.
4.  **Minimize Execution Time per Process**: Keep each individual `exec` or Python call under the system's threshold (e.g., < 5 minutes).

---
*Created: 2026-04-07*
