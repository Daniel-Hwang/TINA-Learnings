# 💻 Windows & PowerShell 环境
# 💻 Windows & PowerShell Environment

## ❌ Windows PowerShell: API 请求编码陷阱
## ❌ Windows PowerShell: API Request Encoding Trap

### 📝 场景 (Scenario)
尝试使用 PowerShell 的 `Invoke-RestMethod` 向 GitHub API 发送包含中文内容的评论。
Attempting to send comments containing Chinese characters to the GitHub API using PowerShell's `Invoke-RestMethod`.

### 🔍 问题 (Problem)
GitHub 接收到的内容变成了乱码（例如：`???????`）。
The content received by GitHub turns into corrupted text (e.g., `???????`).

### 💡 根本原因 (Root Cause)
Windows PowerShell 在发送请求时，默认使用了本地的 ANSI/GBK 编码，而现代 Web API（如 GitHub）严格要求使用 **UTF-8** 编码。当 PowerShell 尝试将多字节字符转换为请求体时，转换过程失效。
When sending requests, Windows PowerShell defaults to the local ANSI/GBK encoding, whereas modern Web APIs (such as GitHub) strictly require **UTF-8** encoding. When PowerShell attempts to convert multi-byte characters into the request body, the conversion fails.

### ✅ 最终解决方案：字节流法 (Final Solution: The "Byte Stream" Approach)
不要依赖 PowerShell 的内置字符串转换。正确的做法是：
Do not rely on PowerShell's built-in string conversion. The correct approach is:

1.  将内容写入一个临时文件，并明确指定使用 `UTF-8` 编码。
    Write the content to a temporary file, explicitly specifying `UTF-8` encoding.
2.  使用 `[System.IO.File]::ReadAllBytes()` 方法以二进制字节流的形式读取该文件。
    Read the file as a raw byte array using the `[System.IO.File]::ReadAllBytes()` method.
3.  将读取到的字节流直接作为 `-Body` 参数传递给 API 请求。
    Pass the resulting byte array directly to the `-Body` parameter of the API request.

**代码示例 (Code Example):**
```powershell
# 将内容写入 UTF-8 临时文件 | Write content to a UTF-8 temp file
$content = '{"body": "你好，世界"}'
$tempFile = [System.IO.Path]::GetTempFileName()
$utf8 = New-Object System.Text.UTF8Encoding $false
[System.IO.File]::WriteAllBytes($tempFile, $utf8.GetBytes($content))

# 以字节流形式读取 | Read as a byte stream
$bytes = [System.IO.File]::ReadAllBytes($tempFile)

# 发送请求 | Send the request
Invoke-RestMethod -Uri $url -Method Post -Body $bytes -ContentType "application/json; charset=utf-8"

# 清理 | Cleanup
Remove-Item $tempFile
```

---
*创建日期 (Created): 2026-04-07*
