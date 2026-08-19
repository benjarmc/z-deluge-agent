# Z Deluge Agent

Documentación estructurada y skills de agente para todo el universo Zoho: **Deluge**, **Creator**, **Client Scripts CRM**, **COQL**, **Widgets**, **Integration Tasks**, **APIs REST** y **Analytics SQL**.

Modelo inspirado en [zoho-deluge-toolkit](https://github.com/creatorscripts/zoho-deluge-toolkit), ampliado a CRM, COQL, widgets separados, integration tasks y APIs de múltiples productos.

**Idiomas:** [English](README.md) · Español (este archivo)  
Docs: [`docs/en/`](docs/en/) · [`docs/es/`](docs/es/)

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

Cada skill tiene `SKILL.md` (lo carga el agente; descripción bilingüe; responde en el idioma del usuario) y `SKILL.en.md` (cuerpo completo en inglés).

## Documentación de referencia

```
docs/
├── INDEX.md                     ← índice bilingüe
├── en/                          ← inglés
└── es/                          ← español
```

## Cursor

Al abrir este repositorio, Cursor carga `.cursor/skills/` automáticamente. Pregunta en inglés o español: el agente elige el skill y responde en tu idioma.

## Claude Code (plugin)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Claude gráfico (app / Cowork)

En **Personalizar → Plugins personales → Subir plugin local**:

1. Sube **`dist/z-deluge-agent.zip`**
2. Activa **z-deluge-agent**
3. Prueba: `/z-deluge-agent:zoho-deluge-creator`

Generar el zip:

```bash
cd plugins/z-deluge-agent && zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Guía: [`claude/upload-local-plugin.es.md`](claude/upload-local-plugin.es.md) · [English](claude/upload-local-plugin.md)

> Solo `.zip` (no `.plugin`). El zip debe tener `.claude-plugin/` y `skills/` en la raíz.

## ChatGPT

ChatGPT no usa skills ni plugins nativos. Usa [`chatgpt/`](chatgpt/):

1. Copia [`chatgpt/gpt-system-prompt.es.md`](chatgpt/gpt-system-prompt.es.md) → **Instructions** del Custom GPT
2. Sube **un idioma** de los 14 archivos de [`chatgpt/knowledge-manifest.md`](chatgpt/knowledge-manifest.md) (`docs/es/` o `docs/en/`)
3. Guía: [`chatgpt/instructions.es.md`](chatgpt/instructions.es.md) · [English](chatgpt/instructions.md)

## Estructura del repositorio

```
z-deluge-agent/
├── .claude-plugin/marketplace.json
├── .cursor/skills/
├── chatgpt/
├── claude/
├── docs/en/  docs/es/
├── plugins/z-deluge-agent/
├── README.md                    ← inglés
└── README.es.md                 ← español
```

## Fuentes oficiales

- Deluge: https://www.zoho.com/deluge/help/
- CRM Developer: https://www.zohocrm.dev/
- COQL: https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html
- Creator API: https://www.zoho.com/creator/help/api/
- Analytics SQL: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/
- Zoho Developer Portal: https://www.zoho.com/developer/
