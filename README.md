# Z Deluge Agent

Structured documentation and agent skills for the full Zoho universe: **Deluge**, **Creator**, **CRM Client Scripts**, **COQL**, **Widgets**, **Integration Tasks**, **REST APIs**, and **Analytics SQL**.

Documentación estructurada y skills de agente para todo el universo Zoho.

Inspired by [zoho-deluge-toolkit](https://github.com/creatorscripts/zoho-deluge-toolkit), extended to CRM, COQL, separate widgets, integration tasks, and multi-product APIs.

**Languages / Idiomas:** English · Español  
Docs: [`docs/en/`](docs/en/) · [`docs/es/`](docs/es/) · index: [`docs/INDEX.md`](docs/INDEX.md)

## Skills (10)

| # | Skill | English | Español |
|---|-------|---------|---------|
| 1 | `zoho-deluge-core` | Deluge language, types, functions, invokeUrl, zoho.* variables | Lenguaje Deluge, tipos, funciones, invokeUrl, variables zoho.* |
| 2 | `zoho-deluge-creator` | Forms, pages, subforms, Custom APIs, API v2.1 | Formularios, páginas, subforms, Custom APIs, API v2.1 |
| 3 | `zoho-crm-client-script` | Client-side JavaScript: events, ZDK, Web API, ZRC | JavaScript cliente: eventos, ZDK, Web API, ZRC |
| 4 | `zoho-crm-coql` | CRM v8 COQL: syntax, limits, invokeUrl | COQL CRM v8: sintaxis, límites, invokeUrl |
| 5 | `zoho-widgets-crm` | HTML/JS widgets in CRM (ZohoEmbededAppSDK) | Widgets HTML/JS en CRM (ZohoEmbededAppSDK) |
| 6 | `zoho-widgets-creator` | Creator widgets (widgetsdk, zet pack) | Widgets Creator (widgetsdk, zet pack) |
| 7 | `zoho-integration-tasks` | ~260 `zoho.*` tasks across 24 services | ~260 tasks `zoho.*` en 24 servicios |
| 8 | `zoho-public-apis` | CRM v8, Books, Desk, OAuth, REST (not deep COQL) | CRM v8, Books, Desk, OAuth, REST (sin COQL profundo) |
| 9 | `zoho-analytics-sql` | Analytics SQL, Query Tables, CloudSQL API | SQL Analytics, Query Tables, CloudSQL API |
| 10 | `zoho-patterns` | Null safety, API limits, troubleshooting | Null safety, límites API, troubleshooting |

Location / Ubicación: `plugins/z-deluge-agent/skills/` and `.cursor/skills/`

Each skill / Cada skill: `SKILL.md` (loaded by the agent; bilingual YAML; replies in the user's language / lo carga el agente; YAML bilingüe; responde en el idioma del usuario) + `SKILL.en.md` (full English body) + `reference.md`.

## Reference docs / Documentación

```
docs/
├── INDEX.md                     ← bilingual hub / índice bilingüe
├── en/                          ← English
└── es/                          ← Español
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

Code samples, API names, Deluge keywords, and Zoho product names stay in English in both locales.

Los ejemplos de código, nombres de API, palabras clave Deluge y productos Zoho se mantienen en inglés en ambos idiomas.

## Cursor

Open this repository; Cursor loads `.cursor/skills/` automatically. Ask in English or Spanish — the agent picks the skill and replies in your language.

Al abrir este repositorio, Cursor carga `.cursor/skills/` automáticamente. Pregunta en inglés o español: el agente elige el skill y responde en tu idioma.

## Claude Code (plugin)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Claude Desktop / Cowork (local plugin)

**Customize → Personal plugins → Upload local plugin** / **Personalizar → Plugins personales → Subir plugin local:**

1. Upload / Sube **`dist/z-deluge-agent.zip`**
2. Enable / Activa **z-deluge-agent**
3. Try / Prueba: `/z-deluge-agent:zoho-deluge-creator`

```bash
cd plugins/z-deluge-agent && zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Guide / Guía: [`claude/upload-local-plugin.md`](claude/upload-local-plugin.md) · [`claude/upload-local-plugin.es.md`](claude/upload-local-plugin.es.md)

> Only `.zip` (not `.plugin`). The zip root must contain `.claude-plugin/` and `skills/`.  
> Solo `.zip` (no `.plugin`). El zip debe tener `.claude-plugin/` y `skills/` en la raíz.

## ChatGPT

ChatGPT has no native skills/plugins. Use [`chatgpt/`](chatgpt/):

ChatGPT no usa skills ni plugins nativos. Usa [`chatgpt/`](chatgpt/):

1. Copy / Copia [`chatgpt/gpt-system-prompt.md`](chatgpt/gpt-system-prompt.md) or [`chatgpt/gpt-system-prompt.es.md`](chatgpt/gpt-system-prompt.es.md) → Custom GPT **Instructions**
2. Upload **one language** of the files in [`chatgpt/knowledge-manifest.md`](chatgpt/knowledge-manifest.md) (`docs/en/` or `docs/es/`) / Sube **un idioma** de los archivos del manifiesto
3. Guide / Guía: [`chatgpt/instructions.md`](chatgpt/instructions.md) · [`chatgpt/instructions.es.md`](chatgpt/instructions.es.md)

## Repository layout / Estructura

```
z-deluge-agent/
├── .claude-plugin/marketplace.json
├── .cursor/skills/              ← symlinks to the 10 skills
├── chatgpt/                     ← Custom GPT prompt + manifest (EN + ES)
├── claude/                      ← local plugin upload guide (EN + ES)
├── docs/en/  docs/es/           ← bilingual reference
├── plugins/z-deluge-agent/      ← plugin + skills
└── README.md                    ← this file (EN + ES)
```

## Official sources / Fuentes oficiales

- Deluge: https://www.zoho.com/deluge/help/
- CRM Developer: https://www.zohocrm.dev/
- COQL: https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html
- Creator API: https://www.zoho.com/creator/help/api/
- Analytics SQL: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/
- Zoho Developer Portal: https://www.zoho.com/developer/
