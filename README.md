# Z Deluge Agent

Documentación estructurada y skills de agente para todo el universo Zoho: **Deluge**, **Creator**, **Client Scripts CRM**, **COQL**, **Widgets**, **Integration Tasks**, **APIs REST** y **Analytics SQL**.

Modelo inspirado en [zoho-deluge-toolkit](https://github.com/creatorscripts/zoho-deluge-toolkit), ampliado a CRM, COQL, widgets separados, integration tasks y APIs de múltiples productos.

## Skills (10 módulos)

| # | Skill | Qué cubre |
|---|-------|-----------|
| 1 | `zoho-deluge-core` | Lenguaje Deluge, tipos, funciones, invokeUrl, variables zoho.* |
| 2 | `zoho-deluge-creator` | Formularios, páginas, subforms, Custom APIs, API v2.1 |
| 3 | `zoho-crm-client-script` | JavaScript cliente: eventos, ZDK, Web API, ZRC |
| 4 | `zoho-crm-coql` | COQL CRM v8: sintaxis, límites, invokeUrl |
| 5 | `zoho-widgets-crm` | Widgets HTML/JS en CRM (ZohoEmbededAppSDK) |
| 6 | `zoho-widgets-creator` | Widgets Creator (widgetsdk, zet pack) |
| 7 | `zoho-integration-tasks` | ~260 tasks `zoho.*` en 24 servicios |
| 8 | `zoho-public-apis` | CRM v8, Books, Desk, OAuth, REST (sin COQL profundo) |
| 9 | `zoho-analytics-sql` | SQL Analytics, Query Tables, CloudSQL API |
| 10 | `zoho-patterns` | Null safety, límites API, troubleshooting |

Ubicación: `plugins/z-deluge-agent/skills/` y `.cursor/skills/`

## Documentación de referencia

```
docs/
├── INDEX.md                    ← índice maestro (10 dominios)
├── deluge/language.md          ← lenguaje Deluge completo
├── creator/deluge-in-creator.md
├── client-script/crm.md
├── coql/crm-coql.md            ← COQL (separado de crm-v8)
├── widgets/crm.md              ← widgets CRM
├── widgets/creator.md            ← widgets Creator
├── integration-tasks/index.md
├── apis/crm-v8.md | books.md | desk.md | other-products.md
├── analytics/sql.md
└── patterns/production.md
```

## Uso con Cursor

Al abrir este repositorio, Cursor carga los skills de `.cursor/skills/` automáticamente. Pide ayuda en lenguaje natural sobre cualquier tema Zoho — el agente selecciona el skill adecuado.

## Uso con Claude Code (plugin)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Uso con ChatGPT

ChatGPT no usa skills ni plugins nativos. Usa la carpeta [`chatgpt/`](chatgpt/):

1. Copia [`chatgpt/gpt-system-prompt.md`](chatgpt/gpt-system-prompt.md) → **Instructions** de un Custom GPT
2. Sube los 14 archivos listados en [`chatgpt/knowledge-manifest.md`](chatgpt/knowledge-manifest.md) → **Knowledge**
3. Guía completa: [`chatgpt/instructions.md`](chatgpt/instructions.md)

## Estructura del repositorio

```
z-deluge-agent/
├── .claude-plugin/marketplace.json
├── .cursor/skills/              ← symlinks a los 10 skills
├── chatgpt/                     ← Custom GPT: prompt + manifest
├── docs/                        ← referencia exhaustiva
├── plugins/z-deluge-agent/      ← plugin + skills fuente
└── README.md
```

## Fuentes oficiales

- Deluge: https://www.zoho.com/deluge/help/
- CRM Developer: https://www.zohocrm.dev/
- COQL: https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html
- Creator API: https://www.zoho.com/creator/help/api/
- Analytics SQL: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/
- Zoho Developer Portal: https://www.zoho.com/developer/
