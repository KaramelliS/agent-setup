# Codex Endpoint Taraması — 2026-07-19

## Mevcut durum

**9router (89.47.113.13:20128)** — production AI gateway
- API key (production): `sk-743245c6e167e4cd-hrtfc5-9a1b0fc0` (name: Production Karamelli)
- `/v1/responses` endpoint **destekleniyor** (Codex CLI'nın kullandığı)
- `providerStrategies.codex` tanımı settings'te var ama **boş**
- Hiçbir `codex` tipinde `providerNode` tanımlı değil
- 614 model, 9 provider node (tümü `openai-compatible` tipinde)

## Codex model envanteri

Upstream provider'larda "codex" isimli modeller var ama iki kaynaktan da çalışmıyor:

| Model | Provider | Upstream | Sonuç |
|-------|----------|----------|-------|
| `cld/gpt-5.1-codex` | cld → 127.0.0.1:9120 (panel) | panel.py `/v1/*` desteklemiyor | 404 |
| `cld/gpt-5.1-codex-mini` | cld | — | 404 |
| `klc/openai/gpt-5.1-codex` | klc → api.kilo.ai | PAID_MODEL_AUTH_REQUIRED | 401 |
| `klc/openai/gpt-5.1-codex-max` | klc | — | 401 |
| `klc/openai/gpt-5.1-codex-mini` | klc | — | 401 |
| `klc/openai/gpt-5.2-codex` | klc | — | 401 |
| `klc/openai/gpt-5.3-codex` | klc | — | 401 |
| `klc/openai/gpt-5-codex` | klc | — | 401 |

## 9router endpoint test sonucu

| Endpoint | Method | Durum |
|----------|--------|-------|
| `/v1/models` | GET | OK (server-side, external timeout — firewall) |
| `/v1/chat/completions` | POST | OK |
| `/v1/responses` | POST | OK ama `codex` provider tanımlı değil → `No active credentials for provider: openai` |
| `/v1/completions` | POST | OK |
| `/v1/embeddings` | POST | OK |
| `/v1/realtime` | — | OK (route var) |
| `/codex`, `/v1/codex` | — | 401 (path tanımlı) |

## Aktif sağlıklı provider'lar

Bunlar `/responses` üzerinden Codex'e route edilebilir:

| Provider | Auth | Test |
|----------|------|------|
| `mistral` (apikey, 5 key) | active | OK |
| `deepseek` (apikey, ~20 key) | active (10+ dead) | OK |
| `aglm` (Apiarium GLM, hf.space) | active | OK |
| `kimi code` | oauth | OK |
| `github` Copilot (2 hesap) | oauth | son kullanım 2026-07-09 |

## Önerilen Codex route stratejisi

1. **Kısa vade**: `klc/openai/gpt-5.1-codex` — Kilo Code paid auth gerekli, vrdons@proton.me hesabıyla login olunmuş ama paid yetki yok. Upgrade gerekli.
2. **Orta vade**: 9router'a yeni bir `codex` providerNode ekle (örn. OpenAI direct API veya GitHub Copilot `gpt-5-codex` modeli). Copilot token'da `agent_mode=1` var, `/responses` desteklemeli.
3. **Proxy tunnel**: External IP'den 20128 timeout. Sadece localhost çalışıyor. Firewall kuralı veya reverse proxy (nginx) gerekli Codex CLI için.

## Dış erişim sorunu

```
curl http://89.47.113.13:20128/v1/models  # 8s timeout
ssh mc 'curl http://127.0.0.1:20128/v1/models'  # OK
```

Port dışarıdan erişilemiyor. Codex CLI uzaktan bağlanamaz. Çözümler:
- `ufw allow 20128/tcp`
- veya Cloudflare Tunnel (settings'te `cloudEnabled: false`)
- veya mevcut 8443'ü HTTPS reverse proxy olarak kullan (docker-proxy bağlı)
