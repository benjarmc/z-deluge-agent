# ChatGPT usage guide

This folder adapts **z-deluge-agent** for ChatGPT, which has no Cursor skills (`.cursor/skills/`) or Claude Code plugins (`.claude-plugin/`).

[Español](instructions.es.md)

## Files

| File | Use |
|------|-----|
| [`gpt-system-prompt.md`](gpt-system-prompt.md) | English Instructions for the Custom GPT |
| [`gpt-system-prompt.es.md`](gpt-system-prompt.es.md) | Spanish Instructions |
| [`knowledge-manifest.md`](knowledge-manifest.md) | Which `docs/en/` or `docs/es/` files to upload |

## Platform comparison

| | Cursor | Claude Code | ChatGPT |
|--|--------|-------------|---------|
| Format | `.cursor/skills/` | Plugin marketplace | Custom GPT / Project |
| Auto-load | Yes, on repo open | `/plugin install` | Manual setup |
| Knowledge | Skills + docs | Plugin skills | Upload `.md` to Knowledge |
| 10-domain routing | Automatic | Automatic | Via system prompt |
| Languages | EN/ES (match user) | EN/ES (match user) | EN/ES (match user) |

## Recommended flow

```
1. Create Custom GPT "Zoho Deluge Agent"
2. Paste gpt-system-prompt.md (or .es.md) → Instructions
3. Upload 14 files from docs/en/ OR docs/es/ (see knowledge-manifest)
4. Try: "Write a Creator Custom API that creates an order from JSON"
```

## Tips

- **Creator Deluge:** say "in Zoho Creator" to help routing.
- **Client Script:** JavaScript — the GPT must not mix it with Deluge.
- **Large files:** `deluge/language.md` and `integration-tasks/index.md` are the heaviest.
- **Stale version:** when Zoho ships a new API, update the `.md` in the repo and re-upload Knowledge.

## Source of truth

- `docs/en/` and `docs/es/` — full reference
- `plugins/z-deluge-agent/skills/` — Cursor/Claude skills

When you expand a skill, update the matching `.md` under both `docs/en/` and `docs/es/`, then re-upload to the Custom GPT.
