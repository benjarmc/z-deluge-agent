# Documentación: Creación de Widgets para Zoho CRM y Zoho Creator

Esta documentación describe cómo crear widgets personalizados para Zoho CRM y Zoho Creator, incluyendo estructura de archivos, SDKs, APIs y ejemplos prácticos.

---

## Tabla de Contenidos

1. [Widgets de Zoho Creator](#widgets-de-zoho-creator)
2. [Widgets de Zoho CRM](#widgets-de-zoho-crm)
3. [Comparación de Diferencias](#comparación-de-diferencias)
4. [Ejemplos Completos](#ejemplos-completos)

---

## Widgets de Zoho Creator

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

## Widgets de Zoho CRM

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

## Comparación de Diferencias

| Aspecto | Zoho Creator | Zoho CRM |
|---------|--------------|----------|
| **Estructura** | Requiere `plugin-manifest.json` | Solo requiere `widget.html` |
| **SDK** | `widgetsdk-min.js` | `ZohoEmbededAppSDK.min.js` |
| **Inicialización** | `ZOHO.CREATOR.init()` | `ZOHO.embeddedApp.init()` |
| **Eventos** | No tiene eventos específicos | `ZOHO.embeddedApp.on("PageLoad", ...)` |
| **API Principal** | `ZOHO.CREATOR.API` | `ZOHO.CRM.API` |
| **Nomenclatura** | `reportName`, `formLinkName` | `Entity` (nombre del módulo) |
| **Funciones** | No disponible directamente | `ZOHO.CRM.FUNCTIONS.execute()` |
| **UI** | No tiene métodos UI específicos | `ZOHO.CRM.UI.Popup.close()` |
| **Empaquetado** | Requiere `zet pack` | No requiere empaquetado |
| **Instalación** | Subir ZIP | Copiar código HTML |

---

## Ejemplos Completos

### Ejemplo 1: Widget Creator - Búsqueda Simple

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Búsqueda de Registros</title>
    <script src="https://js.zohostatic.com/creator/widgets/version/1.0/widgetsdk-min.js"></script>
</head>
<body>
    <div>
        <input type="text" id="busqueda" placeholder="Buscar...">
        <button id="btnBuscar">Buscar</button>
        <div id="resultados"></div>
    </div>

    <script>
        let recordOps = null;

        ZOHO.CREATOR.init().then(function(data) {
            recordOps = ZOHO.CREATOR.API;
            
            document.getElementById("btnBuscar").addEventListener("click", buscar);
        });

        function buscar() {
            const termino = document.getElementById("busqueda").value;
            
            recordOps.getAllRecords({
                reportName: "Mi_Reporte",
                criteria: `Nombre == "${termino}"`
            }).then(function(response) {
                const resultados = document.getElementById("resultados");
                resultados.innerHTML = "";
                
                response.data.forEach(function(record) {
                    resultados.innerHTML += `<div>${record.Nombre}</div>`;
                });
            });
        }
    </script>
</body>
</html>
```

### Ejemplo 2: Widget CRM - Actualizar Registro

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Actualizar Registro</title>
    <script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-3">
        <form id="formActualizar">
            <div class="mb-3">
                <label for="campo1" class="form-label">Campo 1</label>
                <input type="text" class="form-control" id="campo1">
            </div>
            <button type="button" id="btnGuardar" class="btn btn-primary">Guardar</button>
            <button type="button" id="btnCancelar" class="btn btn-secondary">Cancelar</button>
        </form>
        <div id="mensaje" class="mt-3"></div>
    </div>

    <script>
        let RECORD_ID = null;

        ZOHO.embeddedApp.on("PageLoad", async function(data) {
            RECORD_ID = data.EntityId;
            
            // Cargar datos del registro
            const resp = await ZOHO.CRM.API.getRecord({
                Entity: "Contacts",
                RecordID: RECORD_ID
            });
            
            if (resp.data && resp.data.length > 0) {
                const record = resp.data[0];
                document.getElementById("campo1").value = record.Campo1 || "";
            }
        });

        document.getElementById("btnGuardar").addEventListener("click", async function() {
            try {
                await ZOHO.CRM.API.updateRecord({
                    Entity: "Contacts",
                    APIData: {
                        id: RECORD_ID,
                        Campo1: document.getElementById("campo1").value
                    }
                });
                
                document.getElementById("mensaje").innerHTML = 
                    '<div class="alert alert-success">Registro actualizado</div>';
                
                setTimeout(() => {
                    ZOHO.CRM.UI.Popup.closeReload();
                }, 1500);
            } catch (error) {
                document.getElementById("mensaje").innerHTML = 
                    '<div class="alert alert-danger">Error: ' + error.message + '</div>';
            }
        });

        document.getElementById("btnCancelar").addEventListener("click", function() {
            ZOHO.CRM.UI.Popup.close();
        });

        ZOHO.embeddedApp.init();
    </script>
</body>
</html>
```

### Ejemplo 3: Widget CRM - Ejecutar Función

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Ejecutar Función</title>
    <script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
</head>
<body>
    <div>
        <button id="btnEjecutar">Ejecutar Función</button>
        <div id="resultado"></div>
    </div>

    <script>
        let RECORD_ID = null;

        ZOHO.embeddedApp.on("PageLoad", async function(data) {
            RECORD_ID = data.EntityId;
        });

        document.getElementById("btnEjecutar").addEventListener("click", async function() {
            const resultado = document.getElementById("resultado");
            resultado.innerHTML = "Procesando...";

            try {
                const req_data = {
                    arguments: JSON.stringify({
                        recordId: String(RECORD_ID)
                    })
                };

                const result = await ZOHO.CRM.FUNCTIONS.execute("mi_funcion", req_data);
                const output = result?.details?.output || "(sin respuesta)";
                
                resultado.innerHTML = `<div>Resultado: ${output}</div>`;
            } catch (error) {
                resultado.innerHTML = `<div style="color:red">Error: ${error.message}</div>`;
            }
        });

        ZOHO.embeddedApp.init();
    </script>
</body>
</html>
```

---

## Mejores Prácticas

### Para Zoho Creator

1. **Siempre inicializar el SDK** antes de usar cualquier funcionalidad
2. **Manejar errores** con `.catch()` en todas las promesas
3. **Validar datos** antes de enviar a la API
4. **Usar nombres de reportes y formularios** correctos (case-sensitive)
5. **Empaquetar correctamente** con `zet pack` antes de subir

### Para Zoho CRM

1. **Inicializar con `ZOHO.embeddedApp.init()`** al final del script
2. **Escuchar `PageLoad`** para obtener el contexto del registro
3. **Manejar arrays** en `data.EntityId` (puede ser múltiple)
4. **Usar async/await** para código más limpio
5. **Cerrar popups** con `closeReload()` después de actualizaciones
6. **Validar permisos** antes de ejecutar operaciones

### Generales

1. **Manejo de errores**: Siempre usar try/catch o .catch()
2. **Loading states**: Mostrar indicadores de carga durante operaciones
3. **Validación**: Validar datos del usuario antes de enviar
4. **Logging**: Usar `console.log()` para debugging
5. **Responsive**: Diseñar para diferentes tamaños de pantalla
6. **Accesibilidad**: Usar etiquetas semánticas y ARIA cuando sea necesario

---

## Recursos Adicionales

### Documentación Oficial

- **Zoho Creator Widgets**: 
  - https://www.zoho.com/creator/newhelp/app-settings/widgets/create-a-widget.html
  - https://help.zoho.com/portal/en/kb/creator/developer-guide/application-settings/widgets/articles/js-api-documentation

- **Zoho CRM Widgets**:
  - https://www.zoho.com/crm/developer/docs/widgets/v2/

### SDKs

- **Creator SDK**: `https://js.zohostatic.com/creator/widgets/version/1.0/widgetsdk-min.js`
- **CRM SDK v1.2**: `https://live.zwidgets.com/js-sdk/1.2/ZohoEmbededAppSDK.min.js`
- **CRM SDK v1.4**: `https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js`

---

## Notas Finales

- Los widgets de Creator requieren empaquetado con `zet` CLI
- Los widgets de CRM se instalan directamente copiando el código HTML
- Ambos SDKs tienen diferentes APIs pero conceptos similares
- Siempre probar en un entorno de desarrollo antes de producción
- Los nombres de módulos/reportes son case-sensitive
- Los IDs de registros son strings largos numéricos

---

*Documentación generada para uso con modelos de lenguaje (ML)*
