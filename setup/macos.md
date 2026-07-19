# macOS — OpenCode + Claude Code Setup

_Status: DONE_

## Requirements
- macOS 13 Ventura or newer
- Homebrew (`https://brew.sh`)
- Node.js 20+ (`brew install node`)
- Git (`brew install git`)
- GitHub CLI (`brew install gh`) + `gh auth login`

## 1. OpenCode

```bash
curl -fsSL https://opencode.ai/install | bash
```

Verify:
```bash
opencode --version
```

## 2. Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Verify:
```bash
claude --version
```

First run triggers OAuth.

## 3. venesusai.lol API Config

Production endpoint: `https://venesusai.lol/v1`. Get your key at https://venesusai.lol/auth/discord.

### OpenCode — `~/.config/opencode/config.json`
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

### Claude Code — `~/.zshrc`
```bash
export ANTHROPIC_BASE_URL=https://venesusai.lol
export ANTHROPIC_API_KEY=sk-YOUR-KEY
export ANTHROPIC_MODEL=anthropic/claude-opus-4-8
```

## 4. Orca CLI

```bash
curl -fsSL https://get.orca.dev/install | bash
```

Verify:
```bash
orca --version
orca status
```

Mandatory skill auto-load: prepend the description line of `~/.agents/skills/orca-cli/SKILL.md` with `Automatically loaded at the start of every session...`.

## 5. Test Commands

```bash
# venesusai.lol connectivity
curl -s -m 10 -H "Authorization: Bearer sk-YOUR-KEY" https://venesusai.lol/v1/models

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

## SSH (optional)
```bash
# ~/.ssh/config
Host mc
    HostName 89.47.113.13
    Port 2222
    User root
    IdentityFile ~/.ssh/mc_server
    StrictHostKeyChecking no
```
