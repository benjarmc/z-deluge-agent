---
name: zoho-patterns
description: Aplica patrones de producción, null safety, límites API, paginación, cross-app sync y troubleshooting en código Zoho (Deluge, CRM, Creator, Books). Usar al revisar código existente, depurar errores en producción, diseñar integraciones cross-app o cuando aparecen errores como empty set, INVALID_OAUTHTOKEN, TOO_MANY_REQUESTS o Comparison of non numeric expression.
---

# Patrones de producción — Zoho

Patrones battle-tested para código Deluge e integraciones Zoho en producción.

**Referencia completa:** `docs/patterns/production.md`

---

## Null safety — regla #1

| App | Validar con | Ejemplo |
|-----|-------------|---------|
| CRM | `!= null` | `if(rec.get("Phone") != null)` |
| Creator | `!= ""` | `if(campo != "")` |
| Books/Inventory | `containsKey()` | `if(rec.containsKey("custom_field_hash"))` |
| Desk | `!= null` | igual CRM |
| People | `!= null && != ""` | doble check |

### Lookup seguro

```deluge
lookup = ifNull(rec.get("Account_Name"), {"id": "", "name": ""});
accountId = lookup.get("id");
```

### Listas vacías REST

```deluge
data = resp.get("data");
if(data != null && data.size() > 0) {
    first = data.get(0);
}
```

### Custom fields Finance

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

Query vacío es non-null — nunca `if(query != null)`.

---

## toText() vs toString()

```deluge
m = {"a": 1, "b": "x"};
m.toString()  // {a=1, b=x}  ← NO JSON
m.toText()    // {"a":1,"b":"x"}  ← invokeUrl body
```

---

## Límites API

| App | Límite |
|-----|--------|
| Creator | 50 calls/usuario/min |
| CRM integration | ~25,000/día |
| Books/Inventory | 100/min/org |
| Projects | 300/min |
| Creator statements | 1,000–50,000/ejecución |

1 task en loop × N = N external calls.

---

## IDs y tipos

```deluge
if(recordId.toString() == "66359000000015039")
datos.put("Account_Name", {"id": "66359000000015039"});
newId = resp.get("ID");  // Creator integer
```

---

## Fechas y zonas horarias

```deluge
hoy = today();
iso = hoy.toString("yyyy-MM-dd");
criterioCreator = hoy.toString("dd-MMM-yyyy");
enCDMX = now().toTimeZone("America/Mexico_City");
```

Creator criterios: keywords (`today`, `this week`) o string dd-MMM-yyyy.

---

## Paginación universal

```deluge
pagina = 1;
porPagina = 200;
todos = list();
hayMas = true;

for each i in "".leftpad(49).toList("") {
    if(!hayMas) { break; }
    resp = zoho.crm.getRecords("Contacts", pagina, porPagina);
    data = resp.get("data");
    if(data == null || data.size() == 0) { hayMas = false; }
    else {
        todos.addAll(data);
        if(data.size() < porPagina) { hayMas = false; }
        else { pagina = pagina + 1; }
    }
}
```

Max ~10,000 con guardia 50 páginas.

---

## invokeUrl — respuesta segura

```deluge
resp = invokeUrl [ /* ... */ ];
respStr = resp.toString();
if(respStr.contains("expected_key")) {
    val = resp.get("expected_key");
}
```

No `.containsKey()` directo en respuesta invokeUrl.

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

Siempre idempotente: buscar antes de duplicar.

---

## Reducir statements en loops

- Pre-filtrar en query Creator
- Extraer campos una vez por iteración
- Evitar múltiples `.get()` anidados sin guard
- Batch con bulkCreateRecords cuando aplique

---

## Map anidado en invokeUrl

Cuando `.toText()` falla en estructuras complejas:

```deluge
jsonStr = "{\"data\": [{\"id\": \"" + id + "\"}]}";
```

---

## External calls en loops

Cada integration task = 1 external call. Diseñar:
- Paginación con guardia
- Bulk endpoints cuando existan
- Custom API Creator para batch server-side
- Cola/async para volúmenes altos

---

## Checklist revisión de código

1. Null safety correcta para app origen
2. Empty set guards en queries Creator
3. `.toText()` en bodies JSON
4. IDs CRM como string
5. Lookups como `{"id": "..."}` en updates
6. Paginación en syncs masivos
7. External calls minimizados
8. Log respuesta API para debug: `record.API_Response = resp.toString()`

---

## Troubleshooting — tabla completa

| Error | Causa | Fix |
|-------|-------|-----|
| empty set and values cannot be retrieved | Query Creator vacío | `.count()` inline |
| Comparison of non numeric expression | Date en criterio | dd-MMM-yyyy string |
| INVALID_OAUTHTOKEN | Token expirado | Refresh OAuth |
| TOO_MANY_REQUESTS | Rate limit | Backoff, cola, Custom API |
| Function specified in criteria has returned null | .get() en null / Map anidado | ifNull, containsKey |
| Improper Statement | Ternario/while/null rvalue | if/else, for each |
| Error updating field in ZC_SUBFORM | Edit subform en Validate | On User Input |
| Type mismatch | Number vs string ID | `.toString()` |
| JSON parse error API | toString() en body | `.toText()` |
| OAUTH_SCOPE_MISMATCH | Scope faltante | Agregar en API console |
| INVALID_QUERY (COQL) | Sintaxis COQL | skill zoho-crm-coql |
| Page blank Creator | CSS global en page | Scope `#id` |
| Variable 'x' is not defined | input.x en standalone | argumento directo |
| Not able to find 'Subform' function | Sin prefijo form | ParentForm.Subform() |

---

## Skills relacionados

| Dominio | Skill |
|---------|-------|
| Lenguaje Deluge | zoho-deluge-core |
| Creator gotchas | zoho-deluge-creator |
| COQL | zoho-crm-coql |
| Integration tasks | zoho-integration-tasks |
| REST APIs | zoho-public-apis |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Patrones producción | `docs/patterns/production.md` |
| Índice docs | `docs/INDEX.md` |

