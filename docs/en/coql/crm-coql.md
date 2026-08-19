# COQL — CRM Object Query Language (Zoho CRM v8)

> Language: English. Spanish: see the matching path under docs/es/.

> **Official source:** https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html  
> **Endpoint:** `POST https://www.zohoapis.com/crm/v8/coql`  
> **OAuth scope:** `ZohoCRM.coql.READ`  
> **Related skill:** `zoho-crm-coql` — operational guide for agents

COQL lets you query CRM records with SQL-like syntax. For general REST CRUD (without deep COQL), see [`../apis/crm-v8.md`](../apis/crm-v8.md).

---

## Endpoint and authentication

```
POST /crm/v8/coql
Authorization: Zoho-oauthtoken {access_token}
Content-Type: application/json
```

Body:
```json
{
  "select_query": "select Last_Name, Email, Amount from Contacts where Created_Time >= '2024-01-01T00:00:00+00:00' limit 50"
}
```

Successful response: `data` field (array of records), `info` with pagination metadata.

---

## COQL syntax

### Basic SELECT

```sql
SELECT field1, field2 FROM ModuleName
SELECT id, Last_Name, Email FROM Contacts
```

### WHERE — supported operators

| Operator | Example |
|----------|---------|
| `=` | `WHERE Stage = 'Closed Won'` |
| `!=` | `WHERE Email != null` |
| `>`, `<`, `>=`, `<=` | `WHERE Amount > 1000` |
| `BETWEEN` | `WHERE Created_Time BETWEEN '2024-01-01' AND '2024-12-31'` |
| `IN` | `WHERE Stage IN ('Qualification', 'Proposal')` |
| `LIKE` | `WHERE Last_Name LIKE '%García%'` |
| `IS NULL` / `IS NOT NULL` | `WHERE Email IS NOT NULL` |

**Notes:**
- Strings in single quotes
- Datetime dates in ISO 8601: `'2024-01-15T14:30:00+05:30'`
- Date dates: `'2024-01-15'`
- Field API names (case-sensitive)

### JOIN via lookup (dot notation)

```sql
SELECT Last_Name, Account_Name.Account_Name, Account_Name.Industry
FROM Contacts
WHERE Account_Name.Industry = 'Technology'
```

There is no explicit SQL JOIN — lookups are navigated with `LookupField.SubField`.

### ORDER BY and LIMIT

```sql
SELECT Last_Name, Amount FROM Deals
ORDER BY Amount DESC
LIMIT 200 OFFSET 0
```

### Aggregates

```sql
SELECT COUNT(*) FROM Leads WHERE Lead_Status = 'Contacted'

SELECT Stage, SUM(Amount), AVG(Amount), MAX(Amount), MIN(Amount)
FROM Deals
GROUP BY Stage
HAVING SUM(Amount) > 100000
```

---

## COQL limits

| Limit | Value |
|--------|-------|
| Records per request | **200** (use LIMIT) |
| Accumulated total with pagination | up to **10,000** |
| Fields in SELECT | maximum **500** |
| Correlated subqueries | **not supported** |
| CTEs | partial support in v8 |

For larger volumes → Bulk Read API (`POST /read`) in `crm-v8.md`.

---

## Examples by module

### Leads

```sql
SELECT id, Last_Name, Email, Lead_Status, Company
FROM Leads
WHERE Lead_Status = 'Not Contacted' AND Created_Time >= '2024-06-01'
ORDER BY Created_Time DESC
LIMIT 200
```

### Contacts with Account lookup

```sql
SELECT id, Full_Name, Email, Account_Name.Account_Name
FROM Contacts
WHERE Account_Name.Industry = 'Manufacturing'
LIMIT 100
```

### Deals pipeline

```sql
SELECT Deal_Name, Stage, Amount, Closing_Date, Account_Name.Account_Name
FROM Deals
WHERE Stage NOT IN ('Closed Won', 'Closed Lost')
AND Closing_Date BETWEEN '2024-01-01' AND '2024-12-31'
ORDER BY Amount DESC
LIMIT 200
```

### Custom module

```sql
SELECT id, Nombre_Campo, Lookup_Modulo.Campo_Relacionado
FROM Mi_Modulo_Custom
WHERE Estado = 'Activo'
LIMIT 50
```

Use the exact API name of the custom module (e.g. `Pago_Facturas`).

---

## invokeUrl from Deluge

```deluge
email = input.Email;
// Escape quotes in dynamic values
emailSafe = email.replaceAll("'", "\\'", false);
searchQuery = "select id, Last_Name, Email from Contacts where Email = '" + emailSafe + "' limit 1";

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
if(data != null && data.size() > 0)
{
    contactId = data.get(0).get("id");
    info contactId;
}
```

**Critical:** use `.toText()` in the body, not `.toString()`.

---

## COQL pagination

**Dynamic** loop (no `while`):

```deluge
offset = 0;
limit = 200;
allRecords = List();
hasMore = true;
maxPages = 50;
lst_pageLoop = "0,".repeat(maxPages).toList(",");

for each index pageNumber in lst_pageLoop
{
    if(hasMore)
    {
        query = "select id, Last_Name from Contacts where Email is not null limit " + limit + " offset " + offset;
        body = Map();
        body.put("select_query", query);
        resp = invokeUrl [
            url: "https://www.zohoapis.com/crm/v8/coql"
            type: POST
            body: body.toText()
            headers: {"Content-Type": "application/json"}
            connection: "zoho_crm"
        ];
        batch = resp.get("data");
        if(batch == null || batch.size() == 0) { hasMore = false; }
        else
        {
            for each rec in batch { allRecords.add(rec); }
            if(batch.size() < limit) { hasMore = false; }
            else { offset = offset + limit; }
        }
    }
}
```

---

## OAuth scopes

```
ZohoCRM.coql.READ          ← minimum for COQL
ZohoCRM.modules.ALL        ← broad module access
ZohoCRM.modules.contacts.READ  ← specific module
```

---

## Common errors

| Error / symptom | Cause | Solution |
|-----------------|-------|----------|
| `INVALID_QUERY` | Incorrect SQL syntax | Check API names, quotes, FROM Module |
| `OAUTH_SCOPE_MISMATCH` | Missing COQL scope | Add `ZohoCRM.coql.READ` |
| `INVALID_MODULE` | Nonexistent module or typo | Check API name in Settings → Modules |
| `LIMIT_EXCEEDED` | More than 200 without paging | LIMIT 200 + OFFSET |
| Empty lookup field | Record with no relation | Filter `IS NOT NULL` on the lookup |
| Injection in dynamic query | Unescaped user input | Escape `'` in concatenated strings |
| `INVALID_DATA` on datetime | Incorrect date format | ISO 8601 with timezone |

---

## COQL vs searchRecords vs getRecords

| Method | When to use |
|--------|-------------|
| `zoho.crm.searchRecords` (Deluge task) | Simple criteria, few fields |
| COQL | Lookup joins, aggregates, specific SELECT, complex filters |
| `GET /{module}/search` REST | Same as searchRecords via REST |
| Bulk Read | >10,000 records, bulk export |

For deep COQL and operational examples → skill `zoho-crm-coql`.

---

## References

- [COQL Overview (official)](https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html)
- [CRM API v8 general](../apis/crm-v8.md)
- [Production patterns](../patterns/production.md)
