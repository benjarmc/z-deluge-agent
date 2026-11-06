# Deluge in Zoho Creator

> Language: English. Spanish: see the matching path under docs/es/.

Deluge scripting reference within Zoho Creator: forms, workflows, pages, subforms, and integrations.

> Complementary documentation to the base language in [../deluge/language.md](../deluge/language.md).

---

## Execution contexts

| Context | Data access | `input.` prefix |
|----------|----------------|------------------|
| On Load | Initial read/write | Yes (`input.Campo`) |
| On Validate | Pre-save validation | Yes |
| On Success | Post-save | Yes |
| On User Input (field) | Field that changed | Yes |
| On User Input (subform) | Subform row (`row`) | Yes |
| Standalone function | Direct arguments | **No** — use the argument name |
| Custom API | POST parameters | Yes (`input.param`) |
| Creator page | `<%{ }%>` + HTML | Deluge block variables |

---

## Date queries and criteria

**Critical rule:** `Form[...]` criteria do not accept raw Date variables.

```deluge
// Native keywords (preferred)
recsHoy = Tareas[Added_Time in today];
recsSem = Tareas[Due_Date in this week];

// Calculated thresholds — dd-MMM-yyyy format
limite = zoho.currentdate.subDay(7).toString("dd-MMM-yyyy");
atrasados = Tareas[Status == "Pending" && Modified_Time < limite];
```

---

## Empty Set Guard

Assigning an empty query to a variable throws a runtime error.

```deluge
cnt = Tareas[Status == "Open"].count();
total = 0.0;
if(cnt > 0)
{
    recs = Tareas[Status == "Open"];
    for each r in recs { total = total + ifnull(r.Monto, 0.0); }
}
```

---

## Subforms

| Operation | Valid context |
|-----------|-----------------|
| Insert rows | On Load |
| Modify row fields | On User Input of the subform field |
| Read rows | On Load, On Validate, On Success |

```deluge
// Insert row (On Load)
row = Pedidos.Lineas();
row.Producto = "SKU-001";
row.Cantidad = 1;
input.Lineas.insert(row);

// Modify existing row (On User Input of the field)
if(row.Estado == "Completado" && row.Fecha_Completado == null)
{
    row.Fecha_Completado = zoho.currentdate;
}
```

---

## Creator pages

Required structure with a single `<%{ }%>` block:

```
<%{
    total = Clientes[ID != 0].count();
%>
<div id="mi-pagina">
<style>
    #mi-pagina { padding: 24px; }
    #mi-pagina .card { background: #fff; border-radius: 12px; }
</style>
    <div class="card"><h1>Total: <%=total%></h1></div>
</div>
<%
}%>
```

**CSS isolation:** do not use `<!DOCTYPE>`, `<html>`, `<body>`, or global resets (`*`, `body`). Prefix classes with a namespace (`gt-`) and scope them under the container.

---

## Custom APIs (v2.1)

Endpoint: `https://creator.zoho.com/api/v2.1/{owner}/{app}/custom/{api_name}`

OAuth scope: `ZohoCreator.customapi.EXECUTE`

```deluge
// Custom API — arguments as input.*
existing = Clientes[Email == input.email].count();
if(existing > 0)
{
    rec = Clientes[Email == input.email];
    return {"status": "exists", "id": rec.ID};
}
// create record...
return {"status": "created", "id": nuevoId};
```

Useful REST parameters:
- `skip_workflow=form_workflow` — prevents firing the form workflows
- `field_config=custom&fields=Campo1,Campo2` — reduces payload on GET

---

## API v2.1 — field formats

| Type | JSON format |
|------|--------------|
| Text / Number | primitive value |
| Date | `"15-Jun-2026"` (dd-MMM-yyyy) |
| Multi-select | `["A","B"]` |
| Subform | array of nested maps |
| Lookup v2.1 | `{"zc_display_value": "Activo"}` |

PATCH requires explicit `criteria` — omitting it does not update everything; it returns an error.

---

## REST API v2.1 (OpenAPI)

> **Source:** Zoho Creator API v2 OpenAPI (reviewed 2026-11-06).  
> **Base:** `https://www.zohoapis.com/creator/v2.1/` (also `.eu`, `.in`, `.com.au`, `.jp`, `.ca`, `.sa`).  
> Paths below are relative to that base. `{owner}` is the account owner, `{app}` the app link name.

```
GET    /meta/applications
GET    /meta/{owner}/applications
GET    /meta/{owner}/{app}/forms
GET    /meta/{owner}/{app}/forms/{form}/fields
GET    /meta/{owner}/{app}/reports
GET    /meta/{owner}/{app}/pages
GET    /meta/{owner}/{app}/sections

POST   /data/{owner}/{app}/form/{form}                         ← add records
GET    /data/{owner}/{app}/report/{report}                     ← list
GET    /data/{owner}/{app}/report/{report}/{record_ID}         ← get
PATCH  /data/{owner}/{app}/report/{report}                     ← update by criteria
PATCH  /data/{owner}/{app}/report/{report}/{record_ID}
DELETE /data/{owner}/{app}/report/{report}
DELETE /data/{owner}/{app}/report/{report}/{record_ID}

POST   /data/{owner}/{app}/report/{report}/{record_ID}/{field}/upload
GET    /data/{owner}/{app}/report/{report}/{record_ID}/{field}/download

POST   /bulk/{owner}/{app}/report/{report}/read                ← bulk read job
GET    /bulk/{owner}/{app}/report/{report}/read/{job_ID}
GET    /bulk/{owner}/{app}/report/{report}/read/{job_ID}/result
POST   /bulk/{owner}/{app}/form/{form}/insert                  ← upload bulk insert file
PUT    /bulk/{owner}/{app}/form/{form}/insert/{job_ID}         ← start or abort
GET    /bulk/{owner}/{app}/form/{form}/insert/{job_ID}/result

POST   /publish/{owner}/{app}/form/{form}                      ← published form, no OAuth user
GET    /publish/{owner}/{app}/report/{report}
GET    /publish/{owner}/{app}/report/{report}/{record_ID}
```

Records are added on the **form** and read or updated on the **report**. Custom API endpoints (`/custom/{api_name}`) are a separate surface from this spec.

---

## Creator-only gotchas

- No ternary operator (`? :`) — use `if/else`
- No `while`. Official `break` / `continue` work inside `for each`.
- No `addMonth()` — approximate with `addDay(30|90|120)` or rebuild the date manually
- Do not assign `null` as an rvalue — use a boolean flag + a safe default
- Lookups in criteria: use dot notation (`subcontractor.Email == zoho.loginuserid`)
- Form definitions: no `//` comments in the form definition
- Subform insert: only `ParentForm.SubformName()`, not `Map()`

---

## Official sources

- Creator tasks: https://www.zoho.com/deluge/help/creator-tasks.html
- Creator API v2.1: https://www.zoho.com/creator/help/api/v2.1/
- Deluge help: https://www.zoho.com/deluge/help/
