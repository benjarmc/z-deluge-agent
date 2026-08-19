# Documentación Completa: Client Scripts de Zoho CRM

> Idioma: español. English: see the matching path under `docs/en/`.

Esta documentación está basada exclusivamente en la documentación oficial de Zoho CRM Developer Space (https://www.zohocrm.dev) y cubre Event Dictionary, Client API (ZDK), ZDK.Apps.CRM, Web API (`zoho.crm.*`) y ZRC (`zrc`). Estos índices **no** se mezclan.

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
4. [ZDK.Apps.CRM](#zdkappscrm)
5. [Web API (zoho.crm.*)](#web-api-zohocrm)
6. [ZRC (Zoho Request Client)](#zrc-zoho-request-client)
7. [Ejemplos Prácticos](#ejemplos-prácticos)
8. [Mejores Prácticas](#mejores-prácticas)

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
    ZDK.Page.getField("Campo_Oculto").setVisibility(false);
    
    // Mostrar campos
    ZDK.Page.getField("Campo_Visible").setVisibility(true);
    
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
    row.getCell("Quantity").setValue(1);
    row.getCell("Discount").setValue(0);
    
    // Calcular total
    calcularTotalSubform();
};

function calcularTotalSubform() {
    var subform = ZDK.Page.getSubform("Products");
    var rows = subform.getValues();
    var total = 0;
    
    for (var i = 0; i < rows.length; i++) {
        var row = subform.getRow(i);
        var quantity = row.getCell("Quantity").getValue() || 0;
        var price = row.getCell("Price").getValue() || 0;
        var discount = row.getCell("Discount").getValue() || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        total += subtotal;
        
        row.getCell("Subtotal").setValue(subtotal);
    }
    
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
        
        var quantity = parseFloat(row.getCell("Quantity").getValue()) || 0;
        var price = parseFloat(row.getCell("Price").getValue()) || 0;
        var discount = parseFloat(row.getCell("Discount").getValue()) || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        row.getCell("Subtotal").setValue(subtotal);
        
        // Recalcular total general
        calcularTotalSubform();
    }
};
```

---

## Client API

La Client API (ZDK) controla la UI de Zoho CRM desde Client Script. **No** es Deluge, **no** es el Widget SDK (`ZOHO.CRM.*`) y **no** es la Web API `zoho.crm.*`.

**Índice oficial:** https://www.zohocrm.dev/explore/client-script/clientapi/

`getField` / `getButton` / `getSubform` usan **API name**, no el label.

Un loader activo **bloquea** el resto de pop-ups (`showMessage`, `showAlert`, `showConfirmation`, `getInput`, `openMailer`, `openPopup`, `openCallout`, Flyout). Llama `hideLoader()` antes.

### ZDK.Page

| Método | Retorno | Notas |
|--------|---------|-------|
| `getField(api_name)` | Field | Campo por API name |
| `getFields()` | Field[] | Todos los campos de la página |
| `getSubform(api_name)` | Subform | Subformulario por API name |
| `getForm()` | Form | Formulario de Create/Edit/Clone/Detail |
| `getList()` | List | List view (standard/canvas) |
| `getComponent(name)` | Component | Ver nombres abajo |
| `getComponents()` | array | Componentes de la página |
| `getButton(api_name)` | Button | p.ej. `record_save` |
| `getButtonByID(id)` | Button | ID del botón |
| `getButtons()` | Button[] | |
| `getLinkByID(id)` | Link | |
| `getLinks()` | Link[] | |
| `getBlueprintTransitionByID(id)` | BlueprintTransition | |
| `getBlueprintTransitions()` | BlueprintTransition[] | |
| `addTag(name, ...)` | — | Repeatable; Detail |
| `removeTag(name, ...)` | — | Repeatable; Detail |
| `getTags()` | `{name}[]` | Detail (standard y canvas) |

**Componentes (`getComponent`):** `list-custom-view`, `list-view-type`, `canvas-list-view`, `record-create-wizard`, `notes-editor`.

**No está en el índice ZDK:** `getRecordContext()`, `getQueryParams()`. Contexto oficial: `$Page` (abajo).

```javascript
const field = ZDK.Page.getField("Last_Name");
field.getApiName(); // Last_Name

ZDK.Page.getForm().setValues({
    Last_Name: "Zylker",
    Account_Name: {id: "5164254000000366162", name: "John Doe"},
    Email_Opt_Out: true
});

ZDK.Page.addTag("tagname1", "tagname2");
ZDK.Page.getButton("record_save").setLabel("Approve & Save");
```

### ZDK.Client

Markdown en toast/alert/confirm:

| Sintaxis | Efecto |
|----------|--------|
| `_text_` | Cursiva |
| `*text*` | Negrita (no `**`) |
| `__text__` | Subrayado |
| `~text~` | Tachado |
| `` `text` `` | Código |
| `# text` / `### text` | Heading |
| `!text` | Blockquote |
| `[click here](url)` | Enlace |
| `![alt](url)` | Imagen — **solo** `showAlert`, no `showMessage` |

| Método | Notas |
|--------|-------|
| `showMessage(message, options?)` | `options.type`: `info` (default), `error`, `warning`, `success` |
| `showAlert(message, heading?, accept_message?)` | accept default `Okay` |
| `showConfirmation(message, accept?, reject?)` | → `Boolean`; defaults `Yes, Proceed` / `Cancel` |
| `getInput(options[], heading?, accept?, reject?)` | Máx. **7** inputs. Types: `text` (120), `number` (50 dígitos), `textarea` (2000), `picklist` / `multiselectpicklist` (2000 opciones). Picklist: `list_options: [{actual_value, display_value}]`. Retorna array de valores. |
| `openMailer(mail_config)` | `from`, `to[]`, `cc[]` (`{email, label}`), `subject`, `body`, `templateId` |
| `navigateTo(page, params, data?, target?)` | Ver abajo |
| `openPopup(config, data?)` | `type`: `widget` \| `slyteui`; Widget SDK ≥ 1.2; await `$Client.close()` |
| `openCallout(config, target, data?)` | Anclado a un campo; cierra al perder foco por default |
| `createFlyout(name, config?)` | → Flyout |
| `getFlyout(name)` | Flyout existente |
| `showLoader(config)` | `type`: `page`; `template`: `standard` \| `spinner` \| `vertical-bar`; `message` ≤ 240 |
| `hideLoader()` | Quita el loader |

**`navigateTo(page, params, data, target)`**

- `page`: `tab`, `record_detail`, `record_create`, `record_clone`, `record_edit`, `record_list`
- `params.module` (API name), `params.record_id?`, `params.layout_id?`, `params.custom_view_id?`, `params.view?` (`{type: "canvas", id}` — `id` obligatorio en canvas)
- `data`: Web Tab → PageLoad del widget; otras páginas → `$Page.navigation_data`
- `target`: `_self` (default) \| `_blank`

La firma oficial es `(page, params, data, target)`. Algunos samples oficiales ponen `_blank` en el 3.er argumento (posición de `data`); usa la firma, no copies el orden invertido.

```javascript
ZDK.Client.showMessage("This is an *important* warning.", {type: "warning"});

ZDK.Client.showLoader({type: "page", template: "vertical-bar", message: "Loading ..."});
ZDK.Client.hideLoader();

ZDK.Client.navigateTo("record_detail", {module: "Leads", record_id: "11111023162335"});
ZDK.Client.navigateTo("record_list", {module: "Leads", custom_view_id: "111111000000046317"});

var values = ZDK.Client.getInput([
    {type: "number", label: "Enter No. of employees"},
    {type: "text", label: "Enter batch name"}
], "Employees", "OK", "Cancel");
// ['120', 'Batch A']
```

**Popup / callout / flyout**

```javascript
ZDK.Client.openPopup({
    api_name: "sample_widget",
    type: "widget",
    header: "Sample Widget",
    animation_type: 4,
    height: "450px",
    width: "450px"
}, {data: "PageLoad payload"});

ZDK.Client.openCallout({
    api_name: "sample_widget",
    type: "widget",
    header: "Sample Widget",
    height: "450px",
    width: "450px"
}, {type: "field", api_name: "State"}, {data: "sample"});

ZDK.Client.createFlyout("myFlyout", {header: "Sample Flyout", animation_type: 4});
ZDK.Client.getFlyout("myFlyout").open({api_name: "sample_widget", type: "widget"}, {data: "payload"});
ZDK.Client.getFlyout("myFlyout").notify({data: "sample"}, {wait: true});
ZDK.Client.getFlyout("myFlyout").close();
```

`animation_type` 1–6: top, right, left, bottom, fade, zoom.

### ZDK.HTTP

Máximo **15** requests por página.

```javascript
var res = ZDK.HTTP.request({
    url: "https://zylker.com",
    method: "GET",
    parameters: {query1: "txt"}
});
res.getResponse();
res.getHeaders();
res.getStatusCode(); // p.ej. 201
```

`method`: `GET` (default), `POST`, `PUT`, `DELETE`, `PATCH`. `content`, `headers`, `parameters` opcionales.

Para Connections OAuth usa **`zrc`** con `{connection}` (file upload sí). `ZDK.Apps.CRM.Connections.invoke` es el índice viejo (sin files). `ZDK.HTTP.request` es AJAX directo (máx. 15).

### ZDK.UI

| Método | Notas |
|--------|-------|
| `freeze(true\|false)` | Congela la UI |
| `scrollTo(element_id)` | Canvas |
| `highlight(element_id, {time})` | Canvas |
| `getElementByID(element_id)` | → UIElement; Canvas Detail, Wizard Create/Edit |

### Objeto Field

| Método | Notas |
|--------|-------|
| `getApiName()` | String |
| `getValue()` / `setValue(value)` | Lookup `{id, name}`; checkbox boolean; subform array de maps |
| `setMandatory(bool)` | |
| `setMaxLength(n)` | |
| `setReadOnly(bool)` | |
| `setVisibility(bool)` | Mostrar/ocultar. **No** `hide()` / `show()` en este índice |
| `showError(message)` | Error inline. Canvas Detail: Ajax edit y Mandatory form |
| `mask({character, length, reverse})` | No es un patrón tipo `"(999) 999-9999"` |
| `addStyle({backgroundColor, foregroundColor, borderColor, fontWeight})` | |
| `addInfo(message)` / `addTooltip(message)` | |
| `setCriteria(criteria, config?)` | Lookup/picklist/date. Detail: solo lookup |
| `getPicklistOptions()` | Pick List / Multi-Select |

```javascript
var email = ZDK.Page.getField("Email");
email.mask({character: "*", length: 4, reverse: false}); // ****rt@zohomail.com

ZDK.Page.getField("Account_Name").setValue({id: "5164254000000366162", name: "John Doe"});
ZDK.Page.getField("Email_Opt_Out").setValue(true);

var lookup = ZDK.Page.getField("Custom_Lookup1");
lookup.setCriteria("((Last_Name:equals:Burns)and(First_Name:starts_with:M))", {filterOnSearch: true});
```

**`setCriteria`:** lookup — máx. 5 criterios, operadores `starts_with` y `equals`; `id` admite `in`. Picklist — máx. 5, operadores `starts_with`, `equals`, `ends_with`, `contains`, `not_equals`, `in`. Date — un criterio `between`.

Este índice **no** lista `hide()`, `show()`, `clearError()`, `getLabel()`, `getType()`.

### Objeto Form

`getValues()` → Object · `setValues(values)` — Create/Edit/Clone/Detail (standard, canvas, wizard).

### Objeto Subform

| Método | Notas |
|--------|-------|
| `getApiName()` | |
| `getField(api_name)` | → **SubformField** (columna, no celda) |
| `getValues()` / `setValues(array)` | Array de maps por fila |
| `getRow(index)` | → SubformRow; índice desde 0 |
| `insertRows(values, index?)` | Sin index = append. **No** `addRow()` / `getRows()` / `getRowCount()` |
| `deleteRow(index)` | |
| `clear()` | |
| `setMandatory(bool)` | |
| `addInfo(message)` | Create/Edit/Clone (standard, canvas, wizard) |

**SubformRow:** `getValues()` / `setValues(obj)`, `getCell(api_name)` → SubformCell, `setReadOnly(bool)`. **No** `getField` en la fila.

**SubformCell:** `getValue()` / `setValue(value)`, `setReadOnly(bool)`, `showError(message)`.

**SubformField (columna):** `getApiName()`, `setCriteria(criteria, config?)` (criterios dinámicos `{{CFSubform.Single_Line_1}}`), `setReadOnly`, `setMandatory`, `addInfo`, `setVisibility`.

```javascript
var sf = ZDK.Page.getSubform("CFSubform");
sf.insertRows([{Name: "Zylker", Age: "20"}]);
sf.getRow(0).getCell("Name").setValue("Zylker");
sf.getField("Custom_Lookup1").setReadOnly(true);
```

### Objeto List

List Page (standard y canvas).

| Método | Notas |
|--------|-------|
| `freezeColumns(bool)` | Bloquea modificar columnas |
| `maskField(api_name, {length, character, reverse})` | |
| `sortByField(api_name, "asc"\|"desc"\|"unsort")` | |
| `getRecords()` | `$selected` indica si el registro está seleccionado |
| `selectRecords(criteria?)` | Solo campos visibles en la lista |
| `selectRecordsByID(ids[])` | |
| `clearSelection()` | |
| `style({record, field}, criteria)` | `record.backgroundColor`; field: `backgroundColor`, `fontWeight`, `foregroundColor`. `{}` vacíos quitan estilo |

```javascript
ZDK.Page.getList().selectRecords("((Last_Name:equals:Burns)and(First_Name:starts_with:M))");
ZDK.Page.getList().style({
    record: {backgroundColor: "#ff0000"},
    field: {Email: {foregroundColor: "#ff0000", fontWeight: "bold"}}
}, "((Last_Name:equals:Burns)and(First_Name:starts_with:M))");
```

### Button / Link / BlueprintTransition / Component / UIElement / Flyout / Response

**Button:** `getId`, `getType` (`system` \| `custom`), `getLabel`, `getApiName`, `click`, `disable`, `enable`, `setLabel`.

**Link:** `getId`, `getName`, `click`, `disable`, `enable`. Canvas Detail/List, standard Detail.

**BlueprintTransition:** `getId`, `getName`, `initiate()`. Detail (standard y canvas).

**Component:** `setValue` / `getValue`, `setVisibility`, `getOptions` / `setOptions([{id}])`, `transitionTo(api_name)` (wizard `record-create-wizard`).

**UIElement:** `setVisibility`, `addToolTip({text})` / `removeToolTip`, `addStyle(css)`, `freeze(bool)`, `setReadOnly(bool)`, `setContent(text)`, `setImage({type:"url", url})` o `{type:"userId", userId}`, `setActive()` (tab Canvas Detail).

**Flyout:** `open({type, api_name}, data?)`, `notify(data, {wait?})`, `close()`. `Notify` no aplica a components.

**Response (HTTP):** `getResponse()`, `getHeaders()`, `getStatusCode()`.

### Utilidades `$Crm` / `$Client` / `$Page`

**`$Crm`:** `.user` (id, zuid, names, email, type, date_format, role, profile) · `.environment` (`production`/`sandbox`) · `.deployment` (US/EU/CN/JP/IN/AU) · `.org` (type, domain_name, name, id).

**`$Client`:** `.userAgent` · `.isOnline` · `.platform` (`web`) · `.url` · `.geoLocation` (pide permiso) · `.selectedValue` · `.refresh({triggerOnLoad?})` · `.openURL(url)` · `.copyToClipboard(text)` · `.rootContext` (Quick Create → `getField` del padre).

**`$Page`:** `.record` (datos en servidor) · `.record_id` · `.layout_id` · `.module` · `.parent_record_id` (clone) · `.navigation_data` · `.name` (`record_detail` \| `record_create` \| `record_clone` \| `record_edit` \| `record_list` \| `undefined`).

```javascript
var recordId = $Page.record_id;
var module = $Page.module;
var pageName = $Page.name;

$Client.refresh({triggerOnLoad: true});
$Client.rootContext.getField("Name").getValue();
```

---

## ZDK.Apps.CRM

SDK tipado de CRM en Client Script. **No** es `zoho.crm.*`, **no** es `zrc`, **no** es `ZDK.HTTP.request`.

El módulo es el API name: `ZDK.Apps.CRM.Leads`, `ZDK.Apps.CRM.Invoices`, etc. Los campos de módulo son propiedades (`lead.Last_Name`), no `.get("Last_Name")`. Users usa snake_case (`user.last_name`).

### Modules

| Método | Firma | Notas |
|--------|-------|-------|
| `fetch` | `(page?, per_page?, sort_by?, sort_order?)` | `sort_order`: `asc` \| `desc` |
| `fetchById` | `(id)` | |
| `create` | `(object \| array)` | `new ZDK.Apps.CRM.Leads.Models.Leads()` |
| `update` | `(object \| array)` | |
| `updateById` | `(object, record_id)` | |
| `upsert` | `(object \| array)` | |
| `deleteByIds` | `(ids[])` | |
| `fetchDeletedRecords` | `()` | |
| `convert` | `(convertLeads, id)` | Leads → Accounts/Deals |
| `searchByCriteria` | `(criteria)` | p.ej. `((Last_Name:equals:Burns%5C%2CB)and(First_Name:starts_with:M))` |
| `searchByEmail` / `searchByPhone` / `searchByWord` | `(string)` | |
| `fetchRelatedRecords` | `(id, related_list_api_name)` | |
| `delinkRelatedRecords` | `(id, related_list, ids[])` | |
| `delinkRelatedRecord` | `(id, related_list, related_id)` | |
| `updateRelatedRecords` | `(related_obj, record_id, related_list, related_id)` | |
| `fetchBlueprint` | `(record_id)` | |
| `addTagsToRecord` | `(record_id, tag_names[])` | |
| `addTagsToRecords` | `(tag_names[], record_ids[])` | orden: tags primero |
| `removeTagsToRecord` / `removeTagsToRecords` | igual | identificador oficial **To** (no From) |
| `sendMail` | `(mail, record_id)` | `new ZDK.Apps.CRM.__Actions.Models.Mail()` |
| `approveRecord` | `(record_id, action, comment)` | `approve` \| `delegate` \| `reject` |
| `getFeatureInfo` | `(ids[], features[])` | máx. 100 ids, 5 features (`blocked_emails`, `consecutive_negative_emails`) |

```javascript
var leads = ZDK.Apps.CRM.Leads.fetch(2, 20, "Last_Name", "desc");
leads[0].Last_Name;

var lead = new ZDK.Apps.CRM.Leads.Models.Leads();
lead.Last_Name = "James";
lead.Lead_Status = ZDK.Apps.CRM.Leads.Models.Lead_StatusPicklist.CONTACTED;
var response = ZDK.Apps.CRM.Leads.create(lead);

var note = new ZDK.Apps.CRM.Notes.Models.Notes();
note.Note_Content = "create test";
note.Parent_Id_Lookup_Id = "2000000069181";
note.$se_module = "Leads";
ZDK.Apps.CRM.Notes.create(note);
```

Picklists: `ZDK.Apps.CRM.<Module>.Models.<Field>Picklist.<VALUE>`. Inventory: `ZDK.Apps.CRM.Invoices.Models.Line_Items` (`.product(product)`, `.quantity`). Price Books: `Pricing_Details`. Events: `Participants`. Convert: `Leads.Models.ConvertLeads` + `Deals.Models.Deals`.

El sample oficial de `getFeatureInfo` concatena ids/features en un solo string dentro del array; la firma pide **arrays** de strings (máx. 100 / 5). `sendMail` declara `record_id` como `Array.<String>` pero el sample pasa un string.

### Records (instancia)

Lookup: `lead.Owner()` dispara API (registro completo). `lead.Owner_Lookup_Id` es solo el id. Set: `lead.Owner(user)` o `lead.Owner_Lookup_Id = id` + `__update()`.

| Método | Notas |
|--------|-------|
| `__create` / `__update` / `__remove` | |
| `__convert(convertLeads)` | |
| `__addTags(names[])` / `__removeTags(names[])` | |
| `__fetchRelatedRecords(related_list)` | |
| `__delinkRelatedRecords(related_list, ids[])` | |
| `__updateRelatedRecords(record, related_list, id)` | |
| `__fetchBlueprint()` | |
| `__approveRecord(action, comment)` | |
| `__sendMail(mail)` | |
| `__getFeatureSpecificInfo(features[])` | + `unsubscribed_emails`; máx. 5 |

```javascript
var lead = ZDK.Apps.CRM.Leads.fetchById(id);
lead.Last_Name = "Updated Name";
lead.__update();
var notes = lead.__fetchRelatedRecords("Notes");
```

### Users / Org / Functions / Connections.invoke / Settings

**Users:** `fetch()`, `fetchById(id)`, `create(user)`, `update(user)`, `deleteById(id)`. Campos `first_name`, `last_name`, `email`, `profile`, `role` (ids).

**Org:** `fetch()`, `enableMultiCurrency(currency)`, `updateHomeCurrency(currency)`, `fetchCurrencies()`, `fetchCurrencyById(id)`, `createCurrencies([currency])`, `updateCurrencies([currency])`, `updateCurrencyById(currency, id)`.  
Samples oficiales mezclan `prefix_sybmol` y `prefix_symol`. `updateCurrencies` usa `ZDK.Apps.CRM.Org.Models.fetchCurrencyById` — el método listado es `ZDK.Apps.CRM.Org.fetchCurrencyById`.

**Functions:** `ZDK.Apps.CRM.Functions.execute(name, parameters?, functions_body?)`.

**Connections.invoke** (índice **aparte** de `zrc` + `connection`):

```javascript
ZDK.Apps.CRM.Connections.invoke(connection_name, url, method, param_type, parameters, headers);
```

`param_type` **1** = query, **2** = body. Pasar `{}` si no hay parameters/headers. **No** soporta file upload (usar `zrc`).

**Settings:** `fetchTags` / `createTags` / `updateTags` / `updateTagsById` / `deleteTagsById` / `fetchTagsCount` / `mergeTags`; `fetchRoles` / `fetchRolesById`; `fetchProfiles` / `fetchProfilesById`; `fetchTerritories`; `fetchAllModules`; `fetchFields(module)`; `fetchLayouts` / `fetchLayoutsById`; `fetchRelatedLists`; `fetchCustomViews` / `fetchCustomViewsById`; variables (`fetch` / `create` / `update` / `delete` / groups); `fetchAssignmentRules` / `fetchAssignmentRulesByModule`; `fetchApprovalProcess` / `fetchApprovalProcessById`.

---

## Web API (zoho.crm.*)

La Web API (`zoho.crm.*`) es un índice **aparte** de `ZDK.Apps.CRM` y de `zrc`. Sobre todo APIs v2; acceso con `record.get("Field")`. Para REST v8 preferir `zrc.get('/crm/v8/...')`.

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

Identificador: **`zrc`** (minúsculas). **No** `ZRC.get(url, params, headers)`. **No** es `ZDK.HTTP.request` (máx. 15). **No** es `ZDK.Apps.CRM.Connections.invoke`.

REST unificado para CRM APIs (cualquier versión, p.ej. `/crm/v8/Leads`; el dominio se resuelve por DC del usuario), Connections (URL absoluta + `connection`) y APIs externas (azúcar sobre `fetch`, sin tope de requests en el cliente).

La Web API `zoho.crm.*` cubre sobre todo v2; `zrc` llama cualquier versión pública. File upload/download: **sí** en ZRC Connections; **no** en `Connections.invoke`. Auth same-org automática. `async`/`await` y Promises. JSON, query encoding y body (JSON / FormData / URLSearchParams) automáticos.

Respuesta: `{status, data, headers}`.

### Métodos

| Método | Firma |
|--------|-------|
| `zrc.get(path, requestConfig?)` | GET; `params` se encodean |
| `zrc.post(path, body, requestConfig?)` | POST |
| `zrc.put(path, body, requestConfig?)` | PUT |
| `zrc.patch(path, body, requestConfig?)` | PATCH |
| `zrc.delete(path, requestConfig?)` | DELETE |
| `zrc.head(path, requestConfig?)` | HEAD |
| `zrc.options(path, requestConfig?)` | OPTIONS |
| `zrc.request(requestConfig)` | control total; `method` + `path` |
| `zrc.createInstance(requestConfig)` | instancia reutilizable; **ignora** `body` |

```javascript
const users = await zrc.get("/crm/v8/users");
const deal = await zrc.get("/crm/v8/Deals/" + id, {params: {fields: "Deal_Name,Stage,Amount"}});
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.put("/crm/v8/Leads/" + id, {data: [{Last_Name: "Updated Name"}]});
await zrc.delete("/crm/v8/Leads/" + id);

const formData = new FormData();
formData.append("file", fileBlob, "resume.pdf");
await zrc.post("/crm/v8/Leads/" + id + "/Attachments", formData);

const coqlRes = await zrc.post("/crm/v8/coql", {
    select_query: "select Last_Name, Email from Leads where Email like '%@example.com'"
});
```

**Connections** — URL absoluta + link name:

```javascript
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});

const sheetZrc = zrc.createInstance({
    baseUrl: "https://sheets.googleapis.com/v4",
    connection: "google_sheets"
});
await sheetZrc.get("/spreadsheets/" + spreadsheetId);
```

**Externa:**

```javascript
const posts = await zrc.get("https://zylker.com/posts");
```

### requestConfig

| Propiedad | Dónde | Notas |
|-----------|-------|-------|
| `headers` | todos | |
| `connection` | Connections | link name |
| `baseUrl` | Connections y `createInstance` | obligatorio si `path` es relativo |
| `params` | todos | query key-value |
| `responseType` | todos | `json` (default), `text`, `blob`, `stream`, `arraybuffer` |
| `body` | `zrc.request` | en post/put/patch va como 2.º argumento |
| `method` | `request` y `createInstance` | default GET |
| `path` | `zrc.request` | |

### Errores

Siempre `try/catch`. `ZrcError` (setup), `ZrcValidationError` (argumentos), `ApiError` (status ≥ 300 / sin respuesta; `.status`, `.details`), `ConnectionError`.

### Límites

| Ámbito | Límite |
|--------|--------|
| CRM | Usuario logueado; límites API CRM; timeout del browser; sin tope extra en ZRC |
| Connections | Respuesta ≤ **100 MB**; read timeout **40 s** |
| Externa | Sin tope de requests en el cliente |

Funciones CRM vía ZRC: `POST/GET /crm/v7/functions/{api_name}/actions/execute?auth_type=oauth` (oauth2 habilitado en la función). Output en `data.details.output`. En Deluge: argumento Map `crmAPIRequest`.

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
        
        var quantity = parseFloat(row.getCell("Quantity").getValue()) || 0;
        var price = parseFloat(row.getCell("Price").getValue()) || 0;
        var discount = parseFloat(row.getCell("Discount").getValue()) || 0;
        
        var subtotal = (quantity * price) * (1 - discount / 100);
        row.getCell("Subtotal").setValue(subtotal.toFixed(2));
        
        // Recalcular total general
        calcularTotalGeneral();
    }
};

function calcularTotalGeneral() {
    var subform = ZDK.Page.getSubform("Products");
    var rows = subform.getValues();
    var total = 0;
    
    for (var i = 0; i < rows.length; i++) {
        var subtotal = parseFloat(subform.getRow(i).getCell("Subtotal").getValue()) || 0;
        total += subtotal;
    }
    
    ZDK.Page.getField("Total").setValue(total.toFixed(2));
}

subform.Products.onRowAdd = function(rowIndex) {
    var subform = ZDK.Page.getSubform("Products");
    var row = subform.getRow(rowIndex);
    
    // Valores por defecto
    row.getCell("Quantity").setValue(1);
    row.getCell("Discount").setValue(0);
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
    }
    
    // Validar teléfono
    var phone = ZDK.Page.getField("Phone").getValue();
    if (phone && phone.length < 10) {
        errors.push("El teléfono debe tener al menos 10 dígitos");
        ZDK.Page.getField("Phone").showError("Teléfono inválido");
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
field.Mailing_Zip.onChange = async function(fieldValue) {
    if (fieldValue && fieldValue.length >= 5) {
        try {
            const res = await zrc.get("https://api.zipcodeapi.com/rest/info.json/" + fieldValue + "/degrees");
            if (res.data && res.data.city) {
                ZDK.Page.getField("Mailing_City").setValue(res.data.city);
            }
            if (res.data && res.data.state) {
                ZDK.Page.getField("Mailing_State").setValue(res.data.state);
            }
        } catch (error) {
            ZDK.Client.showMessage("No se pudo resolver el CP", {type: "error"});
        }
    }
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

- Los Client Scripts consumen **API Credits** cuando usan Web API (`zoho.crm.*`), `ZDK.Apps.CRM` o `zrc` contra CRM
- Identificador ZRC: **`zrc`** (minúsculas), no `ZRC.get(url, params, headers)`
- `zrc` autentica same-org; Connections: `{connection}` + URL absoluta; respuesta ≤ 100 MB / timeout 40 s
- `ZDK.Apps.CRM` usa propiedades (`Last_Name`); `zoho.crm.*` usa `.get("Last_Name")`
- Los nombres de campos son **case-sensitive**
- Siempre validar datos antes de procesarlos
- Probar scripts en un entorno de desarrollo primero
- Los scripts se ejecutan en el navegador del usuario
- Considerar el rendimiento al hacer múltiples llamadas API

---

*Documentación basada exclusivamente en la documentación oficial de Zoho CRM Developer Space*
*Referencia: https://www.zohocrm.dev/explore/client-script/*
*Última actualización: 2024*
