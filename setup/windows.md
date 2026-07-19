# Windows — OpenCode + Claude Code Kurulumu

_Status: TAMAMLANDI (venesus-c4)_

## Gereksinimler
- Windows 10/11 veya Windows Server 2019+
- PowerShell 5.1+ (yerleşik) veya PowerShell 7
- Node.js 20+ (`winget install OpenJS.NodeJS.LTS`)
- Git (`winget install Git.Git`)
- GitHub CLI (`winget install GitHub.cli`) + `gh auth login`
- 7-Zip (isteğe bağlı, arşiv çıktıları için)

## 1. OpenCode (Windows Native)

OpenCode tek dosya binary; herhangi paket yöneticisi gerekmez.

```powershell
# PowerShell (yönetici gerekmez)
irm https://opencode.ai/install.ps1 | iex
```

Doğrula:
```powershell
opencode --version
```

### WSL2 varyantı (opsiyonel)
Eğer Linux ortamını Windows içinde istiyorsan:
```powershell
wsl --install -d Ubuntu
# WSL içinde:
curl -fsSL https://opencode.ai/install | bash
```

## 2. Claude Code

```powershell
npm install -g @anthropic-ai/claude-code
```

Doğrula:
```powershell
claude --version
```

İlk çalıştırmada tarayıcı oauth açılır; `KaramelliS` GitHub hesabınla login ol.

## 3. 9router API Config

9router production gateway'ine yönlendir. Her iki CLI için de aynı key.

### OpenCode — `~/.config/opencode/config.json` (veya `%USERPROFILE%\.config\opencode\config.json`)
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

### Claude Code — ortam değişkeni (PowerShell profile'a ekle)
```powershell
$env:ANTHROPIC_BASE_URL = "http://89.47.113.13:20128"
$env:ANTHROPIC_API_KEY  = "sk-743245c6e167e4cd-hrtfc5-9a1b0fc0"
$env:ANTHROPIC_MODEL    = "claude-sonnet-5"
```
Kalıcı için:
```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL","http://89.47.113.13:20128","User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY","sk-743245c6e167e4cd-hrtfc5-9a1b0fc0","User")
```

## 4. Orca CLI

Orca Windows installer'ı:
```powershell
winget install Orca.Orca
# veya
irm https://get.orca.dev/install.ps1 | iex
```

Doğrula:
```powershell
orca --version
orca status
```

Skill auto-load (zorunlu): `%USERPROFILE%\.agents\skills\orca-cli\SKILL.md` dosyasının description satırına `Automatically loaded at the start of every session...` ekle.

## 5. Test Komutları

```powershell
# 9router connectivity
curl.exe -s -m 10 -H "Authorization: Bearer sk-743245c6e167e4cd-hrtfc5-9a1b0fc0" http://89.47.113.13:20128/v1/models

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

## SSH (opsiyonel, server erişimi)
```powershell
# ~/.ssh/config
Host mc
    HostName 89.47.113.13
    Port 2222
    User root
    IdentityFile ~/.ssh/mc_server
    StrictHostKeyChecking no
```

Not: 9router'un 20128 portu dışarıdan erişime kapalı (firewall). Localhost'tan çağırılır. Dışarıdan bağlantı için SSH tüneli:
```powershell
ssh -L 20128:127.0.0.1:20128 mc
# sonra http://localhost:20128/v1 kullan
```
