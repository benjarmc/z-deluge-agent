---
name: zoho-patterns
description: Applies production patterns, null safety, API limits, pagination, cross-app sync and troubleshooting in Zoho code (Deluge, CRM, Creator, Books). Use when reviewing production code or errors like empty set, INVALID_OAUTHTOKEN, TOO_MANY_REQUESTS, Comparison of non numeric expression. / Aplica patrones de producción, null safety, límites API, paginación y troubleshooting. Usar al revisar código o errores empty set, INVALID_OAUTHTOKEN, TOO_MANY_REQUESTS.
---

# Production patterns — Zoho

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

Battle-tested patterns for Deluge code and Zoho integrations in production.

**Full reference:** `docs/en/patterns/production.md` · `docs/es/patterns/production.md`

---

## Null safety — rule #1

| App | Validate with | Example |
|-----|-------------|---------|
| CRM | `!= null` | `if(rec.get("Phone") != null)` |
| Creator | `!= ""` | `if(campo != "")` |
| Books/Inventory | `containsKey()` | `if(rec.containsKey("custom_field_hash"))` |
| Desk | `!= null` | same as CRM |
| People | `!= null && != ""` | double check |

### Safe lookup

```deluge
lookup = ifNull(rec.get("Account_Name"), {"id": "", "name": ""});
accountId = lookup.get("id");
```

### Empty REST lists

```deluge
data = resp.get("data");
if(data != null && data.size() > 0) {
    first = data.get(0);
}
```

### Finance custom fields

```deluge
if(rec.containsKey("custom_field_hash")) {
    cf = rec.get("custom_field_hash");
    rfc = ifNull(cf.get("cf_rfc"), "");
}
```

---

## Empty Set Guard (Creator)

```deluge
// ❌ CRASH
recs = Tareas[Status == "Open"];
count = recs.count();

// ✅
count = Tareas[Status == "Open"].count();
if(count > 0) {
    recs = Tareas[Status == "Open"];
}
```

An empty query is non-null — never `if(query != null)`.

---

## toText() vs toString()

```deluge
m = {"a": 1, "b": "x"};
m.toString()  // {a=1, b=x}  ← NO JSON
m.toText()    // {"a":1,"b":"x"}  ← invokeUrl body
```

---

## API limits

| App | Limit |
|-----|--------|
| Creator | 50 calls/user/min |
| CRM integration | ~25,000/day |
| Books/Inventory | 100/min/org |
| Projects | 300/min |
| Creator statements | 1,000–50,000/execution |

1 task in a loop × N = N external calls.

---

## IDs and types

```deluge
if(recordId.toString() == "66359000000015039")
datos.put("Account_Name", {"id": "66359000000015039"});
newId = resp.get("ID");  // Creator integer
```

---

## Dates and time zones

```deluge
hoy = today();
iso = hoy.toString("yyyy-MM-dd");
criterioCreator = hoy.toString("dd-MMM-yyyy");
enCDMX = now().toTimeZone("America/Mexico_City");
```

Creator criteria: keywords (`today`, `this week`) or string dd-MMM-yyyy.

---

## Pagination — dynamic loop (preferred)

When you know the total (or a cap), generate exactly N iterations. Do not use `while`.

```deluge
batchSize = 100;
total = fullList.toList().size();
numOfPages = (total / batchSize).ceil();
lst_pageLoop = "0,".repeat(numOfPages).toList(",");
for each index pageNumber in lst_pageLoop
{
    offset = pageNumber * batchSize;
    // process batch: fullList from offset, batchSize records
}
```

Small fixed N: `for each i in {1,2,3,4,5,6} { }`.

### CRM pagination when the total is unknown up front

```deluge
pagina = 1;
porPagina = 200;
todos = list();
hayMas = true;
maxPages = 50;
lst_pageLoop = "0,".repeat(maxPages).toList(",");

for each index pageNumber in lst_pageLoop
{
    if(hayMas)
    {
        resp = zoho.crm.getRecords("Contacts", pagina, porPagina);
        data = resp.get("data");
        if(data == null || data.size() == 0)
        {
            hayMas = false;
        }
        else
        {
            todos.addAll(data);
            if(data.size() < porPagina)
            {
                hayMas = false;
            }
            else
            {
                pagina = pagina + 1;
            }
        }
    }
}
```

---

## invokeUrl — safe response

```deluge
resp = invokeUrl [ /* ... */ ];
respStr = resp.toString();
if(respStr.contains("expected_key")) {
    val = resp.get("expected_key");
}
```

Do not call `.containsKey()` directly on an invokeUrl response.

---

## Cross-app sync (CRM → Creator)

```deluge
payload = Map();
payload.put("CRM_ID", crmRec.get("id").toString());
payload.put("Email", ifNull(crmRec.get("Email"), ""));

// Buscar existente antes de crear — idempotente
results = zoho.creator.getRecords("app", "Sync_Report");
// update si existe, create si no
```

Always idempotent: look up before duplicating.

---

## Reduce statements in loops

- Pre-filter in the Creator query
- Extract fields once per iteration
- Avoid nested `.get()` without a guard
- Batch with `zoho.crm.v8.bulkCreate` (max 100). Legacy: `bulkCreateRecords`

---

## Nested Map in invokeUrl

When `.toText()` fails on complex structures:

```deluge
jsonStr = "{\"data\": [{\"id\": \"" + id + "\"}]}";
```

---

## External calls in loops

Each integration task = 1 external call. Design for:
- Pagination with a dynamic loop (`"0,".repeat(n).toList(",")`) or a static list (`{1,2,3,...}`)
- Bulk endpoints when they exist
- Creator Custom API for server-side batch
- Queue/async for high volumes

---

## Code review checklist

1. Correct null safety for the source app
2. Empty set guards on Creator queries
3. `.toText()` on JSON bodies
4. CRM IDs as string
5. Lookups as `{"id": "..."}` in updates
6. Pagination on mass syncs
7. Minimized external calls
8. Log API response for debug: `record.API_Response = resp.toString()`

---

## Troubleshooting — full table

| Error | Cause | Fix |
|-------|-------|-----|
| empty set and values cannot be retrieved | Empty Creator query | `.count()` inline |
| Comparison of non numeric expression | Date in criteria | dd-MMM-yyyy string |
| INVALID_OAUTHTOKEN | Expired token | Refresh OAuth |
| TOO_MANY_REQUESTS | Rate limit | Backoff, queue, Custom API |
| Function specified in criteria has returned null | .get() on null / nested Map | ifNull, containsKey |
| Improper Statement | Ternary/while/null rvalue | if/else; static `{1,2,3}` or dynamic `"0,".repeat(n).toList(",")` |
| Error updating field in ZC_SUBFORM | Edit subform in Validate | On User Input |
| Type mismatch | Number vs string ID | `.toString()` |
| JSON parse error API | toString() in body | `.toText()` |
| OAUTH_SCOPE_MISMATCH | Missing scope | Add it in API console |
| INVALID_QUERY (COQL) | COQL syntax | skill zoho-crm-coql |
| Page blank Creator | Global CSS on page | Scope `#id` |
| Variable 'x' is not defined | input.x in standalone | direct argument |
| Not able to find 'Subform' function | Missing form prefix | ParentForm.Subform() |

---

## Related skills

| Domain | Skill |
|---------|-------|
| Deluge language | zoho-deluge-core |
| Creator gotchas | zoho-deluge-creator |
| COQL | zoho-crm-coql |
| Integration tasks | zoho-integration-tasks |
| REST APIs | zoho-public-apis |

---

## Full reference

| Topic | File |
|------|---------|
| Production patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
| Docs index | `docs/INDEX.md` |
