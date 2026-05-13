# Troubleshooting Log: Establishing Stable Feishu Channel & Identity Binding (2026-05-13)

## 🎯 Objective
Establish a reliable, two-way communication channel between the OpenClaw WebChat (Control UI) and the Feishu (Lark) messaging platform, and resolve identity mapping issues to enable automated notifications (e.g., Daily AI News).

## 🔍 The Problem
1. **Silent Failure**: Cron jobs attempting to send notifications via `feishu` were failing with `Channel is unavailable: feishu`.
2. **Identity Confusion**: Manual attempts to send messages to "Danny" or "yhuang" failed because the Feishu API requires a specific `open_id` (e.g., `ou_...`), not a display name.
3. **Metadata Trap**: Interacting with messages labeled with `openclaw-control-ui` metadata led to the false assumption that the content originated from Feishu, when it was actually just a routing label from the web interface.

## 🛠️ The Troubleshooting Journey

### Phase 1: Plugin & Gateway Recovery
* **Discovery**: Checked Gateway configuration and found that while the `feishu` channel was defined, the corresponding plugin was missing.
    * *Error observed*: `plugin not installed: feishu`.
* **Action**: Installed the official plugin using `openclaw plugins install @openclaw/feishu`.
* **Action**: Performed a `gateway restart` to ensure the new plugin was loaded into the runtime.
* **Result**: The `feishu` channel became "available," but targeted messaging still failed due to identity issues.

### Phase 2: Identity Mapping (The "Reverse Search" Method)
* **Obstacle**: The system lacked the mapping between the human name ("Danny") and the Feishu `open_id`.
* **Strategy**: Instead of guessing the ID, I instructed the user to initiate a message *from* the Feishu client. This "Inbound Message" would contain the authoritative `open_id` in its metadata.
* **Action**: User sent a test message from Feishu. 
* **Success**: Captured the `open_id`: `ou_691a4e4bfd3d8e4e978ed7dc94bd7216`.

### Phase 3: Final Verification
* **Action**: Executed a targeted `message` command using the captured `open_id` as the `target`.
* **Result**: Received `ok: true` from the Feishu API. The two-way bridge is now fully operational.

## 🧠 Key Lessons Learned

### 1. The "Metadata Trap" (Crucial!)
* **Warning**: Do not assume a message is from a specific channel just because you are discussing that channel.
* **Rule**: Always inspect the `provider` field in the inbound metadata. If `provider` is `webchat` or contains `openclaw-control-ui`, the message originated from the web interface, NOT the external platform (Feishu/Discord/etc.).

### 2. Feishu API Requirements
* **Rule**: For all targeted messaging, you **must** use the `open_id` (format: `ou_...`). Display names or usernames are not recognized by the Feishu API for direct routing.

### 3. Plugin Lifecycle
* **Rule**: Installing a new communication plugin is a two-step process: `install` + `gateway restart`.

---
*Status: SOLVED ✅*
*Date: 2026-05-13*
