# Deluge en Zoho Creator

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

## Gotchas exclusivos de Creator

- Sin operador ternario (`? :`) — usar `if/else`
- Sin `while` ni `break` en muchas versiones — usar `for each` sobre lista fija
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
