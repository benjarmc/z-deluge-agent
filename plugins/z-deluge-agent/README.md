# Z Deluge Agent — Plugin

Toolkit de skills y documentación para el ecosistema Zoho completo.

## Skills incluidos (10)

| # | Skill | Dominio |
|---|-------|---------|
| 1 | `zoho-deluge-core` | Lenguaje Deluge, invokeUrl, sintaxis base |
| 2 | `zoho-deluge-creator` | Formularios, páginas, subforms, Custom APIs, API v2.1 |
| 3 | `zoho-crm-client-script` | Client Scripts JavaScript en CRM |
| 4 | `zoho-crm-coql` | COQL — CRM Object Query Language v8 |
| 5 | `zoho-widgets-crm` | Widgets embebidos en CRM |
| 6 | `zoho-widgets-creator` | Widgets embebidos en Creator |
| 7 | `zoho-integration-tasks` | Tasks nativas `zoho.*` (24 servicios) |
| 8 | `zoho-public-apis` | APIs REST OAuth: CRM v8, Books, Desk, etc. |
| 9 | `zoho-analytics-sql` | SQL en Zoho Analytics, CloudSQL API |
| 10 | `zoho-patterns` | Null safety, límites, troubleshooting producción |

## Documentación de referencia

Los skills apuntan a la carpeta `docs/` en la raíz del repositorio. Índice: [`docs/INDEX.md`](../../docs/INDEX.md).

## Instalación (Claude Code)

```
/plugin marketplace add benjarmc/z-deluge-agent
/plugin install z-deluge-agent@z-deluge-agent
/reload-plugins
```

## Uso en Cursor

Los skills están disponibles en `.cursor/skills/` del repositorio. Cursor los descubre automáticamente al trabajar en este proyecto.

Invocación por dominio — ejemplos:

- "Revisa este script Deluge de Creator" → `zoho-deluge-creator`
- "Client Script para validar RFC en CRM" → `zoho-crm-client-script`
- "Query COQL para deals abiertos" → `zoho-crm-coql`
- "Widget CRM que actualice un contacto" → `zoho-widgets-crm`
- "Widget Creator con zet pack" → `zoho-widgets-creator`
- "Integrar CRM v8 con OAuth desde Deluge" → `zoho-public-apis`
- "Query Table ventas por región" → `zoho-analytics-sql`

## Estructura

```
plugins/z-deluge-agent/
├── .claude-plugin/plugin.json
└── skills/
    ├── zoho-deluge-core/
    ├── zoho-deluge-creator/
    ├── zoho-crm-client-script/
    ├── zoho-crm-coql/
    ├── zoho-widgets-crm/
    ├── zoho-widgets-creator/
    ├── zoho-integration-tasks/
    ├── zoho-public-apis/
    ├── zoho-analytics-sql/
    └── zoho-patterns/
```

Cada skill contiene `SKILL.md` (guía operativa) y `reference.md` (punteros a docs completas).
