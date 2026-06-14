# Índice de documentación — Zoho Deluge Agent

Referencia estructurada del ecosistema Zoho para agentes y desarrolladores. Organizada en **10 dominios** alineados 1:1 con los skills del plugin.

## 1. Deluge (lenguaje)

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [deluge/language.md](deluge/language.md) | `zoho-deluge-core` | Sintaxis, tipos, operadores, funciones, invokeUrl, variables zoho.* |

## 2. Deluge en Zoho Creator

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [creator/deluge-in-creator.md](creator/deluge-in-creator.md) | `zoho-deluge-creator` | Formularios, páginas, subforms, Custom APIs, API v2.1 |

## 3. Client Scripts CRM

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [client-script/crm.md](client-script/crm.md) | `zoho-crm-client-script` | Eventos, Client API (ZDK), Web API, ZRC |

## 4. COQL (CRM)

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [coql/crm-coql.md](coql/crm-coql.md) | `zoho-crm-coql` | Sintaxis COQL, límites, scopes, invokeUrl, errores |

## 5. Widgets CRM

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [widgets/crm.md](widgets/crm.md) | `zoho-widgets-crm` | ZohoEmbededAppSDK, PageLoad, CRM.API, despliegue |

## 6. Widgets Creator

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [widgets/creator.md](widgets/creator.md) | `zoho-widgets-creator` | widgetsdk, manifest CREATOR, ZOHO.CREATOR.API, zet pack |

## 7. Integration tasks (`zoho.*`)

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [integration-tasks/index.md](integration-tasks/index.md) | `zoho-integration-tasks` | ~260 tasks en 24 servicios Zoho |

## 8. APIs REST públicas

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [apis/crm-v8.md](apis/crm-v8.md) | `zoho-public-apis` | CRM v8 CRUD, bulk, settings (COQL → skill coql) |
| [apis/books.md](apis/books.md) | `zoho-public-apis` | Books API v3 |
| [apis/desk.md](apis/desk.md) | `zoho-public-apis` | Desk API v1 |
| [apis/other-products.md](apis/other-products.md) | `zoho-public-apis` | Inventory, People, Projects, Sign, Billing, etc. |

## 9. Analytics SQL

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [analytics/sql.md](analytics/sql.md) | `zoho-analytics-sql` | SQL en Zoho Analytics, JOINs, window functions, CloudSQL API |

## 10. Patrones y calidad

| Documento | Skill | Contenido |
|-----------|-------|-----------|
| [patterns/production.md](patterns/production.md) | `zoho-patterns` | Null safety, límites API, gotchas, troubleshooting |

---

## Documento legacy (referencia cruzada)

| Documento | Nota |
|-----------|------|
| [widgets/crm-creator.md](widgets/crm-creator.md) | Documento combinado original — preferir `widgets/crm.md` y `widgets/creator.md` |

## Mapa skill → docs

```
zoho-deluge-core        → deluge/
zoho-deluge-creator     → creator/
zoho-crm-client-script  → client-script/
zoho-crm-coql           → coql/
zoho-widgets-crm        → widgets/crm.md
zoho-widgets-creator    → widgets/creator.md
zoho-integration-tasks  → integration-tasks/
zoho-public-apis        → apis/
zoho-analytics-sql      → analytics/
zoho-patterns           → patterns/
```
