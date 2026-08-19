# Z Deluge Agent — Plugin

Bilingual (EN/ES) skills and documentation toolkit for the full Zoho ecosystem.

Toolkit bilingüe (EN/ES) de skills y documentación para el ecosistema Zoho.

## Skills (10)

| # | Skill | Domain / Dominio |
|---|-------|------------------|
| 1 | `zoho-deluge-core` | Deluge language / Lenguaje Deluge |
| 2 | `zoho-deluge-creator` | Creator forms, pages, Custom APIs |
| 3 | `zoho-crm-client-script` | CRM Client Scripts (JavaScript) |
| 4 | `zoho-crm-coql` | COQL v8 |
| 5 | `zoho-widgets-crm` | CRM widgets |
| 6 | `zoho-widgets-creator` | Creator widgets |
| 7 | `zoho-integration-tasks` | Native `zoho.*` tasks (24 services) |
| 8 | `zoho-public-apis` | REST OAuth: CRM v8, Books, Desk |
| 9 | `zoho-analytics-sql` | Analytics SQL, CloudSQL API |
| 10 | `zoho-patterns` | Null safety, limits, troubleshooting |

Each skill: `SKILL.md` (loaded by the agent; bilingual YAML; replies in the user's language), `SKILL.en.md` (full English body), `reference.md` (EN + ES doc pointers).

Docs: [`docs/INDEX.md`](../../docs/INDEX.md) · [`docs/en/`](../../docs/en/) · [`docs/es/`](../../docs/es/)

## Install (Claude Code)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Cursor

Skills live in `.cursor/skills/`. Ask in English or Spanish.

Examples:

- "Review this Creator Deluge script" / "Revisa este script Deluge de Creator" → `zoho-deluge-creator`
- "Client Script to validate RFC" → `zoho-crm-client-script`
- "COQL for open deals" → `zoho-crm-coql`

## Layout

```
plugins/z-deluge-agent/
├── .claude-plugin/plugin.json
└── skills/
    └── zoho-*/SKILL.md + SKILL.en.md + reference.md
```
