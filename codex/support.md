# Codex Support Matrix — 2026-07-19

## Codex CLI uyumluluğu

| Özellik | Destek | Not |
|---------|--------|-----|
| `/v1/responses` endpoint | EVET | 9router'da route var |
| `codex` provider strategy | EVET | settings'de tanımlı, boş |
| `codex` provider node | HAYIR | hiçbir `codex` tipinde node yok |
| Streaming (SSE) | EVET | `/v1/responses` stream desteği var |
| Tool calling | EVET | provider `capabilities.tools: true` |
| External erişim | HAYIR | firewall engelliyor, sadece localhost |
| Auth | API key | `Authorization: Bearer sk-...` |

## Codex CLI config şablonu (kullanıma hazır değil, route eksik)

```toml
# ~/.codex/config.toml
model = "gpt-5.1-codex"   # cld/ prefix gerekir, çalışmıyor
provider = "openai"
base_url = "http://89.47.113.13:20128/v1"
api_key = "sk-743245c6e167e4cd-hrtfc5-9a1b0fc0"
```

**ÇALIŞMIYOR çünkü**:
1. 9router'da `codex` provider node tanımlı değil
2. External IP'den 20128 timeout
3. `cld/gpt-5.1-codex` modelinin upstream'ı panel.py, o da `/v1/*` döndürmüyor

##Çalışması için gerekenler (öncelik sırasıyla)

1. **Firewall**: `ssh mc 'ufw allow 20128/tcp'` (dış erişim)
2. **Codex provider tanımı**: 9router admin paneline giriş → Add Provider → type `codex` → upstream OpenAI API direct veya Copilot
3. **Model mapping**: `gpt-5.1-codex` → seçilen upstream
4. **Test**: `codex --model gpt-5.1-codex "hello"`
