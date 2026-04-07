# Pucho API Schema

**Summary**: Pucho AI API uses a hybrid flat tool-call format — NOT standard OpenAI/Anthropic nested format.
**Tags**: #api #tool-call #streaming #pucho-ai
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Tool Definition Format (flat, not nested)

```json
{
  "type": "function",
  "name": "tool_name",
  "description": "What this tool does",
  "inputSchema": {
    "type": "object",
    "properties": {
      "param": { "type": "string", "description": "..." }
    },
    "required": ["param"]
  }
}
```

**Key differences from standard:**
- `inputSchema` (not `parameters`)
- `type` field required at top level
- Flat structure (no `function` wrapper)

## Streaming Endpoint

```
POST https://api.pucho.ai/api/v1/tool-call/chat-stream_office
Headers: X-api-key: <key>
Body: { "model": "...", "prompt": [...], "tools": [...] }
```

## SSE Event Types

| Event | Payload |
|---|---|
| `answer` | Streamed text tokens |
| `tool-call` | `{ toolCallId, toolName, input }` |
| `tokens` | Usage stats with `provider` and `model` |
| `done` | Stream complete |

## Tool Result Submission

```json
{ "role": "tool", "tool_call_id": "...", "content": "..." }
```

## Preferred Models (cost-optimized)

- **DeepSeek V3** — default for most tasks
- **Kimi K2 / K2.5** — complex reasoning, long context
- **Claude (via API)** — when quality is critical, not cost-sensitive

## Related Notes

- [[pucho-ai-platform]]
- [[pucho-office-excel]]
