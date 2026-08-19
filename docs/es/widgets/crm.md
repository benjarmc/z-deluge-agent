# Widgets — Zoho CRM

> Idioma: español. English: see the matching path under `docs/en/`.

Widgets HTML/JS embebidos en Zoho CRM (iframe). **No** es Client Script ni widget Creator. Ver [`creator.md`](creator.md) para Creator.

**SDK:** v**1.4** y v**1.5** (latest). Una sola versión por widget.  
1.5: https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js  
1.4: https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js

Índices **separados:** `ZOHO.embeddedApp` · `ZOHO.CRM.*` · `ZDK.Client` (subset v1.5) · `zrc` · `$Client.close`. No mezclar con Client Script (`ZDK.Page`, `zoho.crm.*`) ni con Creator (`ZOHO.CREATOR`).

---

## SDK v1.4 / v1.5

```html
<script src="https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js"></script>
<!-- o 1.4: https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js -->
```

| | 1.4 | 1.5 |
|--|-----|-----|
| `ZOHO.embeddedApp` + `ZOHO.CRM.API/UI/CONFIG/FUNCTIONS/CONNECTION/META/…` | sí | sí |
| `zrc` | no | sí |
| `ZDK.Client` / `$Client.close` / `sendResponse` | no | sí |
| Multi-página (React/Angular, soft nav) | no | sí |

Nuevo: **1.5**. Widget 1.4 existente: válido; no uses `zrc` ni `ZDK.Client`. No mezclar ambos CDNs. CRM no requiere `plugin-manifest.json`.

**Orden:** registrar listeners **antes** de `init()`.

```javascript
ZOHO.embeddedApp.on("PageLoad", function(data) {
    console.log(data);
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

| Evento | Cuándo |
|--------|--------|
| `PageLoad` | Carga de página/entidad. Suscribir **antes** de init |
| `DialerActive` | Toggle de softphone |
| `Dial` | Clic en icono de llamada |
| `Notify` | Flyout Client Script (async) |
| `NotifyAndWait` | Flyout síncrono → `ZDK.Client.sendResponse` |
| `ContextUpdate` | Formulario Wizard modificado |

---

## ZOHO.CRM.API

Promises. Módulo = `Entity` (API name). Lista = **`getAllRecords`** (no `getRecords`). Trigger: `"workflow"` \| `"approval"` \| `"blueprint"`; `[]` no ejecuta workflows.

| Método | Notas |
|--------|-------|
| `getRecord({Entity, RecordID, approved?})` | `approved` default true |
| `getAllRecords({Entity, sort_order, converted, approved, page, per_page})` | sort_order `asc`\|`desc` |
| `insertRecord` / `updateRecord` / `upsertRecord` | APIData; upsert: `duplicate_check_fields` |
| `deleteRecord({Entity, RecordID})` | |
| `searchRecord({Entity, Type, Query, delay?}, page?, per_page?)` | Type: email\|phone\|word\|criteria |
| `coql({select_query})` | |
| `addNotes({Entity, RecordID, Title, Content})` | |
| `attachFile({Entity, RecordID, File:{Name, Content}})` | Content = blob |
| `getRelatedRecords` / `updateRelatedRecords` / `delinkRelatedRecord` | Samples: **`RelatedList`** (tabla a veces dice RelatedListName) |
| `getAllUsers({Type, page?, per_page?})` | AllUsers, ActiveUsers, DeactiveUsers, ConfirmedUsers, NotConfirmedUsers, DeletedUsers, ActiveConfirmedUsers, AdminUsers, ActiveConfirmedAdmins |
| `getUser({ID})` | |
| `getAllProfiles()` / `getProfile({ID})` / `updateProfile({ID, APIData})` | |
| `getOrgVariable(ns)` o `{apiKeys:[]}` | |
| `getFile({id})` / `uploadFile(config)` | multipart |
| `getBluePrint` / `updateBluePrint` | BlueprintData.blueprint[].transition_id |
| `approveRecord({Entity, RecordID, actionType, comments?, user?})` | approve\|delegate\|resubmit\|reject; `user` solo delegate |
| `getAllActions` / `getApprovalRecords({type?})` / `getApprovalById` / `getApprovalsHistory` | type: awaiting \| others_awaiting |

```javascript
const resp = await ZOHO.CRM.API.getAllRecords({Entity: "Leads", sort_order: "asc", page: 1, per_page: 2});
await ZOHO.CRM.API.insertRecord({
    Entity: "Leads",
    APIData: {Company: "Zylker", Last_Name: "Peterson"},
    Trigger: ["workflow"]
});
await ZOHO.CRM.API.searchRecord({Entity: "Leads", Type: "criteria", Query: "(Company:equals:Zoho)"});
await ZOHO.CRM.API.coql({select_query: "select Last_Name from Contacts where Last_Name = 'Boyle' limit 2"});
```

`attachFile` description oficial dice “delink” — el método **adjunta** un archivo.

---

## ZOHO.CRM.CONFIG / BLUEPRINT / WIZARD / FUNCTIONS

```javascript
await ZOHO.CRM.CONFIG.getCurrentUser();
await ZOHO.CRM.CONFIG.getOrgInfo();
await ZOHO.CRM.CONFIG.getUserPreference(); // { mode: "day" }

await ZOHO.CRM.BLUEPRINT.proceed();
ZOHO.CRM.WIZARD.post({"field_api_name1": "field_value"});

await ZOHO.CRM.FUNCTIONS.execute("custom_function4", {
    arguments: JSON.stringify({mailid: "user@example.com"})
});
// details.output
```

---

## CONNECTION / CONNECTOR / HTTP / META

**CONNECTION.invoke** — índice aparte de `zrc`:

```javascript
await ZOHO.CRM.CONNECTION.invoke("mailchimp4", {
    url: "http://mailchimp.api/sample_api",
    method: "POST",
    param_type: 1, // 1 = query, 2 = body
    parameters: {param1: "paramvalue1"},
    headers: {header1: "headervalue1"}
});
```

Sin file upload (usar `zrc`).

**CONNECTOR:** `authorize(nameSpace)` · `invokeAPI(nameSpace, data)` (`VARIABLES`, `CONTENT_TYPE: "multipart"`, `PARTS`, `FILE`).

**HTTP** (`url` + `params`/`headers`/`body`; Authorization manual): `get` · `post` · `put` · `patch` · `delete`. Preferir `zrc` para same-org.

**META:** `getModules()` · `getFields({Entity})` · `getLayouts({Entity, Id?})` (sample 2 usa `LayoutId`) · `getCustomViews({Entity, Id?})` · `getRelatedList({Entity})` · `getAssignmentRules({Entity})`.

---

## ZOHO.CRM.UI

| API | Métodos |
|-----|---------|
| `UI.Resize({height, width})` | px; Promise true\|false |
| `UI.Popup` | `close()` · `closeReload()` |
| `UI.Record` | `create({Entity})` · `edit({Entity, RecordID})` · `open({Entity, RecordID})` · `populate(RecordData)` — **no** `refresh()` |
| `UI.Widget.open({Entity, Message})` | Web Tab + PageLoad data |
| `UI.Dialer` | `maximize()` · `minimize()` · `notify()` |

---

## ZDK.Client (widget v1.5)

Subset del Client Script. Loader activo **bloquea** pop-ups. Markdown: `*bold*`, `_italics_`, `__underline__`. `showMessage` **sin** imágenes. `openPopup`: `type: "widget"` (no slyteui). Popup SDK ≥ 1.2.

| Método | Notas |
|--------|-------|
| `getInput(options[], heading?, accept?, reject?)` | Máx. 7; text/number/textarea/picklist/multiselectpicklist |
| `showAlert` / `showConfirmation` | confirm → boolean |
| `showMessage(msg, {type})` | info\|error\|warning\|success |
| `showLoader({type, template, message})` / `hideLoader()` | message ≤ 240 |
| `openPopup(config, data?)` | await `$Client.close()` |
| `sendResponse(request_uuid, data)` | NotifyAndWait (`data.id`) |

Cerrar: `$Client.close({choice: "mail", value: "example@zoho.com"})`.

---

## zrc (widget v1.5)

REST unificado: CRM (`/crm/v8/...`, DC auto), Connections (URL absoluta + `connection`), externa (azúcar sobre `fetch`). File upload **sí**. `createInstance` ignora `body`.

`get/post/put/patch/delete/head/options/request/createInstance`. requestConfig: `headers`, `connection`, `baseUrl`, `params`, `responseType` (`json`\|`text`\|`blob`\|`arraybuffer` — **sin** `stream` en este índice).

Errores: `ZrcError`, `ZrcValidationError`, `ApiError`, `ConnectionError`. CRM: usuario logueado + límites API + timeout browser. Connections: ≤ **100 MB**, **40 s**.

```javascript
const users = await zrc.get("/crm/v8/users");
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});
const formData = new FormData();
formData.append("file", fileBlob, "resume.pdf");
await zrc.post("/crm/v8/Leads/" + id + "/Attachments", formData);
```

---

## Instalación

Setup → Developer Space → Widgets → Create Widget → pegar HTML o subir archivo → ubicación (Detail, List, …) → módulos y perfiles.

Estructura típica: `app/widget.html` + JS/CSS. Sin manifest.

---
