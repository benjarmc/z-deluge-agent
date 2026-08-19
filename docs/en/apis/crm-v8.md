# Zoho CRM API v8 — Complete Reference

> Language: English. Spanish: see the matching path under docs/es/.

> **Official source:** https://www.zoho.com/crm/developer/docs/api/v8/  
> **Base URL:** `https://www.zohoapis.com/crm/v8/`  
> **Authentication:** OAuth 2.0 (`Authorization: Zoho-oauthtoken {token}`)  
> **Current version:** v8 (2024-2025) — v2, v5, v6, v7 are also available

---

## OAuth Scopes — CRM v8

```
ZohoCRM.modules.ALL        ← full module access
ZohoCRM.modules.READ
ZohoCRM.modules.CREATE
ZohoCRM.modules.UPDATE
ZohoCRM.modules.DELETE

ZohoCRM.settings.ALL       ← full settings
ZohoCRM.settings.modules
ZohoCRM.settings.fields
ZohoCRM.settings.layouts
ZohoCRM.settings.territories
ZohoCRM.settings.custom_views
ZohoCRM.settings.related_lists
ZohoCRM.settings.variables
ZohoCRM.settings.tags
ZohoCRM.settings.tab_groups
ZohoCRM.settings.macros
ZohoCRM.settings.custom_links
ZohoCRM.settings.custom_buttons
ZohoCRM.settings.roles
ZohoCRM.settings.profiles
ZohoCRM.settings.currencies

ZohoCRM.users.ALL
ZohoCRM.org.ALL
ZohoCRM.bulk.ALL           ← bulk read/write
ZohoCRM.notifications.ALL  ← push notifications
ZohoCRM.coql.READ          ← COQL queries

// Specific modules (examples):
ZohoCRM.modules.leads.ALL
ZohoCRM.modules.contacts.ALL
ZohoCRM.modules.deals.ALL
ZohoCRM.modules.accounts.ALL
```

---

## CRM Standard Modules

| Module API Name | Description |
|----------------|-------------|
| `Leads` | Prospects / leads |
| `Contacts` | Contacts |
| `Accounts` | Accounts / companies |
| `Deals` | Opportunities |
| `Campaigns` | Marketing campaigns |
| `Tasks` | Tasks |
| `Cases` | Support cases |
| `Events` | Events / activities |
| `Calls` | Calls |
| `Solutions` | Solutions / knowledge base |
| `Products` | Products |
| `Vendors` | Vendors |
| `PriceBooks` | Price lists |
| `Quotes` | Quotes |
| `SalesOrders` | Sales orders |
| `PurchaseOrders` | Purchase orders |
| `Invoices` | Invoices |
| `Activities` | Activities (Tasks + Events + Calls) |
| `Notes` | Notes |
| `Attachments` | Attachments |
| `Users` | CRM users |
| `Services` | Services (Field Service) |
| `Appointments` | Appointments (Field Service) |
| `Appointments_Rescheduled_History` | Reschedule history |

---

## Records — Main Endpoints

### Record CRUD

```
GET    /{module}                               ← list records
GET    /{module}/{record_id}                  ← get by ID
POST   /{module}                               ← create record(s)
PUT    /{module}                               ← update record(s)
DELETE /{module}                               ← delete record(s)
POST   /{module}/upsert                       ← create or update
```

### List Parameters

```
page={n}                    ← pagination (default: 1)
per_page={n}                ← records per page (max: 200)
sort_by={field_api_name}    ← sort field
sort_order=asc|desc
fields={field1,field2}     ← specific fields
converted=true|false        ← Leads only
approved=true|false
territory_id={id}
include=owner,created_by,modified_by
```

### Search

```
GET    /{module}/search?criteria=({field}:{operator}:{value})
GET    /{module}/search?word={searchTerm}
GET    /{module}/search?email={email}
GET    /{module}/search?phone={phone}
GET    /{module}/search?cvid={custom_view_id}
```

Operators: `equals`, `not_equals`, `contains`, `starts_with`, `ends_with`, `greater_than`, `less_than`, `between`, `not_between`, `in`, `not_in`, `is_empty`, `is_not_empty`

### Related Records (Submodules)

```
GET    /{module}/{record_id}/{related_module}
POST   /{module}/{record_id}/{related_module}
PUT    /{module}/{record_id}/{related_module}/{related_id}
DELETE /{module}/{record_id}/{related_module}/{related_id}
```

### Lead Conversion

```
POST   /Leads/{lead_id}/actions/convert
```

### Attachments

```
GET    /{module}/{record_id}/Attachments
POST   /{module}/{record_id}/Attachments   ← upload file
GET    /{module}/{record_id}/Attachments/{attach_id}
DELETE /{module}/{record_id}/Attachments/{attach_id}
```

### Notes

```
GET    /{module}/{record_id}/Notes
POST   /{module}/{record_id}/Notes
PUT    /Notes/{note_id}
DELETE /Notes/{note_id}
```

---

## COQL — CRM Object Query Language

> **Full documentation:** [`docs/coql/crm-coql.md`](../coql/crm-coql.md)  
> **Skill:** `zoho-crm-coql`

```
POST   /coql
```

Minimum body:
```json
{
  "select_query": "select Last_Name, Email from Contacts where Email is not null limit 200"
}
```

Scope: `ZohoCRM.coql.READ` — Limit: **200 records/request**, paginate with OFFSET.

For full syntax, aggregates, lookup joins, Deluge examples, and troubleshooting → see `docs/coql/crm-coql.md`.

## Bulk Read / Write

### Bulk Read (mass export)

```
POST   /read                                   ← create bulk read job
GET    /read/{job_id}                          ← job status
GET    /read/{job_id}/result                   ← download result (ZIP/CSV)
DELETE /read/{job_id}                          ← cancel job
```

POST body:
```json
{
  "query": {
    "module": {"api_name": "Leads"},
    "fields": [{"api_name": "Last_Name"}, {"api_name": "Email"}],
    "criteria": {
      "group_operator": "and",
      "group": [{"field": {"api_name": "Created_Time"}, "comparator": "between", "value": ["2024-01-01T00:00:00+00:00", "2024-12-31T23:59:59+00:00"]}]
    },
    "callback": {"url": "https://tu-webhook.com/notif", "method": "post"}
  }
}
```

### Bulk Write (mass import)

```
POST   /write                                  ← create write job
GET    /write/{job_id}
DELETE /write/{job_id}
```

---

## Users

```
GET    /users                                  ← list users
GET    /users/{user_id}                       ← get user
GET    /users/search?criteria=...
POST   /users                                  ← create user
PUT    /users                                  ← update
DELETE /users/{user_id}
GET    /users?type=ActiveUsers|DeactiveUsers|AdminUsers|CurrentUser
```

---

## Settings

### Modules

```
GET    /settings/modules
GET    /settings/modules/{module_api_name}
POST   /settings/modules                       ← create custom module
PUT    /settings/modules/{module_api_name}
```

### Fields

```
GET    /settings/fields?module={module}        ← module fields
GET    /settings/fields/{field_id}?module={module}
POST   /settings/fields?module={module}        ← create field
PUT    /settings/fields?module={module}
DELETE /settings/fields?module={module}
```

### Layouts

```
GET    /settings/layouts?module={module}
GET    /settings/layouts/{layout_id}?module={module}
POST   /settings/layouts?module={module}
PUT    /settings/layouts/{layout_id}?module={module}
DELETE /settings/layouts/{layout_id}?module={module}
```

### Custom Views

```
GET    /settings/custom_views?module={module}
GET    /settings/custom_views/{cv_id}?module={module}
POST   /settings/custom_views?module={module}
PUT    /settings/custom_views/{cv_id}?module={module}
DELETE /settings/custom_views/{cv_id}?module={module}
```

### Roles

```
GET    /settings/roles
GET    /settings/roles/{role_id}
POST   /settings/roles
PUT    /settings/roles/{role_id}
DELETE /settings/roles/{role_id}
```

### Profiles

```
GET    /settings/profiles
GET    /settings/profiles/{profile_id}
POST   /settings/profiles
PUT    /settings/profiles/{profile_id}
DELETE /settings/profiles/{profile_id}
```

### Organization Variables

```
GET    /settings/variables
GET    /settings/variables/{var_id}
POST   /settings/variables
PUT    /settings/variables/{var_id}
DELETE /settings/variables/{var_id}
```

### Pipelines

```
GET    /settings/pipelines?module={module}
GET    /settings/pipelines/{pipeline_id}?module={module}
POST   /settings/pipelines?module={module}
PUT    /settings/pipelines/{pipeline_id}?module={module}
DELETE /settings/pipelines/{pipeline_id}?module={module}
```

### Tags

```
GET    /settings/tags?module={module}
POST   /settings/tags?module={module}
PUT    /settings/tags/{tag_id}?module={module}
DELETE /settings/tags/{tag_id}?module={module}
POST   /{module}/{record_id}/tags              ← add tags to record
GET    /{module}/tags?tag_ids={id1,id2}        ← records by tag
DELETE /{module}/{record_id}/tags
```

---

## Notifications (Push / Webhooks)

```
POST   /actions/watch                          ← create subscription
GET    /actions/watch                          ← list subscriptions
PATCH  /actions/watch                          ← update
DELETE /actions/watch                          ← cancel
```

Body:
```json
{
  "watch": [{
    "channel_id": "1000000068002",
    "events": ["Leads.create", "Contacts.edit", "Accounts.delete", "Tasks.all"],
    "channel_expiry": "2024-12-31T00:00:00+05:30",
    "token": "TOKEN_SECRETO",
    "notify_url": "https://tu-webhook.com/zoho"
  }]
}
```

---

## Functions (Standalone — REST API)

```
POST   /functions/{function_name}/actions/execute?auth_type=apikey&zapikey={key}
```

Body:
```json
{
  "arguments": "{\"recordId\":\"123456\",\"parametro\":\"valor\"}"
}
```

From Deluge, to call a standalone function:
```deluge
req_data = {
    "arguments": JSON.stringify({"recordId": recordId})
};
result = ZOHO.CRM.FUNCTIONS.execute("nombre_funcion", req_data);
output = result.get("details").get("output");
```

---

## Org

```
GET    /org                                    ← organization info
PUT    /org                                    ← update org
GET    /org/businesshours
PUT    /org/businesshours
GET    /org/currencies
POST   /org/currencies
PUT    /org/currencies/{currency_id}
POST   /org/currencies/{currency_id}/enable_home_currency
```

---

## Complete invokeUrl example from Deluge

```deluge
// Search contacts by email and update in CRM v8
email = input.Email;

// COQL search
searchQuery = "select id, Last_Name, Email from Contacts where Email = '" + email + "' limit 1";
coqlBody = Map();
coqlBody.put("select_query", searchQuery);

headers = Map();
headers.put("Content-Type", "application/json");

searchResp = invokeUrl [
    url:        "https://www.zohoapis.com/crm/v8/coql"
    type:       POST
    body:       coqlBody.toText()
    headers:    headers
    connection: "zoho_crm_v8_conn"
];

data = searchResp.get("data");
if (data != null && data.size() > 0) {
    contactId = data.get(0).get("id");

    // Update the contact
    updateData = list();
    rec = Map();
    rec.put("id", contactId);
    rec.put("Lead_Status", "Calificado");
    updateData.add(rec);

    updateBody = Map();
    updateBody.put("data", updateData);

    updateResp = invokeUrl [
        url:        "https://www.zohoapis.com/crm/v8/Contacts"
        type:       PUT
        body:       updateBody.toText()
        headers:    headers
        connection: "zoho_crm_v8_conn"
    ];
}
```

---

## Official SDKs

| Language | Repository |
|----------|-------------|
| Java | zohocrm/zohocrm-java-sdk-v8 |
| Python | zohocrm/zohocrm-python-sdk-v8 |
| PHP | zohocrm/zohocrm-php-sdk-v8 |
| Node.js | zohocrm/zohocrm-nodejs-sdk-v8 |
| C# | zohocrm/zohocrm-csharp-sdk-v8 |
| Ruby | zohocrm/zohocrm-ruby-sdk-v8 |
| JavaScript (Browser) | zohocrm/zohocrm-js-sdk |

---

## Important Notes v8

- Most responses use the `data` field (array) as the root
- IDs are long numeric strings (e.g.: `"66359000000015039"`)
- Dates in ISO 8601: `"2024-01-15T14:30:00+05:30"`
- Lookup fields return `{"id": "...", "name": "..."}` — to update, send only `{"id": "..."}`
- The `ZohoCRM.modules.ALL` scope grants access to all modules including custom ones
- For custom modules use their API name (e.g.: `Pago_Facturas`, `Mi_Modulo_Custom`)
