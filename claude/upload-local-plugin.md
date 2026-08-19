# Upload a local plugin in Claude (Desktop / Cowork)

Claude Desktop / Cowork can **Upload a local plugin** as a `.zip` file.

[Español](upload-local-plugin.es.md)

## 1. Build the ZIP

From the repo root:

```bash
cd plugins/z-deluge-agent
zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Or use the generated file: **`dist/z-deluge-agent.zip`**

> **Important:** the ZIP root must contain `.claude-plugin/` and `skills/`, **not** an extra parent folder.

Correct zip layout:

```
z-deluge-agent.zip
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    ├── zoho-deluge-core/
    ├── zoho-deluge-creator/
    ├── ... (10 skills)
```

## 2. Upload in Claude

1. Open Claude (Desktop / Cowork).
2. **Customize → Personal plugins → Upload local plugin**.
3. Drop **`dist/z-deluge-agent.zip`** (`.zip` only, not `.plugin`).
4. Accept the security warning (plugin not verified by Anthropic).
5. Click **Upload**.

## 3. Enable the plugin

After install, enable **z-deluge-agent** in the personal plugins list.

## 4. Try it

Invoke a skill:

```
/z-deluge-agent:zoho-deluge-creator
```

Or ask in English or Spanish:

- *"Creator Custom API that creates an order from JSON"*
- *"COQL for Contacts with a non-null email"*
- *"Client Script to validate a field on page.onSave"*

The agent replies in the user's language.

## 5. Update after changes

```bash
cd plugins/z-deluge-agent && zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Re-upload the zip (or uninstall the previous version first).

---

## Notes

| Topic | Detail |
|-------|--------|
| Format | `.zip` only — if you have `.plugin`, rename it to `.zip` |
| `docs/` | Not inside the zip; operational content is in each `SKILL.md`. For long reference, also add a Project with `docs/en/` or `docs/es/` |
| GitHub marketplace | Alternative: `/plugin marketplace add benjarmc/z-deluge-agent` in Claude Code |
| 10 skills | See the table in `README.md` |

## Troubleshooting

| Error | Fix |
|-------|-----|
| Upload failed | Confirm `.zip` and that the zip root has `.claude-plugin/` |
| Skills missing | Re-upload; restart Claude |
| Generic answers | Invoke `/z-deluge-agent:zoho-patterns` or a specific skill |
