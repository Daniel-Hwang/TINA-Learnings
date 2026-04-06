# 💻 Windows & PowerShell Environment

## ❌ Windows PowerShell: API Request Encoding Trap

### 📝 Scenario
Attempting to send Chinese characters (e.g., comments on a GitHub Issue) via PowerShell's `Invoke-RestMethod`.

### 🔍 Problem
The recipient (GitHub API) receives corrupted text like `???????`.

### 💡 Root Cause
Windows PowerShell, by default, uses the local ANSI/GBK encoding when processing strings for HTTP requests. However, modern web APIs (like GitHub's) strictly require **UTF-8** encoding. Standard string conversion in PowerShell often fails to correctly map multi-byte characters to the request body.

### ✅ Final Solution: The "Byte Stream" Approach
Do **not** rely on PowerShell's built-in string-to-JSON conversion for non-ASCII content. Instead:
1.  Write the content to a temporary file using `UTF-8` encoding.
2.  Read the file's raw bytes using `[System.IO.File]::ReadAllBytes($path)`.
3.  Pass the resulting byte array directly to the `-Body` parameter of your API request.

**Example Pattern:**
```powershell
$content = '{"body": "你好，世界"}'
$tempFile = [System.IO.Path]::GetTempFileName()
$utf8 = New-Object System.Text.UTF8Encoding $false
[System.IO.File]::WriteAllBytes($tempFile, $utf8.GetBytes($content))
$bytes = [System.IO.File]::ReadAllBytes($tempFile)

Invoke-RestMethod -Uri $url -Method Post -Body $bytes -ContentType "application/json; charset=utf-8"

Remove-Item $tempFile
```

---

## 🛡️ Avoiding SIGKILL with Task Splitting

### 📝 Scenario
A long-running Python automation script (e.g., scanning thousands of emails and downloading large attachments) is repeatedly terminated by the system with a `SIGKILL` signal.

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
