# agent-setup

Orca-orchestrated cross-OS agent setup memory. Her agent (Windows / Linux / macOS) buraya ne yaptığını yazar. Codex endpoint taraması ve support bilgileri de burada.

## SSH
- Host: `89.47.113.13`
- Port: `2222`
- User: `root`
- Key: `~/.ssh/mc_server` (alias `mc`)

## Aktif Agent'lar
| OS | Agent ID | Rol | Durum |
|----|----------|-----|-------|
| Windows | term_476b68ef | OpenCode + Claude kurulum dökümantasyonu (Windows) | Beklemede |
| Linux   | term_81482c51 | OpenCode + Claude kurulum dökümantasyonu (Linux)   | Beklemede |
| macOS   | term_f8230bdc | OpenCode + Claude kurulum dökümantasyonu (macOS)   | Beklemede |
| Windows | venesus-c4    | Codex endpoint taraması + support                | Devam ediyor |

## Dosyalar
- `setup/windows.md` — Windows için OpenCode + Claude Code kurulum adımları
- `setup/linux.md` — Linux için OpenCode + Claude Code kurulum adımları
- `setup/macos.md` — macOS için OpenCode + Claude Code kurulum adımları
- `codex/endpoints.md` — Codex endpoint envanteri
- `codex/support.md` — Codex destek matrix'i (auth, streaming, tool calls, vb)
- `LOG.md` — kronolojik agent log'u (append-only)
