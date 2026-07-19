# Codex Support Matrix — 2026-07-19

## Codex CLI compatibility on venesusai.lol

| Feature | Support | Note |
|---------|---------|------|
| `/v1/responses` endpoint | NO | 404 — route not implemented |
| `/v1/chat/completions` | YES | OpenAI format, 83 models |
| `/v1/messages` | YES | Anthropic format |
| Tool calling | YES | On every chat model per docs |
| Streaming (SSE) | YES | `stream: true` |
| External access | YES | HTTPS via Cloudflare |
| Auth | API key | `sk-YOUR-KEY` from Discord login |
| Codex-branded models | NO | No `gpt-5-codex`, `gpt-5.1-codex`, etc. |

## Codex CLI config (would-be)

```toml
# ~/.codex/config.toml
model = "ds/deepseek-v4-pro"
base_url = "https://venesusai.lol/v1"
api_key = "sk-YOUR-KEY"
```

**Does NOT work** because Codex CLI calls `POST /v1/responses`, which returns 404.

## Required work to support Codex CLI

1. **Translate proxy** (recommended): deploy a small service at `https://venesusai.lol/v1/responses` that:
   - Accepts the Responses API payload
   - Maps `input` → `messages`
   - Forwards to internal `/v1/chat/completions`
   - Maps response back to Responses format
2. **Expose Codex-style models**: alias `gpt-5-codex` → `ds/deepseek-v4-pro` or similar.
3. **Test**: `codex --model gpt-5-codex "hello"` against venesusai.lol.
