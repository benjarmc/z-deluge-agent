# Índice Completo de Deluge Integration Tasks

> **Fuente oficial:** https://www.zoho.com/deluge/help/integration-tasks.html  
> Deluge ofrece ~260 tasks integradas con 24 servicios Zoho.  
> Sintaxis universal: `zoho.<servicio>.<accion>(<parámetros>)`

---

## Resumen por Servicio

| Servicio | Prefijo | Tasks | Categoría principal |
|----------|---------|-------|---------------------|
| Zoho CRM | `zoho.crm` | 12 | CRM / Ventas |
| Zoho Creator | `zoho.creator` | 6 (v2) / 6 (v2.1) | Plataforma low-code |
| Zoho Books | `zoho.books` | 7 | Finanzas |
| Zoho Desk | `zoho.desk` | 12 | Soporte |
| Zoho Inventory | `zoho.inventory` | 6 | Inventario |
| Zoho Invoice | `zoho.invoice` | 4 | Facturación |
| Zoho Billing | `zoho.billing` | 5 | Suscripciones |
| Zoho People | `zoho.people` | 4 | RRHH |
| Zoho Projects | `zoho.projects` | 9 | Proyectos |
| Zoho Recruit | `zoho.recruit` | 6 | Reclutamiento |
| Zoho Cliq | `zoho.cliq` | 13 | Mensajería |
| Zoho Mail | `zoho.mail` | 11 | Email |
| Zoho Calendar | `zoho.calendar` | 1 | Calendario |
| Zoho Sign | `zoho.sign` | 9 | Firma digital |
| Zoho Writer | `zoho.writer` | 22 | Documentos |
| Zoho Sheet | `zoho.sheet` | 7 | Hojas de cálculo |
| Zoho WorkDrive | `zoho.workdrive` | 3 | Almacenamiento |
| Zoho Analytics | `zoho.analytics` | 3 | Business Intelligence |
| Zoho Bookings | `zoho.bookings` | 6 | Citas |
| Zoho Connect | `zoho.connect` | 10 | Red social empresarial |
| Zoho Map | `zoho.map` | 3 | Geocodificación |
| Zoho Notebook | `zoho.notebook` | 1 | Notas |
| Zoho SalesIQ | `zoho.salesiq` | 2 | Chat en vivo |
| SDP Cloud | `zoho.sdpcloud` | 6 | Service Desk Plus |

---

## Zoho CRM (`zoho.crm.*`)

> Ref: https://www.zoho.com/deluge/help/crm-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.crm.createRecord(module, data)` | Crea un registro en el módulo especificado |
| `zoho.crm.getRecords(module, [page], [perPage])` | Obtiene registros de un módulo |
| `zoho.crm.searchRecords(module, criteria, [page], [perPage])` | Busca por criterios |
| `zoho.crm.getRecordById(module, recordId)` | Obtiene un registro por ID |
| `zoho.crm.updateRecord(module, recordId, data)` | Actualiza un registro |
| `zoho.crm.bulkCreateRecords(module, dataList)` | Crea múltiples registros |
| `zoho.crm.bulkUpdateRecords(module, dataList)` | Actualiza múltiples registros |
| `zoho.crm.getRelatedRecords(module, recordId, subModule)` | Obtiene registros relacionados |
| `zoho.crm.updateRelatedRecord(module, recordId, subModule, relatedId, data)` | Actualiza registro relacionado |
| `zoho.crm.convertLead(leadId)` | Convierte un Lead en Contacto + Cuenta + Deal |
| `zoho.crm.upsertRecord(module, data)` | Actualiza si existe, crea si no |
| `zoho.crm.attachFile(module, recordId, fileContent)` | Adjunta archivo a un registro |

**Variables especiales CRM:**
```deluge
zoho.loginuser          // email del usuario activo
zoho.adminuserid        // email del admin de org
zoho.crm.getOrgVariable("nombre_var")  // org variables
```

---

## Zoho Creator (`zoho.creator.*`)

> Ref: https://www.zoho.com/deluge/help/creator-tasks.html  
> Ref v2.1: https://www.zoho.com/deluge/help/creator-tasks-v2.1.html

### Tasks nativas (v2)

| Task | Descripción |
|------|-------------|
| `zoho.creator.createRecord(app, form, data)` | Crea un registro |
| `zoho.creator.createRecords(app, form, dataList)` | Crea múltiples registros |
| `zoho.creator.getRecordById(app, report, recordId)` | Obtiene registro por ID |
| `zoho.creator.getRecords(app, report)` | Obtiene registros de un reporte |
| `zoho.creator.updateRecordById(app, report, recordId, data)` | Actualiza por ID |
| `zoho.creator.updateRecords(app, report, updateList)` | Actualiza múltiples |

### Tasks v2.1 (mayor control)

| Task | Descripción |
|------|-------------|
| `zoho.creator.createRecord(app, form, data)` | Crea registro (misma sintaxis, nueva URL interna) |
| `zoho.creator.createRecords(app, form, dataList)` | Crea múltiples |
| `zoho.creator.updateRecordById(app, report, recordId, data)` | Actualiza por ID |
| `zoho.creator.updateRecords(app, report, updateList)` | Actualiza múltiples |
| `zoho.creator.getRecordById(app, report, recordId)` | Obtiene por ID |
| `zoho.creator.getRecords(app, report)` | Obtiene registros |

**Nota API v2.1:** Para usar parámetros avanzados (`field_config`, `max_records`, `skip_workflow`, `record_cursor`), usar `invokeUrl` directamente contra `https://creator.zoho.com/api/v2.1/`.

---

## Zoho Books (`zoho.books.*`)

> Ref: https://www.zoho.com/deluge/help/books-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.books.getOrganizations()` | Obtiene todas las organizaciones del usuario |
| `zoho.books.createRecord(module, orgId, data)` | Crea un registro |
| `zoho.books.updateRecord(module, recordId, orgId, data)` | Actualiza un registro |
| `zoho.books.getRecords(module, orgId)` | Obtiene registros de un módulo |
| `zoho.books.getRecordById(module, recordId, orgId)` | Obtiene registro por ID |
| `zoho.books.markStatus(module, recordId, orgId, status)` | Cambia el estado del registro |
| `zoho.books.getTemplates(module, orgId)` | Obtiene plantillas del módulo |

**Módulos disponibles en Books:** `Contacts`, `Invoices`, `Estimates`, `SalesOrders`, `PurchaseOrders`, `Bills`, `Payments`, `Expenses`, `Items`, `CreditNotes`, `VendorCredits`, `Projects`, `Taxes`, `Journals`, `BankAccounts`, `ChartOfAccounts`

**Validación de nulos:** Registros de Books devuelven campos en `custom_field_hash`. Usar `containsKey()` en lugar de `!= null`.

---

## Zoho Desk (`zoho.desk.*`)

> Ref: https://www.zoho.com/deluge/help/desk-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.desk.getRecords(module, orgId)` | Obtiene registros del módulo |
| `zoho.desk.getRecordById(module, recordId, orgId)` | Obtiene registro por ID |
| `zoho.desk.create(module, orgId, data)` | Crea un registro |
| `zoho.desk.update(module, recordId, orgId, data)` | Actualiza un registro |
| `zoho.desk.searchRecords(module, orgId, criteria)` | Busca por criterios |
| `zoho.desk.getRelatedRecords(module, recordId, subModule, orgId)` | Obtiene relacionados |
| `zoho.desk.getRelatedRecordById(module, recordId, subModule, relId, orgId)` | Obtiene relacionado por ID |
| `zoho.desk.createRelatedRecord(module, recordId, subModule, orgId, data)` | Crea registro relacionado |
| `zoho.desk.updateRelatedRecord(module, recordId, subModule, relId, orgId, data)` | Actualiza relacionado |
| `zoho.desk.move(ticketId, deptId, orgId)` | Mueve ticket a otro departamento |
| `zoho.desk.split(ticketId, orgId, data)` | Divide una respuesta como nuevo ticket |
| `zoho.desk.merge(ticketIds, orgId)` | Fusiona dos o más tickets |

**Módulos disponibles:** `tickets`, `contacts`, `accounts`, `agents`, `departments`, `products`, `tasks`, `calls`, `events`, `articles`, `categories`, `threads`

---

## Zoho Inventory (`zoho.inventory.*`)

> Ref: https://www.zoho.com/deluge/help/inventory-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.inventory.getOrganization()` | Obtiene las organizaciones asociadas |
| `zoho.inventory.getRecords(module, orgId)` | Obtiene registros |
| `zoho.inventory.getRecordById(module, recordId, orgId)` | Obtiene por ID |
| `zoho.inventory.createRecord(module, orgId, data)` | Crea un registro |
| `zoho.inventory.updateRecord(module, recordId, orgId, data)` | Actualiza |
| `zoho.inventory.markStatus(module, recordId, orgId, status)` | Cambia estado |

**Módulos:** `Contacts`, `Items`, `SalesOrders`, `Packages`, `Invoices`, `PurchaseOrders`, `Bills`, `Warehouses`, `Shipments`

---

## Zoho Invoice (`zoho.invoice.*`)

> Ref: https://www.zoho.com/deluge/help/invoice-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.invoice.getRecords(module, orgId)` | Obtiene registros |
| `zoho.invoice.getRecordById(module, recordId, orgId)` | Obtiene por ID |
| `zoho.invoice.create(module, orgId, data)` | Crea un registro |
| `zoho.invoice.update(module, recordId, orgId, data)` | Actualiza |

**Nota:** Zoho Invoice fue reemplazado por Zoho Billing (antes Zoho Subscriptions) para facturación recurrente. Invoice sigue disponible para facturas simples.

---

## Zoho Billing (`zoho.billing.*`)

> Ref: https://www.zoho.com/deluge/help/billing-tasks.html  
> (antes conocido como Zoho Subscriptions)

| Task | Descripción |
|------|-------------|
| `zoho.billing.getOrganization()` | Obtiene organizaciones asociadas |
| `zoho.billing.getList(module, orgId)` | Obtiene lista de registros |
| `zoho.billing.retrieve(module, recordId, orgId)` | Obtiene por ID |
| `zoho.billing.create(module, orgId, data)` | Crea un registro |
| `zoho.billing.update(module, recordId, orgId, data)` | Actualiza |

**Módulos:** `Customers`, `Subscriptions`, `Plans`, `Addons`, `Coupons`, `Invoices`, `Payments`, `Products`

---

## Zoho People (`zoho.people.*`)

> Ref: https://www.zoho.com/deluge/help/people-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.people.getRecords(module)` | Obtiene registros del módulo |
| `zoho.people.create(module, data)` | Crea un registro |
| `zoho.people.getRecordById(module, recordId)` | Obtiene por ID |
| `zoho.people.update(module, recordId, data)` | Actualiza un registro |

**Módulos:** `employee`, `leave`, `attendance`, `form` (+ nombre del formulario custom)

---

## Zoho Projects (`zoho.projects.*`)

> Ref: https://www.zoho.com/deluge/help/projects-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.projects.getPortals()` | Obtiene los portales del usuario |
| `zoho.projects.getProjectDetails(portalId)` | Obtiene proyectos del portal |
| `zoho.projects.createProject(portalId, data)` | Crea un proyecto |
| `zoho.projects.getRecords(module, portalId, projectId)` | Obtiene registros |
| `zoho.projects.getRecordById(module, portalId, projectId, recordId)` | Obtiene por ID |
| `zoho.projects.create(module, portalId, projectId, data)` | Crea un registro |
| `zoho.projects.update(module, portalId, projectId, recordId, data)` | Actualiza |
| `zoho.projects.associateLogs(module, portalId, projectId, recordId, data)` | Asocia registro de tiempo |
| `zoho.projects.updateAssociatedLogs(module, portalId, projectId, recordId, logId, data)` | Actualiza registro de tiempo |

**Módulos:** `tasks`, `milestones`, `issues`, `forums`, `users`, `timesheets`

---

## Zoho Recruit (`zoho.recruit.*`)

> Ref: https://www.zoho.com/deluge/help/recruit-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.recruit.addRecords(module, data)` | Crea registros |
| `zoho.recruit.getRecordById(module, recordId)` | Obtiene por ID |
| `zoho.recruit.getRecords(module, [page], [perPage])` | Obtiene registros |
| `zoho.recruit.searchRecords(module, criteria)` | Busca por criterios |
| `zoho.recruit.updateRecord(module, recordId, data)` | Actualiza un registro |
| `zoho.recruit.uploadFile(module, recordId, file)` | Adjunta archivo |

**Módulos:** `JobOpenings`, `Candidates`, `Contacts`, `Clients`, `Applications`, `Interviews`

---

## Zoho Cliq (`zoho.cliq.*`)

> Ref: https://www.zoho.com/deluge/help/cliq-tasks.html

### Mensajería

| Task | Descripción |
|------|-------------|
| `zoho.cliq.postToChat(chatId, data)` | Publica mensaje en un chat |
| `zoho.cliq.postToChatAsAdmin(chatId, data)` | Publica como admin |
| `zoho.cliq.postToChannel(channelName, data)` | Publica en un canal |
| `zoho.cliq.postToChannelAsAdmin(channelName, data)` | Publica en canal como admin |
| `zoho.cliq.postToBot(botName, data)` | Publica en un bot |
| `zoho.cliq.postToBotAsAdmin(botName, data)` | Publica en bot como admin |
| `zoho.cliq.postToUser(emailOrZUID, data)` | Publica a un usuario específico |

### Base de Datos Cliq

| Task | Descripción |
|------|-------------|
| `zoho.cliq.createRecord(module, data)` | Crea un registro |
| `zoho.cliq.getRecords(module)` | Obtiene lista de registros |
| `zoho.cliq.getRecordById(module, recordId)` | Obtiene por ID |
| `zoho.cliq.updateRecord(module, recordId, data)` | Actualiza |
| `zoho.cliq.deleteRecord(module, recordId)` | Elimina por ID |
| `zoho.cliq.deleteRecords(module, criteria)` | Elimina por criterios |

---

## Zoho Mail (`zoho.mail.*`)

> Ref: https://www.zoho.com/deluge/help/mail-tasks.html  
> **Nota:** Estas tasks NO están disponibles en Zoho Creator.

| Task | Descripción |
|------|-------------|
| `zoho.mail.getFolders(userId)` | Obtiene todas las carpetas |
| `zoho.mail.moveToFolder(userId, mailId, folderId)` | Mueve email a carpeta |
| `zoho.mail.createFolder(userId, folderName)` | Crea una carpeta |
| `zoho.mail.setFlag(userId, mailId)` | Marca un email |
| `zoho.mail.removeFlag(userId, mailId)` | Quita la marca |
| `zoho.mail.markAsRead(userId, mailId)` | Marca como leído |
| `zoho.mail.markAsUnread(userId, mailId)` | Marca como no leído |
| `zoho.mail.getMessage(userId, mailId)` | Obtiene detalles de un email |
| `zoho.mail.createTag(userId, tagName)` | Crea una etiqueta |
| `zoho.mail.getLabels(userId)` | Obtiene todas las etiquetas |
| `zoho.mail.setTag(userId, mailId, tagId)` | Asigna etiqueta a email |

---

## Zoho Calendar (`zoho.calendar.*`)

> Ref: https://www.zoho.com/deluge/help/calendar-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.calendar.createEvent(uid, data)` | Crea un evento en Zoho Calendar |

---

## Zoho Sign (`zoho.sign.*`)

> Ref: https://www.zoho.com/deluge/help/sign-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.sign.createDocument(data)` | Sube un documento a Zoho Sign |
| `zoho.sign.getDocumentById(documentId)` | Obtiene documento por ID |
| `zoho.sign.updateDocument(documentId, data)` | Actualiza un documento |
| `zoho.sign.getFieldIds(documentId)` | Obtiene IDs de todos los campos |
| `zoho.sign.downloadDocument(documentId)` | Descarga documentos de Zoho Sign |
| `zoho.sign.submitRequest(requestId)` | Envía la solicitud especificada |
| `zoho.sign.getTemplates()` | Obtiene todas las plantillas |
| `zoho.sign.getTemplateById(templateId)` | Obtiene plantilla por ID |
| `zoho.sign.createUsingTemplate(templateId, data)` | Crea documento desde plantilla |

---

## Zoho Writer (`zoho.writer.*`)

> Ref: https://www.zoho.com/deluge/help/writer-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.writer.getDocuments(criteria)` | Lista documentos según criterio |
| `zoho.writer.uploadDocument(file)` | Sube un documento |
| `zoho.writer.shareDocument(docId, data)` | Comparte con colaboradores por rol |
| `zoho.writer.getMergeFields(docId)` | Lista campos de combinación del doc |
| `zoho.writer.mergeAndSend(docId, data)` | Combina valores y envía por email |
| `zoho.writer.signDocument(docId, data)` | Envía para recolección de firmas |
| `zoho.writer.getAllFields(docId)` | Lista todos los campos del doc |
| `zoho.writer.mergeAndSign(docId, data)` | Combina y envía para firma |
| `zoho.writer.mergeAndStoreV2(docId, data)` | Combina y almacena en WorkDrive |
| `zoho.writer.mergeAndInvoke(docId, data)` | Combina e invoca función personalizada |
| `zoho.writer.markUnmarkFavorite(docId, isFavorite)` | Marca/desmarca como favorito |
| `zoho.writer.renameDocument(docId, newName)` | Renombra el documento |
| `zoho.writer.enableDisableTrackChanges(docId, enable)` | Activa/desactiva control de cambios |
| `zoho.writer.addUpdateDescription(docId, description)` | Agrega/actualiza descripción |
| `zoho.writer.lockUnlockDocument(docId, lock)` | Bloquea/desbloquea el doc |
| `zoho.writer.markDocumentReady(docId)` | Marca el documento como listo |
| `zoho.writer.markRevertFinal(docId, isFinal)` | Habilita/deshabilita edición |
| `zoho.writer.getMergeTemplates()` | Lista plantillas de combinación |
| `zoho.writer.getSignTemplates()` | Lista plantillas de firma |
| `zoho.writer.getFillableTemplates()` | Lista plantillas rellenables |
| `zoho.writer.v2SignDocument(docId, data)` | Envía para firma y rastrea estado |
| `zoho.writer.generateFillableLink(docId, data)` | Genera link prefilled para recolección |
| `zoho.writer.mergeAndSendV2(docId, data)` | Combina y envía v2 |

---

## Zoho Sheet (`zoho.sheet.*`)

> Ref: https://www.zoho.com/deluge/help/sheet-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.sheet.getSheets(resourceId)` | Obtiene hojas de un archivo |
| `zoho.sheet.createRecords(resourceId, sheetId, data)` | Inserta datos en la hoja |
| `zoho.sheet.getRecords(resourceId, sheetId)` | Obtiene datos de la hoja |
| `zoho.sheet.updateRecords(resourceId, sheetId, criteria, data)` | Actualiza filas |
| `zoho.sheet.find(resourceId, sheetId, searchText)` | Encuentra texto específico |
| `zoho.sheet.replace(resourceId, sheetId, oldText, newText)` | Reemplaza todas las ocurrencias |
| `zoho.sheet.insertCsv(resourceId, sheetId, csvData)` | Inserta datos CSV en la hoja |

---

## Zoho WorkDrive (`zoho.workdrive.*`)

> Ref: https://www.zoho.com/deluge/help/workdrive-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.workdrive.uploadFile(folderId, file, fileName)` | Sube archivo a carpeta |
| `zoho.workdrive.createFolder(parentFolderId, folderName)` | Crea subcarpeta |
| `zoho.workdrive.createTeamFolder(teamId, folderName)` | Crea carpeta de equipo |

---

## Zoho Analytics (`zoho.analytics.*`)

> Ref: https://www.zoho.com/deluge/help/analytics-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.analytics.createRow(workspaceId, tableId, data)` | Crea una fila en una tabla |
| `zoho.analytics.deleteRow(workspaceId, tableId, criteria)` | Elimina filas por criterio |
| `zoho.analytics.updateData(workspaceId, tableId, criteria, data)` | Actualiza datos |

---

## Zoho Bookings (`zoho.bookings.*`)

> Ref: https://www.zoho.com/deluge/help/bookings-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.bookings.getWorkspaces()` | Obtiene todos los workspaces |
| `zoho.bookings.getRecordById(module, recordId)` | Obtiene registro por ID |
| `zoho.bookings.getRelatedRecords(module, recordId, subModule)` | Obtiene registros relacionados |
| `zoho.bookings.getAvailableSlots(serviceId, staffId, date)` | Obtiene slots disponibles |
| `zoho.bookings.createAppointment(data)` | Crea una cita |
| `zoho.bookings.updateRecords(recordId, data)` | Actualiza registro por ID |

---

## Zoho Connect (`zoho.connect.*`)

> Ref: https://www.zoho.com/deluge/help/connect-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.connect.myNetworks()` | Obtiene todas tus redes |
| `zoho.connect.myGroups()` | Obtiene todos tus grupos |
| `zoho.connect.addPost(networkId, data)` | Publica contenido |
| `zoho.connect.getPost(postId)` | Obtiene el contenido de un post |
| `zoho.connect.getPostByTPID(tpId)` | Obtiene post por ID de tercero |
| `zoho.connect.updatePost(postId, data)` | Actualiza un post por ID |
| `zoho.connect.deletePost(postId)` | Elimina un post por ID |
| `zoho.connect.sendMePrivateMessage(networkId, data)` | Crea mensaje privado |
| `zoho.connect.addComment(postId, data)` | Agrega comentario a un post |
| `zoho.connect.addUsersToGroup(groupId, emails)` | Agrega usuarios a grupo |

---

## Zoho Map (`zoho.map.*`)

> Ref: https://www.zoho.com/deluge/help/map-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.map.geocode(address)` | Retorna coordenadas GPS de una dirección |
| `zoho.map.reverseGeocode(lat, lng)` | Retorna dirección de coordenadas GPS |
| `zoho.map.distanceBetween(address1, address2)` | Distancia en vuelo entre direcciones |

---

## Zoho Notebook (`zoho.notebook.*`)

> Ref: https://www.zoho.com/deluge/help/notebook-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.notebook.createNotecard(data)` | Crea una tarjeta de notas con el contenido especificado |

---

## Zoho SalesIQ (`zoho.salesiq.*`)

> Ref: https://www.zoho.com/deluge/help/salesiq-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.salesiq.visitorsession.set(key, value)` | Almacena datos temporalmente durante un chat |
| `zoho.salesiq.visitorsession.get(key)` | Recupera datos temporales del chat |

---

## SDP Cloud / ServiceDesk Plus (`zoho.sdpcloud.*`)

> Ref: https://www.zoho.com/deluge/help/sdp-tasks.html

| Task | Descripción |
|------|-------------|
| `zoho.sdpcloud.createRecord(module, data)` | Crea un registro |
| `zoho.sdpcloud.getRecords(module)` | Obtiene todos los registros |
| `zoho.sdpcloud.updateRecord(module, recordId, data)` | Actualiza |
| `zoho.sdpcloud.getRecordById(module, recordId)` | Obtiene por ID |
| `zoho.sdpcloud.associate(module, recordId, subModule, data)` | Asocia subregistro |
| `zoho.sdpcloud.invokeUrl(url, method, headers, data)` | Invoca endpoints propios de SDP |

---

## invokeUrl — APIs Externas y Zoho APIs directas

La alternativa cuando las tasks nativas no cubren el endpoint necesario.

```deluge
// GET básico
resp = invokeUrl [
    url:  "https://api.ejemplo.com/endpoint"
    type: GET
];

// POST con JSON y autenticación Bearer
headers = Map();
headers.put("Content-Type", "application/json");
headers.put("Authorization", "Bearer TOKEN");

body = Map();
body.put("campo1", "valor1");

resp = invokeUrl [
    url:        "https://api.ejemplo.com/endpoint"
    type:       POST
    body:       body.toText()   // toText() genera JSON válido, toString() NO
    headers:    headers
];

// Con Conexión OAuth configurada (recomendado para Zoho APIs)
resp = invokeUrl [
    url:        "https://www.zohoapis.com/crm/v8/Contacts"
    type:       GET
    connection: "zoho_crm_connection"
];

// Con parámetros query string
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

### URLs base de APIs Zoho (para invokeUrl)

| Servicio | URL Base |
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

### Regiones de dominio

| Región | Dominio |
|--------|---------|
| US (default) | `.com` |
| EU | `.eu` |
| India | `.in` |
| Australia | `.com.au` |
| Japón | `.jp` |
| Canadá | `.ca` |

Ejemplo: `https://www.zohoapis.eu/crm/v8/Contacts` para org en Europa.

---

## Consideraciones Comunes a Todas las Tasks

### Costo de API (External Calls)

Cada ejecución de una integration task consume 1 API call del límite de External Calls de tu plan. Si la task está dentro de un `for each` que itera 5 veces, consume 5 calls, no 1.

### toString() vs toText()

```deluge
mapa = {"nombre": "Juan", "edad": 30};

mapa.toString()  // {nombre=Juan, edad=30}   ← representación Deluge, NO válida como JSON
mapa.toText()    // {"nombre":"Juan","edad":30}  ← JSON válido para APIs externas
```

**Siempre usar `.toText()` en el body de invokeUrl cuando la API espera JSON.**

### Validación de nulos por app

```deluge
// CRM — validar con != null
valor = rec.get("Campo");
if (valor != null) { /* ... */ }

// Creator — validar con != ""
valor = rec.get("Campo");
if (valor != "") { /* ... */ }

// Books/Inventory/Invoice — usar containsKey()
if (rec.containsKey("custom_field_hash")) {
    campos = rec.get("custom_field_hash");
}

// Lookup fields (cualquier app) — proteger la cadena .get()
lookup = ifNull(rec.get("Cuenta"), {"id": "", "name": ""});
id = lookup.get("id");
```
