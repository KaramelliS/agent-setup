# agent-setup

Orca-orchestrated cross-OS agent setup memory. Each agent (Windows / Linux / macOS) logs here what it did. Codex endpoint scan + support info included.

## Endpoint
- Base URL: `https://venesusai.lol/v1`
- Auth: `Authorization: Bearer sk-YOUR-KEY` (get key at https://venesusai.lol/auth/discord)
- OpenAI-compatible + Anthropic-compatible
- 83 models (DeepSeek, Gemini, GLM, Grok, Llama, Mistral, Qwen, image, video, audio)


## Active Agents
| OS | Agent ID | Role | Status |
|----|----------|------|--------|
| Windows | term_476b68ef | OpenCode + Claude setup docs (Windows) | DONE (venesus-c4 fallback) |
| Linux   | term_81482c51 | OpenCode + Claude setup docs (Linux)   | DONE (venesus-c4 fallback) |
| macOS   | term_f8230bdc | OpenCode + Claude setup docs (macOS)   | DONE |
| Windows | venesus-c4    | Codex endpoint scan + support          | DONE |

## Files
- `setup/windows.md` — OpenCode + Claude Code install for Windows ✅
- `setup/linux.md` — OpenCode + Claude Code install for Linux ✅
- `setup/macos.md` — OpenCode + Claude Code install for macOS ✅
- `codex/endpoints.md` — Codex endpoint inventory ✅
- `codex/support.md` — Codex support matrix ✅
- `LOG.md` — chronological agent log (append-only)
