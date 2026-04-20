---
name: docx-output-guard
slug: docx-output-guard
version: 0.1.0
skillKey: docx-output-guard
skillVersion: 0.1.0
description: Generate valid Microsoft Word .docx files using the local docx_create tool. Use when the user asks for a Word document, doc file, docx file, or wants a document to be sent or downloaded as .docx. Never create .docx files with write or edit tools.
---

# DOCX Output Guard

## When to Use

- The user asks for a Word document.
- The user mentions `doc`, `docx`, `Word`, `Word 文档`, or requests a downloadable `.docx`.
- The task is to generate content first and then send it as a Word file.

## Required Rule

- Always use `docx_create` to create `.docx` files.
- Never use `write`, `edit`, HTML, Markdown, or plain text to create a file whose extension is `.docx`.

## Workflow

1. Draft the document content in plain text or simple markdown.
2. Call `docx_create` with the title, content, and an output path or filename ending in `.docx`.
3. Use the returned file path for delivery.
4. If the user wants the file sent in chat, call `zapry_action` with `action: "send-document"` and that file path.

## Output Rules

- For `.docx` output, the final artifact must come from `docx_create`.
- If the user asks for Word but the tool call fails, say the Word file generation failed; do not fake a `.docx`.
- Prefer concise, readable headings and bullet lists in the generated content.

## Example

User asks: "帮我生成一个 Word 文档并发给我"

Correct flow:

1. Prepare the content
2. Call `docx_create`
3. Call `zapry_action` `send-document`

Incorrect flow:

- `write report.docx` with Markdown content
- `write report.docx` with HTML content
- Rename `.txt` / `.md` / `.html` to `.docx`
