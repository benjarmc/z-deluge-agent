# COQL — CRM Object Query Language (Zoho CRM v8)

> **Fuente oficial:** https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html  
> **Endpoint:** `POST https://www.zohoapis.com/crm/v8/coql`  
> **Scope OAuth:** `ZohoCRM.coql.READ`  
> **Skill relacionado:** `zoho-crm-coql` — guía operativa para agentes

COQL permite consultar registros CRM con sintaxis SQL-like. Para CRUD REST general (sin COQL profundo), ver [`../apis/crm-v8.md`](../apis/crm-v8.md).

---

## Endpoint y autenticación

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

Respuesta exitosa: campo `data` (array de registros), `info` con metadatos de paginación.

---

## Sintaxis COQL

### SELECT básico

```sql
SELECT field1, field2 FROM ModuleName
SELECT id, Last_Name, Email FROM Contacts
```

### WHERE — operadores soportados

| Operador | Ejemplo |
|----------|---------|
| `=` | `WHERE Stage = 'Closed Won'` |
| `!=` | `WHERE Email != null` |
| `>`, `<`, `>=`, `<=` | `WHERE Amount > 1000` |
| `BETWEEN` | `WHERE Created_Time BETWEEN '2024-01-01' AND '2024-12-31'` |
| `IN` | `WHERE Stage IN ('Qualification', 'Proposal')` |
| `LIKE` | `WHERE Last_Name LIKE '%García%'` |
| `IS NULL` / `IS NOT NULL` | `WHERE Email IS NOT NULL` |

**Notas:**
- Strings entre comillas simples
- Fechas datetime en ISO 8601: `'2024-01-15T14:30:00+05:30'`
- Fechas date: `'2024-01-15'`
- API names de campos (case-sensitive)

### JOIN vía lookup (dot notation)

```sql
SELECT Last_Name, Account_Name.Account_Name, Account_Name.Industry
FROM Contacts
WHERE Account_Name.Industry = 'Technology'
```

No hay JOIN SQL explícito — se navega lookups con `LookupField.SubField`.

### ORDER BY y LIMIT

```sql
SELECT Last_Name, Amount FROM Deals
ORDER BY Amount DESC
LIMIT 200 OFFSET 0
```

### Agregados

```sql
SELECT COUNT(*) FROM Leads WHERE Lead_Status = 'Contacted'

SELECT Stage, SUM(Amount), AVG(Amount), MAX(Amount), MIN(Amount)
FROM Deals
GROUP BY Stage
HAVING SUM(Amount) > 100000
```

---

## Límites COQL

| Límite | Valor |
|--------|-------|
| Registros por request | **200** (usar LIMIT) |
| Total acumulable con paginación | hasta **10,000** |
| Campos en SELECT | máximo **500** |
| Subconsultas correlacionadas | **no soportadas** |
| CTEs | soporte parcial en v8 |

Para volúmenes mayores → Bulk Read API (`POST /read`) en `crm-v8.md`.

---

## Ejemplos por módulo

### Leads

```sql
SELECT id, Last_Name, Email, Lead_Status, Company
FROM Leads
WHERE Lead_Status = 'Not Contacted' AND Created_Time >= '2024-06-01'
ORDER BY Created_Time DESC
LIMIT 200
```

### Contacts con lookup a Account

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

### Módulo custom

```sql
SELECT id, Nombre_Campo, Lookup_Modulo.Campo_Relacionado
FROM Mi_Modulo_Custom
WHERE Estado = 'Activo'
LIMIT 50
```

Usar API name exacto del módulo custom (ej: `Pago_Facturas`).

---

## invokeUrl desde Deluge

```deluge
email = input.Email;
// Escapar comillas en valores dinámicos
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

**Crítico:** usar `.toText()` en body, no `.toString()`.

---

## Paginación COQL

```deluge
offset = 0;
limit = 200;
allRecords = List();
hasMore = true;

for each i in "".leftpad(49).toList("")
{
    if(!hasMore) { break; }
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
```

---

## Scopes OAuth

```
ZohoCRM.coql.READ          ← mínimo para COQL
ZohoCRM.modules.ALL        ← acceso amplio a módulos
ZohoCRM.modules.contacts.READ  ← módulo específico
```

---

## Errores comunes

| Error / síntoma | Causa | Solución |
|-----------------|-------|----------|
| `INVALID_QUERY` | Sintaxis SQL incorrecta | Verificar API names, comillas, FROM Module |
| `OAUTH_SCOPE_MISMATCH` | Falta scope COQL | Agregar `ZohoCRM.coql.READ` |
| `INVALID_MODULE` | Módulo inexistente o typo | Verificar API name en Settings → Modules |
| `LIMIT_EXCEEDED` | Más de 200 sin paginar | LIMIT 200 + OFFSET |
| Campo lookup vacío | Registro sin relación | Filtrar `IS NOT NULL` en lookup |
| Injection en query dinámica | Input usuario sin escapar | Escapar `'` en strings concatenados |
| `INVALID_DATA` en datetime | Formato fecha incorrecto | ISO 8601 con timezone |

---

## COQL vs searchRecords vs getRecords

| Método | Cuándo usar |
|--------|-------------|
| `zoho.crm.searchRecords` (Deluge task) | Criterios simples, pocos campos |
| COQL | Joins lookup, agregados, SELECT específico, filtros complejos |
| `GET /{module}/search` REST | Mismo que searchRecords vía REST |
| Bulk Read | >10,000 registros, export masivo |

Para COQL profundo y ejemplos operativos → skill `zoho-crm-coql`.

---

## Referencias

- [COQL Overview (oficial)](https://www.zoho.com/crm/developer/docs/api/v8/COQL-Overview.html)
- [CRM API v8 general](../apis/crm-v8.md)
- [Patrones producción](../patterns/production.md)
