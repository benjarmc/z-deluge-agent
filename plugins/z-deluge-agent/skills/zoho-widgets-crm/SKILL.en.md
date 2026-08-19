---
name: zoho-widgets-crm
description: Creates, reviews, and debugs custom HTML/JS widgets embedded in Zoho CRM. Use for ZohoEmbededAppSDK v1.4/v1.5, PageLoad, ZOHO.CRM.API, ZDK.Client, zrc, ZOHO.CRM.UI, CONNECTION, FUNCTIONS, live.zwidgets.com. / Crea, revisa y depura widgets HTML/JS en Zoho CRM. Usar para SDK 1.4 o 1.5, PageLoad, ZOHO.CRM.*, ZDK.Client, zrc.
---

# Widgets — Zoho CRM

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

**HTML + JavaScript** components in an iframe. **Not** Client Script and **not** a Creator widget.

**Reference:** `docs/en/widgets/crm.md` · `docs/es/widgets/crm.md`  
**SDK:** v**1.4** or v**1.5** (latest). One version per widget; do not mix CDNs.

---

## Indexes (do not mix)

| Index | Identifier |
|--------|----------------|
| Embed | `ZOHO.embeddedApp.on` / `.init()` |
| Widget CRM API | `ZOHO.CRM.API.*` (Promises; `Entity` + `RecordID`) |
| UI / nav | `ZOHO.CRM.UI.*` / `BLUEPRINT` / `WIZARD` / `CONFIG` / `META` |
| Legacy connection | `ZOHO.CRM.CONNECTION.invoke` / `CONNECTOR.*` |
| Raw HTTP | `ZOHO.CRM.HTTP.get/post/put/patch/delete` |
| Functions | `ZOHO.CRM.FUNCTIONS.execute` |
| ZRC (v1.5) | **`zrc.get` / `zrc.post`** (lowercase) |
| Widget ZDK (v1.5) | `ZDK.Client.showMessage` … + **`sendResponse`** |
| Close ZDK popup | `$Client.close(response)` |
| Client Script | skill `zoho-crm-client-script` — ZDK.Page, `zoho.crm.*` |
| Creator widgets | skill `zoho-widgets-creator` |

Widget `ZDK.Client` does **not** include `navigateTo` / flyouts / slyteui. Widget `zrc` `responseType`: `json` \| `text` \| `blob` \| `arraybuffer` (**no** `stream`).

---

## SDK v1.4 / v1.5

```html
<!-- v1.5 latest: ZRC + ZDK.Client + multi-page -->
<script src="https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js"></script>
<!-- v1.4: ZOHO.embeddedApp + ZOHO.CRM.* (no widget zrc / ZDK.Client) -->
<script src="https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js"></script>
```

| | 1.4 | 1.5 |
|--|-----|-----|
| `ZOHO.embeddedApp` + `ZOHO.CRM.API/UI/CONFIG/…` | yes | yes |
| `zrc` | no | yes |
| `ZDK.Client` / `$Client.close` | no | yes |
| React/Angular soft nav | no | yes |

New widget: **1.5**. Existing 1.4 widget: valid; do not use `zrc` or `ZDK.Client` there.

**Order:** subscribe to events **before** `init()`.

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

v1.5: ZRC, multi-page (React/Angular soft nav), ZDK.Client. v1.4 does not include them.

CRM does **not** require `plugin-manifest.json`.

---

## ZOHO.CRM.API

List method: `getAllRecords` (**not** `getRecords`). `insertRecord`/`updateRecord`/`upsertRecord`: `Trigger` `["workflow"]`\|`["approval"]`\|`["blueprint"]` or `[]` to skip.

| Method | Key config |
|--------|----------------|
| `getRecord` | Entity, RecordID, approved? |
| `getAllRecords` | Entity, sort_order, converted, approved, page, per_page |
| `insertRecord` / `updateRecord` / `upsertRecord` | Entity, APIData, Trigger; upsert: `duplicate_check_fields` |
| `deleteRecord` | Entity, RecordID |
| `searchRecord` | Entity, Type `email\|phone\|word\|criteria`, Query, delay?; `(config, page, per_page)` |
| `coql` | `{select_query}` |
| `addNotes` | Entity, RecordID, Title, Content |
| `attachFile` | Entity, RecordID, File: {Name, Content} |
| `getRelatedRecords` / `updateRelatedRecords` / `delinkRelatedRecord` | samples use **`RelatedList`** |
| `getAllUsers` | Type: AllUsers, ActiveUsers, … |
| `getUser` | ID |
| `getAllProfiles` / `getProfile` / `updateProfile` | |
| `getOrgVariable` | namespace string or `{apiKeys:[]}` |
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

## ZOHO.CRM — other namespaces

**CONFIG:** `getCurrentUser()` · `getOrgInfo()` · `getUserPreference()`  
**BLUEPRINT:** `proceed()`  
**WIZARD:** `post(record_data)`  
**FUNCTIONS:** `execute(func_name, {arguments: JSON.stringify({...})})`  
**CONNECTION.invoke(conn_name, {url, method, parameters, headers, param_type})** — param_type 1=query, 2=body  
**CONNECTOR:** `authorize(nameSpace)` · `invokeAPI(nameSpace, data)`  
**HTTP:** `{url, params?, headers?, body?}` — get/post/put/patch/delete (manual auth)  
**META:** `getModules()` · `getFields({Entity})` · `getLayouts({Entity, Id?})` · `getCustomViews` · `getRelatedList` · `getAssignmentRules`

**UI.Resize({height, width})** — px  
**UI.Popup:** `close()` · `closeReload()`  
**UI.Record:** `create({Entity})` · `edit({Entity, RecordID})` · `open({Entity, RecordID})` · `populate(RecordData)` — **no** `refresh()`  
**UI.Widget.open({Entity, Message})** — Web Tab  
**UI.Dialer:** `maximize` · `minimize` · `notify`

---

## ZDK.Client (widget v1.5)

An active loader **blocks** other pop-ups. Toast markdown: `*bold*` (not `**`). `openPopup` type **`widget`** (not slyteui). Popup SDK ≥ 1.2.

`getInput` · `showAlert` · `showConfirmation` → boolean · `showMessage` · `showLoader({type, template, message})` · `hideLoader` · `openPopup(config, data)` · **`sendResponse(request_uuid, data)`** (NotifyAndWait).

Close a popup opened with `openPopup`: `$Client.close(response)`.

---

## zrc (widget v1.5)

Same pattern as Client Script: CRM path `/crm/v8/...`, Connections absolute URL + `{connection}`, external `fetch`. File upload yes (vs CONNECTION.invoke). `createInstance` ignores `body`. Errors: `ZrcError`, `ZrcValidationError`, `ApiError`, `ConnectionError`. Connections: ≤ 100 MB / 40 s.

```javascript
const users = await zrc.get("/crm/v8/users");
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});
```

---

## Example — edit and close

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

| Problem | Fix |
|----------|-----|
| Empty PageLoad | `on("PageLoad")` **before** `init()` |
| `zrc` / `ZDK.Client` on 1.4 | Upgrade to **1.5** or use `ZOHO.CRM.API` / `CONNECTION.invoke` |
| `getRecords` | `getAllRecords` |
| `Record.refresh()` | Not in the index; `Popup.closeReload()` or `UI.Record.open` |
| CORS fetch | `zrc` or `CONNECTION.invoke` / `FUNCTIONS.execute` |
| File upload on Connections | `zrc.post` + FormData, not `CONNECTION.invoke` |
| EntityId array | `Array.isArray(id) ? id[0] : id` |
| RelatedList vs RelatedListName | Official samples: `RelatedList` |

---

## CRM vs Creator

| | CRM | Creator |
|--|-----|---------|
| SDK | ZohoEmbededAppSDK **1.4 or 1.5** | widgetsdk-min.js |
| Manifest | No | plugin-manifest.json |
| Init | `ZOHO.embeddedApp.init()` | `ZOHO.CREATOR.init()` |
| API | `ZOHO.CRM.API` / `zrc` | `ZOHO.CREATOR.API` |
