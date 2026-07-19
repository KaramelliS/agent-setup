# Codex Endpoint Scan — 2026-07-19

## Public endpoint (venesusai.lol)

```
GET  https://venesusai.lol/v1/models
POST https://venesusai.lol/v1/chat/completions    (OpenAI format)
POST https://venesusai.lol/v1/messages             (Anthropic format)
POST https://venesusai.lol/v1/images/generations
POST https://venesusai.lol/v1/video/generations
POST https://venesusai.lol/v1/audio/speech
```

Auth: `Authorization: Bearer sk-YOUR-KEY` or `x-api-key: sk-YOUR-KEY`.

### `/v1/responses` (Codex CLI native endpoint)
- **NOT supported** — returns `Cannot POST /v1/responses` (404).
- Codex CLI cannot target venesusai.lol directly without a proxy that translates `/responses` → `/chat/completions`.

### Model count
83 total. No GPT-5 or Codex-branded models exposed publicly. Open-weights only:
- DeepSeek (v4-pro, v4-flash, chat, reasoner)
- Zhipu GLM (4.5–5.2)
- xAI Grok (3, 4, 4.3, 4.5, code-fast-1)
- Google Gemini (2.5–3.5, flash/pro)
- Meta Llama, Mistral, Qwen, MiniMax, etc.

## Internal gateway (9router, port 20128)

Server-side only (firewall blocks external). 614 models across 9 providers. `/v1/responses` route exists but `codex` providerNode not defined. See server `/root/.9router/db/data.sqlite` (`settings.providerStrategies.codex`).

## Conclusion

Codex CLI cannot use venesusai.lol out-of-the-box. Two paths:
1. **Translate proxy**: small service that accepts `/v1/responses` and forwards to `/v1/chat/completions`. Run on the server behind venesusai.lol.
2. **Add native support**: implement `/v1/responses` in the venesusai.lol backend (Next.js app at port 20128).
