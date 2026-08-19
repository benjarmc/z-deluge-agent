# Complete Index of Deluge Integration Tasks

> Language: English. Spanish: see the matching path under docs/es/.

> **Official source:** https://www.zoho.com/deluge/help/integration-tasks.html  
> Deluge offers ~260 built-in tasks with 24 Zoho services.  
> Universal syntax: `zoho.<service>.<action>(<parameters>)`

---

## Summary by Service

| Service | Prefix | Tasks | Main category |
|----------|---------|-------|---------------------|
| Zoho CRM V8 | `zoho.crm.v8` | 13 | CRM / Sales (API v8) |
| Zoho CRM Vertical Solutions | `zoho.crm` | 7 | Extensions / Vertical CRM |
| Zoho Creator V2 | `zoho.creator` | 5 | Low-code (API v2) |
| Zoho Creator V2.1 | `zoho.creator.v2_1` | 6 | Low-code (API v2.1; not from Creator) |
| Zoho Books | `zoho.books` | 7 | Finance |
| Zoho Desk | `zoho.desk` | 12 | Support |
| Zoho One | `zoho.one` | 14 | Directory / org (not Creator) |
| Zoho Inventory | `zoho.inventory` | 6 | Inventory |
| Zoho Invoice | `zoho.invoice` | 4 | Invoicing |
| Zoho Billing | `zoho.billing` | 5 | Subscriptions |
| Zoho People | `zoho.people` | 4 | HR |
| Zoho Projects | `zoho.projects` | 9 | Projects |
| Zoho Recruit | `zoho.recruit` | 6 | Recruiting |
| Zoho Cliq | `zoho.cliq` | 13 | Messaging |
| Zoho Mail | `zoho.mail` | 11 | Email |
| Zoho Calendar | `zoho.calendar` | 1 | Calendar |
| Zoho Sign | `zoho.sign` | 9 | Digital signature |
| Zoho Writer | `zoho.writer` | 22 | Documents |
| Zoho Sheet | `zoho.sheet` | 7 | Spreadsheets |
| Zoho WorkDrive | `zoho.workdrive` | 3 | Storage |
| Zoho Analytics | `zoho.analytics` | 3 | Business Intelligence |
| Zoho Bookings | `zoho.bookings` | 6 | Appointments |
| Zoho Connect | `zoho.connect` | 10 | Enterprise social network |
| Zoho Map | `zoho.map` | 3 | Geocoding |
| Zoho Notebook | `zoho.notebook` | 1 | Notes |
| Zoho SalesIQ | `zoho.salesiq` | 2 | Live chat |
| SDP Cloud | `zoho.sdpcloud` | 6 | Service Desk Plus |

---

## Zoho CRM Integration Tasks V8 (`zoho.crm.v8.*`)

> Official index: https://www.zoho.com/deluge/help/crm-integration-tasks-V8.html  
> CRUD between Zoho products and **standard CRM**, built on CRM API v8. **Not** the Vertical Solutions index.

Each execution that receives a response (success or failure) = 1 External Call. A `for each` that iterates 5 times = 5 calls (not the number of times the task appears in the script).

`connection` is **mandatory in Cliq**; optional elsewhere. Parameters are positional: if you use an optional one, preceding params must be explicit (`null` or empty Map).

`options_map` (create / update / bulk): `trigger` key with `workflow`, `approval`, `blueprint`, `orchestration`. If omitted, approval / blueprint / orchestration run by default. Empty `trigger` list = skip automations.

| Help title | Deluge task |
|------------|-------------|
| Create Record | `zoho.crm.v8.createRecord(module, data, [options], [connection])` |
| Get Records | `zoho.crm.v8.getRecords(module, [query], [page], [perPage], [connection])` |
| Search Records | `zoho.crm.v8.searchRecords(module, criteria, [page], [perPage], [searchValue], [connection])` |
| Get Record By ID | `zoho.crm.v8.getRecordById(module, recordId, [query], [connection])` |
| Update Record | `zoho.crm.v8.updateRecord(module, recordId, data, [options], [connection])` |
| Bulk Create Records | `zoho.crm.v8.bulkCreate(module, dataList, [options], [connection])` — max 100 |
| Bulk Update Records | `zoho.crm.v8.bulkUpdate(module, dataList, [options], [connection])` — each map includes `id` |
| Get Related Records | `zoho.crm.v8.getRelatedRecords(relation, parentModule, recordId, [query], [page], [perPage], [connection])` |
| Update Related Record | `zoho.crm.v8.updateRelatedRecord(subModule, relatedId, parentModule, parentId, data, [connection])` |
| Convert Lead | `zoho.crm.v8.convertLead(leadId, [values], [connection])` |
| Upsert Record | `zoho.crm.v8.upsert(module, data, [duplicateCheck], [connection])` |
| Attach File | `zoho.crm.v8.attachFile(module, recordId, fileObject, [connection])` |
| Get Fields | `zoho.crm.v8.getFields(module, [connection])` |

Help titles do **not** always match the Deluge identifier: bulk is `bulkCreate` / `bulkUpdate` (not `bulkCreateRecords`); upsert is `upsert` (not `upsertRecord`).

```deluge
leadinfo = Collection();
leadinfo.insert("Company": "Zylker");
leadinfo.insert("Last_Name": "Williams");
leadinfo.insert("Email": "will@zylker.com");
resp = zoho.crm.v8.createRecord("Leads", leadinfo);
newId = resp.get("id").toLong();

query = Collection();
query.insert("fields": "First_Name,Last_Name,Email");
rec = zoho.crm.v8.getRecordById("Leads", newId, query);

fields = zoho.crm.v8.getFields("Contacts", "crm_connection");
```

Older scripts outside Vertical may still use `zoho.crm.createRecord` / `bulkCreateRecords` / `upsertRecord` (no `.v8`). New code: `zoho.crm.v8.*`.

---

## Zoho CRM Vertical Solutions (`zoho.crm.*`)

> Official index: https://www.zoho.com/deluge/help/crm-tasks.html  
> **Only** extensions and Vertical CRM. Do not use these tasks from Creator/Books/Desk against standard CRM.

Each execution that receives a response = 1 External Call (same rule as V8).

| Help title | Deluge task |
|------------|-------------|
| Get User Data | `zoho.crm.getRecords("users", [page], [perPage], {"type": "ActiveUsers"})` |
| Invoke Connector Task | `zoho.crm.invokeConnector("<connectorApiId>", parameterMap)` |
| Set Organization Variable | `zoho.crm.invokeConnector("crm.set", {"apiname": "Score", "value": 50})` |
| Get Organization Variable | `zoho.crm.getOrgVariable("Score")` |
| Get Module Metadata | `zoho.crm.invokeConnector("crm.modulemetadata", {"module": "Leads"})` |
| Get All Module Metadata | `zoho.crm.invokeConnector("crm.modulemetadata", Map())` — empty map |
| Get Organization Information | `zoho.crm.invokeConnector("crm.getorg", Map())` |

`type` for Get User Data: `AllUsers`, `AdminUsers`, `ActiveUsers`, `DeactiveUsers`.

```deluge
users = zoho.crm.getRecords("users", 1, 200, {"type": "ActiveUsers"});
score = zoho.crm.getOrgVariable("Score");
org = zoho.crm.invokeConnector("crm.getorg", Map());
```

`zoho.loginuser` / `zoho.adminuserid` are Deluge system variables (skill `zoho-deluge-core`), not tasks from this index.

---

## Zoho Creator V2 (`zoho.creator.*`)

> Index: https://www.zoho.com/deluge/help/creator-tasks.html  
> Creator API v2. First parameter = **owner** (`zoho.adminuser`).

Each execution with a response = 1 External Call. Extra V2 billing:
- **Same Creator account:** Developer API **and** External Calls from that account.
- **Other service → Creator:** Developer API on the target Creator; External Calls on the sending service.
- **Creator → another Creator:** Developer API on the target; External Calls on the sender.

`createRecord` accepts a Map (1 record) or a List (up to 200). V2 has **no** separate `createRecords` task. Update by ID is `updateRecord` (not `updateRecordById`).

| Help | Deluge |
|------|--------|
| Get Records | `zoho.creator.getRecords(owner, app, report, [criteria], [from], [limit], [connection])` |
| Get Record by ID | `zoho.creator.getRecordById(owner, app, report, recordId, [connection])` |
| Create Record | `zoho.creator.createRecord(owner, app, form, data, [otherParams], [connection])` |
| Update Records | `zoho.creator.updateRecords(owner, app, report, criteria, data, [otherParams], [connection])` |
| Update Record | `zoho.creator.updateRecord(owner, app, report, recordId, data, [otherParams], [connection])` |

---

## Zoho Creator V2.1 (`zoho.creator.v2_1.*`)

> Index: https://www.zoho.com/deluge/help/creator-tasks-v2.1.html  
> **Not** available from Zoho Creator. Namespace `zoho.creator.v2_1`, distinct from V2.

External Calls only. `configuration`: `environment` (`development`/`stage`), `skip_workflow` (`form_workflow`, `schedules`, `all`), `field_config`, `max_records` (200/500/1000), `criteria`, `message`, `tasks`.

| Help | Deluge |
|------|--------|
| Create Record | `zoho.creator.v2_1.createRecord(owner, app, form, data, [config], [connection])` |
| Create Records | `zoho.creator.v2_1.createRecords(owner, app, form, dataList, [config], [connection])` |
| Update Record by ID | `zoho.creator.v2_1.updateRecordById(owner, app, report, recordId, data, [config], [connection])` |
| Update Records | `zoho.creator.v2_1.updateRecords(owner, app, report, data, [config], [connection])` |
| Get Record by ID | `zoho.creator.v2_1.getRecordById(owner, app, report, recordId, [config], [connection])` |
| Get Records | `zoho.creator.v2_1.getRecords(owner, app, report, [config], [connection])` |

---

## Zoho Books (`zoho.books.*`)

> Index: https://www.zoho.com/deluge/help/books-tasks.html  
> Each execution with a response = 1 External Call.

| Help | Deluge |
|------|--------|
| Get Organizations | `zoho.books.getOrganizations([connection])` |
| Create Record | `zoho.books.createRecord(module, orgId, data, [connection])` |
| Update Record | `zoho.books.updateRecord(module, recordId, orgId, data, [connection])` |
| Get Records | `zoho.books.getRecords(module, orgId, [connection])` |
| Get Records By ID | `zoho.books.getRecordById(module, recordId, orgId, [connection])` |
| Mark Status | `zoho.books.markStatus(module, recordId, orgId, status, [connection])` |
| Get Templates | `zoho.books.getTemplates(module, orgId, [connection])` |

**Modules:** `Contacts`, `Invoices`, `Estimates`, `SalesOrders`, `PurchaseOrders`, `Bills`, `Payments`, `Expenses`, `Items`, `CreditNotes`, `VendorCredits`, `Projects`, `Taxes`, `Journals`, `BankAccounts`, `ChartOfAccounts`. Nulls: `containsKey("custom_field_hash")`.

---

## Zoho Desk (`zoho.desk.*`)

> Index: https://www.zoho.com/deluge/help/desk-tasks.html  
> `orgId` comes **first**. Each execution with a response = 1 External Call.

| Help | Deluge |
|------|--------|
| Get Records | `zoho.desk.getRecords(orgId, module, [from], [limit], [query], [connection])` |
| Get Record By ID | `zoho.desk.getRecordById(orgId, module, recordId, [connection])` |
| Create | `zoho.desk.create(orgId, module, data, [connection])` |
| Update | `zoho.desk.update(orgId, module, recordId, data, [connection])` |
| Search Records | `zoho.desk.searchRecords(orgId, module, criteria, [connection])` |
| Get Related Records | `zoho.desk.getRelatedRecords(orgId, module, recordId, subModule, [connection])` |
| Get Related Record By ID | `zoho.desk.getRelatedRecordById(orgId, module, recordId, subModule, relId, [connection])` |
| Create Related Record | `zoho.desk.createRelatedRecord(orgId, module, recordId, subModule, data, [connection])` |
| Update Related Record | `zoho.desk.updateRelatedRecord(orgId, module, recordId, subModule, relId, data, [connection])` |
| Move | `zoho.desk.move(orgId, ticketId, deptId, [connection])` |
| Split | `zoho.desk.split(orgId, ticketId, data, [connection])` |
| Merge | `zoho.desk.merge(orgId, ticketIds, [connection])` |

Official modules: `tickets`, `contacts`, `accounts`, `tasks`, `products`, `contracts` (+ `agents`, `departments` on getRecords).

---

## Zoho One (`zoho.one.*`)

> Index: https://www.zoho.com/deluge/help/one-tasks.html  
> Applies to **all services except Zoho Creator**. Each execution with a response = 1 External Call.

| Help | Deluge |
|------|--------|
| Add User | `zoho.one.addUser(orgId, userData, connection)` |
| Update User | `zoho.one.updateUser(orgId, userId, userData, connection)` |
| Get User | `zoho.one.getUser(orgId, userId, [queryParams], connection)` |
| Get Users | `zoho.one.getUsers(orgId, page, perPage, [queryParams], connection)` |
| Activate User | `zoho.one.activateUser(orgId, userId, connection)` |
| Deactivate User | `zoho.one.deactivateUser(orgId, userId, connection)` |
| Get Designations | `zoho.one.getDesignations(orgId, page, perPage, connection)` |
| Get Locations | `zoho.one.getLocations(orgId, page, perPage, connection)` |
| Get Groups | `zoho.one.getGroups(orgId, page, perPage, connection)` |
| Get Departments | `zoho.one.getDepartments(orgId, page, perPage, connection)` |
| Add User to a Group | `zoho.one.addUserToGroup(orgId, groupId, userData, connection)` |
| Get Groups for User | `zoho.one.getUserGroups(orgId, userId, page, perPage, connection)` |
| Get Custom Fields | `zoho.one.getCustomFields(orgId, connection)` |
| Remove User from a Group | `zoho.one.removeUserFromGroup(orgId, groupId, userId, connection)` |

Get Groups for User → identifier `getUserGroups` (not `getGroupsForUser`).

---

## Zoho Inventory (`zoho.inventory.*`)

> Ref: https://www.zoho.com/deluge/help/inventory-tasks.html

| Task | Description |
|------|-------------|
| `zoho.inventory.getOrganization()` | Gets associated organizations |
| `zoho.inventory.getRecords(module, orgId)` | Gets records |
| `zoho.inventory.getRecordById(module, recordId, orgId)` | Gets by ID |
| `zoho.inventory.createRecord(module, orgId, data)` | Creates a record |
| `zoho.inventory.updateRecord(module, recordId, orgId, data)` | Updates |
| `zoho.inventory.markStatus(module, recordId, orgId, status)` | Changes status |

**Modules:** `Contacts`, `Items`, `SalesOrders`, `Packages`, `Invoices`, `PurchaseOrders`, `Bills`, `Warehouses`, `Shipments`

---

## Zoho Invoice (`zoho.invoice.*`)

> Ref: https://www.zoho.com/deluge/help/invoice-tasks.html

| Task | Description |
|------|-------------|
| `zoho.invoice.getRecords(module, orgId)` | Gets records |
| `zoho.invoice.getRecordById(module, recordId, orgId)` | Gets by ID |
| `zoho.invoice.create(module, orgId, data)` | Creates a record |
| `zoho.invoice.update(module, recordId, orgId, data)` | Updates |

**Note:** Zoho Invoice was replaced by Zoho Billing (formerly Zoho Subscriptions) for recurring billing. Invoice remains available for simple invoices.

---

## Zoho Billing (`zoho.billing.*`)

> Ref: https://www.zoho.com/deluge/help/billing-tasks.html  
> (formerly known as Zoho Subscriptions)

| Task | Description |
|------|-------------|
| `zoho.billing.getOrganization()` | Gets associated organizations |
| `zoho.billing.getList(module, orgId)` | Gets a list of records |
| `zoho.billing.retrieve(module, recordId, orgId)` | Gets by ID |
| `zoho.billing.create(module, orgId, data)` | Creates a record |
| `zoho.billing.update(module, recordId, orgId, data)` | Updates |

**Modules:** `Customers`, `Subscriptions`, `Plans`, `Addons`, `Coupons`, `Invoices`, `Payments`, `Products`

---

## Zoho People (`zoho.people.*`)

> Ref: https://www.zoho.com/deluge/help/people-tasks.html

| Task | Description |
|------|-------------|
| `zoho.people.getRecords(module)` | Gets records from the module |
| `zoho.people.create(module, data)` | Creates a record |
| `zoho.people.getRecordById(module, recordId)` | Gets by ID |
| `zoho.people.update(module, recordId, data)` | Updates a record |

**Modules:** `employee`, `leave`, `attendance`, `form` (+ custom form name)

---

## Zoho Projects (`zoho.projects.*`)

> Ref: https://www.zoho.com/deluge/help/projects-tasks.html

| Task | Description |
|------|-------------|
| `zoho.projects.getPortals()` | Gets the user's portals |
| `zoho.projects.getProjectDetails(portalId)` | Gets projects in the portal |
| `zoho.projects.createProject(portalId, data)` | Creates a project |
| `zoho.projects.getRecords(module, portalId, projectId)` | Gets records |
| `zoho.projects.getRecordById(module, portalId, projectId, recordId)` | Gets by ID |
| `zoho.projects.create(module, portalId, projectId, data)` | Creates a record |
| `zoho.projects.update(module, portalId, projectId, recordId, data)` | Updates |
| `zoho.projects.associateLogs(module, portalId, projectId, recordId, data)` | Associates a time log |
| `zoho.projects.updateAssociatedLogs(module, portalId, projectId, recordId, logId, data)` | Updates a time log |

**Modules:** `tasks`, `milestones`, `issues`, `forums`, `users`, `timesheets`

---

## Zoho Recruit (`zoho.recruit.*`)

> Ref: https://www.zoho.com/deluge/help/recruit-tasks.html

| Task | Description |
|------|-------------|
| `zoho.recruit.addRecords(module, data)` | Creates records |
| `zoho.recruit.getRecordById(module, recordId)` | Gets by ID |
| `zoho.recruit.getRecords(module, [page], [perPage])` | Gets records |
| `zoho.recruit.searchRecords(module, criteria)` | Searches by criteria |
| `zoho.recruit.updateRecord(module, recordId, data)` | Updates a record |
| `zoho.recruit.uploadFile(module, recordId, file)` | Attaches a file |

**Modules:** `JobOpenings`, `Candidates`, `Contacts`, `Clients`, `Applications`, `Interviews`

---

## Zoho Cliq (`zoho.cliq.*`)

> Ref: https://www.zoho.com/deluge/help/cliq-tasks.html

### Messaging

| Task | Description |
|------|-------------|
| `zoho.cliq.postToChat(chatId, data)` | Posts a message to a chat |
| `zoho.cliq.postToChatAsAdmin(chatId, data)` | Posts as admin |
| `zoho.cliq.postToChannel(channelName, data)` | Posts to a channel |
| `zoho.cliq.postToChannelAsAdmin(channelName, data)` | Posts to a channel as admin |
| `zoho.cliq.postToBot(botName, data)` | Posts to a bot |
| `zoho.cliq.postToBotAsAdmin(botName, data)` | Posts to a bot as admin |
| `zoho.cliq.postToUser(emailOrZUID, data)` | Posts to a specific user |

### Cliq Database

| Task | Description |
|------|-------------|
| `zoho.cliq.createRecord(module, data)` | Creates a record |
| `zoho.cliq.getRecords(module)` | Gets a list of records |
| `zoho.cliq.getRecordById(module, recordId)` | Gets by ID |
| `zoho.cliq.updateRecord(module, recordId, data)` | Updates |
| `zoho.cliq.deleteRecord(module, recordId)` | Deletes by ID |
| `zoho.cliq.deleteRecords(module, criteria)` | Deletes by criteria |

---

## Zoho Mail (`zoho.mail.*`)

> Ref: https://www.zoho.com/deluge/help/mail-tasks.html  
> **Note:** These tasks are NOT available in Zoho Creator.

| Task | Description |
|------|-------------|
| `zoho.mail.getFolders(userId)` | Gets all folders |
| `zoho.mail.moveToFolder(userId, mailId, folderId)` | Moves an email to a folder |
| `zoho.mail.createFolder(userId, folderName)` | Creates a folder |
| `zoho.mail.setFlag(userId, mailId)` | Flags an email |
| `zoho.mail.removeFlag(userId, mailId)` | Removes the flag |
| `zoho.mail.markAsRead(userId, mailId)` | Marks as read |
| `zoho.mail.markAsUnread(userId, mailId)` | Marks as unread |
| `zoho.mail.getMessage(userId, mailId)` | Gets email details |
| `zoho.mail.createTag(userId, tagName)` | Creates a tag |
| `zoho.mail.getLabels(userId)` | Gets all labels |
| `zoho.mail.setTag(userId, mailId, tagId)` | Assigns a tag to an email |

---

## Zoho Calendar (`zoho.calendar.*`)

> Ref: https://www.zoho.com/deluge/help/calendar-tasks.html

| Task | Description |
|------|-------------|
| `zoho.calendar.createEvent(uid, data)` | Creates an event in Zoho Calendar |

---

## Zoho Sign (`zoho.sign.*`)

> Ref: https://www.zoho.com/deluge/help/sign-tasks.html

| Task | Description |
|------|-------------|
| `zoho.sign.createDocument(data)` | Uploads a document to Zoho Sign |
| `zoho.sign.getDocumentById(documentId)` | Gets a document by ID |
| `zoho.sign.updateDocument(documentId, data)` | Updates a document |
| `zoho.sign.getFieldIds(documentId)` | Gets IDs of all fields |
| `zoho.sign.downloadDocument(documentId)` | Downloads documents from Zoho Sign |
| `zoho.sign.submitRequest(requestId)` | Submits the specified request |
| `zoho.sign.getTemplates()` | Gets all templates |
| `zoho.sign.getTemplateById(templateId)` | Gets a template by ID |
| `zoho.sign.createUsingTemplate(templateId, data)` | Creates a document from a template |

---

## Zoho Writer (`zoho.writer.*`)

> Ref: https://www.zoho.com/deluge/help/writer-tasks.html

| Task | Description |
|------|-------------|
| `zoho.writer.getDocuments(criteria)` | Lists documents by criteria |
| `zoho.writer.uploadDocument(file)` | Uploads a document |
| `zoho.writer.shareDocument(docId, data)` | Shares with collaborators by role |
| `zoho.writer.getMergeFields(docId)` | Lists merge fields in the doc |
| `zoho.writer.mergeAndSend(docId, data)` | Merges values and sends by email |
| `zoho.writer.signDocument(docId, data)` | Sends for signature collection |
| `zoho.writer.getAllFields(docId)` | Lists all fields in the doc |
| `zoho.writer.mergeAndSign(docId, data)` | Merges and sends for signature |
| `zoho.writer.mergeAndStoreV2(docId, data)` | Merges and stores in WorkDrive |
| `zoho.writer.mergeAndInvoke(docId, data)` | Merges and invokes a custom function |
| `zoho.writer.markUnmarkFavorite(docId, isFavorite)` | Marks/unmarks as favorite |
| `zoho.writer.renameDocument(docId, newName)` | Renames the document |
| `zoho.writer.enableDisableTrackChanges(docId, enable)` | Enables/disables track changes |
| `zoho.writer.addUpdateDescription(docId, description)` | Adds/updates description |
| `zoho.writer.lockUnlockDocument(docId, lock)` | Locks/unlocks the doc |
| `zoho.writer.markDocumentReady(docId)` | Marks the document as ready |
| `zoho.writer.markRevertFinal(docId, isFinal)` | Enables/disables editing |
| `zoho.writer.getMergeTemplates()` | Lists merge templates |
| `zoho.writer.getSignTemplates()` | Lists sign templates |
| `zoho.writer.getFillableTemplates()` | Lists fillable templates |
| `zoho.writer.v2SignDocument(docId, data)` | Sends for signature and tracks status |
| `zoho.writer.generateFillableLink(docId, data)` | Generates a prefilled link for collection |
| `zoho.writer.mergeAndSendV2(docId, data)` | Merges and sends v2 |

---

## Zoho Sheet (`zoho.sheet.*`)

> Ref: https://www.zoho.com/deluge/help/sheet-tasks.html

| Task | Description |
|------|-------------|
| `zoho.sheet.getSheets(resourceId)` | Gets sheets in a file |
| `zoho.sheet.createRecords(resourceId, sheetId, data)` | Inserts data into the sheet |
| `zoho.sheet.getRecords(resourceId, sheetId)` | Gets data from the sheet |
| `zoho.sheet.updateRecords(resourceId, sheetId, criteria, data)` | Updates rows |
| `zoho.sheet.find(resourceId, sheetId, searchText)` | Finds specific text |
| `zoho.sheet.replace(resourceId, sheetId, oldText, newText)` | Replaces all occurrences |
| `zoho.sheet.insertCsv(resourceId, sheetId, csvData)` | Inserts CSV data into the sheet |

---

## Zoho WorkDrive (`zoho.workdrive.*`)

> Ref: https://www.zoho.com/deluge/help/workdrive-tasks.html

| Task | Description |
|------|-------------|
| `zoho.workdrive.uploadFile(folderId, file, fileName)` | Uploads a file to a folder |
| `zoho.workdrive.createFolder(parentFolderId, folderName)` | Creates a subfolder |
| `zoho.workdrive.createTeamFolder(teamId, folderName)` | Creates a team folder |

REST `invokeurl` to `/workdrive/api/v1/files/{id}`: header `Accept: application/vnd.api+json` (JSON:API). Native tasks do not need it.

---

## Zoho Analytics (`zoho.analytics.*`)

> Ref: https://www.zoho.com/deluge/help/analytics-tasks.html

| Task | Description |
|------|-------------|
| `zoho.analytics.createRow(workspaceId, tableId, data)` | Creates a row in a table |
| `zoho.analytics.deleteRow(workspaceId, tableId, criteria)` | Deletes rows by criteria |
| `zoho.analytics.updateData(workspaceId, tableId, criteria, data)` | Updates data |

---

## Zoho Bookings (`zoho.bookings.*`)

> Ref: https://www.zoho.com/deluge/help/bookings-tasks.html

| Task | Description |
|------|-------------|
| `zoho.bookings.getWorkspaces()` | Gets all workspaces |
| `zoho.bookings.getRecordById(module, recordId)` | Gets a record by ID |
| `zoho.bookings.getRelatedRecords(module, recordId, subModule)` | Gets related records |
| `zoho.bookings.getAvailableSlots(serviceId, staffId, date)` | Gets available slots |
| `zoho.bookings.createAppointment(data)` | Creates an appointment |
| `zoho.bookings.updateRecords(recordId, data)` | Updates a record by ID |

---

## Zoho Connect (`zoho.connect.*`)

> Ref: https://www.zoho.com/deluge/help/connect-tasks.html

| Task | Description |
|------|-------------|
| `zoho.connect.myNetworks()` | Gets all your networks |
| `zoho.connect.myGroups()` | Gets all your groups |
| `zoho.connect.addPost(networkId, data)` | Publishes content |
| `zoho.connect.getPost(postId)` | Gets the content of a post |
| `zoho.connect.getPostByTPID(tpId)` | Gets a post by third-party ID |
| `zoho.connect.updatePost(postId, data)` | Updates a post by ID |
| `zoho.connect.deletePost(postId)` | Deletes a post by ID |
| `zoho.connect.sendMePrivateMessage(networkId, data)` | Creates a private message |
| `zoho.connect.addComment(postId, data)` | Adds a comment to a post |
| `zoho.connect.addUsersToGroup(groupId, emails)` | Adds users to a group |

---

## Zoho Map (`zoho.map.*`)

> Ref: https://www.zoho.com/deluge/help/map-tasks.html

| Task | Description |
|------|-------------|
| `zoho.map.geocode(address)` | Returns GPS coordinates for an address |
| `zoho.map.reverseGeocode(lat, lng)` | Returns an address from GPS coordinates |
| `zoho.map.distanceBetween(address1, address2)` | Straight-line distance between addresses |

---

## Zoho Notebook (`zoho.notebook.*`)

> Ref: https://www.zoho.com/deluge/help/notebook-tasks.html

| Task | Description |
|------|-------------|
| `zoho.notebook.createNotecard(data)` | Creates a notecard with the specified content |

---

## Zoho SalesIQ (`zoho.salesiq.*`)

> Ref: https://www.zoho.com/deluge/help/salesiq-tasks.html

| Task | Description |
|------|-------------|
| `zoho.salesiq.visitorsession.set(key, value)` | Temporarily stores data during a chat |
| `zoho.salesiq.visitorsession.get(key)` | Retrieves temporary chat data |

---

## SDP Cloud / ServiceDesk Plus (`zoho.sdpcloud.*`)

> Ref: https://www.zoho.com/deluge/help/sdp-tasks.html

| Task | Description |
|------|-------------|
| `zoho.sdpcloud.createRecord(module, data)` | Creates a record |
| `zoho.sdpcloud.getRecords(module)` | Gets all records |
| `zoho.sdpcloud.updateRecord(module, recordId, data)` | Updates |
| `zoho.sdpcloud.getRecordById(module, recordId)` | Gets by ID |
| `zoho.sdpcloud.associate(module, recordId, subModule, data)` | Associates a sub-record |
| `zoho.sdpcloud.invokeUrl(url, method, headers, data)` | Invokes SDP's own endpoints |

---

## invokeUrl — External APIs and direct Zoho APIs

The alternative when native tasks do not cover the required endpoint.

```deluge
// Basic GET
resp = invokeUrl [
    url:  "https://api.ejemplo.com/endpoint"
    type: GET
];

// POST with JSON and Bearer authentication
headers = Map();
headers.put("Content-Type", "application/json");
headers.put("Authorization", "Bearer TOKEN");

body = Map();
body.put("campo1", "valor1");

resp = invokeUrl [
    url:        "https://api.ejemplo.com/endpoint"
    type:       POST
    body:       body.toText()   // toText() generates valid JSON, toString() does NOT
    headers:    headers
];

// With a configured OAuth Connection (recommended for Zoho APIs)
resp = invokeUrl [
    url:        "https://www.zohoapis.com/crm/v8/Contacts"
    type:       GET
    connection: "zoho_crm_connection"
];

// With query string parameters
params = Map();
params.put("page", "1");
params.put("per_page", "200");

resp = invokeUrl [
    url:        "https://api.ejemplo.com/registros"
    type:       GET
    parameters: params
    connection: "mi_conexion"
];
```

### Zoho API base URLs (for invokeUrl)

| Service | Base URL |
|----------|----------|
| CRM v8 | `https://www.zohoapis.com/crm/v8/` |
| Books v3 | `https://www.zohoapis.com/books/v3/` |
| Creator v2.1 | `https://creator.zoho.com/api/v2.1/` |
| Desk v1 | `https://desk.zoho.com/api/v1/` |
| Inventory v1 | `https://www.zohoapis.com/inventory/v1/` |
| People v2 | `https://people.zoho.com/people/api/` |
| Projects v3 | `https://projectsapi.zoho.com/restapi/portal/` |
| Recruit v2 | `https://recruit.zoho.com/recruit/v2/` |
| Sign v1 | `https://sign.zoho.com/api/v1/` |
| Analytics v2 | `https://analyticsapi.zoho.com/api/v2/` |
| WorkDrive v1 | `https://www.zohoapis.com/workdrive/api/v1/` |
| Billing v1 | `https://www.zohoapis.com/billing/v1/` |
| Bookings v1 | `https://bookings.zoho.com/api/v1/` |

### Domain regions

| Region | Domain |
|--------|---------|
| US (default) | `.com` |
| EU | `.eu` |
| India | `.in` |
| Australia | `.com.au` |
| Japan | `.jp` |
| Canada | `.ca` |

Example: `https://www.zohoapis.eu/crm/v8/Contacts` for an org in Europe.

---

## Considerations Common to All Tasks

### API cost (External Calls)

Each execution of an integration task consumes 1 API call from your plan's External Calls limit. If the task is inside a `for each` that iterates 5 times, it consumes 5 calls, not 1.

### toString() vs toText()

```deluge
mapa = {"nombre": "Juan", "edad": 30};

mapa.toString()  // {nombre=Juan, edad=30}   ← Deluge representation, NOT valid as JSON
mapa.toText()    // {"nombre":"Juan","edad":30}  ← valid JSON for external APIs
```

**Always use `.toText()` in the invokeUrl body when the API expects JSON.**

### Null validation by app

```deluge
// CRM — validate with != null
valor = rec.get("Campo");
if (valor != null) { /* ... */ }

// Creator — validate with != ""
valor = rec.get("Campo");
if (valor != "") { /* ... */ }

// Books/Inventory/Invoice — use containsKey()
if (rec.containsKey("custom_field_hash")) {
    campos = rec.get("custom_field_hash");
}

// Lookup fields (any app) — protect the .get() chain
lookup = ifNull(rec.get("Cuenta"), {"id": "", "name": ""});
id = lookup.get("id");
```
