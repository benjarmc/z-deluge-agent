# Deluge en Zoho Creator

> Idioma: español. English: see the matching path under `docs/en/`.

Referencia de scripting Deluge dentro de Zoho Creator: formularios, workflows, páginas, subformularios e integraciones.

> Documentación complementaria al lenguaje base en [../deluge/language.md](../deluge/language.md).

---

## Contextos de ejecución

| Contexto | Acceso a datos | Prefijo `input.` |
|----------|----------------|------------------|
| On Load | Lectura/escritura inicial | Sí (`input.Campo`) |
| On Validate | Validación pre-guardado | Sí |
| On Success | Post-guardado | Sí |
| On User Input (campo) | Campo que cambió | Sí |
| On User Input (subform) | Fila del subform (`row`) | Sí |
| Función standalone | Argumentos directos | **No** — usar nombre del argumento |
| Custom API | Parámetros POST | Sí (`input.param`) |
| Página Creator | `<%{ }%>` + HTML | Variables del bloque Deluge |

---

## Consultas y criterios de fecha

**Regla crítica:** los criterios `Form[...]` no aceptan variables Date crudas.

```deluge
// Keywords nativos (preferidos)
recsHoy = Tareas[Added_Time in today];
recsSem = Tareas[Due_Date in this week];

// Umbrales calculados — formato dd-MMM-yyyy
limite = zoho.currentdate.subDay(7).toString("dd-MMM-yyyy");
atrasados = Tareas[Status == "Pending" && Modified_Time < limite];
```

---

## Empty Set Guard

Asignar un query vacío a variable lanza error en runtime.

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

## Subformularios

| Operación | Contexto válido |
|-----------|-----------------|
| Insertar filas | On Load |
| Modificar campos de fila | On User Input del campo subform |
| Leer filas | On Load, On Validate, On Success |

```deluge
// Insertar fila (On Load)
row = Pedidos.Lineas();
row.Producto = "SKU-001";
row.Cantidad = 1;
input.Lineas.insert(row);

// Modificar fila existente (On User Input del campo)
if(row.Estado == "Completado" && row.Fecha_Completado == null)
{
    row.Fecha_Completado = zoho.currentdate;
}
```

---

## Páginas Creator

Estructura obligatoria con bloque único `<%{ }%>`:

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

**Aislamiento CSS:** no usar `<!DOCTYPE>`, `<html>`, `<body>` ni resets globales (`*`, `body`). Prefijar clases con namespace (`gt-`) y scope bajo el contenedor.

---

## Custom APIs (v2.1)

Endpoint: `https://creator.zoho.com/api/v2.1/{owner}/{app}/custom/{api_name}`

Scope OAuth: `ZohoCreator.customapi.EXECUTE`

```deluge
// Custom API — argumentos como input.*
existing = Clientes[Email == input.email].count();
if(existing > 0)
{
    rec = Clientes[Email == input.email];
    return {"status": "exists", "id": rec.ID};
}
// crear registro...
return {"status": "created", "id": nuevoId};
```

Parámetros útiles en REST:
- `skip_workflow=form_workflow` — evita disparar workflows del formulario
- `field_config=custom&fields=Campo1,Campo2` — reduce payload en GET

---

## API v2.1 — formatos de campo

| Tipo | Formato JSON |
|------|--------------|
| Texto / Número | valor primitivo |
| Fecha | `"15-Jun-2026"` (dd-MMM-yyyy) |
| Multi-select | `["A","B"]` |
| Subform | array de maps anidados |
| Lookup v2.1 | `{"zc_display_value": "Activo"}` |

PATCH requiere `criteria` explícito — omitirlo no actualiza todo, devuelve error.

---

## REST API v2.1 (OpenAPI)

> **Fuente:** OpenAPI de Zoho Creator API v2 (revisada 2026-11-06).  
> **Base:** `https://www.zohoapis.com/creator/v2.1/` (también `.eu`, `.in`, `.com.au`, `.jp`, `.ca`, `.sa`).  
> Los paths de abajo son relativos a esa base. `{owner}` es el dueño de la cuenta, `{app}` el link name de la app.

```
GET    /meta/applications
GET    /meta/{owner}/applications
GET    /meta/{owner}/{app}/forms
GET    /meta/{owner}/{app}/forms/{form}/fields
GET    /meta/{owner}/{app}/reports
GET    /meta/{owner}/{app}/pages
GET    /meta/{owner}/{app}/sections

POST   /data/{owner}/{app}/form/{form}                         ← agregar records
GET    /data/{owner}/{app}/report/{report}                     ← listar
GET    /data/{owner}/{app}/report/{report}/{record_ID}         ← obtener
PATCH  /data/{owner}/{app}/report/{report}                     ← actualizar por criteria
PATCH  /data/{owner}/{app}/report/{report}/{record_ID}
DELETE /data/{owner}/{app}/report/{report}
DELETE /data/{owner}/{app}/report/{report}/{record_ID}

POST   /data/{owner}/{app}/report/{report}/{record_ID}/{field}/upload
GET    /data/{owner}/{app}/report/{report}/{record_ID}/{field}/download

POST   /bulk/{owner}/{app}/report/{report}/read                ← job de bulk read
GET    /bulk/{owner}/{app}/report/{report}/read/{job_ID}
GET    /bulk/{owner}/{app}/report/{report}/read/{job_ID}/result
POST   /bulk/{owner}/{app}/form/{form}/insert                  ← subir archivo de bulk insert
PUT    /bulk/{owner}/{app}/form/{form}/insert/{job_ID}         ← iniciar o abortar
GET    /bulk/{owner}/{app}/form/{form}/insert/{job_ID}/result

POST   /publish/{owner}/{app}/form/{form}                      ← form publicado, sin usuario OAuth
GET    /publish/{owner}/{app}/report/{report}
GET    /publish/{owner}/{app}/report/{report}/{record_ID}
```

Los records se crean en el **form** y se leen o actualizan en el **report**. Los Custom API (`/custom/{api_name}`) son otra superficie, aparte de esta spec.

---

## Gotchas exclusivos de Creator

- Sin operador ternario (`? :`) — usar `if/else`
- Sin `while`. `break` / `continue` sí son oficiales dentro de `for each`.
- Sin `addMonth()` — aproximar con `addDay(30|90|120)` o reconstruir fecha manualmente
- No asignar `null` como rvalue — usar flag booleano + default seguro
- Lookups en criterios: usar dot notation (`subcontractor.Email == zoho.loginuserid`)
- Form definitions: sin comentarios `//` en la definición del formulario
- Subform insert: solo `ParentForm.SubformName()`, no `Map()`

---

## Fuentes oficiales

- Creator tasks: https://www.zoho.com/deluge/help/creator-tasks.html
- Creator API v2.1: https://www.zoho.com/creator/help/api/v2.1/
- Deluge help: https://www.zoho.com/deluge/help/
