# Zoho CRM REST v2–v8 y GraphQL

> Idioma: español. English: `docs/en/apis/crm-rest-versions.md`.

> **Fuente:** colecciones Postman de Zoho *Zoho CRM REST APIs* v2, v2.1, v3, v4, v5, v6, v7, v8 y *Zoho CRM GraphQL APIs* (revisadas 2026-11-06).  
> **API actual para trabajo nuevo:** [crm-v8.md](crm-v8.md). Una versión anterior solo si la integración ya está fijada a ella.  
> **Auth:** OAuth 2.0, header `Authorization: Zoho-oauthtoken {token}`, o en Deluge `connection: "connection_name"` (literal — ver abajo).

Las colecciones repiten un request por módulo estándar (Leads, Contacts, …). Aquí los paths usan `{module}` y `{id}`.

**Base:** `https://www.zohoapis.com/crm/v{version}/`  
Los jobs bulk usan `https://www.zohoapis.com/crm/bulk/v{version}/`.

---

## Records (todas las versiones)

```
GET    /{module}
GET    /{module}/{id}
POST   /{module}                         ← máx. 100 registros
PUT    /{module}
PUT    /{module}/{id}
DELETE /{module}?ids={id1},{id2}
POST   /{module}/upsert
GET    /{module}/search?criteria=(Field:equals:value)
GET    /{module}/deleted
GET    /{module}/{id}/{related_module}
POST   /{module}/{id}/actions/convert    ← Leads
POST   /coql
```

También en todas las colecciones: org, users, roles, profiles, territories, modules/fields/layouts/related lists/custom views, notes, tags, attachments y photos, blueprint, variables, currencies, share, mass update, files, notifications (`/actions/watch`), bulk read/write.

v2 documenta además records por **external id** (`/{module}/{external_id}` sobre el campo external id).

---

## Qué agrega cada versión

| Desde | Áreas (patrón de path) |
|-------|------------------------|
| **v2.1** | `GET /{module}/actions/count`. `settings/pipeline` (+ `actions/transfer`). `settings/automation/assignment_rules`. `settings/wizards`. `settings/email_templates`, `settings/inventory_templates`. `settings/users_unavailability`. Send mail. Meeting cancel. Field attachments. |
| **v3** | `POST /__composite_requests`. `settings/automation/scoring_rules` y `/{module}/{id}/actions/run_scoring_rules`. `settings/record_locking_configurations`. `/{module}/{id}/actions/change_owner`. `settings/business_hours`. Services, Appointments, Appointments rescheduled history. `crm/bulk/v3/backup`. Emails. |
| **v4** | `POST /{module}/actions/mass_delete`, `POST /{module}/actions/mass_change_owner`. `settings/layouts/{id}/map_dependency`. `settings/user_groups`. `settings/portals/{portal}`. `settings/business_hours/shift_hours`. `settings/holidays`. |
| **v5** | Los mismos grupos, partidos en la colección en Entity / Metadata / Composite / Bulk / Notifications / Query. Suma `settings/fiscal_year`, `/{module}/{id}/__timeline`, `/{id}/actions/send_mail_merge` y `sign_mail_merge`. |
| **v6** | `POST /{module}/{id}/actions/merge`. `settings/recycle_bin`. `settings/global_picklists`. `settings/unsubscribe_links`. `GET /__features`. |
| **v7** | `POST /{module}/{id}/actions/clone`. `GET /__apis`. `settings/tags/actions/mass_delete`. `settings/automation/cadences` y `/{module}/actions/enrol_in_cadences` / `unenrol_from_cadences`. `settings/duplicate_check_preference`. `GET /settings/fields/{id}/pick_list_values`. `settings/audit_log_export`. Zia: `__zia_org_enrichment`, `__zia_people_enrichment`, `settings/zia/data_enrichment`. |
| **v8** | `settings/automation/workflow_rules`, `field_updates`, `webhooks`, `email_notifications`, `tasks`, `assignment_thresholds`. `settings/connected_workflows`. `settings/data_sharing` (+ `/rules`). `settings/call_preferences`. `/{module}/{id}/__email_drafts`. `/{module}/{id}/actions/fetch_full_data`. `/{module}/actions/permit_threshold`. `/{module}/actions/share_emails`. `/{module}/{id}/actions/get_related_records_count`. |

v8 es la referencia para trabajo nuevo: [crm-v8.md](crm-v8.md). Los paths de records de arriba valen igual, con `/crm/v8/`. La colección v8 agrupa las llamadas en Core, Metadata (Customization, Process Automation, Security Control, Company Settings, Zia), Bulk, Composite, Query y Notifications. External id vuelve a aparecer ahí; existe desde v2.

El request de status de mass delete de tags en la colección v7 omite una barra (`/crm/v7settings/...`). El path es `GET /crm/v7/settings/tags/actions/mass_delete`.

---

## GraphQL

Se habilita una vez por org y después se consulta. Bearer token, mismos scopes OAuth que REST (`ZohoCRM.modules.ALL`, settings, users).

```
POST https://www.zohoapis.com/crm/graphql/actions/enable
POST https://www.zohoapis.com/crm/graphql
```

Records: cada campo es un objeto con `value`. La metadata va bajo `Meta`. Los filtros usan `where` (records) o `filter` (metadata). Las related lists usan el api name `__r`.

```graphql
query {
  Records {
    Contacts(where: { Email: { equals: "maria@empresa.com" } }) {
      _data {
        id { value }
        Email { value }
        Full_Name { value }
        Deals__r {
          _data {
            Deal_Name { value }
            Stage { value }
          }
        }
      }
    }
  }
}
```

```graphql
query {
  Meta {
    Modules(filter: { api_name: "Leads" }) {
      _data { id api_name singular_label }
    }
  }
}
```

Raíces de metadata en la colección: `Modules`, `Users` (`filter.type`: `ConfirmedUsers`), `Profiles`, `ProfilePermissions` (`filter.profile_id`), `Roles` (`filter.api_name`), `Widgets`.

```deluge
query = Map();
query.put("query", "query { Records { Contacts { _data { id { value } Email { value } } } } }");
resp = invokeUrl [
    url: "https://www.zohoapis.com/crm/graphql"
    type: POST
    body: query.toText()
    headers: {"Content-Type": "application/json"}
    connection: "zoho_crm_oauth"
];
```

`connection` es un **string literal**. No se pasa una variable. Ver [language.md](../deluge/language.md).
