---
name: zoho-public-apis
description: Integrates and debugs public Zoho REST APIs via OAuth 2.0 and invokeUrl. Use for CRM API v8 and v2–v8 CRUD/bulk/settings, CRM GraphQL, Books v3, Desk v1, Inventory, People, Projects, Sign, Billing, OAuth scopes, REST pagination, data-center regions. For deep COQL use zoho-crm-coql; for Analytics SQL use zoho-analytics-sql. / Integra y depura APIs REST públicas de Zoho vía OAuth 2.0 e invokeUrl. CRM v8 y v2–v8, GraphQL de CRM, Books, Desk. Para COQL profundo usar zoho-crm-coql; para Analytics SQL usar zoho-analytics-sql.
---

# Public REST APIs — Zoho

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

Server-side integration via OAuth 2.0 + invokeUrl (Deluge) or direct HTTP.

**References:**
- CRM v8 → `docs/en/apis/crm-v8.md` · `docs/es/apis/crm-v8.md`
- CRM v2–v8 and GraphQL → `docs/en/apis/crm-rest-versions.md` · `docs/es/apis/crm-rest-versions.md`
- Books → `docs/en/apis/books.md` · `docs/es/apis/books.md`
- Desk → `docs/en/apis/desk.md` · `docs/es/apis/desk.md`
- Others → `docs/en/apis/other-products.md` · `docs/es/apis/other-products.md`

**COQL:** skill `zoho-crm-coql` — do not duplicate here.  
**Analytics SQL:** skill `zoho-analytics-sql`.

---

## OAuth 2.0

1. Register an app at https://api-console.zoho.com
2. Authorization code (3 min) → access + refresh token
3. Access token expires in **1 hour**
4. Refresh token is unlimited until revocation

```
POST https://accounts.zoho.com/oauth/v2/token
  ?refresh_token=REFRESH
  &client_id=CLIENT
  &client_secret=SECRET
  &grant_type=refresh_token
```

Header: `Authorization: Zoho-oauthtoken {access_token}`

Scope syntax: `Service.entity.OPERATION` (e.g. `ZohoCRM.modules.ALL`)

---

## Regions (data center)

| Region | Accounts | API |
|--------|----------|-----|
| US | accounts.zoho.com | www.zohoapis.com |
| EU | accounts.zoho.eu | www.zohoapis.eu |
| IN | accounts.zoho.in | www.zohoapis.in |
| AU | accounts.zoho.com.au | www.zohoapis.com.au |
| JP | accounts.zoho.jp | www.zohoapis.jp |
| CA | accounts.zohocloud.ca | www.zohoapis.ca |

Tokens are **region-specific** — do not mix domains.

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

Operators: equals, not_equals, contains, starts_with, greater_than, less_than, between, in, is_empty...

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

### COQL (reference)

```
POST /coql
Body: {"select_query": "select id, Email from Contacts limit 200"}
Scope: ZohoCRM.coql.READ
```

→ Full detail in skill `zoho-crm-coql` and `docs/en/coql/crm-coql.md` · `docs/es/coql/crm-coql.md`

### Lookup in payload

```json
{"Account_Name": {"id": "66359000000015039"}}
```

IDs always string. ISO 8601 dates: `"2024-01-15T14:30:00+05:30"`

### Common CRM scopes

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
**Required query:** `organization_id={orgId}`

```
GET  /organizations
GET  /invoices?organization_id={orgId}
POST /invoices?organization_id={orgId}
PUT  /invoices/{id}?organization_id={orgId}
POST /invoices/{id}/status/sent?organization_id={orgId}
```

Modules: Contacts, Invoices, Estimates, Retainer Invoices, Recurring Invoices/Bills/Expenses, Delivery Challans, Purchase Returns, Items, Bills, Expenses, Credit Notes, Sales Orders, Purchase Orders, Fixed Assets, Bank Transactions. Books OpenAPI (45 modules) is summarized in the doc.

Custom fields in `custom_field_hash` — validate with `containsKey()`.

Detail: `docs/en/apis/books.md` · `docs/es/apis/books.md`

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

Detail: `docs/en/apis/desk.md` · `docs/es/apis/desk.md`

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

→ skill `zoho-deluge-creator` for Creator gotchas.

---

## Other products

| Product | Base URL | Doc |
|----------|----------|-----|
| Inventory v1 | `/inventory/v1/` | other-products.md |
| People v2 | `people.zoho.com/people/api/` | other-products.md |
| Projects v3 | `projectsapi.zoho.com/restapi/portal/` | other-products.md |
| Recruit v2 | `recruit.zoho.com/recruit/v2/` | other-products.md |
| Sign v1 | `sign.zoho.com/api/v1/` | other-products.md |
| Billing v1 | `/billing/v1/` | other-products.md |
| WorkDrive v1 | `/workdrive/api/v1/` | other-products.md — GET files: header `Accept: application/vnd.api+json` |
| Analytics v2 | `analyticsapi.zoho.com/api/v2/` | analytics/sql.md |
| Bookings v1 | `bookings.zoho.com/api/v1/` | other-products.md |

---

## invokeUrl pattern (Deluge)

For **Zoho↔Zoho** APIs prefer `invokeapi` (path + `service` only) — skill `zoho-deluge-core`. Full URL or external API → `invokeUrl`.

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

Use `connection:` for OAuth. The name is a **quoted literal** (`connection: "connection_name"`). Do not assign it to a variable: Deluge resolves it when the script is saved. JSON body: always `.toText()`.

CRM v2–v8 map and GraphQL: `docs/en/apis/crm-rest-versions.md` · `docs/es/apis/crm-rest-versions.md`. Base `https://www.zohoapis.com/crm/v2/` … `/crm/v8/`. GraphQL: `POST /crm/graphql` (enable first with `POST /crm/graphql/actions/enable`).

### WorkDrive REST — `Accept` header

Some WorkDrive GETs (`/api/v1/files/{id}`, listings) expect JSON:API. Without the header the response fails or will not parse. Search: `GET /api/v1/teams/{team_id}/records`. Download: `GET /v1/workdrive/download/{resource_id}` (outside `/api/v1/`).

```deluge
header = Map();
header.put("Accept", "application/vnd.api+json");
uri = "https://www.zohoapis.eu/workdrive/api/v1/files/" + resource_id;
response = invokeurl
[
	url :uri
	type :GET
	headers:header
	connection:"workdrive"
];
```

Use the DC domain (`.com` / `.eu` / `.in`). Native `zoho.workdrive.*` tasks do not need this header.

---

## REST pagination

Known total → **dynamic** loop. Fixed N → literal list. No `while`.

```deluge
batchSize = 100;
total = fullList.toList().size();
numOfPages = (total / batchSize).ceil();
lst_pageLoop = "0,".repeat(numOfPages).toList(",");
for each index pageNumber in lst_pageLoop
{
    offset = pageNumber * batchSize;
}

page = 1;
perPage = 200;
allData = List();
maxPages = 50;
lst_pageLoop = "0,".repeat(maxPages).toList(",");
hayMas = true;

for each index pageNumber in lst_pageLoop
{
    if(hayMas)
    {
        url = "https://www.zohoapis.com/crm/v8/Contacts?page=" + page + "&per_page=" + perPage;
        resp = invokeUrl [ url: url type: GET connection: "zoho_crm" ];
        batch = resp.get("data");
        if(batch == null || batch.size() == 0) { hayMas = false; }
        else
        {
            for each rec in batch { allData.add(rec); }
            if(batch.size() < perPage) { hayMas = false; }
            else { page = page + 1; }
        }
    }
}
```

---

## HTTP errors

| Code | Meaning | Action |
|--------|-------------|--------|
| 401 INVALID_OAUTHTOKEN | Expired token | Refresh OAuth |
| 403 | Insufficient scope | Add scope in console |
| 429 | Rate limit | Backoff, reduce frequency |
| 400 INVALID_DATA | Malformed payload | Field types, lookup format |
| 204 | DELETE ok with no body | Normal |

---

## Native task vs REST

| Prefer `zoho.*` task | Prefer REST invokeUrl |
|------------------------|-------------------------|
| Documented simple CRUD | New v8 endpoints |
| Less boilerplate | COQL, bulk, settings |
| | Advanced Creator v2.1 params |

See skill `zoho-integration-tasks`.

---

## Full reference

| Topic | File |
|------|---------|
| CRM v8 | `docs/en/apis/crm-v8.md` · `docs/es/apis/crm-v8.md` |
| COQL | `docs/en/coql/crm-coql.md` · `docs/es/coql/crm-coql.md` |
| Books | `docs/en/apis/books.md` · `docs/es/apis/books.md` |
| Desk | `docs/en/apis/desk.md` · `docs/es/apis/desk.md` |
| Other products | `docs/en/apis/other-products.md` · `docs/es/apis/other-products.md` |
| Patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
