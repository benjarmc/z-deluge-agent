---
name: zoho-crm-client-script
description: Writes, reviews, and debugs Zoho CRM Client Scripts in client-side JavaScript. Use for page.onLoad, ZDK.Page/Client/HTTP/UI, ZDK.Apps.CRM, zrc, zoho.crm.* Web API, $Page/$Crm/$Client, Canvas, Wizard, zohocrm.dev. / Escribe, revisa y depura Client Scripts de Zoho CRM. Usar para ZDK, ZDK.Apps.CRM, zrc, Web API, Canvas, Wizard.
---

# Client Scripts — Zoho CRM (JavaScript)

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

**JavaScript** scripts in the browser. **Not Deluge.**

**Full reference:** `docs/en/client-script/crm.md` · `docs/es/client-script/crm.md`  
**Official docs:** https://www.zohocrm.dev/explore/client-script/

---

## Scope

- Pages: Create, Edit, Clone, Detail (Standard + Canvas), List (Standard), Wizard
- Setup: Developer Hub → Client Script → +New Script → Module + Page + Event
- Runtime: user's browser — respects their profile permissions

---

## Event Dictionary — Page Events

### page.onLoad
Fires when Create/Edit/Clone/Detail loads.

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
Validation before save. Returning `false` blocks save.

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
Fires when any field changes.

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

### field.onBeforeSave (field validation)

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

Index: https://www.zohocrm.dev/explore/client-script/clientapi/  
`getField(api_name)` — API name, not label.

| Method | Returns |
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

Components: `list-custom-view`, `list-view-type`, `canvas-list-view`, `record-create-wizard`, `notes-editor`.

Page context is **`$Page`**, not `getRecordContext()` — `$Page.record`, `$Page.record_id`, `$Page.module`, `$Page.layout_id`, `$Page.name`, `$Page.navigation_data`, `$Page.parent_record_id`.

---

## ZDK.Client

An active loader **blocks** other pop-ups. Toast markdown: `*bold*`, `_italics_`, `__underline__`, `~strike~`, `` `code` ``, `#` / `###`, `!blockquote`, `[text](url)`. **No** images in `showMessage`.

| Method | Notes |
|--------|-------|
| `showMessage(msg, {type})` | type: `info` (default), `error`, `warning`, `success` |
| `showAlert(msg, heading?, accept?)` | accept default `Okay`; markdown + `![alt](url)` |
| `showConfirmation(msg, accept?, reject?)` | → `true`/`false`; defaults Yes, Proceed / Cancel |
| `getInput(options[], heading?, accept?, reject?)` | max 7 inputs: text/number/textarea/picklist/multiselectpicklist |
| `openMailer(config)` | from, to[], cc[], subject, body, templateId |
| `navigateTo(page, params, data?, target?)` | page: `tab`, `record_detail`, `record_create`, `record_clone`, `record_edit`, `record_list`; target `_self`/`_blank`; data → `$Page.navigation_data` |
| `openPopup(config, data)` / `openCallout(config, target, data)` | widget \| slyteui; SDK ≥ 1.2 |
| `createFlyout(name, config)` / `getFlyout(name)` | then `.open()` / `.notify()` / `.close()` |
| `showLoader({type, template, message})` / `hideLoader()` | template: `standard`, `spinner`, `vertical-bar`; message ≤ 240 |

```javascript
ZDK.Client.showMessage("This is an *important* warning.", {type: "warning"});
ZDK.Client.showLoader({type: "page", template: "vertical-bar", message: "Loading ..."});
ZDK.Client.hideLoader();
ZDK.Client.navigateTo("record_detail", {module: "Leads", record_id: id});
```

---

## ZDK.HTTP / ZDK.UI

`ZDK.HTTP.request({url, method?, content?, headers?, parameters?})` — max **15** requests/page. Methods: GET (default), POST, PUT, DELETE, PATCH. Response: `.getResponse()`, `.getHeaders()`, `.getStatusCode()`.

`ZDK.UI.freeze(bool)` · `scrollTo(element_id)` · `highlight(id, {time})` · `getElementByID(id)` → UIElement (Canvas/Wizard).

---

## Field / Form / Subform objects

**Field:** `getApiName`, `getValue`/`setValue`, `setMandatory`, `setMaxLength`, `setReadOnly`, `setVisibility` (**not** `hide`/`show`), `showError`, `mask({character, length, reverse})`, `addStyle`, `addInfo`, `addTooltip`, `setCriteria`, `getPicklistOptions`.  
Lookup `setValue({id, name})`. Checkbox boolean. Subform-as-field: array of maps.

**Form:** `getValues()` / `setValues(obj)`.

**Subform:** `getApiName`, `getField(api)` → SubformField, `getValues`/`setValues`, `getRow(i)` → SubformRow (0-based), `insertRows(rows, index?)` (append if no index; **not** `addRow`), `deleteRow(i)`, `clear()`, `setMandatory`, `addInfo`.  
**SubformRow:** `getValues`/`setValues`, `getCell(api)` → SubformCell, `setReadOnly`. **No** `getField` on the row.  
**SubformCell:** `getValue`/`setValue`, `setReadOnly`, `showError`.

**List:** `freezeColumns`, `maskField`, `sortByField(api, "asc"|"desc"|"unsort")`, `getRecords`, `selectRecords(criteria)`, `selectRecordsByID(ids)`, `clearSelection`, `style(config, criteria)`.

**Button:** `getId`/`getType`/`getLabel`/`getApiName`, `click`, `disable`/`enable`, `setLabel`.  
**Link / BlueprintTransition:** `click`/`disable`/`enable`; transition `initiate()`.

---

## $Crm / $Client / $Page

`$Crm.user` · `$Crm.environment` (`production`/`sandbox`) · `$Crm.deployment` · `$Crm.org`  
`$Client.userAgent` · `$Client.isOnline` · `$Client.platform` · `$Client.url` · `$Client.geoLocation` · `$Client.selectedValue` · `$Client.refresh({triggerOnLoad?})` · `$Client.openURL(url)` · `$Client.copyToClipboard(text)` · `$Client.rootContext` (Quick Create → parent)

---

## HTTP / CRM indexes (do not mix)

| Index | Identifier | Notes |
|--------|---------------|-------|
| Client API UI | `ZDK.Page` / `ZDK.Client` | Fields, toast, navigate |
| AJAX | `ZDK.HTTP.request` | Max **15**/page |
| Typed CRM SDK | `ZDK.Apps.CRM.<Module>` | Models, `.Last_Name`, `__update()` |
| Web API | `zoho.crm.getRecords` | Mostly v2; `record.get("Field")` |
| ZRC | **`zrc.get` / `zrc.post`** (lowercase) | Any CRM version (`/crm/v8/...`); `await`; **not** `ZRC.get(url, params, headers)` |
| Legacy connections | `ZDK.Apps.CRM.Connections.invoke` | No file upload |
| ZRC connections | `zrc.get(absUrl, {connection})` | File upload/download |

---

## ZDK.Apps.CRM — Modules

`ZDK.Apps.CRM.Leads.fetch()` (module is the API name). Create: `new ZDK.Apps.CRM.Leads.Models.Leads()`. Picklist: `ZDK.Apps.CRM.Leads.Models.Lead_StatusPicklist.CONTACTED`. Inventory: `Invoices.Models.Line_Items`. Lookup: `lead.Owner()` fires an API call; `lead.Owner_Lookup_Id` is the id only.

| Method | Signature |
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
| `removeTagsToRecord` / `removeTagsToRecords` | official identifier is **To**, not From |
| `sendMail` | `(mail, record_id)` — `new ZDK.Apps.CRM.__Actions.Models.Mail()` |
| `approveRecord` | `(record_id, "approve"\|"delegate"\|"reject", comment)` |
| `getFeatureInfo` | `(ids[], features[])` — max 100 ids, 5 features |

**Record instance:** `__create` `__update` `__remove` `__convert` `__addTags` `__removeTags` `__fetchRelatedRecords` `__delinkRelatedRecords` `__updateRelatedRecords` `__fetchBlueprint` `__approveRecord` `__sendMail` `__getFeatureSpecificInfo`.

**Users:** `fetch` `fetchById` `create` `update` `deleteById` — fields `last_name` (snake_case).  
**Org:** `fetch` `enableMultiCurrency` `updateHomeCurrency` `fetchCurrencies` `fetchCurrencyById` `createCurrencies` `updateCurrencies` `updateCurrencyById`.  
**Functions:** `execute(name, parameters?, functions_body?)`.  
**Connections.invoke:** `(connection_name, url, method, param_type, parameters, headers)` — `param_type` 1=query, 2=body; `{}` if unused.  
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

A **separate** index. Mostly v2 APIs. Access with `record.get("Deal_Name")`. For v8 REST prefer **`zrc`**.

```javascript
var resp = zoho.crm.getRecords("Deals", 1, 50);
var rec = zoho.crm.getRecord("Contacts", recordId);
zoho.crm.searchRecords("Contacts", "(Email:equals:maria@empresa.com)");
zoho.crm.createRecord("Leads", {"Last_Name": "García"});
zoho.crm.updateRecord("Contacts", recordId, {"Phone": "+525512345678"});
zoho.crm.deleteRecord("Tasks", taskId);
```

**Limits:** max 200/page, user profile, does not expose OAuth secrets.

---

## ZRC — `zrc` (lowercase)

Unified REST: CRM (relative path, DC auto), Connections (`connection` + absolute URL), external APIs (sugar over `fetch`, no request cap). **async/await**. Auto JSON. File upload **yes** in ZRC; **no** in `Connections.invoke`.

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

| Method | Signature |
|--------|-------|
| `get` / `head` / `options` / `delete` | `(path, requestConfig?)` |
| `post` / `put` / `patch` | `(path, body, requestConfig?)` |
| `request` | `({method, path, body, ...})` |
| `createInstance` | `(requestConfig)` — **no** `body` |

**requestConfig:** `headers`, `connection`, `baseUrl`, `params`, `responseType` (`json` default \| `text` \| `blob` \| `stream` \| `arraybuffer`), `body`/`method`/`path` on `request()`. JSON/FormData/URLSearchParams body auto.

**Errors** (`try/catch`): `ZrcError`, `ZrcValidationError`, `ApiError` (status ≥ 300), `ConnectionError`.

**Limits:** CRM = logged-in user + CRM API limits, browser timeout. Connections: response ≤ **100 MB**, **40 s** read timeout.

```javascript
try {
    const res = await zrc.get("/crm/v8/Leads", {params: {fields: "Last_Name"}});
    console.log(res.status, res.data, res.headers);
} catch (error) {
    if (error.name === "ApiError") { console.error(error.status, error.message); }
}
```

---

## Paste-ready patterns

### Conditional show/hide

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

### Cross-field validation onSave

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

### Prefill from `$Page.navigation_data`

```javascript
page.onLoad = function() {
    var nav = $Page.navigation_data;
    if(nav && nav.campaign_id) {
        ZDK.Page.getField("Campaign_Source").setValue(nav.campaign_id);
    }
};
```

### Email duplicates

```javascript
field.Email.onChange = function(val) {
    if(!val) return;
    var dupes = zoho.crm.searchRecords("Contacts", "(Email:equals:" + val + ")");
    if(dupes.length > 0) {
        ZDK.Client.showMessage("Email ya existe en Contacts", {type: "warning"});
    }
};
```

### Subform total

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
| Runtime | Browser | Server |
| Language | JavaScript | Deluge |
| Immediate UI | ✅ | ❌ |
| OAuth secrets | ❌ | ✅ |
| Limits | User profile | Integration limits |

UX → Client Script. Heavy/sensitive integrations → Deluge.

---

## Gotchas

| Problem | Fix |
|----------|-----|
| Frozen UI | Avoid long synchronous loops |
| undefined in getValue | Always null-check |
| Hardcoded picklist | Use API names |
| Canvas Detail | Verify layout selectors |
| CORS on fetch | Use `zrc` (external or `{connection}`) or `ZDK.HTTP.request` (max 15/page) |
| `ZRC.get(url, params)` | Official identifier: `zrc.get(path, {params, connection})` |
| `record.get("Last_Name")` on Apps.CRM | Use `lead.Last_Name` / Models |
| File upload on Connections.invoke | Use `zrc.post` + FormData + `{connection}` |
| API name typo | Setup → Fields → API Name |
| `hide()` / `show()` | Use `setVisibility(bool)` |
| `getRow().getField()` | On a row: `getCell(api)`; column-level: `subform.getField(api)` |
| Loader + pop-up | `hideLoader()` before alert/confirm/popup |
| Record context | `$Page.record` / `$Page.record_id`, not `getRecordContext()` |

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Script does not run | Wrong event/page | Verify Module + Page + Event |
| Field not found | Incorrect API name | Check API Name in Setup |
| Save is not blocked | onSave does not return false | return false explicitly |
| Web API 403 | No profile permission | Adjust profile or use Deluge |
| ZRC / ConnectionError | Wrong link name or relative URL | Setup → Connections; absolute URL + `{connection}` |

---

## Full reference

| Topic | File |
|------|---------|
| CRM Client Scripts | `docs/en/client-script/crm.md` · `docs/es/client-script/crm.md` |
| Patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
