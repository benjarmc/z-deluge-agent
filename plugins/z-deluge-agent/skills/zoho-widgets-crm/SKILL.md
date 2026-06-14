---
name: zoho-widgets-crm
description: Crea, revisa y depura widgets personalizados HTML/JS embebidos en Zoho CRM. Usar cuando el usuario menciona widget CRM, ZohoEmbededAppSDK, PageLoad, ZOHO.CRM.API, ZOHO.CRM.FUNCTIONS, ZOHO.CRM.UI.Popup, iframe CRM, extensión CRM o live.zwidgets.com SDK.
---

# Widgets — Zoho CRM

Componentes **HTML + JavaScript** en iframe dentro de CRM.

**Referencia completa:** `docs/widgets/crm.md`  
**Widgets Creator:** skill `zoho-widgets-creator` (SDK distinto)

---

## Estructura

```
mi_widget_crm/
├── app/
│   ├── widget.html      # Punto de entrada
│   ├── script.js
│   └── styles.css
└── README.md
```

CRM **no requiere** `plugin-manifest.json`. Instalación: copiar HTML o subir código.

---

## SDK — ZohoEmbededAppSDK

```html
<!-- v1.2 o v1.4 (preferir 1.4) -->
<script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
```

### Inicialización (orden crítico)

```javascript
ZOHO.embeddedApp.on("PageLoad", async function(data) {
    console.log("PageLoad:", data);
    // data.Entity — módulo API name ("Contacts")
    // data.EntityId — ID registro (puede ser array)
    var recordIds = Array.isArray(data.EntityId) ? data.EntityId : [data.EntityId];
    await loadRecord(data.Entity, recordIds[0]);
});

// SIEMPRE al final del script
ZOHO.embeddedApp.init();
```

---

## Contextos de despliegue

| Ubicación | Uso |
|-----------|-----|
| Record Detail | Popup/panel en vista registro |
| Related List | Widget en lista relacionada |
| Settings | Configuración admin |
| Home Page | Dashboard widget |

Setup → Developer Space → Widgets → Create Widget → seleccionar módulos y perfiles.

---

## ZOHO.CRM.API

### getRecord

```javascript
const resp = await ZOHO.CRM.API.getRecord({
    Entity: "Contacts",
    RecordID: "66359000000015039"
});
const record = resp.data[0];
```

### updateRecord

```javascript
await ZOHO.CRM.API.updateRecord({
    Entity: "Contacts",
    APIData: {
        id: recordId,
        Phone: "+525512345678",
        Owner: { id: "4543897000000272001" }
    },
    Trigger: ["workflow"]
});
```

### insertRecord

```javascript
await ZOHO.CRM.API.insertRecord({
    Entity: "Tasks",
    APIData: { Subject: "Seguimiento", Due_Date: "2024-12-31" }
});
```

### getRecords (lista)

```javascript
const resp = await ZOHO.CRM.API.getRecords({
    Entity: "Deals",
    page: 1,
    per_page: 200
});
```

### deleteRecord

```javascript
await ZOHO.CRM.API.deleteRecord({
    Entity: "Tasks",
    RecordID: taskId
});
```

---

## ZOHO.CRM.FUNCTIONS.execute

Ejecutar funciones Deluge standalone desde widget:

```javascript
const req_data = {
    arguments: JSON.stringify({
        recordId: String(recordId),
        accion: "aprobar"
    })
};
const result = await ZOHO.CRM.FUNCTIONS.execute("mi_funcion", req_data);
const output = result?.details?.output || "";
```

---

## ZOHO.CRM.UI

```javascript
// Cerrar popup
ZOHO.CRM.UI.Popup.close();
ZOHO.CRM.UI.Popup.closeReload();  // tras update

// Refresh registro
ZOHO.CRM.UI.Record.refresh();
```

---

## Ejemplo completo — editar y cerrar

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container mt-3">
    <input id="campo1" class="form-control mb-2">
    <button id="btnGuardar" class="btn btn-primary">Guardar</button>
    <div id="msg" class="mt-2"></div>
</div>
<script>
let RECORD_ID = null;
ZOHO.embeddedApp.on("PageLoad", async function(data) {
    RECORD_ID = data.EntityId;
    const resp = await ZOHO.CRM.API.getRecord({Entity: "Contacts", RecordID: RECORD_ID});
    if(resp.data?.[0]) document.getElementById("campo1").value = resp.data[0].Campo1 || "";
});
document.getElementById("btnGuardar").addEventListener("click", async function() {
    try {
        await ZOHO.CRM.API.updateRecord({
            Entity: "Contacts",
            APIData: { id: RECORD_ID, Campo1: document.getElementById("campo1").value }
        });
        setTimeout(() => ZOHO.CRM.UI.Popup.closeReload(), 1500);
    } catch(e) {
        document.getElementById("msg").textContent = e.message;
    }
});
ZOHO.embeddedApp.init();
</script>
</body>
</html>
```

---

## Instalación

1. Setup → Developer Space → Widgets → Create Widget
2. Nombre, tipo Custom Widget
3. Pegar código HTML o subir archivo
4. Ubicación: Detail / List / etc.
5. Módulos y perfiles permitidos

---

## Gotchas CRM widgets

| Problema | Fix |
|----------|-----|
| SDK undefined | Cargar script antes de app JS |
| init() no resuelve | Llamar `ZOHO.embeddedApp.init()` al final |
| EntityId array | Normalizar: `Array.isArray(id) ? id[0] : id` |
| Entity typo | API name exacto case-sensitive |
| CORS en fetch externo | Deluge function + FUNCTIONS.execute |
| Popup no cierra | Usar closeReload tras update |

---

## CRM vs Creator widgets

| | CRM | Creator |
|--|-----|---------|
| SDK | ZohoEmbededAppSDK | widgetsdk-min.js |
| Manifest | No requerido | plugin-manifest.json |
| Init | embeddedApp.init() | ZOHO.CREATOR.init() |
| API | ZOHO.CRM.API | ZOHO.CREATOR.API |
| Deploy | Copiar HTML | zet pack + ZIP |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Widgets CRM | `docs/widgets/crm.md` |
| CRM API REST | `docs/apis/crm-v8.md` |
| Client Scripts | skill `zoho-crm-client-script` |

