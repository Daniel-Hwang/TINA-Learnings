# GitHub CLI Parsing and Environment Refresh

**Date:** 2026-04-12
**Tags:** #TINA-Learning #Automation #GitHub #CLI

## 📖 Background
While attempting to automate a GitHub issue comment using the `gh` CLI, I encountered repeated command failures and "command not found" errors, despite the tool being installed and a valid token being provided.

## 🪤 The Trap (What went wrong)
1.  **Argument Parsing Ambiguity:** When attempting to pass a long, multi-line string directly as the `--body` argument, the `gh` CLI (and the underlying shell) misidentified the number of arguments, leading to the error: `accepts 1 arg(s), received 2`. This is a common issue when special characters or newlines in a string confuse the command-line parser.
2.  **Environment Path Lag:** After installing `gh` via Chocolatey, the command was not immediately available in the current execution context because the `PATH` environment variable had not been refreshed in the active shell session.

## 🛠️ The Fix (How it was solved)
1.  **File-based Input (The "Golden Rule"):** Instead of passing the body text as a command-line string, I wrote the content to a temporary file (`temp_body.txt`) and used the `-F` flag: `gh issue comment 18 -F temp_body.txt`. This completely bypasss shell parsing issues and is the most robust way to handle complex, multi-line text.
2.  **Manual PATH Refresh:** I used a PowerShell command to manually reload the `PATH` from the machine and user environment settings within the session: `$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")`.

## 💡 Lesson Learned
When interacting with CLI tools that handle large, complex, or multi-line text blocks, **always prefer file-based input (`-F` or `--body-file`) over direct command-line strings**. This avoids shell-specific parsing errors and encoding traps. 

Additionally, when installing new software via a package manager in a persistent session, remember that the `PATH` may need a manual refresh before the tool becomes accessible.

---
*Logged locally by TINA's Learning Loop*
