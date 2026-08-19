# REST APIs — Other Zoho Products

> Language: English. Spanish: see the matching path under docs/es/.

> Coverage of APIs with the largest surface area in development.  
> For each app: base URL, scopes, modules, and main endpoints.

---

## Zoho Inventory API v1

> **Base URL:** `https://www.zohoapis.com/inventory/v1/`  
> **Auth:** OAuth 2.0  
> **Required parameter:** `?organization_id={orgId}`

### Scopes
```
ZohoInventory.contacts.ALL
ZohoInventory.items.ALL
ZohoInventory.salesorders.ALL
ZohoInventory.invoices.ALL
ZohoInventory.purchaseorders.ALL
ZohoInventory.bills.ALL
ZohoInventory.settings.ALL
```

### Modules and Endpoints

```
GET    /organizations
GET    /contacts
POST   /contacts
GET    /contacts/{contact_id}
PUT    /contacts/{contact_id}
DELETE /contacts/{contact_id}

GET    /items
POST   /items
GET    /items/{item_id}
PUT    /items/{item_id}
DELETE /items/{item_id}
POST   /items/{item_id}/active
POST   /items/{item_id}/inactive

GET    /itemgroups                         ← product groups
POST   /itemgroups
GET    /itemgroups/{group_id}
PUT    /itemgroups/{group_id}

GET    /salesorders
POST   /salesorders
GET    /salesorders/{so_id}
PUT    /salesorders/{so_id}
DELETE /salesorders/{so_id}
POST   /salesorders/{so_id}/confirmed
POST   /salesorders/{so_id}/closed
POST   /salesorders/{so_id}/void
POST   /salesorders/{so_id}/email
POST   /salesorders/{so_id}/packages      ← create shipping package
GET    /salesorders/{so_id}/packages

GET    /packages
POST   /packages
GET    /packages/{package_id}
PUT    /packages/{package_id}
DELETE /packages/{package_id}
POST   /packages/{package_id}/shipments   ← create shipment

GET    /shipments
POST   /shipments
GET    /shipments/{shipment_id}
PUT    /shipments/{shipment_id}

GET    /invoices
POST   /invoices
GET    /invoices/{invoice_id}
PUT    /invoices/{invoice_id}
DELETE /invoices/{invoice_id}
POST   /invoices/{invoice_id}/email

GET    /purchaseorders
POST   /purchaseorders
GET    /purchaseorders/{po_id}
PUT    /purchaseorders/{po_id}
DELETE /purchaseorders/{po_id}
POST   /purchaseorders/{po_id}/issued
POST   /purchaseorders/{po_id}/billed

GET    /bills
POST   /bills
GET    /bills/{bill_id}
PUT    /bills/{bill_id}
DELETE /bills/{bill_id}

GET    /warehouses
POST   /warehouses
GET    /warehouses/{warehouse_id}
PUT    /warehouses/{warehouse_id}

GET    /taxes
POST   /taxes
GET    /taxes/{tax_id}
PUT    /taxes/{tax_id}
DELETE /taxes/{tax_id}
```

### From Deluge (native task)
```deluge
// Get items from Inventory
orgId = "10234695";
resp = zoho.inventory.getRecords("Items", orgId);
items = resp.get("items");

for each item in items {
    info item.get("name") + " — Stock: " + item.get("stock_on_hand").toString();
}

// Create sales order
soData = Map();
soData.put("customer_id", "460000000026041");
soData.put("line_items", [{"item_id": "460000000044208", "quantity": 5}]);

resp = zoho.inventory.createRecord("SalesOrders", orgId, soData);
soId = resp.get("salesorder").get("salesorder_id");
```

---

## Zoho People API v2

> **Base URL:** `https://people.zoho.com/people/api/`  
> **Auth:** OAuth 2.0  
> **Note:** API with a different structure from other Zoho apps

### Scopes
```
ZOHOPEOPLE.employee.ALL
ZOHOPEOPLE.forms.ALL
ZOHOPEOPLE.leave.ALL
ZOHOPEOPLE.attendance.ALL
ZOHOPEOPLE.shift.ALL
ZOHOPEOPLE.payslip.READ
```

### Modules and Endpoints

```
// Employees
GET    /forms/P_Employee/getRecords
POST   /forms/P_Employee/insertRecord
GET    /forms/P_Employee/getDataByID?recordId={id}
POST   /forms/P_Employee/updateRecord

// Custom forms
GET    /forms/{form_link_name}/getRecords
POST   /forms/{form_link_name}/insertRecord
POST   /forms/{form_link_name}/updateRecord
GET    /forms/{form_link_name}/getDataByID?recordId={id}

// Leave
GET    /leave/getholidaylist
GET    /leave/getLeaveTypeList
GET    /leave/getUserLeaveList?userId={id}&from={date}&to={date}
POST   /leave/applyLeave
POST   /leave/approveLeave
POST   /leave/rejectLeave

// Attendance
GET    /attendance/getUserAttendance?userId={id}&dateFormat=dd/MM/yyyy&from={date}&to={date}
POST   /attendance/addManualEntry

// Shifts
GET    /shift/getShiftDetails
POST   /shift/assignShiftToUser

// Departments and designations
GET    /forms/P_Department/getRecords
GET    /forms/P_Designation/getRecords
```

### From Deluge
```deluge
// Get employees from People
resp = zoho.people.getRecords("employee");
empleados = resp.get("response").get("result");

for each emp in empleados {
    nombre = emp.get("First Name") + " " + emp.get("Last Name");
    info nombre;
}

// Create record in a custom form
datos = Map();
datos.put("Nombre_Empleado", "Juan");
datos.put("Departamento", "Ventas");

resp = zoho.people.create("mi_formulario_custom", datos);
```

---

## Zoho Projects API v3

> **Base URL:** `https://projectsapi.zoho.com/restapi/portal/{portal_id}/`  
> **Auth:** OAuth 2.0  
> **First step:** get portal_id with `GET /portals/`

### Scopes
```
ZohoProjects.portals.ALL
ZohoProjects.projects.ALL
ZohoProjects.tasks.ALL
ZohoProjects.milestones.ALL
ZohoProjects.issues.ALL
ZohoProjects.users.ALL
ZohoProjects.timesheets.ALL
```

### Endpoints

```
// Portals
GET    /portals/

// Projects
GET    /portals/{portal_id}/projects/
POST   /portals/{portal_id}/projects/
GET    /portals/{portal_id}/projects/{project_id}/
PUT    /portals/{portal_id}/projects/{project_id}/
DELETE /portals/{portal_id}/projects/{project_id}/

// Tasks
GET    /portals/{portal_id}/projects/{project_id}/tasks/
POST   /portals/{portal_id}/projects/{project_id}/tasks/
GET    /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/
PUT    /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/
DELETE /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/
POST   /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/subtasks/

// Milestones
GET    /portals/{portal_id}/projects/{project_id}/milestones/
POST   /portals/{portal_id}/projects/{project_id}/milestones/
PUT    /portals/{portal_id}/projects/{project_id}/milestones/{milestone_id}/
DELETE /portals/{portal_id}/projects/{project_id}/milestones/{milestone_id}/

// Issues / Bugs
GET    /portals/{portal_id}/projects/{project_id}/bugs/
POST   /portals/{portal_id}/projects/{project_id}/bugs/
GET    /portals/{portal_id}/projects/{project_id}/bugs/{bug_id}/
PUT    /portals/{portal_id}/projects/{project_id}/bugs/{bug_id}/
DELETE /portals/{portal_id}/projects/{project_id}/bugs/{bug_id}/

// Project users
GET    /portals/{portal_id}/projects/{project_id}/users/
POST   /portals/{portal_id}/projects/{project_id}/users/

// Timelogs
GET    /portals/{portal_id}/projects/{project_id}/logs/
POST   /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/logs/
PUT    /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/logs/{log_id}/
DELETE /portals/{portal_id}/projects/{project_id}/tasks/{task_id}/logs/{log_id}/

// Forums
GET    /portals/{portal_id}/projects/{project_id}/forums/
POST   /portals/{portal_id}/projects/{project_id}/forums/
GET    /portals/{portal_id}/projects/{project_id}/forums/{forum_id}/
POST   /portals/{portal_id}/projects/{project_id}/forums/{forum_id}/posts/
```

### From Deluge
```deluge
// Create a task in Projects from CRM
portales = zoho.projects.getPortals();
portalId = portales.get("portals").get(0).get("id_string");

proyectos = zoho.projects.getProjectDetails(portalId);
projectId = proyectos.get("projects").get(0).get("id_string");

tareaData = Map();
tareaData.put("name", "Seguimiento cliente: " + crmDeal.get("Deal_Name"));
tareaData.put("person_responsible", responsableEmail);
tareaData.put("priority", "High");
tareaData.put("due_date", addDays(today(), 7).toString("MM-dd-yyyy"));

resp = zoho.projects.create("tasks", portalId, projectId, tareaData);
taskId = resp.get("tasks").get(0).get("id_string");
```

---

## Zoho Recruit API v2

> **Base URL:** `https://recruit.zoho.com/recruit/v2/`  
> **Auth:** OAuth 2.0

### Scopes
```
ZohoRecruit.modules.ALL
ZohoRecruit.settings.ALL
ZohoRecruit.bulk.ALL
```

### Standard Modules
`JobOpenings`, `Candidates`, `Contacts`, `Clients`, `Applications`, `Interviews`, `Offers`, `Tasks`, `Events`, `Calls`, `Notes`

### Endpoints

```
GET    /{module}
GET    /{module}/{record_id}
POST   /{module}
PUT    /{module}
DELETE /{module}
POST   /{module}/upsert
GET    /{module}/search?criteria=...

// Files
POST   /{module}/{record_id}/Attachments
GET    /{module}/{record_id}/Attachments
DELETE /{module}/{record_id}/Attachments/{attach_id}

// Related
GET    /{module}/{record_id}/{related_module}
POST   /{module}/{record_id}/{related_module}

// Recruit-specific actions
POST   /Candidates/{id}/status        ← change candidate status
POST   /Candidates/{id}/associate     ← associate to job opening
GET    /JobOpenings/{id}/Candidates   ← candidates for a vacancy
POST   /Interviews/{id}/reschedule    ← reschedule interview
```

### From Deluge
```deluge
// Search candidate by email
resp = zoho.recruit.searchRecords("Candidates", "(Email:equals:" + email + ")");
candidatos = resp.get("data");

if (candidatos != null && candidatos.size() > 0) {
    candidato = candidatos.get(0);
    info "Candidato encontrado: " + candidato.get("Full_Name");
} else {
    // Create new candidate
    nuevoCandidate = Map();
    nuevoCandidate.put("First_Name", firstName);
    nuevoCandidate.put("Last_Name", lastName);
    nuevoCandidate.put("Email", email);
    nuevoCandidate.put("Experience_in_Years", "3");

    resp = zoho.recruit.addRecords("Candidates", nuevoCandidate);
    candidateId = resp.get("data").get(0).get("id");
}
```

---

## Zoho Sign API v1

> **Base URL:** `https://sign.zoho.com/api/v1/`  
> **Auth:** OAuth 2.0

### Scopes
```
ZohoSign.documents.ALL
ZohoSign.templates.READ
ZohoSign.documents.CREATE
ZohoSign.documents.READ
ZohoSign.documents.UPDATE
ZohoSign.documents.DELETE
```

### Endpoints

```
// Documents
POST   /requests                               ← create/upload document
GET    /requests                               ← list documents
GET    /requests/{request_id}                 ← get
PUT    /requests/{request_id}                 ← update
DELETE /requests/{request_id}                 ← delete
POST   /requests/{request_id}/submit          ← send for signature
POST   /requests/{request_id}/recall          ← cancel send
POST   /requests/{request_id}/remind          ← reminder
GET    /requests/{request_id}/pdf             ← download signed PDF

// Templates
GET    /templates                              ← list templates
GET    /templates/{template_id}               ← get template
POST   /templates/{template_id}/createrequest ← create doc from template

// Signature fields
GET    /requests/{request_id}/fields          ← document fields

// Signer status
GET    /requests/{request_id}/recipients
```

### Typical signing flow

```deluge
// 1. Upload document and configure signers
signData = Map();
signData.put("request_name", "Contrato " + clienteNombre);
signData.put("expiry_days", 30);
signData.put("is_sequential", true);

recipients = list();
firmante = Map();
firmante.put("recipient_name", clienteNombre);
firmante.put("recipient_email", clienteEmail);
firmante.put("action_type", "SIGN");
firmante.put("signing_order", 1);
recipients.add(firmante);

signData.put("actions", recipients);

// 2. Create document in Sign
requestBody = Map();
requestBody.put("requests", signData);

resp = zoho.sign.createDocument(requestBody);
requestId = resp.get("requests").get("request_id");

// 3. Send for signature
zoho.sign.submitRequest(requestId);

// 4. Download when signed (in another workflow)
if (docStatus == "completed") {
    pdfContent = zoho.sign.downloadDocument(requestId);
    // Attach to the CRM record
    zoho.crm.attachFile("Deals", dealId, pdfContent);
}
```

---

## Zoho Analytics API v2 (CloudSQL)

> **Base URL:** `https://analyticsapi.zoho.com/api/v2/`  
> **Auth:** OAuth 2.0  
> **Primary use from Deluge:** `zoho.analytics.*` tasks or `invokeUrl`

### Scopes
```
ZohoAnalytics.data.ALL
ZohoAnalytics.data.READ
ZohoAnalytics.data.CREATE
ZohoAnalytics.data.UPDATE
ZohoAnalytics.data.DELETE
ZohoAnalytics.modeling.ALL
ZohoAnalytics.publish.ALL
```

### CloudSQL API (for SQL queries)

```
POST   /bulk/{owner_email}/{workspace_name}/data
```

Query string parameters:
```
CONFIG={"responseFormat":"json"}
ZOHO_ACTION=IMPORT | EXPORT | DELETE | UPDATE
ZOHO_IMPORT_TYPE=TRUNCATEADD | UPDATEADD | REPLACE
ZOHO_MATCHING_COLUMNS=col1,col2
ZOHO_SQLQUERY=SELECT * FROM "Sales" WHERE Region='East'
```

### From Deluge

```deluge
// Add row to Analytics from a Creator workflow
workspaceId = "460000000012345";
tableId = "460000000012346";

rowData = Map();
rowData.put("Fecha", today().toString("yyyy-MM-dd"));
rowData.put("Monto", totalVentas);
rowData.put("Region", "CDMX");
rowData.put("Vendedor", vendedorNombre);

resp = zoho.analytics.createRow(workspaceId, tableId, rowData);

// Update data by criteria
criteria = "Fecha = '" + today().toString("yyyy-MM-dd") + "' AND Region = 'CDMX'";
updateData = Map();
updateData.put("Monto", nuevoTotal);
resp = zoho.analytics.updateData(workspaceId, tableId, criteria, updateData);

// Delete rows
resp = zoho.analytics.deleteRow(workspaceId, tableId, criteria);

// SQL query via invokeUrl
sqlQuery = "SELECT Region, SUM(Monto) AS Total FROM \"Ventas\" GROUP BY Region";
params = Map();
params.put("ZOHO_ACTION", "EXPORT");
params.put("ZOHO_OUTPUT_FORMAT", "JSON");
params.put("ZOHO_SQLQUERY", sqlQuery);
params.put("ZOHO_ERROR_FORMAT", "JSON");

resp = invokeUrl [
    url:        "https://analyticsapi.zoho.com/api/v2/" + ownerEmail + "/" + workspaceName
    type:       GET
    parameters: params
    connection: "zoho_analytics_conn"
];
```

---

## Zoho WorkDrive API v1

> **Base URL:** `https://www.zohoapis.com/workdrive/api/v1/`  
> **Auth:** OAuth 2.0

### Scopes
```
WorkDrive.workspace.ALL
WorkDrive.files.ALL
WorkDrive.teamfolders.ALL
WorkDrive.users.READ
```

### Main Endpoints

```
// Team Folders
GET    /teamfolders
POST   /teamfolders
GET    /teamfolders/{tf_id}
PUT    /teamfolders/{tf_id}
DELETE /teamfolders/{tf_id}
GET    /teamfolders/{tf_id}/files

// Folders
POST   /files                                  ← create folder
GET    /files/{folder_id}
PUT    /files/{folder_id}
DELETE /files/{folder_id}
GET    /files/{folder_id}/files               ← contents

// Files
POST   /upload                                 ← upload file
GET    /files/{file_id}/download              ← download
PUT    /files/{file_id}                        ← update metadata
DELETE /files/{file_id}
POST   /files/{file_id}/copy
POST   /files/{file_id}/move

// Sharing
POST   /files/{file_id}/permissions
GET    /files/{file_id}/permissions
PUT    /files/{file_id}/permissions/{perm_id}
DELETE /files/{file_id}/permissions/{perm_id}

// Search
GET    /files/search?search_string={query}
```

### Header `Accept` (JSON:API)

Several WorkDrive endpoints return **JSON:API**. Without this header the response may be empty, malformed, or an HTML error. Use it on GET file/metadata calls (`/files/{id}`, listings, etc.):

```deluge
header = Map();
header.put("Accept", "application/vnd.api+json");
uri = "https://www.zohoapis.eu/workdrive/api/v1/files/" + resource_id;
response = invokeurl
[
	url :uri
	type :GET
	headers:header
	connection:"workdrive"
];
```

Match the domain to the org data center (`.com`, `.eu`, `.in`, `.com.au`). Native `zoho.workdrive.*` tasks do not need this header; **`invokeurl` REST calls do**.

### From Deluge

```deluge
// Upload file to WorkDrive
folderId = "tu_folder_id";
archivo = input.Adjunto;  // File-type field

resp = zoho.workdrive.uploadFile(folderId, archivo, "reporte_ventas.pdf");
fileId = resp.get("data").get("id");

// Create folder for the client
resp = zoho.workdrive.createFolder(folderId, "Cliente_" + clienteNombre);
nuevaCarpeta = resp.get("data").get("id");
```

---

## Zoho Billing (formerly Subscriptions) API v1

> **Base URL:** `https://www.zohoapis.com/billing/v1/`  
> **Auth:** OAuth 2.0  
> **Parameter:** `?organization_id={orgId}`

### Modules and Endpoints

```
GET    /organizations                          ← get org ID

// Customers
GET    /customers
POST   /customers
GET    /customers/{customer_id}
PUT    /customers/{customer_id}
DELETE /customers/{customer_id}
GET    /customers/{customer_id}/subscriptions

// Subscriptions
GET    /subscriptions
POST   /subscriptions
GET    /subscriptions/{sub_id}
PUT    /subscriptions/{sub_id}
POST   /subscriptions/{sub_id}/cancel
POST   /subscriptions/{sub_id}/reactivate
POST   /subscriptions/{sub_id}/buy_addon

// Plans
GET    /plans
POST   /plans
GET    /plans/{plan_code}
PUT    /plans/{plan_code}
DELETE /plans/{plan_code}

// Addons
GET    /addons
POST   /addons
GET    /addons/{addon_code}
PUT    /addons/{addon_code}
DELETE /addons/{addon_code}

// Coupons
GET    /coupons
POST   /coupons
GET    /coupons/{coupon_code}
PUT    /coupons/{coupon_code}

// Invoices
GET    /invoices
GET    /invoices/{invoice_id}

// Payments
GET    /payments
POST   /payments/{payment_id}/refund

// Products
GET    /products
POST   /products
GET    /products/{product_id}
PUT    /products/{product_id}
```

### From Deluge

```deluge
// Create subscription for a customer
orgId = "10234695";
subData = Map();
subData.put("customer_id", customerId);
subData.put("plan", {"plan_code": "monthly_basic", "quantity": 1});
subData.put("starts_at", today().toString("yyyy-MM-dd"));

resp = zoho.billing.create("subscriptions", orgId, subData);
subId = resp.get("subscription").get("subscription_id");
```
