# Documentación Completa: Client Scripts de Zoho CRM

Esta documentación está basada exclusivamente en la documentación oficial de Zoho CRM Developer Space (https://www.zohocrm.dev) y proporciona una guía exhaustiva sobre Client Scripts, incluyendo el Event Dictionary, Client API, Web API, y ZRC (Zoho Request Client).

**Referencias Oficiales:**
- Event Dictionary: https://www.zohocrm.dev/explore/client-script/event-dictionary/
- Client API: https://www.zohocrm.dev/explore/client-script/clientapi/
- Web API: https://www.zohocrm.dev/explore/client-script/webapi/
- ZRC: https://www.zohocrm.dev/explore/client-script/zrc/

---

## Tabla de Contenidos

1. [Introducción a Client Scripts](#introducción-a-client-scripts)
2. [Event Dictionary (Diccionario de Eventos)](#event-dictionary-diccionario-de-eventos)
3. [Client API](#client-api)
4. [Web API (zoho.crm.*)](#web-api-zohocrm)
5. [ZRC (Zoho Request Client)](#zrc-zoho-request-client)
6. [Ejemplos Prácticos](#ejemplos-prácticos)
7. [Mejores Prácticas](#mejores-prácticas)

---

## Introducción a Client Scripts

Los **Client Scripts** de Zoho CRM son scripts JavaScript que se ejecutan en el lado del cliente (navegador) para personalizar y automatizar el comportamiento de la interfaz de CRM.

### Características Principales

- **Ejecución en Tiempo Real**: Los scripts se ejecutan directamente en el navegador del usuario
- **Sin Recarga de Página**: Permiten actualizaciones dinámicas sin recargar
- **Validación en Tiempo Real**: Validación inmediata de datos antes de guardar
- **Interacción con UI**: Control total sobre campos, formularios y componentes
- **Integración con APIs**: Acceso a APIs de CRM y servicios externos

### Páginas Soportadas

- **Create Page**: Página de creación de registros
- **Edit Page**: Página de edición de registros
- **Clone Page**: Página de clonación de registros
- **Detail Page**: Página de detalle de registros (Standard y Canvas)
- **List Page**: Página de lista de registros (Standard)
- **Wizard Page**: Páginas de asistente

### Crear un Client Script

1. Ir a **Setup** > **Developer Hub** > **Client Script**
2. Clic en **+New Script**
3. Proporcionar **Name** y **Description**
4. En **Page Details**:
   - Seleccionar **Category** como **Module**
   - Elegir el **Module** deseado (ej: Leads, Contacts)
   - Seleccionar la **Page** donde se ejecutará (ej: Create Page, Edit Page)
5. En **Event Details**:
   - Elegir **Event Type** (Page Event, Field Event, Subform Event)
   - Seleccionar el **Event** específico
6. Escribir el código JavaScript
7. Clic en **Save**

---

## Event Dictionary (Diccionario de Eventos)

### Eventos de Página

#### page.onLoad

Se dispara cuando se carga una página (Create, Edit, Clone, Detail).

**Sintaxis Oficial:**
```javascript
page.onLoad = function() {
    // Código que se ejecuta al cargar la página
};
```

**Ejemplo:**
```javascript
page.onLoad = function() {
    // Establecer valores por defecto
    var acc_type = ZDK.Page.getField("Account_Type");
    acc_type.setValue("Wholesaler");
    acc_type.setReadOnly(true);
    
    var payment_method = ZDK.Page.getField("Payment_Method");
    payment_method.setValue("Prepaid");
    payment_method.setReadOnly(true);
    
    // Ocultar campos
    ZDK.Page.getField("Campo_Oculto").hide();
    
    // Mostrar campos
    ZDK.Page.getField("Campo_Visible").show();
    
    // Establecer campos como obligatorios
    ZDK.Page.getField("Email").setMandatory(true);
    
    // Establecer campos como solo lectura
    ZDK.Page.getField("ID").setReadOnly(true);
};
```

**Uso Común:**
- Inicializar valores por defecto
- Ocultar/mostrar campos según condiciones
- Cargar datos relacionados
- Configurar campos dinámicamente

#### page.onSave

Se dispara cuando el usuario hace clic en el botón "Guardar".

**Sintaxis Oficial:**
```javascript
page.onSave = function() {
    // Código que se ejecuta antes de guardar
    // Retornar false para prevenir el guardado
    // Retornar true o nada para permitir el guardado
};
```

**Ejemplo:**
```javascript
page.onSave = function() {
    var email = ZDK.Page.getField("Email").getValue();
    var phone = ZDK.Page.getField("Phone").getValue();
    
    // Validación de email
    if (!email || !email.includes("@")) {
        ZDK.Client.showMessage("Por favor ingrese un email válido", {type: "error"});
        return false; // Previene el guardado
    }
    
    // Validación de teléfono
    if (phone && phone.length < 10) {
        ZDK.Client.showMessage("El teléfono debe tener al menos 10 dígitos", {type: "error"});
        return false;
    }
    
    // Si todo está bien, permitir guardado
    return true;
};
```

**Retorno:**
- `false`: Previene el guardado
- `true` o `undefined`: Permite el guardado

#### page.onChange

Se dispara cuando cambia cualquier campo en la página.

**Sintaxis Oficial:**
```javascript
page.onChange = function(fieldName, fieldValue) {
    // fieldName: Nombre API del campo que cambió
    // fieldValue: Nuevo valor del campo
};
```

**Ejemplo:**
```javascript
page.onChange = function(fieldName, fieldValue) {
    console.log("Campo cambiado:", fieldName, "Nuevo valor:", fieldValue);
    
    // Lógica basada en el campo que cambió
    if (fieldName === "Industry") {
        if (fieldValue === "Technology") {
            ZDK.Page.getField("Priority").setValue("High");
        }
    }
};
```

### Eventos de Campo

#### field.onChange

Se dispara cuando cambia el valor de un campo específico.

**Sintaxis Oficial:**
```javascript
// Para el campo "Email"
field.Email.onChange = function(fieldValue) {
    // fieldValue: Nuevo valor del campo
};
```

**Ejemplo:**
```javascript
field.Email.onChange = function(fieldValue) {
    var emailField = ZDK.Page.getField("Email");
    
    // Validar formato de email
    if (fieldValue && !fieldValue.includes("@")) {
        emailField.showError("Por favor ingrese un email válido");
    } else {
        emailField.clearError();
    }
    
    // Buscar duplicados usando Web API
    if (fieldValue) {
        zoho.crm.searchRecords("Contacts", "(Email:equals:" + fieldValue + ")")
            .then(function(response) {
                if (response && response.length > 0) {
                    ZDK.Client.showMessage("Este email ya existe en el sistema", {type: "warning"});
                }
            })
            .catch(function(error) {
                console.error("Error:", error);
            });
    }
};
```

**Sintaxis para otros campos:**
```javascript
// Para cualquier campo, usar el nombre API
field.Field_API_Name.onChange = function(fieldValue) {
    // Código aquí
};
```

### Eventos de Subformulario

#### subform.onRowAdd

Se dispara cuando se agrega una nueva fila a un subformulario.

**Sintaxis Oficial:**
```javascript
// Para el subformulario "Products"
subform.Products.onRowAdd = function(rowIndex) {
    // rowIndex: Índice de la fila agregada
};
```

**Ejemplo:**
```javascript
subform.Products.onRowAdd = function(rowIndex) {
    var subform = ZDK.Page.getSubform("Products");
    var row = subform.getRow(rowIndex);
    
    // Establecer valores por defecto
    row.getField("Quantity").setValue(1);
    row.getField("Discount").setValue(0);
    
    // Calcular total
    calcularTotalSubform();
};

function calcularTotalSubform() {
    var subform = ZDK.Page.getSubform("Products");
    var rows = subform.getRows();
    var total = 0;
    
    for (var i = 0; i < rows.length; i++) {
        var quantity = rows[i].getField("Quantity").getValue() || 0;
        var price = rows[i].getField("Price").getValue() || 0;
        var discount = rows[i].getField("Discount").getValue() || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        total += subtotal;
        
        // Actualizar subtotal de la fila
        rows[i].getField("Subtotal").setValue(subtotal);
    }
    
    // Actualizar total en el formulario principal
    ZDK.Page.getField("Total").setValue(total);
}
```

#### subform.onRowDelete

Se dispara después de eliminar una fila de un subformulario.

**Sintaxis Oficial:**
```javascript
subform.Products.onRowDelete = function(rowIndex) {
    // rowIndex: Índice de la fila eliminada
};
```

**Ejemplo:**
```javascript
subform.Products.onRowDelete = function(rowIndex) {
    // Recalcular total después de eliminar fila
    calcularTotalSubform();
};
```

#### subform.onCellChange

Se dispara cuando cambia el valor de una celda en un subformulario.

**Sintaxis Oficial:**
```javascript
subform.Products.onCellChange = function(rowIndex, fieldName, fieldValue) {
    // rowIndex: Índice de la fila
    // fieldName: Nombre API del campo que cambió
    // fieldValue: Nuevo valor
};
```

**Ejemplo:**
```javascript
subform.Products.onCellChange = function(rowIndex, fieldName, fieldValue) {
    // Si cambió Quantity o Price, recalcular subtotal
    if (fieldName === "Quantity" || fieldName === "Price") {
        var subform = ZDK.Page.getSubform("Products");
        var row = subform.getRow(rowIndex);
        
        var quantity = parseFloat(row.getField("Quantity").getValue()) || 0;
        var price = parseFloat(row.getField("Price").getValue()) || 0;
        var discount = parseFloat(row.getField("Discount").getValue()) || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        row.getField("Subtotal").setValue(subtotal);
        
        // Recalcular total general
        calcularTotalSubform();
    }
};
```

---

## Client API

La Client API proporciona métodos para interactuar con la interfaz de usuario de Zoho CRM.

### ZDK.Page

#### ZDK.Page.getField(fieldName)

Obtiene un objeto de campo para manipularlo.

```javascript
var emailField = ZDK.Page.getField("Email");
var emailValue = emailField.getValue();
```

**Parámetros:**
- `fieldName` (String): Nombre API del campo

**Retorno:**
- Objeto Field con métodos disponibles

#### Métodos del Objeto Field

```javascript
var field = ZDK.Page.getField("Email");

// getValue(): Obtener valor del campo
var value = field.getValue();

// setValue(value): Establecer valor del campo
field.setValue("[email protected]");

// setReadOnly(isReadOnly): Establecer como solo lectura
field.setReadOnly(true);  // Solo lectura
field.setReadOnly(false); // Editable

// setMandatory(isMandatory): Establecer como obligatorio
field.setMandatory(true);  // Obligatorio
field.setMandatory(false); // Opcional

// setMaxLength(length): Establecer longitud máxima
field.setMaxLength(100);

// showError(message): Mostrar error en el campo
field.showError("Este campo es requerido");

// clearError(): Limpiar error del campo
field.clearError();

// mask(maskPattern): Aplicar máscara al campo
field.mask("(999) 999-9999"); // Para teléfono

// hide(): Ocultar campo
field.hide();

// show(): Mostrar campo
field.show();
```

#### ZDK.Page.getForm()

Obtiene el objeto del formulario.

```javascript
var form = ZDK.Page.getForm();

// getValues(): Obtener todos los valores del formulario
var formValues = form.getValues();
console.log(formValues); // Objeto con todos los campos y valores

// setValues(values): Establecer múltiples valores
form.setValues({
    'Field_API_Name1': 'Value1',
    'Field_API_Name2': 'Value2',
    'Email': '[email protected]',
    'Phone': '+1234567890'
});
```

**Ejemplo Completo:**
```javascript
page.onLoad = function() {
    // Obtener todos los valores del formulario
    var form = ZDK.Page.getForm();
    var allValues = form.getValues();
    console.log("Todos los valores:", allValues);
    
    // Establecer múltiples valores a la vez
    form.setValues({
        'Account_Type': 'Wholesaler',
        'Payment_Method': 'Prepaid',
        'Status': 'Active'
    });
};
```

#### ZDK.Page.getSubform(subformName)

Obtiene un subformulario.

```javascript
// Obtener subformulario
var subform = ZDK.Page.getSubform("Products");

// Obtener todas las filas
var rows = subform.getRows();

// Obtener fila específica por índice
var row = subform.getRow(0);

// Obtener número de filas
var rowCount = subform.getRowCount();

// Agregar nueva fila
subform.addRow();

// Eliminar fila por índice
subform.deleteRow(0);

// Obtener valores de una fila
var rowValues = subform.getRow(0).getValues();

// Establecer valores de una fila
subform.getRow(0).setValues({
    'Quantity': 1,
    'Price': 100,
    'Discount': 0
});
```

### ZDK.Client

#### ZDK.Client.showMessage(message, options)

Muestra un mensaje toast en la página.

```javascript
// Mensaje informativo
ZDK.Client.showMessage("Operación completada");

// Mensaje de éxito
ZDK.Client.showMessage("Registro guardado exitosamente", {type: "success"});

// Mensaje de error
ZDK.Client.showMessage("Error al guardar", {type: "error"});

// Mensaje de advertencia
ZDK.Client.showMessage("Este campo será obligatorio próximamente", {type: "warning"});

// Mensaje con markdown
ZDK.Client.showMessage("Este es un *mensaje* **importante**", {type: "info"});
```

**Parámetros:**
- `message` (String): Texto del mensaje (soporta markdown)
- `options` (Object, opcional):
  - `type` (String): Tipo de mensaje (`"info"`, `"success"`, `"error"`, `"warning"`)

#### ZDK.Client.showAlert(message, heading, acceptMessage)

Muestra un alert con botón de aceptar.

```javascript
ZDK.Client.showAlert("El registro se guardó correctamente");

ZDK.Client.showAlert(
    "Este registro será eliminado permanentemente",
    "Confirmar Eliminación",
    "Entendido"
);
```

**Parámetros:**
- `message` (String): Mensaje principal
- `heading` (String, opcional): Título del alert
- `acceptMessage` (String, opcional): Texto del botón (default: "Okay")

#### ZDK.Client.showConfirmation(message, acceptMessage, rejectMessage)

Muestra un cuadro de confirmación.

```javascript
var confirmar = ZDK.Client.showConfirmation(
    "¿Está seguro de eliminar este registro?",
    "Sí",
    "No"
);

if (confirmar) {
    // Usuario hizo clic en "Sí"
    eliminarRegistro();
} else {
    // Usuario hizo clic en "No"
    ZDK.Client.showMessage("Operación cancelada");
}
```

**Parámetros:**
- `message` (String): Mensaje de confirmación
- `acceptMessage` (String): Texto del botón de aceptar
- `rejectMessage` (String): Texto del botón de rechazar

**Retorno:**
- `true`: Usuario aceptó
- `false`: Usuario rechazó

### ZDK.Page.getRecordContext()

Obtiene información sobre el registro actual.

```javascript
var recordContext = ZDK.Page.getRecordContext();

// Obtener ID del registro
var recordId = recordContext.getId();

// Obtener nombre del módulo
var moduleName = recordContext.getModuleName();

// Obtener tipo de página
var pageType = recordContext.getPageType(); // "create", "edit", "detail", etc.

// Verificar si es nuevo registro
var isNew = recordContext.isNew();
```

---

## Web API (zoho.crm.*)

La Web API permite interactuar con los datos de Zoho CRM desde el cliente.

### zoho.crm.getRecords(module, page, perPage)

Obtiene múltiples registros de un módulo.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.getRecords(<module String>, <page Long>, <perPage Long>);
```

**Ejemplo:**
```javascript
// Obtener todos los registros (página 1, 200 por defecto)
var response = zoho.crm.getRecords("Deals");
console.log(response);

// Con parámetros de paginación
var response = zoho.crm.getRecords("Contacts", 1, 50);
console.log(response);

// Procesar registros
for (var i = 0; i < response.length; i++) {
    var record = response[i];
    console.log("Nombre:", record.get("First_Name"));
    console.log("Email:", record.get("Email"));
    
    // Acceder a campos
    if (record.get("Amount") >= 1000) {
        console.log("Deal importante:", record.get("Deal_Name"));
    }
}
```

**Parámetros:**
- `module` (String): Nombre API del módulo (ej: "Contacts", "Leads", "Deals")
- `page` (Long, opcional): Número de página (default: 1)
- `perPage` (Long, opcional): Registros por página (default: 200, máximo: 200)

**Retorno:**
- Array de objetos Record

**Acceder a valores del registro:**
```javascript
var record = response[0];
var firstName = record.get("First_Name");
var email = record.get("Email");
var recordId = record.get("id");
```

### zoho.crm.getRecord(module, recordId)

Obtiene un registro específico por su ID.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.getRecord(<module String>, <recordId String>);
```

**Ejemplo:**
```javascript
var recordId = "66359000000015039";
var response = zoho.crm.getRecord("Contacts", recordId);

if (response) {
    var firstName = response.get("First_Name");
    var email = response.get("Email");
    
    // Usar los datos
    ZDK.Page.getField("Related_Contact").setValue(recordId);
    ZDK.Page.getField("Contact_Email").setValue(email);
}
```

**Parámetros:**
- `module` (String): Nombre API del módulo
- `recordId` (String): ID del registro

**Retorno:**
- Objeto Record o null si no se encuentra

### zoho.crm.searchRecords(module, criteria)

Busca registros según criterios.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.searchRecords(<module String>, <criteria String>);
```

**Ejemplo:**
```javascript
// Búsqueda simple
var criteria = "(Email:equals:[email protected])";
var response = zoho.crm.searchRecords("Contacts", criteria);

if (response && response.length > 0) {
    ZDK.Client.showMessage("Contacto encontrado: " + response[0].get("First_Name"));
} else {
    ZDK.Client.showMessage("No se encontró el contacto");
}

// Búsqueda con múltiples criterios (AND)
var criteria = "(Email:equals:[email protected])and(Status:equals:Active)";
var response = zoho.crm.searchRecords("Contacts", criteria);

// Búsqueda con OR
var criteria = "(First_Name:equals:Juan)or(First_Name:equals:María)";
var response = zoho.crm.searchRecords("Contacts", criteria);
```

**Criterios de Búsqueda:**
- `equals`: Igual a
- `not_equals`: No igual a
- `contains`: Contiene
- `starts_with`: Comienza con
- `ends_with`: Termina con
- `greater_than`: Mayor que
- `less_than`: Menor que
- `and`: Operador AND
- `or`: Operador OR

**Parámetros:**
- `module` (String): Nombre API del módulo
- `criteria` (String): Criterio de búsqueda

**Retorno:**
- Array de objetos Record

### zoho.crm.createRecord(module, data)

Crea un nuevo registro.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.createRecord(<module String>, <data Map>);
```

**Ejemplo:**
```javascript
var newRecord = {
    "First_Name": "Juan",
    "Last_Name": "Pérez",
    "Email": "[email protected]",
    "Phone": "+1234567890",
    "Mailing_City": "Madrid"
};

var response = zoho.crm.createRecord("Contacts", newRecord);

if (response) {
    var createdId = response.get("id");
    console.log("Registro creado con ID:", createdId);
    ZDK.Client.showMessage("Contacto creado exitosamente", {type: "success"});
    
    // Actualizar campo relacionado
    ZDK.Page.getField("Related_Contact").setValue(createdId);
}
```

**Parámetros:**
- `module` (String): Nombre API del módulo
- `data` (Map/Object): Objeto con los datos del registro (usar nombres API de campos)

**Retorno:**
- Objeto Record con el registro creado (incluye el ID)

### zoho.crm.updateRecord(module, recordId, data)

Actualiza un registro existente.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.updateRecord(<module String>, <recordId String>, <data Map>);
```

**Ejemplo:**
```javascript
var recordId = "66359000000015039";
var updateData = {
    "Phone": "+9876543210",
    "Mailing_City": "Barcelona",
    "Description": "Contacto actualizado desde Client Script"
};

var response = zoho.crm.updateRecord("Contacts", recordId, updateData);

if (response) {
    ZDK.Client.showMessage("Registro actualizado", {type: "success"});
    // Opcional: recargar página
    // location.reload();
}
```

**Parámetros:**
- `module` (String): Nombre API del módulo
- `recordId` (String): ID del registro a actualizar
- `data` (Map/Object): Objeto con los campos a actualizar

**Retorno:**
- Objeto Record con el registro actualizado

### zoho.crm.deleteRecord(module, recordId)

Elimina un registro.

**Sintaxis Oficial:**
```javascript
<Response> = zoho.crm.deleteRecord(<module String>, <recordId String>);
```

**Ejemplo:**
```javascript
var confirmar = ZDK.Client.showConfirmation(
    "¿Está seguro de eliminar este registro?",
    "Sí, eliminar",
    "Cancelar"
);

if (confirmar) {
    var recordId = "66359000000015039";
    var response = zoho.crm.deleteRecord("Contacts", recordId);
    
    if (response) {
        ZDK.Client.showMessage("Registro eliminado", {type: "success"});
        // Redirigir a la lista
        // window.location.href = "/crm/Contacts";
    }
}
```

**Parámetros:**
- `module` (String): Nombre API del módulo
- `recordId` (String): ID del registro a eliminar

**Retorno:**
- Objeto de respuesta

---

## ZRC (Zoho Request Client)

ZRC es una biblioteca simplificada para hacer peticiones HTTP a APIs de Zoho y servicios externos. Proporciona autenticación automática y manejo simplificado de JSON.

### ZRC.get(url, params, headers)

Realiza una petición GET.

**Sintaxis Oficial:**
```javascript
<Response> = ZRC.get(<url String>, <params Map>, <headers Map>);
```

**Ejemplo:**
```javascript
// GET simple
var response = ZRC.get("/crm/v2/Contacts");
console.log(response);

// GET con parámetros
var params = {
    page: 1,
    per_page: 50
};
var response = ZRC.get("/crm/v2/Contacts", params);
console.log(response);

// GET con headers personalizados
var headers = {
    "Custom-Header": "value"
};
var response = ZRC.get("/crm/v2/Contacts", params, headers);
```

**Parámetros:**
- `url` (String): URL de la API (relativa o absoluta)
- `params` (Map, opcional): Parámetros de consulta
- `headers` (Map, opcional): Headers personalizados

**Retorno:**
- Objeto de respuesta (JSON parseado automáticamente)

### ZRC.post(url, body, headers)

Realiza una petición POST.

**Sintaxis Oficial:**
```javascript
<Response> = ZRC.post(<url String>, <body Map>, <headers Map>);
```

**Ejemplo:**
```javascript
// POST para crear registro
var body = {
    data: [{
        "First_Name": "Juan",
        "Email": "[email protected]"
    }]
};
var response = ZRC.post("/crm/v2/Contacts", body);
console.log(response);

// POST con headers
var headers = {
    "Content-Type": "application/json"
};
var response = ZRC.post("/crm/v2/Contacts", body, headers);
```

**Parámetros:**
- `url` (String): URL de la API
- `body` (Map/Object): Cuerpo de la petición
- `headers` (Map, opcional): Headers personalizados

### ZRC.put(url, body, headers)

Realiza una petición PUT.

**Sintaxis Oficial:**
```javascript
<Response> = ZRC.put(<url String>, <body Map>, <headers Map>);
```

**Ejemplo:**
```javascript
var recordId = "66359000000015039";
var body = {
    data: [{
        "id": recordId,
        "Phone": "+1234567890"
    }]
};
var response = ZRC.put("/crm/v2/Contacts", body);
console.log(response);
```

### ZRC.delete(url, headers)

Realiza una petición DELETE.

**Sintaxis Oficial:**
```javascript
<Response> = ZRC.delete(<url String>, <headers Map>);
```

**Ejemplo:**
```javascript
var recordId = "66359000000015039";
var response = ZRC.delete("/crm/v2/Contacts/" + recordId);
console.log(response);
```

### Características de ZRC

- **Autenticación Automática**: Para APIs de Zoho CRM dentro de la misma organización, ZRC maneja la autenticación automáticamente
- **Manejo Automático de JSON**: ZRC parsea y stringifica JSON automáticamente
- **Soporte para Async/Await**: ZRC soporta tanto promesas como async/await
- **Conexiones Externas**: Soporte para conexiones configuradas para APIs externas

### Ejemplo: Llamada a API Externa

```javascript
// Llamada a API externa con conexión configurada
// Nota: La conexión debe estar configurada en Zoho CRM
var response = ZRC.get("https://api.external.com/data", null, null, "external_api_connection");

// Usar los datos
if (response && response.data) {
    ZDK.Page.getField("External_Data").setValue(JSON.stringify(response.data));
}
```

---

## Ejemplos Prácticos

### Ejemplo 1: Validación de Email y Búsqueda de Duplicados

```javascript
field.Email.onChange = function(fieldValue) {
    var emailField = ZDK.Page.getField("Email");
    
    // Validar formato
    if (fieldValue && !fieldValue.includes("@")) {
        emailField.showError("Por favor ingrese un email válido");
        return;
    }
    
    emailField.clearError();
    
    // Buscar duplicados
    if (fieldValue) {
        var criteria = "(Email:equals:" + fieldValue + ")";
        var response = zoho.crm.searchRecords("Contacts", criteria);
        
        if (response && response.length > 0) {
            var duplicate = response[0];
            var confirmar = ZDK.Client.showConfirmation(
                "Este email ya existe para: " + duplicate.get("First_Name") + " " + duplicate.get("Last_Name") + ". ¿Desea continuar?",
                "Sí, continuar",
                "Cancelar"
            );
            
            if (!confirmar) {
                emailField.setValue("");
            }
        }
    }
};
```

### Ejemplo 2: Cálculo Automático de Total en Subformulario

```javascript
subform.Products.onCellChange = function(rowIndex, fieldName, fieldValue) {
    // Si cambió Quantity, Price o Discount, recalcular
    if (fieldName === "Quantity" || fieldName === "Price" || fieldName === "Discount") {
        var subform = ZDK.Page.getSubform("Products");
        var row = subform.getRow(rowIndex);
        
        var quantity = parseFloat(row.getField("Quantity").getValue()) || 0;
        var price = parseFloat(row.getField("Price").getValue()) || 0;
        var discount = parseFloat(row.getField("Discount").getValue()) || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        row.getField("Subtotal").setValue(subtotal.toFixed(2));
        
        // Recalcular total general
        calcularTotalGeneral();
    }
};

function calcularTotalGeneral() {
    var subform = ZDK.Page.getSubform("Products");
    var rows = subform.getRows();
    var total = 0;
    
    for (var i = 0; i < rows.length; i++) {
        var subtotal = parseFloat(rows[i].getField("Subtotal").getValue()) || 0;
        total += subtotal;
    }
    
    ZDK.Page.getField("Total").setValue(total.toFixed(2));
}

subform.Products.onRowAdd = function(rowIndex) {
    var subform = ZDK.Page.getSubform("Products");
    var row = subform.getRow(rowIndex);
    
    // Valores por defecto
    row.getField("Quantity").setValue(1);
    row.getField("Discount").setValue(0);
};

subform.Products.onRowDelete = function(rowIndex) {
    calcularTotalGeneral();
};
```

### Ejemplo 3: Auto-completar Campos desde Registro Relacionado

```javascript
field.Account_Name.onChange = function(fieldValue) {
    var accountField = ZDK.Page.getField("Account_Name");
    var account = accountField.getValue();
    
    if (account && account.id) {
        // Obtener datos de la cuenta
        var accountData = zoho.crm.getRecord("Accounts", account.id);
        
        if (accountData) {
            // Auto-completar campos relacionados
            if (accountData.get("Billing_City")) {
                ZDK.Page.getField("Mailing_City").setValue(accountData.get("Billing_City"));
            }
            if (accountData.get("Billing_State")) {
                ZDK.Page.getField("Mailing_State").setValue(accountData.get("Billing_State"));
            }
            if (accountData.get("Billing_Country")) {
                ZDK.Page.getField("Mailing_Country").setValue(accountData.get("Billing_Country"));
            }
            if (accountData.get("Phone")) {
                ZDK.Page.getField("Phone").setValue(accountData.get("Phone"));
            }
        }
    }
};
```

### Ejemplo 4: Validación Compleja antes de Guardar

```javascript
page.onSave = function() {
    var errors = [];
    
    // Validar email
    var email = ZDK.Page.getField("Email").getValue();
    if (!email || !email.includes("@")) {
        errors.push("Email es requerido y debe ser válido");
        ZDK.Page.getField("Email").showError("Email inválido");
    } else {
        ZDK.Page.getField("Email").clearError();
    }
    
    // Validar teléfono
    var phone = ZDK.Page.getField("Phone").getValue();
    if (phone && phone.length < 10) {
        errors.push("El teléfono debe tener al menos 10 dígitos");
        ZDK.Page.getField("Phone").showError("Teléfono inválido");
    } else {
        ZDK.Page.getField("Phone").clearError();
    }
    
    // Validar que al menos un método de contacto esté presente
    if (!email && !phone) {
        errors.push("Debe proporcionar al menos un email o teléfono");
    }
    
    // Si hay errores, mostrar y prevenir guardado
    if (errors.length > 0) {
        ZDK.Client.showMessage("Errores de validación:\n" + errors.join("\n"), {type: "error"});
        return false;
    }
    
    return true;
};
```

### Ejemplo 5: Usar ZRC para Obtener Datos Externos

```javascript
page.onLoad = function() {
    field.Mailing_Zip.onChange = function(fieldValue) {
        if (fieldValue && fieldValue.length >= 5) {
            // Llamar a API externa usando ZRC
            var url = "https://api.zipcodeapi.com/rest/info.json/" + fieldValue + "/degrees";
            var response = ZRC.get(url);
            
            if (response && response.city) {
                ZDK.Page.getField("Mailing_City").setValue(response.city);
            }
            if (response && response.state) {
                ZDK.Page.getField("Mailing_State").setValue(response.state);
            }
        }
    };
};
```

---

## Mejores Prácticas

### 1. Manejo de Errores

```javascript
// Validar respuestas de API
var response = zoho.crm.getRecord("Contacts", recordId);
if (response) {
    // Procesar respuesta
    var email = response.get("Email");
} else {
    ZDK.Client.showMessage("Error al obtener registro", {type: "error"});
}
```

### 2. Validación de Datos

```javascript
// Validar antes de usar
var email = ZDK.Page.getField("Email").getValue();
if (email && email.length > 0) {
    // Procesar email
} else {
    // Manejar caso de email vacío
    ZDK.Client.showMessage("Email es requerido", {type: "warning"});
}
```

### 3. Optimización de Llamadas API

```javascript
// Evitar múltiples llamadas innecesarias
// ❌ Malo: Múltiples llamadas en loop
var ids = ["id1", "id2", "id3"];
for (var i = 0; i < ids.length; i++) {
    zoho.crm.getRecord("Contacts", ids[i]);
}

// ✅ Bueno: Usar getRecords con filtros o procesar en batch
var allRecords = zoho.crm.getRecords("Contacts");
// Filtrar o procesar según necesidad
```

### 4. Uso de Variables

```javascript
// Usar variables para evitar repetir código
var page = ZDK.Page;
var emailField = page.getField("Email");
var email = emailField.getValue();
```

### 5. Nombres de Campos API

```javascript
// Usar nombres API correctos (case-sensitive)
// Encontrar nombres API en: Setup > Developer Space > APIs > CRM API > API Names
ZDK.Page.getField("Email"); // ✅ Correcto
ZDK.Page.getField("email"); // ❌ Incorrecto (puede no funcionar)
```

### 6. Consideraciones de Rendimiento

```javascript
// Evitar operaciones pesadas en eventos onChange
// Usar debounce para búsquedas
var searchTimeout;
field.Search.onChange = function(fieldValue) {
    clearTimeout(searchTimeout);
    searchTimeout = setTimeout(function() {
        // Realizar búsqueda después de 500ms de inactividad
        realizarBusqueda(fieldValue);
    }, 500);
};
```

---

## Recursos Adicionales

### Documentación Oficial

- **Zoho CRM Developer Space**: https://www.zohocrm.dev
- **Event Dictionary**: https://www.zohocrm.dev/explore/client-script/event-dictionary/
- **Client API**: https://www.zohocrm.dev/explore/client-script/clientapi/
- **Web API**: https://www.zohocrm.dev/explore/client-script/webapi/
- **ZRC**: https://www.zohocrm.dev/explore/client-script/zrc/

### Encontrar Nombres API

1. Ir a **Setup** > **Developer Space** > **APIs**
2. Seleccionar **CRM API** > **API Names**
3. Buscar el módulo y campo deseado

### Debugging

```javascript
// Usar console.log para debugging
console.log("Valor del campo:", value);
console.log("Respuesta API:", response);

// Ver en la consola del navegador (F12)
```

---

## Notas Finales

- Los Client Scripts consumen **API Credits** cuando usan métodos de Web API (`zoho.crm.*`)
- Los nombres de campos son **case-sensitive**
- Siempre validar datos antes de procesarlos
- Probar scripts en un entorno de desarrollo primero
- Los scripts se ejecutan en el navegador del usuario
- Considerar el rendimiento al hacer múltiples llamadas API
- ZRC maneja automáticamente la autenticación para APIs de Zoho CRM

---

*Documentación basada exclusivamente en la documentación oficial de Zoho CRM Developer Space*
*Referencia: https://www.zohocrm.dev/explore/client-script/*
*Última actualización: 2024*
