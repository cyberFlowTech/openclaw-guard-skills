---
name: file-send-safety-guard
slug: file-send-safety-guard
version: 0.1.0
skillKey: file-send-safety-guard
skillVersion: 0.1.0
description: Apply safety rules when creating or sending files, documents, attachments, or downloads in chat. Use when the user asks to send a file, attachment, Word/PDF/docx/xlsx/pptx/txt/csv/json/html/zip/png/jpg document, or when file sending may fail. Never expose local filesystem paths in user-facing replies unless the user explicitly asks for the local path for debugging. For Zapry sends, always normalize chat_id to a plain ID before calling zapry_action.
---

# File Send Safety Guard

## When to Use

- The user asks to send a file or attachment in chat.
- The task creates a document and then sends it.
- A file-send tool call succeeds or fails.
- The assistant is about to mention a generated file to the user.

## Hard Rules

- Never reveal absolute local paths in normal user-facing replies.
- Never paste workspace paths, home-directory paths, temp paths, or OpenClaw internal paths when discussing file delivery.
- When a file is ready, refer to it by filename only, for example `report.docx`.
- If sending fails, refuse briefly and explain the delivery failure without exposing the local path.
- Do not offer the local path as a fallback unless the user explicitly asks for the local path, local file location, or debugging details.
- If the delivery tool is `zapry_action`, always strip `zapry:` or `chat:` prefixes from `chat_id` before the tool call.
- Never copy `deliveryContext.to`, `lastTo`, or similar prefixed values directly into `zapry_action.chat_id`.

## Safe Reply Style

- Success: say the file was generated or sent, and mention only the filename.
- Send failure: say the file could not be delivered, name the filename if helpful, and give the high-level reason only.
- Debugging: only reveal the full local path after an explicit user request for path-level debugging.

## Preferred Workflow

1. Generate the file with the correct document tool.
2. If sending through Zapry, normalize the current chat target to a plain `chat_id`.
3. Send it with the correct delivery tool.
4. If delivery succeeds, confirm with filename only.
5. If delivery fails, stop and give a concise refusal or failure message.
6. Do not append the local path automatically.

## Failure Message Pattern

Use a short pattern like:

`发送失败，当前渠道不允许直接投递该附件。`

Optional second sentence:

`如果你要我继续排查，我再查看发送权限或投递方式。`

## Examples

Good:

- `已发给你，文件名：openclaw-集成计划-正式版.docx`
- `发送失败，当前私聊权限不允许直接发送该附件。`

Bad:

- `文件在 /Users/name/.../openclaw-集成计划.docx`
- `发送失败，你去这个本地路径里拿文件`
- `我先把生成目录发给你`
