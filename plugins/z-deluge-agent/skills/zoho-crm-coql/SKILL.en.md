---
name: zoho-crm-coql
description: Queries and debugs COQL (CRM Object Query Language) in Zoho CRM v8. Use for select_query, POST /coql, lookup joins, GROUP BY, OFFSET pagination, ZohoCRM.coql.READ, invokeUrl COQL, INVALID_QUERY, SQL-like CRM searches. / Consulta y depura COQL en Zoho CRM v8. Usar para select_query, POST /coql, joins lookup, GROUP BY, OFFSET, ZohoCRM.coql.READ, errores INVALID_QUERY.
---

# COQL — Zoho CRM Object Query Language

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

**Full reference:** `docs/en/coql/crm-coql.md` · `docs/es/coql/crm-coql.md`  
**General CRM REST:** `docs/en/apis/crm-v8.md` · `docs/es/apis/crm-v8.md` (no deep COQL)  
**OAuth/invokeUrl:** skill `zoho-public-apis`

COQL is the SQL-like language for querying CRM records via `POST /crm/v8/coql`.

---

## Endpoint

```
POST https://www.zohoapis.com/crm/v8/coql
Authorization: Zoho-oauthtoken {token}
Content-Type: application/json
```

Body:
```json
{
  "select_query": "select Last_Name, Email from Contacts where Email is not null limit 200"
}
```

Minimum scope: `ZohoCRM.coql.READ`

---

## SELECT syntax

```sql
SELECT field1, field2, field3 FROM ModuleName
SELECT id, Last_Name, Email, Lead_Status FROM Leads
```

- Field API names (case-sensitive)
- Standard modules: Leads, Contacts, Accounts, Deals, Tasks, etc.
- Custom modules: use the exact API name (`Pago_Facturas`)

---

## WHERE — operators

| Operator | Example |
|----------|---------|
| `=` | `Stage = 'Closed Won'` |
| `!=` | `Email != null` |
| `>`, `<`, `>=`, `<=` | `Amount > 50000` |
| `BETWEEN` | `Closing_Date BETWEEN '2024-01-01' AND '2024-12-31'` |
| `IN` | `Stage IN ('Qualification', 'Proposal')` |
| `LIKE` | `Last_Name LIKE '%García%'` |
| `IS NULL` / `IS NOT NULL` | `Phone IS NOT NULL` |

Strings: single quotes `'valor'`  
Datetime ISO: `'2024-01-15T14:30:00+05:30'`  
Date: `'2024-01-15'`

**Not supported:** correlated subqueries.

---

## JOIN via lookup (dot notation)

```sql
SELECT Last_Name, Account_Name.Account_Name, Account_Name.Industry
FROM Contacts
WHERE Account_Name.Industry = 'Technology'
AND Account_Name.Annual_Revenue > 1000000
```

There is no explicit SQL `JOIN` — navigate lookups with `LookupField.SubField`.

---

## ORDER BY, LIMIT, OFFSET

```sql
SELECT Deal_Name, Amount, Stage FROM Deals
WHERE Stage != 'Closed Lost'
ORDER BY Amount DESC
LIMIT 200 OFFSET 0
```

| Limit | Value |
|--------|-------|
| Records/request | **200 max** (LIMIT) |
| Total pageable | **10,000** |
| SELECT fields | **500 max** |

---

## Aggregates

```sql
SELECT COUNT(*) FROM Leads WHERE Lead_Status = 'Contacted'

SELECT Stage, SUM(Amount), AVG(Amount), MAX(Amount), MIN(Amount)
FROM Deals
GROUP BY Stage
HAVING SUM(Amount) > 100000
```

---

## Examples by module

### Active Leads

```sql
SELECT id, Last_Name, Email, Company, Lead_Status
FROM Leads
WHERE Lead_Status NOT IN ('Junk Lead', 'Lost Lead')
AND Created_Time >= '2024-01-01T00:00:00+00:00'
ORDER BY Created_Time DESC
LIMIT 200
```

### Contacts with Account

```sql
SELECT id, Full_Name, Email, Account_Name.Account_Name, Account_Name.Industry
FROM Contacts
WHERE Account_Name IS NOT NULL
AND Email IS NOT NULL
LIMIT 200
```

### Deals pipeline Q4

```sql
SELECT id, Deal_Name, Stage, Amount, Closing_Date, Owner.email
FROM Deals
WHERE Closing_Date BETWEEN '2024-10-01' AND '2024-12-31'
AND Stage NOT IN ('Closed Won', 'Closed Lost')
ORDER BY Amount DESC
LIMIT 200
```

### Custom module

```sql
SELECT id, Nombre, Estado, Cliente_Lookup.Nombre_Cliente
FROM Facturacion_Custom
WHERE Estado = 'Pendiente'
LIMIT 100
```

---

## invokeUrl from Deluge

```deluge
email = input.Email;
emailSafe = email.replaceAll("'", "\\'", false);
query = "select id, Last_Name, Email from Contacts where Email = '" + emailSafe + "' limit 1";

body = Map();
body.put("select_query", query);
headers = Map();
headers.put("Content-Type", "application/json");

resp = invokeUrl [
    url: "https://www.zohoapis.com/crm/v8/coql"
    type: POST
    body: body.toText()
    headers: headers
    connection: "zoho_crm_v8"
];

data = resp.get("data");
if(data != null && data.size() > 0) {
    contactId = data.get(0).get("id");
}
```

**Critical:** `.toText()` on the body. Escape `'` in dynamic values.

---

## COQL pagination

**Dynamic** loop (no `while`). Cap pages if the total is unknown:

```deluge
offset = 0;
limit = 200;
allRecs = List();
more = true;
maxPages = 50;
lst_pageLoop = "0,".repeat(maxPages).toList(",");

for each index pageNumber in lst_pageLoop
{
    if(more)
    {
        q = "select id, Last_Name from Contacts where Email is not null limit " + limit + " offset " + offset;
        b = Map();
        b.put("select_query", q);
        r = invokeUrl [
            url: "https://www.zohoapis.com/crm/v8/coql"
            type: POST
            body: b.toText()
            headers: {"Content-Type": "application/json"}
            connection: "zoho_crm"
        ];
        batch = r.get("data");
        if(batch == null || batch.size() == 0) { more = false; }
        else {
            for each rec in batch { allRecs.add(rec); }
            if(batch.size() < limit) { more = false; }
            else { offset = offset + limit; }
        }
    }
}
```

For >10,000 records → Bulk Read API (`POST /read`).

---

## OAuth scopes

```
ZohoCRM.coql.READ
ZohoCRM.modules.ALL
ZohoCRM.modules.contacts.READ
ZohoCRM.modules.deals.READ
```

---

## COQL vs alternatives

| Method | When |
|--------|--------|
| COQL | Lookup joins, aggregates, specific SELECT, complex filters |
| `zoho.crm.searchRecords` | Simple criteria from Deluge |
| `GET /{module}/search` | REST equivalent of searchRecords |
| Bulk Read | Mass export >10K |

---

## Common errors

| Error | Cause | Fix |
|-------|-------|-----|
| INVALID_QUERY | Syntax / API name typo | Verify FROM, quotes, fields |
| OAUTH_SCOPE_MISMATCH | Missing COQL scope | `ZohoCRM.coql.READ` |
| INVALID_MODULE | Wrong module | Settings → API name |
| Empty data | Filter too restrictive | Try without WHERE |
| LIMIT exceeded | >200 without paging | LIMIT 200 + OFFSET |
| SQL injection | Unescaped input | `replaceAll("'", "\\'")` |

---

## Full reference

| Topic | File |
|------|---------|
| Full COQL | `docs/en/coql/crm-coql.md` · `docs/es/coql/crm-coql.md` |
| CRM v8 REST | `docs/en/apis/crm-v8.md` · `docs/es/apis/crm-v8.md` |
| Pagination patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
