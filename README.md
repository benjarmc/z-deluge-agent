# Z Deluge Agent

Structured documentation and agent skills for the full Zoho universe: **Deluge**, **Creator**, **CRM Client Scripts**, **COQL**, **Widgets**, **Integration Tasks**, **REST APIs**, and **Analytics SQL**.

Inspired by [zoho-deluge-toolkit](https://github.com/creatorscripts/zoho-deluge-toolkit), extended to CRM, COQL, separate widgets, integration tasks, and multi-product APIs.

**Languages:** English (this file) · [Español](README.es.md)  
Docs: [`docs/en/`](docs/en/) · [`docs/es/`](docs/es/)

## Skills (10 modules)

| # | Skill | Covers |
|---|-------|--------|
| 1 | `zoho-deluge-core` | Deluge language, types, functions, invokeUrl, zoho.* variables |
| 2 | `zoho-deluge-creator` | Forms, pages, subforms, Custom APIs, API v2.1 |
| 3 | `zoho-crm-client-script` | Client-side JavaScript: events, ZDK, Web API, ZRC |
| 4 | `zoho-crm-coql` | CRM v8 COQL: syntax, limits, invokeUrl |
| 5 | `zoho-widgets-crm` | HTML/JS widgets in CRM (ZohoEmbededAppSDK) |
| 6 | `zoho-widgets-creator` | Creator widgets (widgetsdk, zet pack) |
| 7 | `zoho-integration-tasks` | ~260 `zoho.*` tasks across 24 services |
| 8 | `zoho-public-apis` | CRM v8, Books, Desk, OAuth, REST (not deep COQL) |
| 9 | `zoho-analytics-sql` | Analytics SQL, Query Tables, CloudSQL API |
| 10 | `zoho-patterns` | Null safety, API limits, troubleshooting |

Location: `plugins/z-deluge-agent/skills/` and `.cursor/skills/`

Each skill has `SKILL.md` (loaded by the agent; bilingual description; replies in the user's language) and `SKILL.en.md` (full English body).

## Reference docs

```
docs/
├── INDEX.md                     ← bilingual hub
├── en/                          ← English
└── es/                          ← Spanish
    ├── deluge/language.md
    ├── creator/deluge-in-creator.md
    ├── client-script/crm.md
    ├── coql/crm-coql.md
    ├── widgets/crm.md | creator.md
    ├── integration-tasks/index.md
    ├── apis/crm-v8.md | books.md | desk.md | other-products.md
    ├── analytics/sql.md
    └── patterns/production.md
```

## Cursor

Open this repository; Cursor loads `.cursor/skills/` automatically. Ask in English or Spanish — the agent picks the skill and replies in your language.

## Claude Code (plugin)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Claude Desktop / Cowork (local plugin)

**Customize → Personal plugins → Upload local plugin:**

1. Upload **`dist/z-deluge-agent.zip`**
2. Enable **z-deluge-agent**
3. Try: `/z-deluge-agent:zoho-deluge-creator`

Generate the zip:

```bash
cd plugins/z-deluge-agent && zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Full guide: [`claude/upload-local-plugin.md`](claude/upload-local-plugin.md) · [Español](claude/upload-local-plugin.es.md)

> Only `.zip` (not `.plugin`). The zip root must contain `.claude-plugin/` and `skills/`.

## ChatGPT

ChatGPT has no native skills/plugins. Use [`chatgpt/`](chatgpt/):

1. Copy [`chatgpt/gpt-system-prompt.md`](chatgpt/gpt-system-prompt.md) → Custom GPT **Instructions**
2. Upload **one language** of the 14 files listed in [`chatgpt/knowledge-manifest.md`](chatgpt/knowledge-manifest.md) (`docs/en/` or `docs/es/`)
3. Guide: [`chatgpt/instructions.md`](chatgpt/instructions.md) · [Español](chatgpt/instructions.es.md)

## Repository layout

```
z-deluge-agent/
├── .claude-plugin/marketplace.json
├── .cursor/skills/              ← symlinks to the 10 skills
├── chatgpt/                     ← Custom GPT prompt + manifest
├── claude/                      ← local plugin upload guide
├── docs/en/  docs/es/           ← bilingual reference
├── plugins/z-deluge-agent/      ← plugin + skills
├── README.md                    ← English
└── README.es.md                 ← Spanish
```

## Official sources

- Deluge: https://www.zoho.com/deluge/help/
- CRM Developer: https://www.zohocrm.dev/
- COQL: https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html
- Creator API: https://www.zoho.com/creator/help/api/
- Analytics SQL: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/
- Zoho Developer Portal: https://www.zoho.com/developer/
