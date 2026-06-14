---
name: zoho-public-apis
description: Integra y depura APIs REST públicas de Zoho vía OAuth 2.0 e invokeUrl. Usar para CRM API v8 CRUD/bulk/settings, Books v3, Desk v1, Inventory, People, Projects, Sign, Billing, OAuth scopes, paginación REST, regiones data center o arquitectura server-to-server. Para COQL profundo usar skill zoho-crm-coql; para Analytics SQL usar zoho-analytics-sql.
---

# APIs REST públicas — Zoho

Integración server-side vía OAuth 2.0 + invokeUrl (Deluge) o HTTP directo.

**Referencias:**
- CRM v8 → `docs/apis/crm-v8.md`
- Books → `docs/apis/books.md`
- Desk → `docs/apis/desk.md`
- Otros → `docs/apis/other-products.md`

**COQL:** skill `zoho-crm-coql` — no duplicar aquí.  
**Analytics SQL:** skill `zoho-analytics-sql`.

---

## OAuth 2.0

1. Registrar app en https://api-console.zoho.com
2. Authorization code (3 min) → access + refresh token
3. Access token expira en **1 hora**
4. Refresh token ilimitado hasta revocación

```
POST https://accounts.zoho.com/oauth/v2/token
  ?refresh_token=REFRESH
  &client_id=CLIENT
  &client_secret=SECRET
  &grant_type=refresh_token
```

Header: `Authorization: Zoho-oauthtoken {access_token}`

Scope syntax: `Service.entity.OPERATION` (ej. `ZohoCRM.modules.ALL`)

---

## Regiones (data center)

| Región | Accounts | API |
|--------|----------|-----|
| US | accounts.zoho.com | www.zohoapis.com |
| EU | accounts.zoho.eu | www.zohoapis.eu |
| IN | accounts.zoho.in | www.zohoapis.in |
| AU | accounts.zoho.com.au | www.zohoapis.com.au |
| JP | accounts.zoho.jp | www.zohoapis.jp |
| CA | accounts.zohocloud.ca | www.zohoapis.ca |

Tokens **region-specific** — no mezclar dominios.

---

## CRM API v8

**Base:** `https://www.zohoapis.com/crm/v8/`

### CRUD

```
GET    /Contacts?page=1&per_page=200
GET    /Contacts/{id}
POST   /Contacts                    ← max 100 records body
PUT    /Contacts                    ← bulk update
DELETE /Contacts?ids=id1,id2
POST   /Contacts/upsert
```

### Search

```
GET /Contacts/search?criteria=(Email:equals:maria@empresa.com)
GET /Contacts/search?word=busqueda
GET /Contacts/search?email=email@domain.com
```

Operadores: equals, not_equals, contains, starts_with, greater_than, less_than, between, in, is_empty...

### Related records

```
GET  /Contacts/{id}/Deals
POST /Contacts/{id}/Notes
```

### Settings

```
GET /settings/modules
GET /settings/fields?module=Contacts
GET /settings/layouts?module=Contacts
GET /settings/roles
GET /settings/profiles
```

### Bulk Read/Write

```
POST /read          ← job export masivo
GET  /read/{job_id}
POST /write         ← import masivo
```

### Lead conversion

```
POST /Leads/{id}/actions/convert
```

### COQL (referencia)

```
POST /coql
Body: {"select_query": "select id, Email from Contacts limit 200"}
Scope: ZohoCRM.coql.READ
```

→ Detalle completo en skill `zoho-crm-coql` y `docs/coql/crm-coql.md`

### Lookup en payload

```json
{"Account_Name": {"id": "66359000000015039"}}
```

IDs siempre string. Fechas ISO 8601: `"2024-01-15T14:30:00+05:30"`

### Scopes CRM comunes

```
ZohoCRM.modules.ALL
ZohoCRM.modules.READ / CREATE / UPDATE / DELETE
ZohoCRM.settings.ALL
ZohoCRM.users.ALL
ZohoCRM.bulk.ALL
ZohoCRM.coql.READ
```

---

## Books API v3

**Base:** `https://www.zohoapis.com/books/v3/`  
**Query obligatorio:** `organization_id={orgId}`

```
GET  /organizations
GET  /invoices?organization_id={orgId}
POST /invoices?organization_id={orgId}
PUT  /invoices/{id}?organization_id={orgId}
POST /invoices/{id}/status/sent?organization_id={orgId}
```

Módulos: Contacts, Invoices, Estimates, Items, Bills, Expenses, CreditNotes, SalesOrders, PurchaseOrders...

Custom fields en `custom_field_hash` — validar con `containsKey()`.

Detalle: `docs/apis/books.md`

---

## Desk API v1

**Base:** `https://desk.zoho.com/api/v1/`  
Header: `orgId: {deskOrgId}`

```
GET   /tickets
POST  /tickets
PATCH /tickets/{id}
GET   /tickets/{id}/threads
POST  /tickets/{id}/sendReply
GET   /contacts
POST  /contacts
```

Detalle: `docs/apis/desk.md`

---

## Creator API v2.1

**Base:** `https://creator.zoho.com/api/v2.1/{owner}/{app}/`

```
POST  /form/{formLinkName}
GET   /report/{reportLinkName}
PATCH /report/{reportLinkName}?criteria=ID==123
POST  /custom/{apiName}
```

Params: `skip_workflow`, `field_config`, `max_records`, `record_cursor`

→ skill `zoho-deluge-creator` para gotchas Creator.

---

## Otros productos

| Producto | Base URL | Doc |
|----------|----------|-----|
| Inventory v1 | `/inventory/v1/` | other-products.md |
| People v2 | `people.zoho.com/people/api/` | other-products.md |
| Projects v3 | `projectsapi.zoho.com/restapi/portal/` | other-products.md |
| Recruit v2 | `recruit.zoho.com/recruit/v2/` | other-products.md |
| Sign v1 | `sign.zoho.com/api/v1/` | other-products.md |
| Billing v1 | `/billing/v1/` | other-products.md |
| WorkDrive v1 | `/workdrive/api/v1/` | other-products.md |
| Analytics v2 | `analyticsapi.zoho.com/api/v2/` | analytics/sql.md |
| Bookings v1 | `bookings.zoho.com/api/v1/` | other-products.md |

---

## Patrón invokeUrl (Deluge)

```deluge
resp = invokeUrl [
    url: "https://www.zohoapis.com/crm/v8/Contacts/" + recordId
    type: GET
    connection: "zoho_crm_oauth"
];

respStr = resp.toString();
if(respStr.contains(""data"")) {
    data = resp.get("data");
}

// POST
headers = Map();
headers.put("Content-Type", "application/json");
body = Map();
body.put("data", recordsList);
resp = invokeUrl [
    url: "https://www.zohoapis.com/crm/v8/Contacts"
    type: POST
    body: body.toText()
    headers: headers
    connection: "zoho_crm_oauth"
];
```

Usar `connection:` para OAuth. Body JSON: `.toText()` siempre.

---

## Paginación REST

```deluge
page = 1;
perPage = 200;
allData = List();

for each i in "".leftpad(49).toList("") {
    url = "https://www.zohoapis.com/crm/v8/Contacts?page=" + page + "&per_page=" + perPage;
    resp = invokeUrl [ url: url type: GET connection: "zoho_crm" ];
    batch = resp.get("data");
    if(batch == null || batch.size() == 0) { break; }
    for each rec in batch { allData.add(rec); }
    if(batch.size() < perPage) { break; }
    page = page + 1;
}
```

---

## Errores HTTP

| Código | Significado | Acción |
|--------|-------------|--------|
| 401 INVALID_OAUTHTOKEN | Token expirado | Refresh OAuth |
| 403 | Scope insuficiente | Agregar scope en console |
| 429 | Rate limit | Backoff, reducir frecuencia |
| 400 INVALID_DATA | Payload mal formado | Tipos de campo, lookup format |
| 204 | DELETE ok sin body | Normal |

---

## Task nativa vs REST

| Preferir `zoho.*` task | Preferir REST invokeUrl |
|------------------------|-------------------------|
| CRUD simple documentado | Endpoints nuevos v8 |
| Menos boilerplate | COQL, bulk, settings |
| | Params Creator v2.1 avanzados |

Ver skill `zoho-integration-tasks`.

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| CRM v8 | `docs/apis/crm-v8.md` |
| COQL | `docs/coql/crm-coql.md` |
| Books | `docs/apis/books.md` |
| Desk | `docs/apis/desk.md` |
| Otros productos | `docs/apis/other-products.md` |
| Patrones | `docs/patterns/production.md` |

