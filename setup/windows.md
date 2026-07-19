# Windows — OpenCode + Claude Code Setup

_Status: DONE (venesus-c4)_

## Requirements
- Windows 10/11 or Windows Server 2019+
- PowerShell 5.1+ (built-in) or PowerShell 7
- Node.js 20+ (`winget install OpenJS.NodeJS.LTS`)
- Git (`winget install Git.Git`)
- GitHub CLI (`winget install GitHub.cli`) + `gh auth login`

## 1. OpenCode (Windows Native)

Single-file binary, no package manager needed.

```powershell
irm https://opencode.ai/install.ps1 | iex
```

Verify:
```powershell
opencode --version
```

### WSL2 variant (optional)
```powershell
wsl --install -d Ubuntu
# Inside WSL:
curl -fsSL https://opencode.ai/install | bash
```

## 2. Claude Code

```powershell
npm install -g @anthropic-ai/claude-code
```

Verify:
```powershell
claude --version
```

First run opens OAuth in browser; log in with `KaramelliS` GitHub account.

## 3. venesusai.lol API Config

Production endpoint: `https://venesusai.lol/v1`. Get your key at https://venesusai.lol/auth/discord.

### OpenCode — `%USERPROFILE%\.config\opencode\config.json`
```json
{
  "provider": {
    "venesus": {
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "https://venesusai.lol/v1",
        "apiKey": "sk-YOUR-KEY"
      },
      "models": {
        "ds/deepseek-v4-pro":  { "title": "DeepSeek V4 Pro" },
        "zhipu/glm-5.2":       { "title": "GLM-5.2" },
        "xai/grok-4.5":        { "title": "Grok 4.5" }
      }
    }
  }
}
```

### Claude Code — PowerShell profile
```powershell
$env:ANTHROPIC_BASE_URL = "https://venesusai.lol"
$env:ANTHROPIC_API_KEY  = "sk-YOUR-KEY"
$env:ANTHROPIC_MODEL    = "anthropic/claude-opus-4-8"
```
Persist:
```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL","https://venesusai.lol","User")
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY","sk-YOUR-KEY","User")
```

## 4. Orca CLI

```powershell
winget install Orca.Orca
# or
irm https://get.orca.dev/install.ps1 | iex
```

Verify:
```powershell
orca --version
orca status
```

Mandatory skill auto-load: prepend the description line of `%USERPROFILE%\.agents\skills\orca-cli\SKILL.md` with `Automatically loaded at the start of every session...`.

## 5. Test Commands

```powershell
# venesusai.lol connectivity
curl.exe -s -m 10 -H "Authorization: Bearer sk-YOUR-KEY" https://venesusai.lol/v1/models

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

## SSH (optional, server access)
```powershell
# ~/.ssh/config
Host mc
    HostName 89.47.113.13
    Port 2222
    User root
    IdentityFile ~/.ssh/mc_server
    StrictHostKeyChecking no
```

Note: port 20128 (9router internal) is not exposed externally. venesusai.lol is the public API surface.
