---
name: zoho-crm-client-script
description: Writes, reviews, and debugs Zoho CRM Client Scripts in client-side JavaScript. Use for page.onLoad, ZDK.Page/Client/HTTP/UI, ZDK.Apps.CRM, zrc, zoho.crm.* Web API, $Page/$Crm/$Client, Canvas, Wizard, zohocrm.dev. / Escribe, revisa y depura Client Scripts de Zoho CRM. Usar para ZDK, ZDK.Apps.CRM, zrc, Web API, Canvas, Wizard.
---

# Client Scripts — Zoho CRM (JavaScript)

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

Scripts **JavaScript** en el navegador. **No es Deluge.**

**Referencia completa:** `docs/en/client-script/crm.md` · `docs/es/client-script/crm.md`  
**Docs oficiales:** https://www.zohocrm.dev/explore/client-script/

---

## Alcance

- Páginas: Create, Edit, Clone, Detail (Standard + Canvas), List (Standard), Wizard
- Setup: Developer Hub → Client Script → +New Script → Module + Page + Event
- Runtime: browser del usuario — respeta permisos de su perfil

---

## Event Dictionary — Page Events

### page.onLoad
Dispara al cargar Create/Edit/Clone/Detail.

```javascript
page.onLoad = function() {
    var acc = ZDK.Page.getField("Account_Type");
    acc.setValue("Wholesaler");
    acc.setReadOnly(true);
    ZDK.Page.getField("Campo_Oculto").setVisibility(false);
    ZDK.Page.getField("Email").setMandatory(true);
};
```

### page.onSave
Validación antes de guardar. Retornar `false` bloquea save.

```javascript
page.onSave = function() {
    var email = ZDK.Page.getField("Email").getValue();
    if(!email || !email.includes("@")) {
        ZDK.Client.showMessage("Email inválido", {type: "error"});
        return false;
    }
    return true;
};
```

### page.onChange
Dispara cuando cambia cualquier campo.

```javascript
page.onChange = function(fieldName, fieldValue) {
    if(fieldName === "Industry" && fieldValue === "Technology") {
        ZDK.Page.getField("Priority").setValue("High");
    }
};
```

---

## Event Dictionary — Field Events

### field.{API_NAME}.onChange

```javascript
field.Email.onChange = function(fieldValue) {
    var f = ZDK.Page.getField("Email");
    if(fieldValue && !fieldValue.includes("@")) {
        f.showError("Email inválido");
    }
};
```

### field.onBeforeSave (validación campo)

```javascript
field.Phone.onBeforeSave = function(value) {
    if(value && value.length < 10) {
        return {valid: false, message: "Mínimo 10 dígitos"};
    }
    return {valid: true};
};
```

---

## Event Dictionary — Subform Events

### subform.onRowAdd

```javascript
subform.onRowAdd = function(rowIndex) {
    var sf = ZDK.Page.getSubform("Productos");
    sf.getRow(rowIndex).getCell("Cantidad").setValue(1);
};
```

### subform.onRowDelete

```javascript
subform.onRowDelete = function(rowIndex) {
    recalcularTotal();
};
```

### subform.onCellChange

```javascript
subform.onCellChange = function(rowIndex, fieldName, value) {
    if(fieldName === "Cantidad" || fieldName === "Precio") {
        recalcularFila(rowIndex);
    }
};
```

---

## ZDK.Page

Índice: https://www.zohocrm.dev/explore/client-script/clientapi/  
`getField(api_name)` — API name, no label.

| Método | Retorno |
|--------|---------|
| `getField(api_name)` | Field |
| `getFields()` | Field[] |
| `getSubform(api_name)` | Subform |
| `getForm()` | Form |
| `getList()` | List |
| `getComponent(name)` / `getComponents()` | Component |
| `getButton(api_name)` / `getButtonByID(id)` / `getButtons()` | Button |
| `getLinkByID(id)` / `getLinks()` | Link |
| `getBlueprintTransitionByID(id)` / `getBlueprintTransitions()` | BlueprintTransition |
| `addTag(...)` / `removeTag(...)` / `getTags()` | tags (Detail) |

Componentes: `list-custom-view`, `list-view-type`, `canvas-list-view`, `record-create-wizard`, `notes-editor`.

Contexto de página: **`$Page`**, no `getRecordContext()` — `$Page.record`, `$Page.record_id`, `$Page.module`, `$Page.layout_id`, `$Page.name`, `$Page.navigation_data`, `$Page.parent_record_id`.

---

## ZDK.Client

Un loader activo **bloquea** el resto de pop-ups. Markdown toast: `*bold*`, `_italics_`, `__underline__`, `~strike~`, `` `code` ``, `#` / `###`, `!blockquote`, `[text](url)`. **No** imágenes en `showMessage`.

| Método | Notas |
|--------|-------|
| `showMessage(msg, {type})` | type: `info` (default), `error`, `warning`, `success` |
| `showAlert(msg, heading?, accept?)` | accept default `Okay`; markdown + `![alt](url)` |
| `showConfirmation(msg, accept?, reject?)` | → `true`/`false`; defaults Yes, Proceed / Cancel |
| `getInput(options[], heading?, accept?, reject?)` | máx. 7 inputs: text/number/textarea/picklist/multiselectpicklist |
| `openMailer(config)` | from, to[], cc[], subject, body, templateId |
| `navigateTo(page, params, data?, target?)` | page: `tab`, `record_detail`, `record_create`, `record_clone`, `record_edit`, `record_list`; target `_self`/`_blank`; data → `$Page.navigation_data` |
| `openPopup(config, data)` / `openCallout(config, target, data)` | widget \| slyteui; SDK ≥ 1.2 |
| `createFlyout(name, config)` / `getFlyout(name)` | luego `.open()` / `.notify()` / `.close()` |
| `showLoader({type, template, message})` / `hideLoader()` | template: `standard`, `spinner`, `vertical-bar`; message ≤ 240 |

```javascript
ZDK.Client.showMessage("This is an *important* warning.", {type: "warning"});
ZDK.Client.showLoader({type: "page", template: "vertical-bar", message: "Loading ..."});
ZDK.Client.hideLoader();
ZDK.Client.navigateTo("record_detail", {module: "Leads", record_id: id});
```

---

## ZDK.HTTP / ZDK.UI

`ZDK.HTTP.request({url, method?, content?, headers?, parameters?})` — máx. **15** requests/página. Methods: GET (default), POST, PUT, DELETE, PATCH. Respuesta: `.getResponse()`, `.getHeaders()`, `.getStatusCode()`.

`ZDK.UI.freeze(bool)` · `scrollTo(element_id)` · `highlight(id, {time})` · `getElementByID(id)` → UIElement (Canvas/Wizard).

---

## Objetos Field / Form / Subform

**Field:** `getApiName`, `getValue`/`setValue`, `setMandatory`, `setMaxLength`, `setReadOnly`, `setVisibility` (**no** `hide`/`show`), `showError`, `mask({character, length, reverse})`, `addStyle`, `addInfo`, `addTooltip`, `setCriteria`, `getPicklistOptions`.  
Lookup `setValue({id, name})`. Checkbox boolean. Subform-as-field: array de maps.

**Form:** `getValues()` / `setValues(obj)`.

**Subform:** `getApiName`, `getField(api)` → SubformField, `getValues`/`setValues`, `getRow(i)` → SubformRow (índice 0), `insertRows(rows, index?)` (append si no hay index; **no** `addRow`), `deleteRow(i)`, `clear()`, `setMandatory`, `addInfo`.  
**SubformRow:** `getValues`/`setValues`, `getCell(api)` → SubformCell, `setReadOnly`. **No** `getField` en la fila.  
**SubformCell:** `getValue`/`setValue`, `setReadOnly`, `showError`.

**List:** `freezeColumns`, `maskField`, `sortByField(api, "asc"|"desc"|"unsort")`, `getRecords`, `selectRecords(criteria)`, `selectRecordsByID(ids)`, `clearSelection`, `style(config, criteria)`.

**Button:** `getId`/`getType`/`getLabel`/`getApiName`, `click`, `disable`/`enable`, `setLabel`.  
**Link / BlueprintTransition:** `click`/`disable`/`enable`; transition `initiate()`.

---

## $Crm / $Client / $Page

`$Crm.user` · `$Crm.environment` (`production`/`sandbox`) · `$Crm.deployment` · `$Crm.org`  
`$Client.userAgent` · `$Client.isOnline` · `$Client.platform` · `$Client.url` · `$Client.geoLocation` · `$Client.selectedValue` · `$Client.refresh({triggerOnLoad?})` · `$Client.openURL(url)` · `$Client.copyToClipboard(text)` · `$Client.rootContext` (Quick Create → padre)

---

## Índices HTTP / CRM (no mezclar)

| Índice | Identificador | Notas |
|--------|---------------|-------|
| Client API UI | `ZDK.Page` / `ZDK.Client` | Campos, toast, navigate |
| AJAX | `ZDK.HTTP.request` | Máx. **15**/página |
| SDK tipado CRM | `ZDK.Apps.CRM.<Module>` | Models, `.Last_Name`, `__update()` |
| Web API | `zoho.crm.getRecords` | Sobre todo v2; `record.get("Field")` |
| ZRC | **`zrc.get` / `zrc.post`** (minúsculas) | Cualquier versión CRM (`/crm/v8/...`); `await`; **no** `ZRC.get(url, params, headers)` |
| Connections viejo | `ZDK.Apps.CRM.Connections.invoke` | Sin file upload |
| Connections ZRC | `zrc.get(absUrl, {connection})` | File upload/download |

---

## ZDK.Apps.CRM — Modules

`ZDK.Apps.CRM.Leads.fetch()` (el módulo es el API name). Create: `new ZDK.Apps.CRM.Leads.Models.Leads()`. Picklist: `ZDK.Apps.CRM.Leads.Models.Lead_StatusPicklist.CONTACTED`. Inventory: `Invoices.Models.Line_Items`. Lookup: `lead.Owner()` dispara API; `lead.Owner_Lookup_Id` es solo el id.

| Método | Firma |
|--------|-------|
| `fetch` | `(page?, per_page?, sort_by?, sort_order?)` → array. sort_order: `asc`\|`desc` |
| `fetchById` | `(id)` |
| `create` / `update` / `upsert` | `(object \| array)` |
| `updateById` | `(object, record_id)` |
| `deleteByIds` | `(ids[])` |
| `fetchDeletedRecords` | `()` |
| `convert` | `(convertLeads, id)` — Leads |
| `searchByCriteria` / `searchByEmail` / `searchByPhone` / `searchByWord` | |
| `fetchRelatedRecords` | `(id, related_list_api_name)` |
| `delinkRelatedRecords` / `delinkRelatedRecord` | |
| `updateRelatedRecords` | `(related_obj, record_id, related_list, related_id)` |
| `fetchBlueprint` | `(record_id)` |
| `addTagsToRecord` / `addTagsToRecords` | |
| `removeTagsToRecord` / `removeTagsToRecords` | identificador oficial **To**, no From |
| `sendMail` | `(mail, record_id)` — `new ZDK.Apps.CRM.__Actions.Models.Mail()` |
| `approveRecord` | `(record_id, "approve"\|"delegate"\|"reject", comment)` |
| `getFeatureInfo` | `(ids[], features[])` — máx. 100 ids, 5 features |

**Record instance:** `__create` `__update` `__remove` `__convert` `__addTags` `__removeTags` `__fetchRelatedRecords` `__delinkRelatedRecords` `__updateRelatedRecords` `__fetchBlueprint` `__approveRecord` `__sendMail` `__getFeatureSpecificInfo`.

**Users:** `fetch` `fetchById` `create` `update` `deleteById` — campos `last_name` (snake_case).  
**Org:** `fetch` `enableMultiCurrency` `updateHomeCurrency` `fetchCurrencies` `fetchCurrencyById` `createCurrencies` `updateCurrencies` `updateCurrencyById`.  
**Functions:** `execute(name, parameters?, functions_body?)`.  
**Connections.invoke:** `(connection_name, url, method, param_type, parameters, headers)` — `param_type` 1=query, 2=body; `{}` si no hay params/headers.  
**Settings:** tags, roles, profiles, territories, `fetchAllModules`, `fetchFields`, layouts, related lists, custom views, variables / groups, assignment rules, approval process.

```javascript
var leads = ZDK.Apps.CRM.Leads.fetch(2, 20, "Last_Name", "desc");
leads[0].Last_Name;

var lead = new ZDK.Apps.CRM.Leads.Models.Leads();
lead.Last_Name = "James";
lead.Lead_Status = ZDK.Apps.CRM.Leads.Models.Lead_StatusPicklist.CONTACTED;
ZDK.Apps.CRM.Leads.create(lead);

var fetched = ZDK.Apps.CRM.Leads.fetchById(id);
fetched.Last_Name = "Updated";
fetched.__update();
```

---

## Web API — zoho.crm.*

Índice **aparte**. Sobre todo APIs v2. Acceso con `record.get("Deal_Name")`. Para v8 REST preferir **`zrc`**.

```javascript
var resp = zoho.crm.getRecords("Deals", 1, 50);
var rec = zoho.crm.getRecord("Contacts", recordId);
zoho.crm.searchRecords("Contacts", "(Email:equals:maria@empresa.com)");
zoho.crm.createRecord("Leads", {"Last_Name": "García"});
zoho.crm.updateRecord("Contacts", recordId, {"Phone": "+525512345678"});
zoho.crm.deleteRecord("Tasks", taskId);
```

**Límites:** max 200/página, perfil del usuario, no expone OAuth secrets.

---

## ZRC — `zrc` (minúsculas)

REST unificado: CRM (path relativo, DC auto), Connections (`connection` + URL absoluta), APIs externas (azúcar sobre `fetch`, sin límite de requests). **async/await**. JSON auto. File upload **sí** en ZRC; **no** en `Connections.invoke`.

```javascript
const users = await zrc.get("/crm/v8/users");
const deal = await zrc.get("/crm/v8/Deals/" + id, {params: {fields: "Deal_Name,Stage"}});
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.put("/crm/v8/Leads/" + id, {data: [{Last_Name: "Updated"}]});
await zrc.patch("/crm/v8/settings/fields/" + fieldId + "?module=Leads", {fields: [...]});
await zrc.delete("/crm/v8/Leads/" + id);

const res = await zrc.get("https://sheets.googleapis.com/v4/spreadsheets/" + sid, {connection: "google_sheets"});
const sheetZrc = zrc.createInstance({baseUrl: "https://sheets.googleapis.com/v4", connection: "google_sheets"});
await sheetZrc.get("/spreadsheets/" + sid);

const posts = await zrc.get("https://zylker.com/posts");
```

| Método | Firma |
|--------|-------|
| `get` / `head` / `options` / `delete` | `(path, requestConfig?)` |
| `post` / `put` / `patch` | `(path, body, requestConfig?)` |
| `request` | `({method, path, body, ...})` |
| `createInstance` | `(requestConfig)` — **sin** `body` |

**requestConfig:** `headers`, `connection`, `baseUrl`, `params`, `responseType` (`json` default \| `text` \| `blob` \| `stream` \| `arraybuffer`), `body`/`method`/`path` en `request()`. Body JSON/FormData/URLSearchParams auto.

**Errores** (`try/catch`): `ZrcError`, `ZrcValidationError`, `ApiError` (status ≥ 300), `ConnectionError`.

**Límites:** CRM = usuario logueado + límites API CRM, timeout del browser. Connections: respuesta ≤ **100 MB**, read timeout **40 s**.

```javascript
try {
    const res = await zrc.get("/crm/v8/Leads", {params: {fields: "Last_Name"}});
    console.log(res.status, res.data, res.headers);
} catch (error) {
    if (error.name === "ApiError") { console.error(error.status, error.message); }
}
```

---

## Patrones paste-ready

### Show/hide condicional

```javascript
page.onLoad = function() { toggleCampos(); };
field.Tipo_Cuenta.onChange = function() { toggleCampos(); };

function toggleCampos() {
    var tipo = ZDK.Page.getField("Tipo_Cuenta").getValue();
    var esEmpresa = (tipo === "Empresa");
    ZDK.Page.getField("RFC").setVisibility(esEmpresa);
    ZDK.Page.getField("Razon_Social").setMandatory(esEmpresa);
}
```

### Validación cruzada onSave

```javascript
page.onSave = function() {
    var ini = ZDK.Page.getField("Fecha_Inicio").getValue();
    var fin = ZDK.Page.getField("Fecha_Fin").getValue();
    if(ini && fin && new Date(fin) < new Date(ini)) {
        return {valid: false, message: "Fin debe ser posterior a inicio"};
    }
    return {valid: true};
};
```

### Prefill desde `$Page.navigation_data`

```javascript
page.onLoad = function() {
    var nav = $Page.navigation_data;
    if(nav && nav.campaign_id) {
        ZDK.Page.getField("Campaign_Source").setValue(nav.campaign_id);
    }
};
```

### Duplicados email

```javascript
field.Email.onChange = function(val) {
    if(!val) return;
    var dupes = zoho.crm.searchRecords("Contacts", "(Email:equals:" + val + ")");
    if(dupes.length > 0) {
        ZDK.Client.showMessage("Email ya existe en Contacts", {type: "warning"});
    }
};
```

### Total subform

```javascript
subform.onCellChange = function(rowIdx, field, val) {
    recalcTotal();
};
function recalcTotal() {
    var sf = ZDK.Page.getSubform("Lineas");
    var rows = sf.getValues();
    var total = 0;
    for(var i = 0; i < rows.length; i++) {
        var row = sf.getRow(i);
        var qty = parseFloat(row.getCell("Cantidad").getValue()) || 0;
        var price = parseFloat(row.getCell("Precio").getValue()) || 0;
        total += qty * price;
    }
    ZDK.Page.getField("Total").setValue(total);
}
```

---

## Client Script vs Deluge (server)

| | Client Script | Deluge Function |
|--|---------------|-----------------|
| Runtime | Browser | Servidor |
| Lenguaje | JavaScript | Deluge |
| UI inmediata | ✅ | ❌ |
| Secretos OAuth | ❌ | ✅ |
| Límites | Perfil usuario | Integration limits |

UX → Client Script. Integraciones pesadas/sensibles → Deluge.

---

## Gotchas

| Problema | Fix |
|----------|-----|
| UI congelada | Evitar loops síncronos largos |
| undefined en getValue | Siempre null-check |
| Picklist hardcoded | Usar API names |
| Canvas Detail | Verificar selectores layout |
| CORS en fetch | Usar `zrc` (externa o `{connection}`) o `ZDK.HTTP.request` (máx. 15/página) |
| `ZRC.get(url, params)` | Identificador oficial: `zrc.get(path, {params, connection})` |
| `record.get("Last_Name")` en Apps.CRM | Usar `lead.Last_Name` / Models |
| File upload en Connections.invoke | Usar `zrc.post` + FormData + `{connection}` |
| API name typo | Setup → Fields → API Name |
| `hide()` / `show()` | Usar `setVisibility(bool)` |
| `getRow().getField()` | En fila: `getCell(api)`; columna: `subform.getField(api)` |
| Loader + pop-up | `hideLoader()` antes de alert/confirm/popup |
| Contexto de registro | `$Page.record` / `$Page.record_id`, no `getRecordContext()` |

---

## Troubleshooting

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Script no ejecuta | Evento/página incorrectos | Verificar Module + Page + Event |
| Field not found | API name incorrecto | Revisar API Name en Setup |
| Save no bloquea | onSave no retorna false | return false explícito |
| Web API 403 | Sin permiso perfil | Ajustar perfil o usar Deluge |
| ZRC / ConnectionError | Link name o URL relativa | Setup → Connections; URL absoluta + `{connection}` |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Client Scripts CRM | `docs/en/client-script/crm.md` · `docs/es/client-script/crm.md` |
| Patrones | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |

