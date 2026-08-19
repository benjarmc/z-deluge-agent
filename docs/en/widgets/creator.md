# Widgets — Zoho Creator

> Language: English. Spanish: see the matching path under docs/es/.

HTML/JS widgets embedded in Zoho Creator pages. See also [`crm.md`](crm.md) for CRM widgets.

---

## Introduction

### File Structure

Zoho Creator widgets require a specific folder structure:

```
nombre_widget/
├── app/
│   ├── widget.html          # Main HTML file (required)
│   ├── script.js            # JavaScript (optional, may be inline)
│   ├── styles.css           # CSS (optional, may be inline)
│   └── [other resources]    # Images, fonts, etc.
├── plugin-manifest.json     # Widget configuration (required)
└── README.md                # Documentation (optional)
```

### plugin-manifest.json

Minimum required configuration file:

```json
{
  "service": "CREATOR",
  "config": []
}
```

The `config` field can contain configurable fields that will appear in the Zoho Creator interface when the widget is added to a page.

### widget.html - Basic Structure

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Widget</title>
    
    <!-- Zoho Creator SDK (REQUIRED) -->
    <script type="text/javascript" 
            src="https://js.zohostatic.com/creator/widgets/version/1.0/widgetsdk-min.js">
    </script>
    
    <!-- CSS styles -->
    <link rel="stylesheet" href="styles.css">
    <!-- Or inline styles -->
    <style>
        /* Styles here */
    </style>
</head>
<body>
    <div class="widget-container">
        <!-- Widget content -->
        <h1>Mi Widget</h1>
        <button id="btnAccion">Ejecutar Acción</button>
        <div id="resultado"></div>
    </div>

    <!-- JavaScript -->
    <script src="script.js"></script>
    <!-- Or inline JavaScript -->
    <script>
        // JavaScript code here
    </script>
</body>
</html>
```

### SDK Initialization

The Zoho Creator SDK must be initialized before using any functionality:

```javascript
// Initialize SDK
function inicializarWidget() {
    var creatorSdkPromise = ZOHO.CREATOR.init();

    creatorSdkPromise.then(function(data) {
        console.log("✅ SDK inicializado:", data);
        // The SDK is ready to use
        // data contains context information
        
        // Configure events and functionality
        configurarEventos();
    }).catch(function(error) {
        console.error("❌ Error inicializando SDK:", error);
    });
}

// Initialize when the DOM is ready
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', inicializarWidget);
} else {
    inicializarWidget();
}
```

### Zoho Creator API

Once the SDK is initialized, you can use `ZOHO.CREATOR.API` to interact with data:

#### Get Record by ID

```javascript
ZOHO.CREATOR.API.getRecordById({
    reportName: "Nombre_Reporte",
    recordId: "66359000000015039"
}).then(function(response) {
    console.log("Registro obtenido:", response);
    const registro = response.data;
    // Use the record data
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Get All Records

```javascript
ZOHO.CREATOR.API.getAllRecords({
    reportName: "Nombre_Reporte",
    criteria: "Status != \"Closed\"",  // Optional
    pageNumber: 1,                      // Optional
    pageSize: 200                       // Optional
}).then(function(response) {
    console.log("Registros obtenidos:", response);
    const registros = response.data;
    // Process records
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Add Record

```javascript
ZOHO.CREATOR.API.addRecord({
    formLinkName: "Nombre_Formulario",
    data: {
        "Campo1": "Valor1",
        "Campo2": "Valor2"
    }
}).then(function(response) {
    console.log("Registro creado:", response);
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Update Record

```javascript
ZOHO.CREATOR.API.updateRecord({
    reportName: "Nombre_Reporte",
    recordId: "66359000000015039",
    data: {
        "Campo1": "NuevoValor1"
    }
}).then(function(response) {
    console.log("Registro actualizado:", response);
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Delete Record

```javascript
ZOHO.CREATOR.API.deleteRecord({
    reportName: "Nombre_Reporte",
    criteria: "ID == 66359000000015039"
}).then(function(response) {
    console.log("Registro eliminado:", response);
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Upload File

```javascript
// Get the file from a file input
const fileInput = document.getElementById("fileInput");
const file = fileInput.files[0];

ZOHO.CREATOR.API.uploadFile({
    reportName: "Nombre_Reporte",
    recordId: "66359000000015039",
    fieldName: "Attachment",
    file: file,
    parentId: "66359000000013592"  // Optional
}).then(function(response) {
    console.log("Archivo subido:", response);
}).catch(function(error) {
    console.error("Error:", error);
});
```

### Packaging and Installation

1. **Validate the widget:**
   ```bash
   cd nombre_widget
   zet validate
   ```

2. **Pack the widget:**
   ```bash
   zet pack
   ```
   This generates a ZIP file in the `dist/` folder.

3. **Install in Zoho Creator:**
   - Open your application in Zoho Creator
   - Go to **Settings** > **Widgets**
   - Click **Create** or **New Widget**
   - Configure:
     - **Name**: Widget name
     - **Hosting type**: Internal
     - **ZIP file**: Select the generated file
     - **Index file**: `/widget.html`
   - Click **Create**

---
