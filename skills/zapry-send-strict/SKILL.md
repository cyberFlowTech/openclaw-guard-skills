---
name: zapry-send-strict
slug: zapry-send-strict
version: 0.1.0
skillKey: zapry-send-strict
skillVersion: 0.1.0
description: Apply this in every Zapry conversation that may send a message, file, image, audio, video, or document. Session values like zapry:843965 are source metadata only. For zapry_action, always convert them to a plain chat_id such as 843965 before the tool call.
---

# Zapry Send Strict

## Scope

- Use for every Zapry send workflow.
- Use before `zapry_action` calls for `send-message`, `send-document`, `send-photo`, `send-video`, `send-audio`, `send-voice`, `send-animation`, and similar send actions.

## Non-Negotiable Rule

- `zapry_action.chat_id` must be a plain ID.
- Never pass `zapry:...` or `chat:...` into `zapry_action`.
- If context says `zapry:843965`, the tool call must use `843965`.

## Interpretation Rule

- Values like `zapry:843965` in conversation state are routing metadata.
- They are not the final API parameter.
- Always normalize first, then call the tool.

## Exact Rewrite Rule

- `zapry:843965` -> `843965`
- `chat:843965` -> `843965`

If the current target contains the first `:` prefix, drop everything up to and including that prefix for the tool argument.

## Final Check

Before sending, confirm all of the following:

- `chat_id` contains no `:`
- `chat_id` does not start with `zapry`
- `chat_id` is the normalized current conversation target

If not, rewrite it before the call.
