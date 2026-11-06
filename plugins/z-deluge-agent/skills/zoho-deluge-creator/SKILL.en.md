---
name: zoho-deluge-creator
description: Writes, reviews, and debugs Deluge in Zoho Creator: forms, workflows, HTML pages, subforms, Custom APIs, REST API v2.1, skip_workflow, record fetch, empty set, OAuth, production gotchas. Use for Zoho Creator, thisapp, openUrl, Creator pages, creator.zoho.com/api, Form[criteria]. / Escribe, revisa y depura Deluge en Zoho Creator: formularios, workflows, páginas HTML, subformularios, Custom APIs, API REST v2.1, skip_workflow, empty set. Usar para Zoho Creator, thisapp, openUrl, páginas Creator.
---

# Deluge in Zoho Creator

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

**Full reference:** `docs/en/creator/deluge-in-creator.md` · `docs/es/creator/deluge-in-creator.md`  
**Patterns:** `docs/en/patterns/production.md` · `docs/es/patterns/production.md`  
**Core language:** skill `zoho-deluge-core`

---

## Creator context

Zoho Creator runs Deluge in:
- **Form workflows:** On Load, On Validate, On Success, On User Input
- **Report workflows:** buttons, schedules
- **HTML pages** with `<%{ %>` blocks
- **Custom APIs** (HTTP microservices)
- **Standalone functions** (no `input.`)

---

## Record fetch and criteria

```deluge
// Obtener un registro por ID
rec = MiFormulario[ID == input.ID];

// Contar sin empty set crash
cnt = Tareas[Status == "Open"].count();
if(cnt > 0) {
    recs = Tareas[Status == "Open"];
    for each r in recs { info r.Nombre; }
}

// Criterios de fecha — NO variable Date cruda
hoyStr = zoho.currentdate.toString("dd-MMM-yyyy");
recs = Citas[Fecha == hoyStr];
// O keywords: today, this week, this month
```

### Empty Set Guard (required)

```deluge
// ❌ CRASH: empty set and values cannot be retrieved
recs = Tareas[Status == "Inexistente"];
n = recs.count();

// ✅
n = Tareas[Status == "Inexistente"].count();
if(n > 0) {
    recs = Tareas[Status == "Inexistente"];
}
```

An empty query is **non-null** — never `if(query != null)`.

---

## input. vs arguments

| Context | Syntax |
|----------|----------|
| Form workflow (Load/Validate/Success/User Input) | `input.Campo` |
| Standalone function | direct argument — **not** `input.x` |
| Custom API POST | `input.paramName` |
| Schedule | no input — use global criteria |

---

## CRUD in Creator (native Deluge)

```deluge
// Insert
insert into Clientes [ Nombre = "Acme" Email = "a@acme.com" ];

// Update
update Clientes[ID == recId] [ Status = "Activo" ];

// Delete
delete from Clientes[ID == recId];

// Fetch múltiple
for each c in Clientes[Status == "Activo"] {
    info c.Nombre;
}
```

---

## Subforms

```deluge
// Insertar fila (On Load / On User Input)
row = Pedidos.Lineas();
row.SKU = "A-100";
row.Qty = 2;
row.Precio = 99.50;
input.Lineas.insert(row);

// Lazy seed On Edit
if(input.Onboarding_Stages.count() == 0) {
    row = Vendors.Onboarding_Stages();
    row.Etapa = "Inicial";
    input.Onboarding_Stages.insert(row);
}

// Modificar fila — keyword row en On User Input del subform
if(row.Estado == "Done" && row.Fecha == null) {
    row.Fecha = zoho.currentdate;
}
```

| Operation | On Load | On Validate/Success | Subform On User Input |
|-----------|---------|---------------------|----------------------|
| Insert rows | ✅ | ❌ | — |
| Modify row fields | — | ❌ | ✅ |
| Read rows | ✅ | ✅ | ✅ |

Error `Error updating field in ZC_SUBFORM` → move the logic to On User Input.

---

## Navigation

```deluge
// Abrir formulario interno
thisapp.Open.DetalleProyecto(input.Proyecto);

// Página Creator
openUrl("#Page:Dashboard", "same window");
openUrl("#Form:Clientes?ID=" + recId, "new window");
```

---

## Creator pages (HTML + Deluge)

```
<%{
    abiertas = Tickets[Status == "Open"].count();
    hoyStr = zoho.currentdate.toString("MMM dd, yyyy");
%>
<div id="dash-tickets">
<style>
    #dash-tickets { padding: 24px; font-family: Inter, sans-serif; }
    #dash-tickets .card { background: #fff; border-radius: 12px; padding: 20px; }
</style>
    <div class="card">
        <h1><%=abiertas%> tickets abiertos</h1>
        <p><%=hoyStr%></p>
        <a href="#Page:Lista">Ver todos</a>
    </div>
</div>
<%
}%>
```

Rules:
- One `<%{ }%>` block per page
- No global `<!DOCTYPE>`, `<html>`, or `<body>`
- CSS scoped under `#contenedor-id`
- Classes with a namespace prefix (`dash-`, `app-`)

---

## Custom API

Endpoint: `POST https://creator.zoho.com/api/v2.1/{owner}/{app}/custom/{api_name}`

```deluge
// Custom API: Crear_Pedido
cnt = Clientes[Email == input.email].count();
if(cnt > 0) {
    c = Clientes[Email == input.email];
    clienteId = c.ID;
} else {
    m = Map();
    m.put("Email", input.email);
    m.put("Nombre", input.nombre);
    resp = zoho.creator.createRecord(zoho.adminuser, zoho.appname, "Clientes", m, Map(), "creator_oauth");
    clienteId = resp.get("ID");
}
result = Map();
result.put("cliente_id", clienteId);
return result;
```

OAuth scope: `ZohoCreator.customapi.EXECUTE`

---

## REST API v2.1 (invokeUrl)

OpenAPI base: `https://www.zohoapis.com/creator/v2.1/` (DC: `.eu`, `.in`, `.com.au`, `.jp`, `.ca`, `.sa`).

```
POST   /data/{owner}/{app}/form/{form}                 ← create (max 200/request)
GET    /data/{owner}/{app}/report/{report}             ← list
PATCH  /data/{owner}/{app}/report/{report}             ← update (criteria required)
GET    /meta/{owner}/{app}/forms
POST   /bulk/{owner}/{app}/report/{report}/read
POST   /publish/{owner}/{app}/form/{form}              ← published, no user OAuth
```

Custom API stays at `https://creator.zoho.com/api/v2.1/{owner}/{app}/custom/{api_name}`. Detail: `docs/en/creator/deluge-in-creator.md`.

Key query parameters:
- `skip_workflow=form_workflow` — suppresses On Success/Validate
- `field_config=quick_view` — minimal fields
- `max_records=200`
- `record_cursor` — pagination
- `criteria` — required on PATCH: `"ID == 12345"`

Date payload: `"15-Jun-2026"`  
Subforms: array of nested maps  
Credits: GET/POST = 1; heavy operations = 5+

```deluge
url = "https://www.zohoapis.com/creator/v2.1/data/owner/app/form/Pedidos?skip_workflow=form_workflow";
payload = Map();
payload.put("Nombre", "Pedido 001");
resp = invokeUrl [ url: url type: POST body: payload.toText() connection: "creator_oauth" ];
```

For `skip_workflow` / `field_config` / `max_records` from **another** service: `zoho.creator.v2_1.*`. From Creator: V2 `zoho.creator.*` or `invokeUrl` v2.1. `record_cursor` still needs invokeUrl.

---

## zoho.creator.* (V2) and `zoho.creator.v2_1.*`

Separate indexes: skill `zoho-integration-tasks`. V2: owner + app + form/report. Update by ID = `updateRecord`. Bulk create = `createRecord` with a List (no `createRecords` in V2). V2.1: `createRecords` / `updateRecordById`; **not** from Creator.

```deluge
empty = Map();
zoho.creator.createRecord(zoho.adminuser, zoho.appname, "Form", dataMap, empty, "creator_oauth");
zoho.creator.getRecordById(zoho.adminuser, zoho.appname, "Report", recordId, "creator_oauth");
zoho.creator.updateRecord(zoho.adminuser, zoho.appname, "Report", recordId, data, empty, "creator_oauth");
zoho.creator.updateRecords(zoho.adminuser, zoho.appname, "Report", "ID!=0", data, empty, "creator_oauth");
```

Creator null safety: validate with `!= ""` not `!= null`.

---

## Creator gotchas (checklist)

| ❌ Do not use | ✅ Alternative |
|-----------|----------------|
| Ternary `? :` | `if/else` |
| `while` | Static `{1,2,3}` or dynamic `"0,".repeat(n).toList(",")` |
| Skip / exit `for each` | `continue;` / `break;` (official) |
| Inconsistent `addMonth()` | `addDay(30)` or manual |
| `variable = null` | flag + default |
| Lookup by ID in criteria | `Lookup.Campo == valor` |
| `query != null` | `.count() > 0` |
| Nested Map invokeUrl | Manual JSON string |
| Comments in form definition | Only in workflow scripts |
| Raw Date in criteria | string dd-MMM-yyyy or keyword |

---

## Creator limits

| Limit | Value |
|--------|-------|
| API calls | 50/user/minute |
| Statements | 1,000–50,000 depending on plan |
| POST form records | 200/request |
| External calls | 1 per integration task in a loop |

---

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| empty set and values cannot be retrieved | Empty query → variable | `.count()` inline first |
| Comparison of non numeric expression | Date in criteria | dd-MMM-yyyy string |
| Error updating field in ZC_SUBFORM | Edit subform in Validate | On User Input |
| Not able to find 'Subform' function | Missing form prefix | `ParentForm.Subform()` |
| Variable 'x' is not defined | `input.x` in standalone | direct argument |
| Page blank | Global CSS / `<html>` | Scope under `#id` |
| TOO_MANY_REQUESTS | API rate limit | Custom API / queue |

---

## Full reference

| Topic | File |
|------|---------|
| Deluge in Creator | `docs/en/creator/deluge-in-creator.md` · `docs/es/creator/deluge-in-creator.md` |
| Deluge language | `docs/en/deluge/language.md` · `docs/es/deluge/language.md` |
| Patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
| Creator integration tasks | `docs/en/integration-tasks/index.md` · `docs/es/integration-tasks/index.md` § Creator |
