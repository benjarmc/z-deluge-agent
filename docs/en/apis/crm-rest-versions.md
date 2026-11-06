# Zoho CRM REST v2–v8 and GraphQL

> Language: English. Spanish: `docs/es/apis/crm-rest-versions.md`.

> **Source:** Zoho Postman collections *Zoho CRM REST APIs* v2, v2.1, v3, v4, v5, v6, v7, v8 and *Zoho CRM GraphQL APIs* (reviewed 2026-11-06).  
> **Current API for new work:** [crm-v8.md](crm-v8.md). Use an older version only when the integration is already pinned to it.  
> **Auth:** OAuth 2.0, header `Authorization: Zoho-oauthtoken {token}`, or Deluge `connection: "connection_name"` (literal — see below).

The collections repeat one request per standard module (Leads, Contacts, …). Paths here use `{module}` and `{id}`.

**Base:** `https://www.zohoapis.com/crm/v{version}/`  
Bulk jobs use `https://www.zohoapis.com/crm/bulk/v{version}/`.

---

## Records (every version)

```
GET    /{module}
GET    /{module}/{id}
POST   /{module}                         ← max 100 records
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

Also in every collection: org, users, roles, profiles, territories, modules/fields/layouts/related lists/custom views, notes, tags, attachments and photos, blueprint, variables, currencies, share, mass update, files, notifications (`/actions/watch`), bulk read/write.

v2 also documents records by **external id** (`/{module}/{external_id}` on the external-id field).

---

## What each version adds

| From | Areas (path pattern) |
|------|----------------------|
| **v2.1** | `GET /{module}/actions/count`. `settings/pipeline` (+ `actions/transfer`). `settings/automation/assignment_rules`. `settings/wizards`. `settings/email_templates`, `settings/inventory_templates`. `settings/users_unavailability`. Send mail. Meeting cancel. Field attachments. |
| **v3** | `POST /__composite_requests`. `settings/automation/scoring_rules` and `/{module}/{id}/actions/run_scoring_rules`. `settings/record_locking_configurations`. `/{module}/{id}/actions/change_owner`. `settings/business_hours`. Services, Appointments, Appointments rescheduled history. `crm/bulk/v3/backup`. Emails. |
| **v4** | `POST /{module}/actions/mass_delete`, `POST /{module}/actions/mass_change_owner`. `settings/layouts/{id}/map_dependency`. `settings/user_groups`. `settings/portals/{portal}`. `settings/business_hours/shift_hours`. `settings/holidays`. |
| **v5** | Same groups, split in the collection into Entity / Metadata / Composite / Bulk / Notifications / Query. Adds `settings/fiscal_year`, `/{module}/{id}/__timeline`, `/{id}/actions/send_mail_merge` and `sign_mail_merge`. |
| **v6** | `POST /{module}/{id}/actions/merge`. `settings/recycle_bin`. `settings/global_picklists`. `settings/unsubscribe_links`. `GET /__features`. |
| **v7** | `POST /{module}/{id}/actions/clone`. `GET /__apis`. `settings/tags/actions/mass_delete`. `settings/automation/cadences` and `/{module}/actions/enrol_in_cadences` / `unenrol_from_cadences`. `settings/duplicate_check_preference`. `GET /settings/fields/{id}/pick_list_values`. `settings/audit_log_export`. Zia: `__zia_org_enrichment`, `__zia_people_enrichment`, `settings/zia/data_enrichment`. |
| **v8** | `settings/automation/workflow_rules`, `field_updates`, `webhooks`, `email_notifications`, `tasks`, `assignment_thresholds`. `settings/connected_workflows`. `settings/data_sharing` (+ `/rules`). `settings/call_preferences`. `/{module}/{id}/__email_drafts`. `/{module}/{id}/actions/fetch_full_data`. `/{module}/actions/permit_threshold`. `/{module}/actions/share_emails`. `/{module}/{id}/actions/get_related_records_count`. |

v8 is the reference for new work: [crm-v8.md](crm-v8.md). Record paths above still apply, with `/crm/v8/`. The v8 collection groups calls into Core, Metadata (Customization, Process Automation, Security Control, Company Settings, Zia), Bulk, Composite, Query, and Notifications. External id is listed there again; it has existed since v2.

The v7 collection’s tag mass-delete status request is missing a slash (`/crm/v7settings/...`). The path is `GET /crm/v7/settings/tags/actions/mass_delete`.

---

## GraphQL

Enable once per org, then query. Bearer token, same OAuth scopes as REST (`ZohoCRM.modules.ALL`, settings, users).

```
POST https://www.zohoapis.com/crm/graphql/actions/enable
POST https://www.zohoapis.com/crm/graphql
```

Records: each field is an object with `value`. Metadata lives under `Meta`. Filters use `where` (records) or `filter` (metadata). Related lists use the `__r` api name.

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

Metadata roots in the collection: `Modules`, `Users` (`filter.type`: `ConfirmedUsers`), `Profiles`, `ProfilePermissions` (`filter.profile_id`), `Roles` (`filter.api_name`), `Widgets`.

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

`connection` is a **string literal**. Do not pass a variable. See [language.md](../deluge/language.md).
