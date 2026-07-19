# macOS — OpenCode + Claude Code + 9router Kurulumu

_Status: TAMAMLANDI — term_f8230bdc_

Gereksinimler: macOS 13+, Node 22+, Homebrew. Apple Silicon ve Intel ikisi de desteklenir.

## 1. OpenCode

Homebrew:

```bash
brew install sst/tap/opencode
```

Yedek kurulum (curl):

```bash
curl -fsSL https://opencode.ai/install | bash
```

Doğrula:

```bash
opencode --version
```

İlk çalıştırmada `~/.config/opencode/opencode.json` oluşturulur.

## 2. Claude Code

Node 22 şart:

```bash
brew install node@22
node --version   # v22.x çıkmalı
```

Claude Code'u global kur:

```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

## 3. 9router API config

Üretim gateway'i: `http://89.47.113.13:20128/v1`, key `sk-743245c6e167e4cd-hrtfc5-9a1b0fc0`.

### OpenCode provider (`~/.config/opencode/opencode.json`)

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "9router": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "9router",
      "options": {
        "baseURL": "http://89.47.113.13:20128/v1",
        "apiKey": "sk-743245c6e167e4cd-hrtfc5-9a1b0fc0"
      },
      "models": {
        "useai/gpt-5-1": { "name": "useai/gpt-5-1" },
        "anthropic/claude-sonnet-4-6": { "name": "anthropic/claude-sonnet-4-6" }
      }
    }
  }
}
```

Model listesini uzaktan çekmek için: `opencode models`.

### Claude Code (`~/.claude/settings.json`)

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "http://89.47.113.13:20128",
    "ANTHROPIC_API_KEY": "sk-743245c6e167e4cd-hrtfc5-9a1b0fc0"
  }
}
```

Claude Code `/v1/messages` kullanır; base URL'ye `/v1` eklemeyin (otomatik eklenir).

## 4. Orca CLI

İndir + kur:

```bash
curl -fsSL https://get.orca.app | bash
```

ya DMG: https://orca.app/download → kur, ilk açılışta CLI PATH'e eklenir.

Doğrula:

```bash
orca --version
orca status
```

Runtime'ı başlat:

```bash
orca open
```

## 5. Test komutları

### 9router connectivity

```bash
# Model listesi (200 + JSON dönmeli)
curl -s -H "Authorization: Bearer sk-743245c6e167e4cd-hrtfc5-9a1b0fc0" \
  http://89.47.113.13:20128/v1/models | head -40

# Chat completion
curl -s -X POST http://89.47.113.13:20128/v1/chat/completions \
  -H "Authorization: Bearer sk-743245c6e167e4cd-hrtfc5-9a1b0fc0" \
  -H "Content-Type: application/json" \
  -d '{"model":"useai/gpt-5-1","messages":[{"role":"user","content":"say hi"}]}'
```

Beklenen: 200 OK, content "hi" veya benzeri.

Not: 89.47.113.13:20128 dışarıdan timeout atıyorsa (firewall), SSH tüneli açın:

```bash
ssh -L 20128:127.0.0.1:20128 mc
# sonra http://127.0.0.1:20128/v1 kullanın
```

### OpenCode

```bash
cd ~/projects/test
opencode
# TUI'da 9router seç, mesaj gönder
```

### Claude Code

```bash
cd ~/projects/test
claude
# "say hi" yaz
```

### Orca

```bash
orca orchestration check    # inbox
orca worktree current       # aktif worktree
```

## Sorun giderme

- **OpenCode model listesi boş**: opencode.json provider bloğu sözdizimi hatalı. `opencode.json` JSON'unu `node -e 'JSON.parse(require("fs").readFileSync(process.env.HOME+"/.config/opencode/opencode.json","utf8"))'` ile doğrula.
- **Claude Code 401**: settings.json `ANTHROPIC_API_KEY` boş/yanlış. Key `sk-7432...` ile başlamalı.
- **9router timeout (curl 8s+)**: dış firewall. SSH tüneli veya `ufw allow 20128/tcp` (server tarafında).
- **`claude` komutu yok**: `npm bin -g` PATH'te değil. `~/.npm-global/bin` veya `/opt/homebrew/bin` ekleyin.
