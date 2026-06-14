# Widgets — Zoho CRM

Widgets HTML/JS embebidos en Zoho CRM (iframe). Ver también [`creator.md`](creator.md) para widgets Creator.

---

## Introducción

### Estructura de Archivos

Los widgets de Zoho CRM tienen una estructura más simple:

```
nombre_widget/
└── widget.html              # Archivo principal (puede estar en subcarpeta app/)
```

**Nota:** Los widgets de CRM no requieren `plugin-manifest.json`. Pueden tener subcarpetas como `app/` para organizar recursos.

### widget.html - Estructura Básica

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Widget CRM</title>
    
    <!-- SDK de Zoho CRM (OBLIGATORIO) -->
    <!-- Versión 1.2 -->
    <script src="https://live.zwidgets.com/js-sdk/1.2/ZohoEmbededAppSDK.min.js"></script>
    <!-- O versión 1.4 -->
    <script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
    
    <!-- Bootstrap u otros frameworks (opcional) -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container">
        <h1>Mi Widget CRM</h1>
        <button id="btnAccion">Ejecutar Acción</button>
        <div id="resultado"></div>
    </div>

    <script>
        // Código JavaScript aquí
    </script>
</body>
</html>
```

### Inicialización del SDK

```javascript
// Inicializar SDK
ZOHO.embeddedApp.init();

// Escuchar evento PageLoad (cuando se carga el widget)
ZOHO.embeddedApp.on("PageLoad", async function(data) {
    console.log("📡 PageLoad recibido:", data);
    
    // data.EntityId contiene el ID del registro actual
    const recordId = data.EntityId;
    
    // Puede ser un array si hay múltiples registros seleccionados
    const recordIds = Array.isArray(recordId) ? recordId : [recordId];
    
    // Inicializar funcionalidad del widget
    inicializarWidget(recordIds);
});
```

### API de Zoho CRM

#### Obtener Registro

```javascript
const response = await ZOHO.CRM.API.getRecord({
    Entity: "Nombre_Modulo",  // Ej: "Contacts", "Deals", "Pago_Facturas"
    RecordID: "66359000000015039"
});

if (response.data && response.data.length > 0) {
    const record = response.data[0];
    console.log("Registro obtenido:", record);
    // Usar los datos del registro
}
```

#### Actualizar Registro

```javascript
const response = await ZOHO.CRM.API.updateRecord({
    Entity: "Nombre_Modulo",
    APIData: {
        id: "66359000000015039",
        "Campo1": "NuevoValor1",
        "Campo2": "NuevoValor2",
        Owner: { id: "4543897000000272001" }  // Opcional: cambiar propietario
    },
    Trigger: ["workflow"]  // Opcional: activar workflows
});

console.log("Registro actualizado:", response);
```

#### Crear Registro

```javascript
const response = await ZOHO.CRM.API.insertRecord({
    Entity: "Nombre_Modulo",
    APIData: {
        "Campo1": "Valor1",
        "Campo2": "Valor2"
    }
});

console.log("Registro creado:", response);
```

#### Obtener Múltiples Registros

```javascript
const response = await ZOHO.CRM.API.getRecords({
    Entity: "Nombre_Modulo",
    page: 1,
    per_page: 200
});

if (response.data && response.data.length > 0) {
    const records = response.data;
    // Procesar registros
}
```

#### Eliminar Registro

```javascript
const response = await ZOHO.CRM.API.deleteRecord({
    Entity: "Nombre_Modulo",
    RecordID: "66359000000015039"
});

console.log("Registro eliminado:", response);
```

### Ejecutar Funciones de CRM

Los widgets pueden ejecutar funciones personalizadas definidas en Zoho CRM:

```javascript
// Preparar argumentos
const req_data = {
    arguments: JSON.stringify({
        recordId: "66359000000015039",
        parametro1: "valor1"
    })
};

// Ejecutar función
try {
    const result = await ZOHO.CRM.FUNCTIONS.execute("nombre_funcion", req_data);
    console.log("Resultado función:", result);
    
    // Acceder a la respuesta
    const output = result?.details?.output || "(sin respuesta)";
} catch (error) {
    console.error("Error ejecutando función:", error);
}
```

### UI de Zoho CRM

#### Cerrar Popup

```javascript
// Cerrar popup sin recargar
ZOHO.CRM.UI.Popup.close();

// Cerrar popup y recargar la página
ZOHO.CRM.UI.Popup.closeReload();
```

#### Mostrar Mensajes

```javascript
// Usar alertas de Bootstrap o crear mensajes personalizados
function showMessage(msg, type = "info") {
    const box = document.getElementById("messageBox");
    box.textContent = msg;
    box.className = "alert";
    box.classList.remove("d-none");
    
    if (type === "success") box.classList.add("alert-success");
    else if (type === "error") box.classList.add("alert-danger");
    else box.classList.add("alert-secondary");
}
```

### Instalación en Zoho CRM

1. **Crear el widget:**
   - Accede a Zoho CRM
   - Ve a **Configuración** > **Desarrollador** > **Widgets**
   - Haz clic en **Crear Widget**

2. **Configurar el widget:**
   - **Nombre**: Nombre del widget
   - **Tipo**: Widget personalizado
   - **Código**: Copia y pega el contenido de `widget.html` o sube el archivo
   - **Ubicación**: Selecciona dónde aparecerá (Detalle de registro, Lista, etc.)

3. **Configurar permisos:**
   - Selecciona los módulos donde estará disponible
   - Configura los perfiles que pueden usarlo

---

