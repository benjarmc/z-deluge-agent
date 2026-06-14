# Widgets — Zoho Creator

Widgets HTML/JS embebidos en páginas de Zoho Creator. Ver también [`crm.md`](crm.md) para widgets CRM.

---

## Introducción

### Estructura de Archivos

Los widgets de Zoho Creator requieren una estructura específica de carpetas:

```
nombre_widget/
├── app/
│   ├── widget.html          # Archivo principal HTML (obligatorio)
│   ├── script.js            # JavaScript (opcional, puede estar inline)
│   ├── styles.css           # CSS (opcional, puede estar inline)
│   └── [otros recursos]     # Imágenes, fuentes, etc.
├── plugin-manifest.json     # Configuración del widget (obligatorio)
└── README.md                # Documentación (opcional)
```

### plugin-manifest.json

Archivo de configuración mínimo requerido:

```json
{
  "service": "CREATOR",
  "config": []
}
```

El campo `config` puede contener campos configurables que aparecerán en la interfaz de Zoho Creator cuando se agregue el widget a una página.

### widget.html - Estructura Básica

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Widget</title>
    
    <!-- SDK de Zoho Creator (OBLIGATORIO) -->
    <script type="text/javascript" 
            src="https://js.zohostatic.com/creator/widgets/version/1.0/widgetsdk-min.js">
    </script>
    
    <!-- Estilos CSS -->
    <link rel="stylesheet" href="styles.css">
    <!-- O estilos inline -->
    <style>
        /* Estilos aquí */
    </style>
</head>
<body>
    <div class="widget-container">
        <!-- Contenido del widget -->
        <h1>Mi Widget</h1>
        <button id="btnAccion">Ejecutar Acción</button>
        <div id="resultado"></div>
    </div>

    <!-- JavaScript -->
    <script src="script.js"></script>
    <!-- O JavaScript inline -->
    <script>
        // Código JavaScript aquí
    </script>
</body>
</html>
```

### Inicialización del SDK

El SDK de Zoho Creator debe inicializarse antes de usar cualquier funcionalidad:

```javascript
// Inicializar SDK
function inicializarWidget() {
    var creatorSdkPromise = ZOHO.CREATOR.init();

    creatorSdkPromise.then(function(data) {
        console.log("✅ SDK inicializado:", data);
        // El SDK está listo para usar
        // data contiene información del contexto
        
        // Configurar eventos y funcionalidad
        configurarEventos();
    }).catch(function(error) {
        console.error("❌ Error inicializando SDK:", error);
    });
}

// Inicializar cuando el DOM esté listo
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', inicializarWidget);
} else {
    inicializarWidget();
}
```

### API de Zoho Creator

Una vez inicializado el SDK, puedes usar `ZOHO.CREATOR.API` para interactuar con los datos:

#### Obtener Registro por ID

```javascript
ZOHO.CREATOR.API.getRecordById({
    reportName: "Nombre_Reporte",
    recordId: "66359000000015039"
}).then(function(response) {
    console.log("Registro obtenido:", response);
    const registro = response.data;
    // Usar los datos del registro
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Obtener Todos los Registros

```javascript
ZOHO.CREATOR.API.getAllRecords({
    reportName: "Nombre_Reporte",
    criteria: "Status != \"Closed\"",  // Opcional
    pageNumber: 1,                      // Opcional
    pageSize: 200                       // Opcional
}).then(function(response) {
    console.log("Registros obtenidos:", response);
    const registros = response.data;
    // Procesar registros
}).catch(function(error) {
    console.error("Error:", error);
});
```

#### Agregar Registro

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

#### Actualizar Registro

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

#### Eliminar Registro

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

#### Subir Archivo

```javascript
// Obtener el archivo desde un input file
const fileInput = document.getElementById("fileInput");
const file = fileInput.files[0];

ZOHO.CREATOR.API.uploadFile({
    reportName: "Nombre_Reporte",
    recordId: "66359000000015039",
    fieldName: "Attachment",
    file: file,
    parentId: "66359000000013592"  // Opcional
}).then(function(response) {
    console.log("Archivo subido:", response);
}).catch(function(error) {
    console.error("Error:", error);
});
```

### Empaquetado e Instalación

1. **Validar el widget:**
   ```bash
   cd nombre_widget
   zet validate
   ```

2. **Empaquetar el widget:**
   ```bash
   zet pack
   ```
   Esto genera un archivo ZIP en la carpeta `dist/`.

3. **Instalar en Zoho Creator:**
   - Accede a tu aplicación en Zoho Creator
   - Ve a **Configuración** > **Widgets**
   - Haz clic en **Crear** o **Nuevo Widget**
   - Configura:
     - **Nombre**: Nombre del widget
     - **Tipo de alojamiento**: Interno
     - **Archivo ZIP**: Selecciona el archivo generado
     - **Archivo de índice**: `/widget.html`
   - Haz clic en **Crear**

---

