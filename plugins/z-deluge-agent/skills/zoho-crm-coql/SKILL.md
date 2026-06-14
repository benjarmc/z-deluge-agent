---
name: zoho-crm-coql
description: Consulta y depura COQL (CRM Object Query Language) en Zoho CRM v8. Usar para select_query, POST /coql, joins lookup, agregados GROUP BY, paginación OFFSET, scope ZohoCRM.coql.READ, invokeUrl COQL desde Deluge, errores INVALID_QUERY o cuando el usuario menciona COQL, CRM query language o búsquedas SQL-like en CRM.
---

# COQL — Zoho CRM Object Query Language

**Referencia completa:** `docs/coql/crm-coql.md`  
**CRM REST general:** `docs/apis/crm-v8.md` (sin COQL profundo)  
**OAuth/invokeUrl:** skill `zoho-public-apis`

COQL es el lenguaje SQL-like para consultar registros CRM vía `POST /crm/v8/coql`.

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

Scope mínimo: `ZohoCRM.coql.READ`

---

## Sintaxis SELECT

```sql
SELECT field1, field2, field3 FROM ModuleName
SELECT id, Last_Name, Email, Lead_Status FROM Leads
```

- API names de campos (case-sensitive)
- Módulos estándar: Leads, Contacts, Accounts, Deals, Tasks, etc.
- Módulos custom: usar API name exacto (`Pago_Facturas`)

---

## WHERE — operadores

| Operador | Ejemplo |
|----------|---------|
| `=` | `Stage = 'Closed Won'` |
| `!=` | `Email != null` |
| `>`, `<`, `>=`, `<=` | `Amount > 50000` |
| `BETWEEN` | `Closing_Date BETWEEN '2024-01-01' AND '2024-12-31'` |
| `IN` | `Stage IN ('Qualification', 'Proposal')` |
| `LIKE` | `Last_Name LIKE '%García%'` |
| `IS NULL` / `IS NOT NULL` | `Phone IS NOT NULL` |

Strings: comillas simples `'valor'`  
Datetime ISO: `'2024-01-15T14:30:00+05:30'`  
Date: `'2024-01-15'`

**No soportado:** subconsultas correlacionadas.

---

## JOIN via lookup (dot notation)

```sql
SELECT Last_Name, Account_Name.Account_Name, Account_Name.Industry
FROM Contacts
WHERE Account_Name.Industry = 'Technology'
AND Account_Name.Annual_Revenue > 1000000
```

No hay `JOIN` SQL explícito — navegar lookups con `LookupField.SubField`.

---

## ORDER BY, LIMIT, OFFSET

```sql
SELECT Deal_Name, Amount, Stage FROM Deals
WHERE Stage != 'Closed Lost'
ORDER BY Amount DESC
LIMIT 200 OFFSET 0
```

| Límite | Valor |
|--------|-------|
| Registros/request | **200 max** (LIMIT) |
| Total paginable | **10,000** |
| Campos SELECT | **500 max** |

---

## Agregados

```sql
SELECT COUNT(*) FROM Leads WHERE Lead_Status = 'Contacted'

SELECT Stage, SUM(Amount), AVG(Amount), MAX(Amount), MIN(Amount)
FROM Deals
GROUP BY Stage
HAVING SUM(Amount) > 100000
```

---

## Ejemplos por módulo

### Leads activos

```sql
SELECT id, Last_Name, Email, Company, Lead_Status
FROM Leads
WHERE Lead_Status NOT IN ('Junk Lead', 'Lost Lead')
AND Created_Time >= '2024-01-01T00:00:00+00:00'
ORDER BY Created_Time DESC
LIMIT 200
```

### Contacts con Account

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

## invokeUrl desde Deluge

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

**Crítico:** `.toText()` en body. Escapar `'` en valores dinámicos.

---

## Paginación COQL

```deluge
offset = 0;
limit = 200;
allRecs = List();
more = true;

for each i in "".leftpad(49).toList("") {
    if(!more) { break; }
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
```

Para >10,000 registros → Bulk Read API (`POST /read`).

---

## Scopes OAuth

```
ZohoCRM.coql.READ
ZohoCRM.modules.ALL
ZohoCRM.modules.contacts.READ
ZohoCRM.modules.deals.READ
```

---

## COQL vs alternativas

| Método | Cuándo |
|--------|--------|
| COQL | Joins lookup, agregados, SELECT específico, filtros complejos |
| `zoho.crm.searchRecords` | Criterios simples desde Deluge |
| `GET /{module}/search` | REST equivalente searchRecords |
| Bulk Read | Export masivo >10K |

---

## Errores comunes

| Error | Causa | Fix |
|-------|-------|-----|
| INVALID_QUERY | Sintaxis / typo API name | Verificar FROM, comillas, campos |
| OAUTH_SCOPE_MISMATCH | Sin scope COQL | `ZohoCRM.coql.READ` |
| INVALID_MODULE | Módulo incorrecto | Settings → API name |
| Empty data | Filtro muy restrictivo | Probar sin WHERE |
| LIMIT exceeded | >200 sin paginar | LIMIT 200 + OFFSET |
| SQL injection | Input sin escapar | `replaceAll("'", "\\'")` |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| COQL completo | `docs/coql/crm-coql.md` |
| CRM v8 REST | `docs/apis/crm-v8.md` |
| Patrones paginación | `docs/patterns/production.md` |

