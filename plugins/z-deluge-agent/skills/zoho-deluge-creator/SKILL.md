---
name: zoho-deluge-creator
description: Writes, reviews, and debugs Deluge in Zoho Creator: forms, workflows, HTML pages, subforms, Custom APIs, REST API v2.1, skip_workflow, record fetch, empty set, OAuth, production gotchas. Use for Zoho Creator, thisapp, openUrl, Creator pages, creator.zoho.com/api, Form[criteria]. / Escribe, revisa y depura Deluge en Zoho Creator: formularios, workflows, páginas HTML, subformularios, Custom APIs, API REST v2.1, skip_workflow, empty set. Usar para Zoho Creator, thisapp, openUrl, páginas Creator.
---

# Deluge en Zoho Creator

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

**Referencia completa:** `docs/en/creator/deluge-in-creator.md` · `docs/es/creator/deluge-in-creator.md`  
**Patrones:** `docs/en/patterns/production.md` · `docs/es/patterns/production.md`  
**Lenguaje base:** skill `zoho-deluge-core`

---

## Contexto Creator

Zoho Creator ejecuta Deluge en:
- **Workflows de formulario:** On Load, On Validate, On Success, On User Input
- **Workflows de reporte:** botones, schedules
- **Páginas HTML** con bloques `<%{ %>`
- **Custom APIs** (microservicios HTTP)
- **Funciones standalone** (sin `input.`)

---

## Record fetch y criterios

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

### Empty Set Guard (obligatorio)

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

Query vacío es **non-null** — nunca `if(query != null)`.

---

## input. vs argumentos

| Contexto | Sintaxis |
|----------|----------|
| Workflow form (Load/Validate/Success/User Input) | `input.Campo` |
| Función standalone | argumento directo — **no** `input.x` |
| Custom API POST | `input.paramName` |
| Schedule | sin input — usar criterios globales |

---

## CRUD en Creator (Deluge nativo)

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

## Subformularios

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

| Operación | On Load | On Validate/Success | Subform On User Input |
|-----------|---------|---------------------|----------------------|
| Insert filas | ✅ | ❌ | — |
| Modificar campos fila | — | ❌ | ✅ |
| Leer filas | ✅ | ✅ | ✅ |

Error `Error updating field in ZC_SUBFORM` → mover lógica a On User Input.

---

## Navegación

```deluge
// Abrir formulario interno
thisapp.Open.DetalleProyecto(input.Proyecto);

// Página Creator
openUrl("#Page:Dashboard", "same window");
openUrl("#Form:Clientes?ID=" + recId, "new window");
```

---

## Páginas Creator (HTML + Deluge)

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

Reglas:
- Un solo bloque `<%{ }%>` por página
- Sin `<!DOCTYPE>`, `<html>`, `<body>` global
- CSS scoped bajo `#contenedor-id`
- Clases con prefijo namespace (`dash-`, `app-`)

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

Scope OAuth: `ZohoCreator.customapi.EXECUTE`

---

## API REST v2.1 (invokeUrl)

Base: `https://creator.zoho.com/api/v2.1/{owner}/{app}/`

```
POST   /form/{formLinkName}           ← crear (max 200/request)
GET    /report/{reportLinkName}       ← listar
PATCH  /report/{reportLinkName}       ← actualizar (requiere criteria)
POST   /custom/{customApiName}        ← Custom API
```

Parámetros query clave:
- `skip_workflow=form_workflow` — suprime On Success/Validate
- `field_config=quick_view` — campos mínimos
- `max_records=200`
- `record_cursor` — paginación
- `criteria` — obligatorio en PATCH: `"ID == 12345"`

Payload fechas: `"15-Jun-2026"`  
Subforms: array de maps anidados  
Créditos: GET/POST = 1; operaciones pesadas = 5+

```deluge
url = "https://creator.zoho.com/api/v2.1/owner/app/form/Pedidos?skip_workflow=form_workflow";
payload = Map();
payload.put("Nombre", "Pedido 001");
resp = invokeUrl [ url: url type: POST body: payload.toText() connection: "creator_oauth" ];
```

Para `skip_workflow` / `field_config` / `max_records` desde **otro** servicio: `zoho.creator.v2_1.*`. Desde Creator: V2 `zoho.creator.*` o `invokeUrl` v2.1. `record_cursor` sigue siendo invokeUrl.

---

## zoho.creator.* (V2) y `zoho.creator.v2_1.*`

Índices distintos: skill `zoho-integration-tasks`. V2: owner + app + form/report. Update por ID = `updateRecord`. Bulk create = `createRecord` con List (no hay `createRecords` en V2). V2.1: `createRecords` / `updateRecordById`; **no** desde Creator.

```deluge
empty = Map();
zoho.creator.createRecord(zoho.adminuser, zoho.appname, "Form", dataMap, empty, "creator_oauth");
zoho.creator.getRecordById(zoho.adminuser, zoho.appname, "Report", recordId, "creator_oauth");
zoho.creator.updateRecord(zoho.adminuser, zoho.appname, "Report", recordId, data, empty, "creator_oauth");
zoho.creator.updateRecords(zoho.adminuser, zoho.appname, "Report", "ID!=0", data, empty, "creator_oauth");
```

Null safety Creator: validar con `!= ""` no `!= null`.

---

## Gotchas Creator (checklist)

| ❌ No usar | ✅ Alternativa |
|-----------|----------------|
| Ternario `? :` | `if/else` |
| `while` | Estático `{1,2,3}` o dinámico `"0,".repeat(n).toList(",")` |
| Saltar / salir de `for each` | `continue;` / `break;` (oficiales) |
| `addMonth()` inconsistente | `addDay(30)` o manual |
| `variable = null` | flag + default |
| Lookup por ID en criterio | `Lookup.Campo == valor` |
| `query != null` | `.count() > 0` |
| Map anidado invokeUrl | JSON string manual |
| Comentarios en definición form | Solo en scripts workflow |
| Date cruda en criterio | string dd-MMM-yyyy o keyword |

---

## Límites Creator

| Límite | Valor |
|--------|-------|
| API calls | 50/usuario/minuto |
| Statements | 1,000–50,000 según plan |
| Registros POST form | 200/request |
| External calls | 1 por integration task en loop |

---

## Troubleshooting

| Error | Causa | Fix |
|-------|-------|-----|
| empty set and values cannot be retrieved | Query vacío → variable | `.count()` inline primero |
| Comparison of non numeric expression | Date en criterio | dd-MMM-yyyy string |
| Error updating field in ZC_SUBFORM | Edit subform en Validate | On User Input |
| Not able to find 'Subform' function | Sin prefijo form | `ParentForm.Subform()` |
| Variable 'x' is not defined | `input.x` en standalone | argumento directo |
| Page blank | CSS global / `<html>` | Scope bajo `#id` |
| TOO_MANY_REQUESTS | Rate limit API | Custom API / cola |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Deluge en Creator | `docs/en/creator/deluge-in-creator.md` · `docs/es/creator/deluge-in-creator.md` |
| Lenguaje Deluge | `docs/en/deluge/language.md` · `docs/es/deluge/language.md` |
| Patrones | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
| Integration tasks Creator | `docs/en/integration-tasks/index.md` · `docs/es/integration-tasks/index.md` § Creator |

