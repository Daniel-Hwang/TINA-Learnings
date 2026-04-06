# 📧 邮件自动化 (Himalaya & SMTP)
# 📧 Email Automation (Himalaya & SMTP)

## 🚀 稳健的邮件通信：从命令行转向 Python
## 🚀 Robust Email Communication: Moving from CLI to Python

### 📝 场景 (Scenario)
自动化流水线需要可靠地发送通知或管理邮件。
The automation pipeline needs to send notifications or manage emails reliably.

### 🔍 问题 (Problem)
第三方命令行工具 `himalaya` 在处理关键任务时不够稳定，遇到了以下问题：
The third-party CLI tool `himalaya` proved to be unstable for mission-critical tasks, encountering:
1.  **解析崩溃 (Parsing Panics)**：在处理复杂的原始邮件头或多字节（中文）字符时发生崩溃。
    Crashes when handling complex raw message headers or multi-byte (Chinese) characters.
2.  **编码错误 (Encoding Errors)**：难以通过命令行参数精确控制 UTF-8 编码。
    Difficulty in controlling UTF-8 encoding precisely via command-line arguments.

### ✅ 最终解决方案：Python `smtplib` 方案 (Final Solution: The "Python smtplib" Approach)
对于任何需要高可靠性的任务（如发送状态更新或最终报告），应绕过 CLI 工具，改用专业的 Python 脚本，利用标准的 `smtplib` 和 `email.mime` 模块。
For any task requiring high reliability (such as sending status updates or final reports), bypass the CLI tools and use a dedicated Python script utilizing the standard `smtplib` and `email.mime` modules.

**为什么有效 (Why this works):**
*   **原生 UTF-8 支持 (Native UTF-8 Support)**：Python 对多字节字符的处理既原生又可靠。
    Python handles multi-byte characters natively and reliably.
*   **协议精准控制 (Protocol Precision)**：可以直接控制 SSL/TLS 握手和 SMTP 指令。
    Allows direct control over SSL/TLS handshakes and SMTP commands.
*   **完善的异常处理 (Robust Error Handling)**：可以通过 `try-except` 模块实现更复杂的重试逻辑和错误报告。
    Allows for sophisticated retry logic and error reporting via `try-except` blocks.

**实现模式 (Implementation Pattern):**
```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

def send_reliable_email(subject, body):
    msg = MIMEMultipart()
    msg['Subject'] = subject
    msg.attach(MIMEText(body, 'plain'))
    # 使用 SMTP_SSL 连接 465 端口 | Use SMTP_SSL for port 465
    with smtplib.SMTP_SSL('smtp.qq.com', 465) as server:
        server.login('user@qq.com', 'auth_code')
        server.sendmail('user@qq.com', 'receiver@qq.com', msg.as_string())
```

---
*创建日期 (Created): 2026-04-07*
