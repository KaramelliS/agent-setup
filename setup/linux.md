# Linux — OpenCode + Claude Code Kurulumu

_Status: TAMAMLANDI (venesus-c4)_

## Gereksinimler
- Ubuntu/Debian 22.04+ veya RHEL/Fedora 38+
- Bash 5+ veya Zsh
- cURL, git, Node.js 20+
- GitHub CLI (`apt install gh` veya dnf)

```bash
# Debian/Ubuntu
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash -
sudo apt install -y nodejs git curl gh

# Fedora/RHEL
sudo dnf install -y nodejs git curl gh
```

## 1. OpenCode

```bash
curl -fsSL https://opencode.ai/install | bash
```

Doğrula:
```bash
opencode --version
```

## 2. Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Doğrula:
```bash
claude --version
```

İlk çalıştırmada oauth açılır.

## 3. 9router API Config

### OpenCode — `~/.config/opencode/config.json`
```json
{
  "provider": {
    "9router": {
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "http://89.47.113.13:20128/v1",
        "apiKey": "sk-743245c6e167e4cd-hrtfc5-9a1b0fc0"
      },
      "models": {
        "claude-sonnet-5": { "title": "Sonnet 5 (9router)" },
        "gpt-5.4":          { "title": "GPT-5.4 (9router)" },
        "glm-5.2":          { "title": "GLM-5.2 (9router)" }
      }
    }
  }
}
```

### Claude Code — `~/.bashrc` veya `~/.zshrc`
```bash
export ANTHROPIC_BASE_URL=http://89.47.113.13:20128
export ANTHROPIC_API_KEY=sk-743245c6e167e4cd-hrtfc5-9a1b0fc0
export ANTHROPIC_MODEL=claude-sonnet-5
```

## 4. Orca CLI

```bash
curl -fsSL https://get.orca.dev/install | bash
```

Doğrula:
```bash
orca --version
orca status
```

Skill auto-load: `~/.agents/skills/orca-cli/SKILL.md` description başına `Automatically loaded at the start of every session...` ekle.

## 5. Test Komutları

```bash
# 9router connectivity
curl -s -m 10 -H "Authorization: Bearer sk-743245c6e167e4cd-hrtfc5-9a1b0fc0" http://89.47.113.13:20128/v1/models

# OpenCode
opencode --version
opencode run "echo hello"

# Claude Code
claude --version
claude -p "hello"

# Orca
orca terminal list
orca orchestration inbox
```

## SSH (opsiyonel)
```bash
# ~/.ssh/config
Host mc
    HostName 89.47.113.13
    Port 2222
    User root
    IdentityFile ~/.ssh/mc_server
    StrictHostKeyChecking no
```

9router port 20128 dışarıdan kapalı (firewall). Server üzerinde localhost'tan erişilir. SSH tüneli:
```bash
ssh -L 20128:127.0.0.1:20128 mc
```
