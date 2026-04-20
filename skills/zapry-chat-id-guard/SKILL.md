---
name: zapry-chat-id-guard
slug: zapry-chat-id-guard
version: 0.1.0
skillKey: zapry-chat-id-guard
skillVersion: 0.1.0
description: Enforce strict plain chat_id usage for every Zapry send action. Use whenever sending messages, documents, images, audio, video, voice, animation, or any Zapry action with chat_id. If conversation context shows values like zapry:843965, treat that as source metadata only and strip the prefix before the tool call.
---

# Zapry Chat ID Guard

## When to Use

- The assistant is about to call `zapry_action`.
- The task sends a message, document, image, video, audio, voice, or animation.
- The user asks to send content to the current chat.

## Required Rule

- For `zapry_action`, always pass a plain `chat_id`.
- Do not include prefixes such as `zapry:` or `chat:` in `chat_id`.
- If the current conversation context contains values like `zapry:843965`, strip the prefix first and use `843965`.
- Treat prefixed values in session context, delivery context, history, or previous tool calls as transport metadata, not as the final `chat_id` argument.
- Before every `zapry_action`, normalize once mentally: `zapry:843965 -> 843965`, then pass only the normalized value.
- If you are about to call `zapry_action` with `chat_id` containing `:`, stop and rewrite it to the plain numeric or string ID first.

## Examples

Good:

- `{"action":"send-document","chat_id":"843965","document":"..."}`
- `{"action":"send-message","chat_id":"843965","text":"..."}`
- Context says `to = "zapry:843965"` -> tool call uses `"chat_id":"843965"`

Bad:

- `{"action":"send-document","chat_id":"zapry:843965","document":"..."}`
- `{"action":"send-message","chat_id":"chat:843965","text":"..."}`
- Session shows `lastTo = "zapry:843965"` and the tool call copies it unchanged

## Sending Workflow

1. Determine the current chat target from context.
2. Normalize it to a plain ID by removing `zapry:` or `chat:` prefixes.
3. Call `zapry_action` with the normalized `chat_id`.
4. If sending fails, report the failure briefly without changing the ID format speculatively.

## Pre-Call Check

Before any `zapry_action`, silently verify:

- The `chat_id` field does not contain `:`
- The `chat_id` field does not start with `zapry`
- The `chat_id` field matches the plain current conversation ID after normalization

If any check fails, rewrite the argument before calling the tool.

## Notes

- Do not invent alternate prefixes.
- Do not retry with multiple `chat_id` formats in one reply.
- If the chat target is unknown, ask for clarification instead of guessing.
