# 2026-05-20: GitHub Repository Compliance and Automation

## 📌 Context
During this session, I focused on hardening the U20-F50 repository by implementing compliance measures (disclaimers) and standardizing community feedback through automated updates to the README.md and GitHub Issue Templates.

## 🧠 Key Lessons Learned

### 1. Defensive Maintenance & Compliance (合规性防御维护)
- **Challenge**: Handling discussions related to sensitive/potentially illegal topics (e.g., IMEI tampering) on public GitHub issues.
- **Lesson**: Instead of outright refusal, use a **'Recovery-First' stance**. Clearly state that guidance for illegal/restricted modifications will NOT be provided, but troubleshooting for *recovering* a broken state (e.g., modem/baseband recovery) is permitted.
- **Action**: Implemented a bilingual **Disclaimer** in the README.md and a mandatory checklist in the **Issue Template** to set clear expectations for contributors.

### 2. Automated Documentation Hardening (自动化文档加固)
- **Challenge**: Updating multi-file documentation (README, Issue Templates) across branches and ensuring content consistency.
- **Lesson**: Use targeted scripts (Python/Bash) to perform **exact string insertion** (e.g., after specific headings like '### 中文') rather than simple appending. This prevents messy formatting and allows for precise placement of legal disclaimers.
- **Action**: Developed a script to automate the addition of bilingual disclaimers and standardized issue templates.

### 3. Tooling & Environment Pitfalls (工具与环境陷阱)
- **Challenge**: Encountering PowerShell command parsing errors (e.g., && operator ambiguity, redirection issues) when executing complex one-liners.
- **Lesson**: In Windows/PowerShell environments, avoid complex multi-command chains using &&. Instead, use semicolons ; for sequential execution or, preferably, encapsulate logic in a **Python script** for maximum reliability and cross-platform stability.

## ✅ Best Practices
- **Standardize Feedback**: Use Issue Templates to force users to provide Model, Firmware, and Baseband info upfront. This reduces the 'back-and-forth' cycle.
- **Explicit Disclaimer**: Place disclaimers in both the README.md (for visibility) and the Issue Template (for procedural compliance).
- **Script Over CLI**: For multi-step repo maintenance, always prefer a dedicated script over shell-heavy one-liners to ensure idempotent and error-resistant updates.
