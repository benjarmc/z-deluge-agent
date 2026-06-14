---
name: zoho-widgets-creator
description: Crea, revisa y depura widgets personalizados HTML/JS embebidos en Zoho Creator. Usar cuando el usuario menciona widget Creator, plugin-manifest.json CREATOR, widgetsdk-min.js, ZOHO.CREATOR.API, ZOHO.CREATOR.init, zet validate, zet pack o js.zohostatic.com/creator/widgets.
---

# Widgets — Zoho Creator

Componentes **HTML + JavaScript** embebidos en páginas Creator.

**Referencia completa:** `docs/widgets/creator.md`  
**Widgets CRM:** skill `zoho-widgets-crm` (SDK distinto)

---

## Estructura obligatoria

```
nombre_widget/
├── app/
│   ├── widget.html          # Obligatorio — index
│   ├── script.js
│   ├── styles.css
│   └── assets/
├── plugin-manifest.json     # Obligatorio
└── README.md
```

---

## plugin-manifest.json

```json
{
  "service": "CREATOR",
  "config": [
    {
      "name": "report_name",
      "type": "string",
      "default": "All_Records",
      "display_name": "Reporte"
    },
    {
      "name": "max_rows",
      "type": "number",
      "default": "50"
    }
  ]
}
```

`config` → parámetros editables al instalar widget en Creator UI.

---

## SDK — widgetsdk-min.js

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://js.zohostatic.com/creator/widgets/version/1.0/widgetsdk-min.js"></script>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="widget-root"></div>
    <script src="script.js"></script>
</body>
</html>
```

---

## Inicialización

```javascript
function initWidget() {
    ZOHO.CREATOR.init().then(function(data) {
        console.log("SDK ready:", data);
        var params = ZOHO.CREATOR.UTIL.getWidgetParams();
        document.getElementById("report-label").textContent = params.report_name;
        loadRecords(params.report_name);
    }).catch(function(err) {
        console.error("Init error:", err);
    });
}

if(document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initWidget);
} else {
    initWidget();
}
```

---

## ZOHO.CREATOR.API

### getRecordById

```javascript
ZOHO.CREATOR.API.getRecordById({
    reportName: "All_Pedidos",
    recordId: "66359000000015039"
}).then(function(resp) {
    console.log(resp.data);
});
```

### getAllRecords

```javascript
ZOHO.CREATOR.API.getAllRecords({
    reportName: "All_Clientes",
    criteria: 'Status != "Closed"',
    pageNumber: 1,
    pageSize: 200
}).then(function(resp) {
    renderTable(resp.data);
});
```

### addRecord

```javascript
ZOHO.CREATOR.API.addRecord({
    formLinkName: "Pedidos",
    data: { "Cliente": "Acme", "Monto": 1500 }
});
```

### updateRecord

```javascript
ZOHO.CREATOR.API.updateRecord({
    reportName: "All_Pedidos",
    recordId: recordId,
    data: { "Estado": "Aprobado" }
});
```

### deleteRecord

```javascript
ZOHO.CREATOR.API.deleteRecord({
    reportName: "All_Pedidos",
    criteria: "ID == 66359000000015039"
});
```

### uploadFile

```javascript
ZOHO.CREATOR.API.uploadFile({
    reportName: "Documentos",
    recordId: recordId,
    fieldName: "Adjunto",
    file: fileInput.files[0]
});
```

---

## Leer config del manifest

```javascript
ZOHO.CREATOR.init().then(function() {
    var cfg = ZOHO.CREATOR.UTIL.getWidgetParams();
    var report = cfg.report_name || "All_Records";
    var maxRows = parseInt(cfg.max_rows) || 50;
});
```

---

## Despliegue con zet CLI

```bash
cd nombre_widget
zet validate    # validar estructura
zet pack        # genera dist/nombre_widget.zip
```

Instalación Creator:
1. Settings → Widgets → Create
2. Hosting: Internal
3. Subir ZIP de `dist/`
4. Index file: `/widget.html`

---

## Ejemplo — búsqueda en reporte

```html
<script>
let recordOps = null;
ZOHO.CREATOR.init().then(function() {
    recordOps = ZOHO.CREATOR.API;
    document.getElementById("btnBuscar").addEventListener("click", buscar);
});
function buscar() {
    var term = document.getElementById("q").value;
    recordOps.getAllRecords({
        reportName: "Mi_Reporte",
        criteria: 'Nombre == "' + term + '"'
    }).then(function(resp) {
        var html = resp.data.map(function(r) {
            return "<div>" + r.Nombre + "</div>";
        }).join("");
        document.getElementById("resultados").innerHTML = html;
    }).catch(function(e) { console.error(e); });
}
</script>
```

---

## Gotchas Creator widgets

| Problema | Fix |
|----------|-----|
| SDK undefined | Script en `<head>` antes de app |
| init() fail | Verificar manifest service: CREATOR |
| Empty data | reportName/formLinkName case-sensitive |
| criteria syntax | Comillas Creator: `'Campo == "valor"'` |
| ZIP reject | `zet validate` antes de pack |
| Config undefined | Definir en manifest + getWidgetParams() |

---

## Creator vs CRM widgets

| | Creator | CRM |
|--|---------|-----|
| Manifest | Obligatorio CREATOR | No requerido |
| SDK | widgetsdk-min.js | ZohoEmbededAppSDK |
| Deploy | zet pack + ZIP | HTML directo |
| Eventos | init() promise | PageLoad event |
| Funciones Deluge | vía Custom API externa | FUNCTIONS.execute |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Widgets Creator | `docs/widgets/creator.md` |
| Deluge Creator | skill `zoho-deluge-creator` |
| Creator API v2.1 | `docs/creator/deluge-in-creator.md` |

