# 📧 Email Automation (Himalaya & SMTP)

## 🚀 Robust Email Communication: Moving from CLI to Python

### 📝 Scenario
The automation pipeline needs to send notifications or manage emails reliably.

### 🔍 Problem
The third-party CLI tool `himalaya` proved to be unstable for mission-critical tasks. It encountered:
1.  **Parsing Panics**: Crashing when handling complex raw message headers or multi-byte (Chinese) characters.
2.  **Encoding Errors**: Difficulty in managing UTF-8 encoding via command-line arguments.

### ✅ Final Solution: The "Python Smtplib" Approach
For any task requiring high reliability (like sending status updates or final reports), bypass the CLI and use a dedicated Python script utilizing the standard `smtplib` and `email.mime` libraries.

**Why this works:**
*   **Native UTF-8 Support**: Python handles multi-byte characters natively and reliably.
*   **Protocol Precision**: Direct control over SSL/TLS handshake and SMTP commands.
*   **Error Handling**: Granular `try-except` blocks allow for much more sophisticated retry logic and error reporting.

**Implementation Pattern:**
```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

def send_reliable_email(subject, body):
    msg = MIMEMultipart()
    msg['Subject'] = subject
    msg.attach(MIMEText(body, 'plain'))
    # Use SMTP_SSL for port 465
    with smtplib.SMTP_SSL('smtp.qq.com', 465) as server:
        server.login('user@qq.com', 'auth_code')
        server.sendmail('user@qq.com', 'receiver@qq.com', msg.as_string())
```

---

## 🔍 Pagination in Email Scanning

### 📝 Scenario
Scanning a large mailbox for specific files (e.g., invoices) after a certain date.

### 🔍 Problem
Standard API calls often return only the first page of results (e.g., the first 20 or 50 emails), causing the automation to miss relevant data located on later pages.

### ✅ Final Solution: The "Iterative Page Loop" Strategy
Never assume a single API call provides a complete dataset. Implement a loop that explicitly requests subsequent pages until no more data is returned.

**Strategy:**
1.  **Start at Page 1**.
2.  **Request with `--page {n}` and `--page-size {limit}`**.
3.  **Check Response**: If the returned list is non-empty and its length matches the requested limit (or a specific threshold), increment `n` and repeat.
4.  **Terminate** when the response is empty.

**Example Logic (Pseudocode):**
```python
page = 1
all_envelopes = []
while True:
    batch = himalaya_envelope_list(page=page, page_size=100)
    if not batch:
        break
    all_envelopes.extend(batch)
    page += 1
```

---
*Created: 2026-04-07*
