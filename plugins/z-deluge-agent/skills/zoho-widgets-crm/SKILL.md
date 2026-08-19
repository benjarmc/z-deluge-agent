---
name: zoho-widgets-crm
description: Creates, reviews, and debugs custom HTML/JS widgets embedded in Zoho CRM. Use for ZohoEmbededAppSDK v1.4/v1.5, PageLoad, ZOHO.CRM.API, ZDK.Client, zrc, ZOHO.CRM.UI, CONNECTION, FUNCTIONS, live.zwidgets.com. / Crea, revisa y depura widgets HTML/JS en Zoho CRM. Usar para SDK 1.4 o 1.5, PageLoad, ZOHO.CRM.*, ZDK.Client, zrc.
---

# Widgets — Zoho CRM

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

Componentes **HTML + JavaScript** en iframe. **No es Client Script** ni widget Creator.

**Referencia:** `docs/en/widgets/crm.md` · `docs/es/widgets/crm.md`  
**SDK:** v**1.4** o v**1.5** (latest). Una sola versión por widget; no mezclar CDNs.

---

## Índices (no mezclar)

| Índice | Identificador |
|--------|----------------|
| Embed | `ZOHO.embeddedApp.on` / `.init()` |
| CRM API widget | `ZOHO.CRM.API.*` (Promises; `Entity` + `RecordID`) |
| UI / nav | `ZOHO.CRM.UI.*` / `BLUEPRINT` / `WIZARD` / `CONFIG` / `META` |
| Connection viejo | `ZOHO.CRM.CONNECTION.invoke` / `CONNECTOR.*` |
| HTTP crudo | `ZOHO.CRM.HTTP.get/post/put/patch/delete` |
| Funciones | `ZOHO.CRM.FUNCTIONS.execute` |
| ZRC (v1.5) | **`zrc.get` / `zrc.post`** (minúsculas) |
| ZDK en widget (v1.5) | `ZDK.Client.showMessage` … + **`sendResponse`** |
| Cerrar popup ZDK | `$Client.close(response)` |
| Client Script | skill `zoho-crm-client-script` — ZDK.Page, `zoho.crm.*` |
| Creator widgets | skill `zoho-widgets-creator` |

Widget `ZDK.Client` **no** incluye `navigateTo` / flyouts / slyteui. Widget `zrc` `responseType`: `json` \| `text` \| `blob` \| `arraybuffer` (**sin** `stream`).

---

## SDK v1.4 / v1.5

```html
<!-- v1.5 latest: ZRC + ZDK.Client + multi-page -->
<script src="https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js"></script>
<!-- v1.4: ZOHO.embeddedApp + ZOHO.CRM.* (sin zrc / ZDK.Client de widget) -->
<script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
```

| | 1.4 | 1.5 |
|--|-----|-----|
| `ZOHO.embeddedApp` + `ZOHO.CRM.API/UI/CONFIG/…` | sí | sí |
| `zrc` | no | sí |
| `ZDK.Client` / `$Client.close` | no | sí |
| React/Angular soft nav | no | sí |

Nuevo widget: **1.5**. Widget 1.4 existente: válido; no uses `zrc` ni `ZDK.Client` ahí.

**Orden:** suscribir eventos **antes** de `init()`.

```javascript
ZOHO.embeddedApp.on("PageLoad", function(data) {
    // Related List: { Entity: "Leads", EntityId: "3000000032096" }
});
ZOHO.embeddedApp.on("DialerActive", function() {});
ZOHO.embeddedApp.on("Dial", function(data) {});
ZOHO.embeddedApp.on("Notify", function(data) {});
ZOHO.embeddedApp.on("NotifyAndWait", function(data) {
    ZDK.Client.sendResponse(data.id, {choice: "mail", value: "example@zoho.com"});
});
ZOHO.embeddedApp.on("ContextUpdate", function(data) {}); // Wizard
ZOHO.embeddedApp.init();
```

v1.5: ZRC, multi-page (React/Angular soft nav), ZDK.Client. v1.4 no los incluye.

CRM **no** requiere `plugin-manifest.json`.

---

## ZOHO.CRM.API

Lista: `getAllRecords` (**no** `getRecords`). `insertRecord`/`updateRecord`/`upsertRecord`: `Trigger` `["workflow"]`\|`["approval"]`\|`["blueprint"]` o `[]` para no ejecutar.

| Método | Config clave |
|--------|----------------|
| `getRecord` | Entity, RecordID, approved? |
| `getAllRecords` | Entity, sort_order, converted, approved, page, per_page |
| `insertRecord` / `updateRecord` / `upsertRecord` | Entity, APIData, Trigger; upsert: `duplicate_check_fields` |
| `deleteRecord` | Entity, RecordID |
| `searchRecord` | Entity, Type `email\|phone\|word\|criteria`, Query, delay?; `(config, page, per_page)` |
| `coql` | `{select_query}` |
| `addNotes` | Entity, RecordID, Title, Content |
| `attachFile` | Entity, RecordID, File: {Name, Content} |
| `getRelatedRecords` / `updateRelatedRecords` / `delinkRelatedRecord` | samples usan **`RelatedList`** |
| `getAllUsers` | Type: AllUsers, ActiveUsers, … |
| `getUser` | ID |
| `getAllProfiles` / `getProfile` / `updateProfile` | |
| `getOrgVariable` | namespace string o `{apiKeys:[]}` |
| `getFile` / `uploadFile` | |
| `getBluePrint` / `updateBluePrint` | Entity, RecordID; BlueprintData |
| `approveRecord` | actionType: approve\|delegate\|resubmit\|reject |
| `getAllActions` / `getApprovalRecords` / `getApprovalById` / `getApprovalsHistory` | |

```javascript
const resp = await ZOHO.CRM.API.getRecord({Entity: "Leads", RecordID: id});
await ZOHO.CRM.API.updateRecord({
    Entity: "Leads",
    APIData: {id: id, Company: "Zylker"},
    Trigger: ["workflow"]
});
```

---

## ZOHO.CRM — otros namespaces

**CONFIG:** `getCurrentUser()` · `getOrgInfo()` · `getUserPreference()`  
**BLUEPRINT:** `proceed()`  
**WIZARD:** `post(record_data)`  
**FUNCTIONS:** `execute(func_name, {arguments: JSON.stringify({...})})`  
**CONNECTION.invoke(conn_name, {url, method, parameters, headers, param_type})** — param_type 1=query, 2=body  
**CONNECTOR:** `authorize(nameSpace)` · `invokeAPI(nameSpace, data)`  
**HTTP:** `{url, params?, headers?, body?}` — get/post/put/patch/delete (auth manual)  
**META:** `getModules()` · `getFields({Entity})` · `getLayouts({Entity, Id?})` · `getCustomViews` · `getRelatedList` · `getAssignmentRules`

**UI.Resize({height, width})** — px  
**UI.Popup:** `close()` · `closeReload()`  
**UI.Record:** `create({Entity})` · `edit({Entity, RecordID})` · `open({Entity, RecordID})` · `populate(RecordData)` — **no** `refresh()`  
**UI.Widget.open({Entity, Message})** — Web Tab  
**UI.Dialer:** `maximize` · `minimize` · `notify`

---

## ZDK.Client (widget v1.5)

Loader activo **bloquea** pop-ups. Markdown toast: `*bold*` (no `**`). `openPopup` type **`widget`** (no slyteui). SDK popup ≥ 1.2.

`getInput` · `showAlert` · `showConfirmation` → boolean · `showMessage` · `showLoader({type, template, message})` · `hideLoader` · `openPopup(config, data)` · **`sendResponse(request_uuid, data)`** (NotifyAndWait).

Cerrar popup abierto con `openPopup`: `$Client.close(response)`.

---

## zrc (widget v1.5)

Igual que Client Script: CRM path `/crm/v8/...`, Connections URL absoluta + `{connection}`, externa `fetch`. File upload sí (vs CONNECTION.invoke). `createInstance` ignora `body`. Errores: `ZrcError`, `ZrcValidationError`, `ApiError`, `ConnectionError`. Connections: ≤ 100 MB / 40 s.

```javascript
const users = await zrc.get("/crm/v8/users");
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});
```

---

## Ejemplo — editar y cerrar

```html
<script src="https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js"></script>
<script>
let RECORD_ID = null;
ZOHO.embeddedApp.on("PageLoad", async function(data) {
    RECORD_ID = Array.isArray(data.EntityId) ? data.EntityId[0] : data.EntityId;
    const resp = await ZOHO.CRM.API.getRecord({Entity: data.Entity, RecordID: RECORD_ID});
    document.getElementById("campo1").value = resp.data[0].Last_Name || "";
});
async function guardar() {
    await ZOHO.CRM.API.updateRecord({
        Entity: "Leads",
        APIData: {id: RECORD_ID, Last_Name: document.getElementById("campo1").value},
        Trigger: ["workflow"]
    });
    ZOHO.CRM.UI.Popup.closeReload();
}
ZOHO.embeddedApp.init();
</script>
```

---

## Gotchas

| Problema | Fix |
|----------|-----|
| PageLoad vacío | `on("PageLoad")` **antes** de `init()` |
| `zrc` / `ZDK.Client` en 1.4 | Subir a **1.5** o usar `ZOHO.CRM.API` / `CONNECTION.invoke` |
| `getRecords` | `getAllRecords` |
| `Record.refresh()` | No está en el índice; `Popup.closeReload()` o `UI.Record.open` |
| CORS fetch | `zrc` o `CONNECTION.invoke` / `FUNCTIONS.execute` |
| File upload Connections | `zrc.post` + FormData, no `CONNECTION.invoke` |
| EntityId array | `Array.isArray(id) ? id[0] : id` |
| RelatedList vs RelatedListName | Samples oficiales: `RelatedList` |

---

## CRM vs Creator

| | CRM | Creator |
|--|-----|---------|
| SDK | ZohoEmbededAppSDK **1.4 o 1.5** | widgetsdk-min.js |
| Manifest | No | plugin-manifest.json |
| Init | `ZOHO.embeddedApp.init()` | `ZOHO.CREATOR.init()` |
| API | `ZOHO.CRM.API` / `zrc` | `ZOHO.CREATOR.API` |
