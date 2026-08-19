# Widgets — Zoho CRM

> Language: English. Spanish: see the matching path under `docs/es/`.

HTML/JS widgets embedded in Zoho CRM (iframe). **Not** Client Script and **not** a Creator widget. See [`creator.md`](creator.md) for Creator.

**SDK:** v**1.4** and v**1.5** (latest). One version per widget.  
1.5: https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js  
1.4: https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js

**Separate** indexes: `ZOHO.embeddedApp` · `ZOHO.CRM.*` · `ZDK.Client` (v1.5 subset) · `zrc` · `$Client.close`. Do not mix with Client Script (`ZDK.Page`, `zoho.crm.*`) or Creator (`ZOHO.CREATOR`).

---

## SDK v1.4 / v1.5

```html
<script src="https://live.zwidgets.com/js-sdk/1.5/ZohoEmbededAppSDK.min.js"></script>
<!-- or 1.4: https://live.zwidgets.com/js-sdk/1.4/ZohoEmbededAppSDK.min.js -->
```

| | 1.4 | 1.5 |
|--|-----|-----|
| `ZOHO.embeddedApp` + `ZOHO.CRM.API/UI/CONFIG/FUNCTIONS/CONNECTION/META/…` | yes | yes |
| `zrc` | no | yes |
| `ZDK.Client` / `$Client.close` / `sendResponse` | no | yes |
| Multi-page (React/Angular, soft nav) | no | yes |

New: **1.5**. Existing 1.4 widget: valid; do not use `zrc` or `ZDK.Client`. Do not mix both CDNs. CRM does not require `plugin-manifest.json`.

**Order:** register listeners **before** `init()`.

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

| Event | When |
|--------|--------|
| `PageLoad` | Entity page load. Subscribe **before** init |
| `DialerActive` | Softphone window toggled |
| `Dial` | Call icon clicked |
| `Notify` | Client Script flyout (async) |
| `NotifyAndWait` | Sync flyout → `ZDK.Client.sendResponse` |
| `ContextUpdate` | Wizard form changed |

---

## ZOHO.CRM.API

Promises. Module = `Entity` (API name). List = **`getAllRecords`** (not `getRecords`). Trigger: `"workflow"` \| `"approval"` \| `"blueprint"`; `[]` skips workflows.

| Method | Notes |
|--------|-------|
| `getRecord({Entity, RecordID, approved?})` | `approved` default true |
| `getAllRecords({Entity, sort_order, converted, approved, page, per_page})` | sort_order `asc`\|`desc` |
| `insertRecord` / `updateRecord` / `upsertRecord` | APIData; upsert: `duplicate_check_fields` |
| `deleteRecord({Entity, RecordID})` | |
| `searchRecord({Entity, Type, Query, delay?}, page?, per_page?)` | Type: email\|phone\|word\|criteria |
| `coql({select_query})` | |
| `addNotes({Entity, RecordID, Title, Content})` | |
| `attachFile({Entity, RecordID, File:{Name, Content}})` | Content = blob |
| `getRelatedRecords` / `updateRelatedRecords` / `delinkRelatedRecord` | Samples: **`RelatedList`** (table sometimes says RelatedListName) |
| `getAllUsers({Type, page?, per_page?})` | AllUsers, ActiveUsers, DeactiveUsers, ConfirmedUsers, NotConfirmedUsers, DeletedUsers, ActiveConfirmedUsers, AdminUsers, ActiveConfirmedAdmins |
| `getUser({ID})` | |
| `getAllProfiles()` / `getProfile({ID})` / `updateProfile({ID, APIData})` | |
| `getOrgVariable(ns)` or `{apiKeys:[]}` | |
| `getFile({id})` / `uploadFile(config)` | multipart |
| `getBluePrint` / `updateBluePrint` | BlueprintData.blueprint[].transition_id |
| `approveRecord({Entity, RecordID, actionType, comments?, user?})` | approve\|delegate\|resubmit\|reject; `user` only for delegate |
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

The official `attachFile` description says “delink” — the method **uploads** an attachment.

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

**CONNECTION.invoke** — a separate index from `zrc`:

```javascript
await ZOHO.CRM.CONNECTION.invoke("mailchimp4", {
    url: "http://mailchimp.api/sample_api",
    method: "POST",
    param_type: 1, // 1 = query, 2 = body
    parameters: {param1: "paramvalue1"},
    headers: {header1: "headervalue1"}
});
```

No file upload (use `zrc`).

**CONNECTOR:** `authorize(nameSpace)` · `invokeAPI(nameSpace, data)` (`VARIABLES`, `CONTENT_TYPE: "multipart"`, `PARTS`, `FILE`).

**HTTP** (`url` + `params`/`headers`/`body`; manual Authorization): `get` · `post` · `put` · `patch` · `delete`. Prefer `zrc` for same-org.

**META:** `getModules()` · `getFields({Entity})` · `getLayouts({Entity, Id?})` (sample 2 uses `LayoutId`) · `getCustomViews({Entity, Id?})` · `getRelatedList({Entity})` · `getAssignmentRules({Entity})`.

---

## ZOHO.CRM.UI

| API | Methods |
|-----|---------|
| `UI.Resize({height, width})` | px; Promise true\|false |
| `UI.Popup` | `close()` · `closeReload()` |
| `UI.Record` | `create({Entity})` · `edit({Entity, RecordID})` · `open({Entity, RecordID})` · `populate(RecordData)` — **no** `refresh()` |
| `UI.Widget.open({Entity, Message})` | Web Tab + PageLoad data |
| `UI.Dialer` | `maximize()` · `minimize()` · `notify()` |

---

## ZDK.Client (widget v1.5)

Subset of Client Script. An active loader **blocks** other pop-ups. Markdown: `*bold*`, `_italics_`, `__underline__`. `showMessage` has **no** images. `openPopup`: `type: "widget"` (not slyteui). Popup SDK ≥ 1.2.

| Method | Notes |
|--------|-------|
| `getInput(options[], heading?, accept?, reject?)` | Max 7; text/number/textarea/picklist/multiselectpicklist |
| `showAlert` / `showConfirmation` | confirm → boolean |
| `showMessage(msg, {type})` | info\|error\|warning\|success |
| `showLoader({type, template, message})` / `hideLoader()` | message ≤ 240 |
| `openPopup(config, data?)` | await `$Client.close()` |
| `sendResponse(request_uuid, data)` | NotifyAndWait (`data.id`) |

Close: `$Client.close({choice: "mail", value: "example@zoho.com"})`.

---

## zrc (widget v1.5)

Unified REST: CRM (`/crm/v8/...`, DC auto), Connections (absolute URL + `connection`), external (sugar over `fetch`). File upload **yes**. `createInstance` ignores `body`.

`get/post/put/patch/delete/head/options/request/createInstance`. requestConfig: `headers`, `connection`, `baseUrl`, `params`, `responseType` (`json`\|`text`\|`blob`\|`arraybuffer` — **no** `stream` in this index).

Errors: `ZrcError`, `ZrcValidationError`, `ApiError`, `ConnectionError`. CRM: logged-in user + API limits + browser timeout. Connections: ≤ **100 MB**, **40 s**.

```javascript
const users = await zrc.get("/crm/v8/users");
await zrc.post("/crm/v8/Leads", {data: [{Last_Name: "Doe", Company: "Zylker"}]});
await zrc.get("https://desk.zoho.com/api/v1/tickets", {connection: "zoho_desk"});
const formData = new FormData();
formData.append("file", fileBlob, "resume.pdf");
await zrc.post("/crm/v8/Leads/" + id + "/Attachments", formData);
```

---

## Installation

Setup → Developer Space → Widgets → Create Widget → paste HTML or upload a file → location (Detail, List, …) → modules and profiles.

Typical layout: `app/widget.html` + JS/CSS. No manifest.

---
