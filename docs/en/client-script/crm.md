# Complete Documentation: Zoho CRM Client Scripts

> Language: English. Spanish: see the matching path under docs/es/.

This documentation is based exclusively on the official Zoho CRM Developer Space documentation (https://www.zohocrm.dev) and covers the Event Dictionary, Client API (ZDK), ZDK.Apps.CRM, Web API (`zoho.crm.*`), and ZRC (`zrc`). These indexes are **not** mixed.

**Official References:**
- Event Dictionary: https://www.zohocrm.dev/explore/client-script/event-dictionary/
- Client API: https://www.zohocrm.dev/explore/client-script/clientapi/
- Web API: https://www.zohocrm.dev/explore/client-script/webapi/
- ZRC: https://www.zohocrm.dev/explore/client-script/zrc/

---

## Table of Contents

1. [Introduction to Client Scripts](#introduction-to-client-scripts)
2. [Event Dictionary](#event-dictionary)
3. [Client API](#client-api)
4. [ZDK.Apps.CRM](#zdkappscrm)
5. [Web API (zoho.crm.*)](#web-api-zohocrm)
6. [ZRC (Zoho Request Client)](#zrc-zoho-request-client)
7. [Practical Examples](#practical-examples)
8. [Best Practices](#best-practices)

---

## Introduction to Client Scripts

Zoho CRM **Client Scripts** are JavaScript scripts that run on the client side (browser) to customize and automate CRM user-interface behavior.

### Key Features

- **Real-Time Execution**: Scripts run directly in the user's browser
- **No Page Reload**: Allow dynamic updates without reloading
- **Real-Time Validation**: Immediate data validation before save
- **UI Interaction**: Full control over fields, forms, and components
- **API Integration**: Access to CRM APIs and external services

### Supported Pages

- **Create Page**: Record creation page
- **Edit Page**: Record edit page
- **Clone Page**: Record clone page
- **Detail Page**: Record detail page (Standard and Canvas)
- **List Page**: Record list page (Standard)
- **Wizard Page**: Wizard pages

### Create a Client Script

1. Go to **Setup** > **Developer Hub** > **Client Script**
2. Click **+New Script**
3. Provide **Name** and **Description**
4. In **Page Details**:
   - Select **Category** as **Module**
   - Choose the desired **Module** (e.g. Leads, Contacts)
   - Select the **Page** where it will run (e.g. Create Page, Edit Page)
5. In **Event Details**:
   - Choose **Event Type** (Page Event, Field Event, Subform Event)
   - Select the specific **Event**
6. Write the JavaScript code
7. Click **Save**

---

## Event Dictionary

### Page Events

#### page.onLoad

Fires when a page loads (Create, Edit, Clone, Detail).

**Official Syntax:**
```javascript
page.onLoad = function() {
    // Código que se ejecuta al cargar la página
};
```

**Example:**
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

**Common Uses:**
- Initialize default values
- Hide/show fields based on conditions
- Load related data
- Configure fields dynamically

#### page.onSave

Fires when the user clicks the "Save" button.

**Official Syntax:**
```javascript
page.onSave = function() {
    // Código que se ejecuta antes de guardar
    // Retornar false para prevenir el guardado
    // Retornar true o nada para permitir el guardado
};
```

**Example:**
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

**Return:**
- `false`: Prevents the save
- `true` or `undefined`: Allows the save

#### page.onChange

Fires when any field on the page changes.

**Official Syntax:**
```javascript
page.onChange = function(fieldName, fieldValue) {
    // fieldName: Nombre API del campo que cambió
    // fieldValue: Nuevo valor del campo
};
```

**Example:**
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

### Field Events

#### field.onChange

Fires when a specific field's value changes.

**Official Syntax:**
```javascript
// Para el campo "Email"
field.Email.onChange = function(fieldValue) {
    // fieldValue: Nuevo valor del campo
};
```

**Example:**
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

**Syntax for other fields:**
```javascript
// Para cualquier campo, usar el nombre API
field.Field_API_Name.onChange = function(fieldValue) {
    // Código aquí
};
```

### Subform Events

#### subform.onRowAdd

Fires when a new row is added to a subform.

**Official Syntax:**
```javascript
// Para el subformulario "Products"
subform.Products.onRowAdd = function(rowIndex) {
    // rowIndex: Índice de la fila agregada
};
```

**Example:**
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

Fires after a row is deleted from a subform.

**Official Syntax:**
```javascript
subform.Products.onRowDelete = function(rowIndex) {
    // rowIndex: Índice de la fila eliminada
};
```

**Example:**
```javascript
subform.Products.onRowDelete = function(rowIndex) {
    // Recalcular total después de eliminar fila
    calcularTotalSubform();
};
```

#### subform.onCellChange

Fires when a cell value changes in a subform.

**Official Syntax:**
```javascript
subform.Products.onCellChange = function(rowIndex, fieldName, fieldValue) {
    // rowIndex: Índice de la fila
    // fieldName: Nombre API del campo que cambió
    // fieldValue: Nuevo valor
};
```

**Example:**
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

The Client API (ZDK) controls the Zoho CRM UI from Client Script. It is **not** Deluge, **not** the Widget SDK (`ZOHO.CRM.*`), and **not** the `zoho.crm.*` Web API.

**Official index:** https://www.zohocrm.dev/explore/client-script/clientapi/

`getField` / `getButton` / `getSubform` use the **API name**, not the label.

An active loader **blocks** other pop-ups (`showMessage`, `showAlert`, `showConfirmation`, `getInput`, `openMailer`, `openPopup`, `openCallout`, Flyout). Call `hideLoader()` first.

### ZDK.Page

| Method | Returns | Notes |
|--------|---------|-------|
| `getField(api_name)` | Field | Field by API name |
| `getFields()` | Field[] | All fields on the page |
| `getSubform(api_name)` | Subform | Subform by API name |
| `getForm()` | Form | Create/Edit/Clone/Detail form |
| `getList()` | List | List view (standard/canvas) |
| `getComponent(name)` | Component | See names below |
| `getComponents()` | array | Page components |
| `getButton(api_name)` | Button | e.g. `record_save` |
| `getButtonByID(id)` | Button | Button ID |
| `getButtons()` | Button[] | |
| `getLinkByID(id)` | Link | |
| `getLinks()` | Link[] | |
| `getBlueprintTransitionByID(id)` | BlueprintTransition | |
| `getBlueprintTransitions()` | BlueprintTransition[] | |
| `addTag(name, ...)` | — | Repeatable; Detail |
| `removeTag(name, ...)` | — | Repeatable; Detail |
| `getTags()` | `{name}[]` | Detail (standard and canvas) |

**Components (`getComponent`):** `list-custom-view`, `list-view-type`, `canvas-list-view`, `record-create-wizard`, `notes-editor`.

**Not in the ZDK index:** `getRecordContext()`, `getQueryParams()`. Official context: `$Page` (below).

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

Markdown in toast/alert/confirm:

| Syntax | Effect |
|--------|--------|
| `_text_` | Italics |
| `*text*` | Bold (not `**`) |
| `__text__` | Underline |
| `~text~` | Strikeout |
| `` `text` `` | Code |
| `# text` / `### text` | Heading |
| `!text` | Blockquote |
| `[click here](url)` | Hyperlink |
| `![alt](url)` | Image — **only** `showAlert`, not `showMessage` |

| Method | Notes |
|--------|-------|
| `showMessage(message, options?)` | `options.type`: `info` (default), `error`, `warning`, `success` |
| `showAlert(message, heading?, accept_message?)` | accept default `Okay` |
| `showConfirmation(message, accept?, reject?)` | → `Boolean`; defaults `Yes, Proceed` / `Cancel` |
| `getInput(options[], heading?, accept?, reject?)` | Max **7** inputs. Types: `text` (120), `number` (50 digits), `textarea` (2000), `picklist` / `multiselectpicklist` (2000 options). Picklist: `list_options: [{actual_value, display_value}]`. Returns an array of values. |
| `openMailer(mail_config)` | `from`, `to[]`, `cc[]` (`{email, label}`), `subject`, `body`, `templateId` |
| `navigateTo(page, params, data?, target?)` | See below |
| `openPopup(config, data?)` | `type`: `widget` \| `slyteui`; Widget SDK ≥ 1.2; await `$Client.close()` |
| `openCallout(config, target, data?)` | Anchored to a field; closes on blur by default |
| `createFlyout(name, config?)` | → Flyout |
| `getFlyout(name)` | Existing flyout |
| `showLoader(config)` | `type`: `page`; `template`: `standard` \| `spinner` \| `vertical-bar`; `message` ≤ 240 |
| `hideLoader()` | Hides the loader |

**`navigateTo(page, params, data, target)`**

- `page`: `tab`, `record_detail`, `record_create`, `record_clone`, `record_edit`, `record_list`
- `params.module` (API name), `params.record_id?`, `params.layout_id?`, `params.custom_view_id?`, `params.view?` (`{type: "canvas", id}` — `id` is mandatory for canvas)
- `data`: Web Tab → widget PageLoad; other pages → `$Page.navigation_data`
- `target`: `_self` (default) \| `_blank`

The official signature is `(page, params, data, target)`. Some official samples pass `_blank` as the 3rd argument (the `data` slot); follow the signature, do not copy the swapped order.

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

Maximum **15** requests per page.

```javascript
var res = ZDK.HTTP.request({
    url: "https://zylker.com",
    method: "GET",
    parameters: {query1: "txt"}
});
res.getResponse();
res.getHeaders();
res.getStatusCode(); // e.g. 201
```

`method`: `GET` (default), `POST`, `PUT`, `DELETE`, `PATCH`. `content`, `headers`, `parameters` optional.

For OAuth Connections use **`zrc`** with `{connection}` (file upload yes). `ZDK.Apps.CRM.Connections.invoke` is the legacy index (no files). `ZDK.HTTP.request` is direct AJAX (max 15).

### ZDK.UI

| Method | Notes |
|--------|-------|
| `freeze(true\|false)` | Freeze the UI |
| `scrollTo(element_id)` | Canvas |
| `highlight(element_id, {time})` | Canvas |
| `getElementByID(element_id)` | → UIElement; Canvas Detail, Wizard Create/Edit |

### Field object

| Method | Notes |
|--------|-------|
| `getApiName()` | String |
| `getValue()` / `setValue(value)` | Lookup `{id, name}`; checkbox boolean; subform array of maps |
| `setMandatory(bool)` | |
| `setMaxLength(n)` | |
| `setReadOnly(bool)` | |
| `setVisibility(bool)` | Show/hide. **Not** `hide()` / `show()` in this index |
| `showError(message)` | Inline error. Canvas Detail: Ajax edit and Mandatory form |
| `mask({character, length, reverse})` | Not a pattern such as `"(999) 999-9999"` |
| `addStyle({backgroundColor, foregroundColor, borderColor, fontWeight})` | |
| `addInfo(message)` / `addTooltip(message)` | |
| `setCriteria(criteria, config?)` | Lookup/picklist/date. Detail: lookup only |
| `getPicklistOptions()` | Pick List / Multi-Select |

```javascript
var email = ZDK.Page.getField("Email");
email.mask({character: "*", length: 4, reverse: false}); // ****rt@zohomail.com

ZDK.Page.getField("Account_Name").setValue({id: "5164254000000366162", name: "John Doe"});
ZDK.Page.getField("Email_Opt_Out").setValue(true);

var lookup = ZDK.Page.getField("Custom_Lookup1");
lookup.setCriteria("((Last_Name:equals:Burns)and(First_Name:starts_with:M))", {filterOnSearch: true});
```

**`setCriteria`:** lookup — max 5 criteria, `starts_with` and `equals`; `id` supports `in`. Picklist — max 5, `starts_with`, `equals`, `ends_with`, `contains`, `not_equals`, `in`. Date — one `between` criterion.

This index does **not** list `hide()`, `show()`, `clearError()`, `getLabel()`, `getType()`.

### Form object

`getValues()` → Object · `setValues(values)` — Create/Edit/Clone/Detail (standard, canvas, wizard).

### Subform object

| Method | Notes |
|--------|-------|
| `getApiName()` | |
| `getField(api_name)` | → **SubformField** (column, not cell) |
| `getValues()` / `setValues(array)` | Array of maps per row |
| `getRow(index)` | → SubformRow; 0-based |
| `insertRows(values, index?)` | No index = append. **Not** `addRow()` / `getRows()` / `getRowCount()` |
| `deleteRow(index)` | |
| `clear()` | |
| `setMandatory(bool)` | |
| `addInfo(message)` | Create/Edit/Clone (standard, canvas, wizard) |

**SubformRow:** `getValues()` / `setValues(obj)`, `getCell(api_name)` → SubformCell, `setReadOnly(bool)`. **No** `getField` on the row.

**SubformCell:** `getValue()` / `setValue(value)`, `setReadOnly(bool)`, `showError(message)`.

**SubformField (column):** `getApiName()`, `setCriteria(criteria, config?)` (dynamic criteria `{{CFSubform.Single_Line_1}}`), `setReadOnly`, `setMandatory`, `addInfo`, `setVisibility`.

```javascript
var sf = ZDK.Page.getSubform("CFSubform");
sf.insertRows([{Name: "Zylker", Age: "20"}]);
sf.getRow(0).getCell("Name").setValue("Zylker");
sf.getField("Custom_Lookup1").setReadOnly(true);
```

### List object

List Page (standard and canvas).

| Method | Notes |
|--------|-------|
| `freezeColumns(bool)` | Disable column changes |
| `maskField(api_name, {length, character, reverse})` | |
| `sortByField(api_name, "asc"\|"desc"\|"unsort")` | |
| `getRecords()` | `$selected` shows whether the record is selected |
| `selectRecords(criteria?)` | Only fields visible in the list |
| `selectRecordsByID(ids[])` | |
| `clearSelection()` | |
| `style({record, field}, criteria)` | `record.backgroundColor`; field: `backgroundColor`, `fontWeight`, `foregroundColor`. Empty `{}` removes style |

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

**BlueprintTransition:** `getId`, `getName`, `initiate()`. Detail (standard and canvas).

**Component:** `setValue` / `getValue`, `setVisibility`, `getOptions` / `setOptions([{id}])`, `transitionTo(api_name)` (wizard `record-create-wizard`).

**UIElement:** `setVisibility`, `addToolTip({text})` / `removeToolTip`, `addStyle(css)`, `freeze(bool)`, `setReadOnly(bool)`, `setContent(text)`, `setImage({type:"url", url})` or `{type:"userId", userId}`, `setActive()` (tab Canvas Detail).

**Flyout:** `open({type, api_name}, data?)`, `notify(data, {wait?})`, `close()`. `Notify` is not supported for components.

**Response (HTTP):** `getResponse()`, `getHeaders()`, `getStatusCode()`.

### `$Crm` / `$Client` / `$Page` utilities

**`$Crm`:** `.user` (id, zuid, names, email, type, date_format, role, profile) · `.environment` (`production`/`sandbox`) · `.deployment` (US/EU/CN/JP/IN/AU) · `.org` (type, domain_name, name, id).

**`$Client`:** `.userAgent` · `.isOnline` · `.platform` (`web`) · `.url` · `.geoLocation` (asks permission) · `.selectedValue` · `.refresh({triggerOnLoad?})` · `.openURL(url)` · `.copyToClipboard(text)` · `.rootContext` (Quick Create → parent `getField`).

**`$Page`:** `.record` (server-stored data) · `.record_id` · `.layout_id` · `.module` · `.parent_record_id` (clone) · `.navigation_data` · `.name` (`record_detail` \| `record_create` \| `record_clone` \| `record_edit` \| `record_list` \| `undefined`).

```javascript
var recordId = $Page.record_id;
var module = $Page.module;
var pageName = $Page.name;

$Client.refresh({triggerOnLoad: true});
$Client.rootContext.getField("Name").getValue();
```

---

## ZDK.Apps.CRM

Typed CRM SDK in Client Script. **Not** `zoho.crm.*`, **not** `zrc`, **not** `ZDK.HTTP.request`.

The module is the API name: `ZDK.Apps.CRM.Leads`, `ZDK.Apps.CRM.Invoices`, etc. Module fields are properties (`lead.Last_Name`), not `.get("Last_Name")`. Users use snake_case (`user.last_name`).

### Modules

| Method | Signature | Notes |
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
| `searchByCriteria` | `(criteria)` | e.g. `((Last_Name:equals:Burns%5C%2CB)and(First_Name:starts_with:M))` |
| `searchByEmail` / `searchByPhone` / `searchByWord` | `(string)` | |
| `fetchRelatedRecords` | `(id, related_list_api_name)` | |
| `delinkRelatedRecords` | `(id, related_list, ids[])` | |
| `delinkRelatedRecord` | `(id, related_list, related_id)` | |
| `updateRelatedRecords` | `(related_obj, record_id, related_list, related_id)` | |
| `fetchBlueprint` | `(record_id)` | |
| `addTagsToRecord` | `(record_id, tag_names[])` | |
| `addTagsToRecords` | `(tag_names[], record_ids[])` | tags first |
| `removeTagsToRecord` / `removeTagsToRecords` | same | official identifier is **To** (not From) |
| `sendMail` | `(mail, record_id)` | `new ZDK.Apps.CRM.__Actions.Models.Mail()` |
| `approveRecord` | `(record_id, action, comment)` | `approve` \| `delegate` \| `reject` |
| `getFeatureInfo` | `(ids[], features[])` | max 100 ids, 5 features (`blocked_emails`, `consecutive_negative_emails`) |

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

The official `getFeatureInfo` sample concatenates ids/features into a single string inside the array; the signature wants **arrays** of strings (max 100 / 5). `sendMail` types `record_id` as `Array.<String>` but the sample passes a string.

### Records (instance)

Lookup: `lead.Owner()` fires an API call (full record). `lead.Owner_Lookup_Id` is the id only. Set: `lead.Owner(user)` or `lead.Owner_Lookup_Id = id` then `__update()`.

| Method | Notes |
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
| `__getFeatureSpecificInfo(features[])` | + `unsubscribed_emails`; max 5 |

```javascript
var lead = ZDK.Apps.CRM.Leads.fetchById(id);
lead.Last_Name = "Updated Name";
lead.__update();
var notes = lead.__fetchRelatedRecords("Notes");
```

### Users / Org / Functions / Connections.invoke / Settings

**Users:** `fetch()`, `fetchById(id)`, `create(user)`, `update(user)`, `deleteById(id)`. Fields `first_name`, `last_name`, `email`, `profile`, `role` (ids).

**Org:** `fetch()`, `enableMultiCurrency(currency)`, `updateHomeCurrency(currency)`, `fetchCurrencies()`, `fetchCurrencyById(id)`, `createCurrencies([currency])`, `updateCurrencies([currency])`, `updateCurrencyById(currency, id)`.  
Official samples mix `prefix_sybmol` and `prefix_symol`. `updateCurrencies` uses `ZDK.Apps.CRM.Org.Models.fetchCurrencyById` — the listed method is `ZDK.Apps.CRM.Org.fetchCurrencyById`.

**Functions:** `ZDK.Apps.CRM.Functions.execute(name, parameters?, functions_body?)`.

**Connections.invoke** (a **separate** index from `zrc` + `connection`):

```javascript
ZDK.Apps.CRM.Connections.invoke(connection_name, url, method, param_type, parameters, headers);
```

`param_type` **1** = query, **2** = body. Pass `{}` if parameters/headers are unused. **No** file upload (use `zrc`).

**Settings:** `fetchTags` / `createTags` / `updateTags` / `updateTagsById` / `deleteTagsById` / `fetchTagsCount` / `mergeTags`; `fetchRoles` / `fetchRolesById`; `fetchProfiles` / `fetchProfilesById`; `fetchTerritories`; `fetchAllModules`; `fetchFields(module)`; `fetchLayouts` / `fetchLayoutsById`; `fetchRelatedLists`; `fetchCustomViews` / `fetchCustomViewsById`; variables (`fetch` / `create` / `update` / `delete` / groups); `fetchAssignmentRules` / `fetchAssignmentRulesByModule`; `fetchApprovalProcess` / `fetchApprovalProcessById`.

---

## Web API (zoho.crm.*)

The Web API (`zoho.crm.*`) is a **separate** index from `ZDK.Apps.CRM` and from `zrc`. Mostly v2 APIs; access with `record.get("Field")`. For v8 REST prefer `zrc.get('/crm/v8/...')`.

### zoho.crm.getRecords(module, page, perPage)

Gets multiple records from a module.

**Official Syntax:**
```javascript
<Response> = zoho.crm.getRecords(<module String>, <page Long>, <perPage Long>);
```

**Example:**
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

**Parameters:**
- `module` (String): Module API name (e.g. "Contacts", "Leads", "Deals")
- `page` (Long, optional): Page number (default: 1)
- `perPage` (Long, optional): Records per page (default: 200, maximum: 200)

**Return:**
- Array of Record objects

**Accessing record values:**
```javascript
var record = response[0];
var firstName = record.get("First_Name");
var email = record.get("Email");
var recordId = record.get("id");
```

### zoho.crm.getRecord(module, recordId)

Gets a specific record by ID.

**Official Syntax:**
```javascript
<Response> = zoho.crm.getRecord(<module String>, <recordId String>);
```

**Example:**
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

**Parameters:**
- `module` (String): Module API name
- `recordId` (String): Record ID

**Return:**
- Record object or null if not found

### zoho.crm.searchRecords(module, criteria)

Searches records by criteria.

**Official Syntax:**
```javascript
<Response> = zoho.crm.searchRecords(<module String>, <criteria String>);
```

**Example:**
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

**Search Criteria:**
- `equals`: Equal to
- `not_equals`: Not equal to
- `contains`: Contains
- `starts_with`: Starts with
- `ends_with`: Ends with
- `greater_than`: Greater than
- `less_than`: Less than
- `and`: AND operator
- `or`: OR operator

**Parameters:**
- `module` (String): Module API name
- `criteria` (String): Search criteria

**Return:**
- Array of Record objects

### zoho.crm.createRecord(module, data)

Creates a new record.

**Official Syntax:**
```javascript
<Response> = zoho.crm.createRecord(<module String>, <data Map>);
```

**Example:**
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

**Parameters:**
- `module` (String): Module API name
- `data` (Map/Object): Object with record data (use field API names)

**Return:**
- Record object for the created record (includes the ID)

### zoho.crm.updateRecord(module, recordId, data)

Updates an existing record.

**Official Syntax:**
```javascript
<Response> = zoho.crm.updateRecord(<module String>, <recordId String>, <data Map>);
```

**Example:**
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

**Parameters:**
- `module` (String): Module API name
- `recordId` (String): ID of the record to update
- `data` (Map/Object): Object with the fields to update

**Return:**
- Record object for the updated record

### zoho.crm.deleteRecord(module, recordId)

Deletes a record.

**Official Syntax:**
```javascript
<Response> = zoho.crm.deleteRecord(<module String>, <recordId String>);
```

**Example:**
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

**Parameters:**
- `module` (String): Module API name
- `recordId` (String): ID of the record to delete

**Return:**
- Response object

---

## ZRC (Zoho Request Client)

Identifier: **`zrc`** (lowercase). **Not** `ZRC.get(url, params, headers)`. **Not** `ZDK.HTTP.request` (max 15). **Not** `ZDK.Apps.CRM.Connections.invoke`.

Unified REST for CRM APIs (any version, e.g. `/crm/v8/Leads`; domain resolved from the user's DC), Connections (absolute URL + `connection`), and external APIs (sugar over `fetch`, no client request cap).

`zoho.crm.*` Web API is mostly v2; `zrc` can call any public version. File upload/download: **yes** on ZRC Connections; **no** on `Connections.invoke`. Same-org auth is automatic. `async`/`await` and Promises. JSON, query encoding, and body (JSON / FormData / URLSearchParams) are automatic.

Response: `{status, data, headers}`.

### Methods

| Method | Signature |
|--------|-------|
| `zrc.get(path, requestConfig?)` | GET; `params` are encoded |
| `zrc.post(path, body, requestConfig?)` | POST |
| `zrc.put(path, body, requestConfig?)` | PUT |
| `zrc.patch(path, body, requestConfig?)` | PATCH |
| `zrc.delete(path, requestConfig?)` | DELETE |
| `zrc.head(path, requestConfig?)` | HEAD |
| `zrc.options(path, requestConfig?)` | OPTIONS |
| `zrc.request(requestConfig)` | full control; `method` + `path` |
| `zrc.createInstance(requestConfig)` | reusable instance; **ignores** `body` |

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

**Connections** — absolute URL + link name:

```javascript
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});

const sheetZrc = zrc.createInstance({
    baseUrl: "https://sheets.googleapis.com/v4",
    connection: "google_sheets"
});
await sheetZrc.get("/spreadsheets/" + spreadsheetId);
```

**External:**

```javascript
const posts = await zrc.get("https://zylker.com/posts");
```

### requestConfig

| Property | Where | Notes |
|-----------|-------|-------|
| `headers` | all | |
| `connection` | Connections | link name |
| `baseUrl` | Connections and `createInstance` | required if `path` is relative |
| `params` | all | query key-value |
| `responseType` | all | `json` (default), `text`, `blob`, `stream`, `arraybuffer` |
| `body` | `zrc.request` | for post/put/patch it is the 2nd argument |
| `method` | `request` and `createInstance` | default GET |
| `path` | `zrc.request` | |

### Errors

Always `try/catch`. `ZrcError` (setup), `ZrcValidationError` (arguments), `ApiError` (status ≥ 300 / no response; `.status`, `.details`), `ConnectionError`.

### Limits

| Scope | Limit |
|--------|--------|
| CRM | Logged-in user; CRM API limits; browser timeout; no extra ZRC cap |
| Connections | Response ≤ **100 MB**; **40 s** read timeout |
| External | No client request cap |

CRM functions via ZRC: `POST/GET /crm/v7/functions/{api_name}/actions/execute?auth_type=oauth` (oauth2 enabled on the function). Output in `data.details.output`. In Deluge: Map argument `crmAPIRequest`.

---

## Practical Examples

### Example 1: Email Validation and Duplicate Search

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

### Example 2: Automatic Total Calculation in a Subform

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

### Example 3: Auto-fill Fields from a Related Record

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

### Example 4: Complex Validation before Save

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

### Example 5: Using ZRC to Fetch External Data

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
            ZDK.Client.showMessage("Could not resolve ZIP", {type: "error"});
        }
    }
};
```

---

## Best Practices

### 1. Error Handling

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

### 2. Data Validation

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

### 3. API Call Optimization

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

### 4. Using Variables

```javascript
// Usar variables para evitar repetir código
var page = ZDK.Page;
var emailField = page.getField("Email");
var email = emailField.getValue();
```

### 5. Field API Names

```javascript
// Usar nombres API correctos (case-sensitive)
// Encontrar nombres API en: Setup > Developer Space > APIs > CRM API > API Names
ZDK.Page.getField("Email"); // ✅ Correcto
ZDK.Page.getField("email"); // ❌ Incorrecto (puede no funcionar)
```

### 6. Performance Considerations

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

## Additional Resources

### Official Documentation

- **Zoho CRM Developer Space**: https://www.zohocrm.dev
- **Event Dictionary**: https://www.zohocrm.dev/explore/client-script/event-dictionary/
- **Client API**: https://www.zohocrm.dev/explore/client-script/clientapi/
- **Web API**: https://www.zohocrm.dev/explore/client-script/webapi/
- **ZRC**: https://www.zohocrm.dev/explore/client-script/zrc/

### Finding API Names

1. Go to **Setup** > **Developer Space** > **APIs**
2. Select **CRM API** > **API Names**
3. Search for the desired module and field

### Debugging

```javascript
// Usar console.log para debugging
console.log("Valor del campo:", value);
console.log("Respuesta API:", response);

// Ver en la consola del navegador (F12)
```

---

## Final Notes

- Client Scripts consume **API Credits** when they use Web API (`zoho.crm.*`), `ZDK.Apps.CRM`, or `zrc` against CRM
- ZRC identifier: **`zrc`** (lowercase), not `ZRC.get(url, params, headers)`
- `zrc` authenticates same-org; Connections: `{connection}` + absolute URL; response ≤ 100 MB / 40 s timeout
- `ZDK.Apps.CRM` uses properties (`Last_Name`); `zoho.crm.*` uses `.get("Last_Name")`
- Field names are **case-sensitive**
- Always validate data before processing it
- Test scripts in a development environment first
- Scripts run in the user's browser
- Consider performance when making multiple API calls

---

*Documentation based exclusively on the official Zoho CRM Developer Space documentation*
*Reference: https://www.zohocrm.dev/explore/client-script/*
*Last updated: 2024*
