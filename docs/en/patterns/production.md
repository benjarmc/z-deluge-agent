# Production Patterns — Deluge and Zoho APIs

> Language: English. Spanish: see the matching path under docs/es/.

> Battle-tested patterns, known gotchas, and null-safety rules for production Deluge code.

---

## 1. Null Safety — Rules by App

This is the most frequent error in Deluge. Validation rules differ depending on the application the data comes from.

### Rules Table

| App | Validate with | Example |
|-----|-------------|---------|
| Zoho CRM | `!= null` | `if (rec.get("Phone") != null)` |
| Zoho Creator | `!= ""` | `if (campo != "")` |
| Zoho Books | `containsKey()` | `if (rec.containsKey("custom_field_hash"))` |
| Zoho Inventory | `containsKey()` | same as Books |
| Zoho Invoice | `containsKey()` | same as Books |
| Zoho Desk | `!= null` | `if (rec.get("status") != null)` |
| Zoho People | `!= null && != ""` | double validation recommended |

### Safe Pattern for Lookup Fields

```deluge
// ❌ BAD — fails if the lookup is null
accountId = rec.get("Account_Name").get("id");

// ✅ GOOD — shield with ifNull
accountLookup = ifNull(rec.get("Account_Name"), {"id": "", "name": ""});
accountId = accountLookup.get("id");

// ✅ ALSO VALID — check first
if (rec.get("Account_Name") != null) {
    accountId = rec.get("Account_Name").get("id");
}
```

### Pattern for Empty Lists

```deluge
// searchRecords or getRecords result may be empty
resultados = zoho.crm.searchRecords("Contacts", "(Email:equals:" + email + ")");

// ❌ BAD
primerContacto = resultados.get("data").get(0);

// ✅ GOOD
data = resultados.get("data");
if (data != null && data.size() > 0) {
    primerContacto = data.get(0);
}
```

### Pattern for Finance Custom Fields

```deluge
// Books, Inventory, Invoice store custom fields in "custom_field_hash"
rec = zoho.books.getRecordById("Invoices", invoiceId, orgId);

if (rec.containsKey("custom_field_hash")) {
    cf = rec.get("custom_field_hash");
    rfc = ifNull(cf.get("cf_rfc"), "");
    folio = ifNull(cf.get("cf_folio_fiscal"), "");
}
```

---

## 2. API Limits — Reference Table

### By Application

| App | Limit | Unit | Error |
|-----|--------|--------|-------|
| Zoho Creator | 50 calls | /user/minute | HTTP 429 code 2955 |
| Zoho CRM | 25,000 | /day (integration tasks) | HTTP 429 |
| Zoho Books | 100 calls | /minute/org | HTTP 429 |
| Zoho Desk | depends on edition | credits/day | HTTP 429 |
| Zoho Inventory | 100 calls | /minute/org | HTTP 429 |
| Zoho Projects | 300 calls | /minute | HTTP 429 |

### Deluge Statement Limits by App

| App | Statements | Per execution |
|-----|-----------|---------------|
| Zoho CRM (function) | 2,000 – 10,000 | depends on plan |
| Zoho Creator (Free) | 1,000 | per function |
| Zoho Creator (Standard+) | 5,000 – 50,000 | depends on plan and environment |

### Universal Pagination Pattern

When the total is known, use the **dynamic** loop (`repeat` + `toList`). Fixed N: a literal list.

```deluge
batchSize = 100;
total = fullList.toList().size();
numOfPages = (total / batchSize).ceil();
lst_pageLoop = "0,".repeat(numOfPages).toList(",");
for each index pageNumber in lst_pageLoop
{
    offset = pageNumber * batchSize;
    // process batch pageNumber
}
```

```deluge
// Static — N is known
for each i in {1,2,3,4,5,6}
{
    info i;
}
```

When the total is unknown (e.g. CRM `getRecords`):

```deluge
pagina   = 1;
porPagina = 200;
todos    = list();
hayMas   = true;
maxPages = 50;
lst_pageLoop = "0,".repeat(maxPages).toList(",");

for each index pageNumber in lst_pageLoop
{
    if(hayMas)
    {
        resp = zoho.crm.getRecords("Contacts", pagina, porPagina);
        data = resp.get("data");
        if (data == null || data.size() == 0) {
            hayMas = false;
        } else {
            todos.addAll(data);
            if (data.size() < porPagina) {
                hayMas = false;
            } else {
                pagina = pagina + 1;
            }
        }
    }
}

info "Total registros obtenidos: " + todos.size().toString();
```

---

## 3. Common Gotchas

### toString() vs toText() — Critical for APIs

```deluge
mapa = {"nombre": "Juan Pérez", "edad": 30, "activo": true};

// toString() → Deluge representation, INVALID as JSON
mapa.toString()  // "{nombre=Juan Pérez, edad=30, activo=true}"

// toText()   → valid JSON for external APIs
mapa.toText()    // '{"nombre":"Juan Pérez","edad":30,"activo":true}'

// ✅ ALWAYS use toText() in invokeUrl body
resp = invokeUrl [
    url:     "https://api.externo.com/endpoint"
    type:    POST
    body:    mapa.toText()     // ← toText()
    headers: {"Content-Type": "application/json"}
];
```

### `connection` must be a quoted literal

Deluge binds `connection` at save time. Do not assign the link name to a variable.

```deluge
// ✅
connection: "connection_name"

// ❌ rejected
connName = "connection_name";
connection: connName
```

Same rule on `invokeapi`.

### Updating Lookup Fields in CRM

```deluge
// ❌ BAD — does not work, sends the name, not the ID
datos = Map();
datos.put("Account_Name", "Acme Corp");
zoho.crm.updateRecord("Contacts", recId, datos);

// ✅ GOOD — send an object with the ID
datos = Map();
datos.put("Account_Name", {"id": "66359000000015039"});
zoho.crm.updateRecord("Contacts", recId, datos);
```

### CRM IDs Are Strings, Not Numbers

```deluge
// ❌ BAD — numeric comparison fails
if (record.get("id") == 66359000000015039) { /* ... */ }

// ✅ GOOD — always convert to string
recordId = record.get("id").toString();
if (recordId == "66359000000015039") { /* ... */ }
```

### Dates and Time Zone

```deluge
// today() returns Date type in the org time zone
hoy = today();

// now() returns DateTime in the org time zone
ahora = now();

// To send to an API that expects ISO 8601
fechaISO = hoy.toString("yyyy-MM-dd");
datetimeISO = ahora.toString("yyyy-MM-dd'T'HH:mm:ssZ");

// Time zone conversion
enCDMX = ahora.toTimeZone("America/Mexico_City");
enUTC  = ahora.toTimeZone("UTC");
```

### CRM: fields returns a Map, not a direct value

```deluge
// When updating multiple records with bulkUpdateRecords,
// the response contains "data" which is a list of Maps
resp = zoho.crm.bulkUpdateRecords("Contacts", updateList);
data = resp.get("data");

if (data != null) {
    for each item in data {
        status = item.get("status");  // "success" or "error"
        if (status == "error") {
            errorMsg = item.get("message");
            info "Error en registro " + item.get("details").get("id") + ": " + errorMsg;
        }
    }
}
```

### Creator: insert returns ID as Integer, not String

```deluge
// In Creator, the new record ID comes as a number
resp = insert into MiFormulario [Campo1 = "valor"];
nuevoId = resp.ID;  // it is an integer

// To use it in other tasks, convert
idStr = nuevoId.toString();
```

### Loops and statement limit

```deluge
// ❌ BAD — multiple .get() calls inside a loop each count as one
for each rec in registros {
    nombre  = rec.get("First_Name");   // +1 statement
    email   = rec.get("Email");        // +1 statement
    ciudad  = rec.get("City");         // +1 statement
    empresa = rec.get("Account_Name"); // +1 statement
    // ... 5 fields × 1000 records = 5000 statements just here
}

// ✅ BETTER — reduce operations, pre-filter in the query
campos = {"id", "First_Name", "Email"};  // request only what is needed
```

---

## 4. Cross-App Synchronization

### CRM → Creator Pattern

```deluge
// Function in CRM (Button or Workflow) that syncs to Creator
// Use when the record is created/updated in CRM and must be reflected in Creator

crmRecord = input;  // the CRM record that triggered the function

// 1. Check whether it already exists in Creator
existente = zoho.creator.getRecords("mi_app", "Reporte_Sincronizados");
datos = existente.get("data");

creatorRecId = null;
for each rec in datos {
    if (rec.get("CRM_ID") == crmRecord.get("id").toString()) {
        creatorRecId = rec.get("ID");
        break;
    }
}

// 2. Create or update
payload = Map();
payload.put("CRM_ID",    crmRecord.get("id").toString());
payload.put("Nombre",    crmRecord.get("First_Name") + " " + crmRecord.get("Last_Name"));
payload.put("Email",     ifNull(crmRecord.get("Email"), ""));
payload.put("Ultima_Sync", now().toString("yyyy-MM-dd HH:mm:ss"));

if (creatorRecId == null) {
    zoho.creator.createRecord("mi_app", "Formulario_Sync", payload);
} else {
    zoho.creator.updateRecordById("mi_app", "Reporte_Sincronizados", creatorRecId, payload);
}
```

### Creator → CRM Pattern (Bidirectional)

```deluge
// Workflow in Creator (On Submit) that updates CRM
// Prevent loops: only sync if the change comes from the user, not from the sync

if (input.Origen_Cambio != "Sync_Automatica") {
    crmId = input.CRM_ID;

    if (crmId != "") {
        // Update in CRM
        updateData = Map();
        updateData.put("Phone",       input.Telefono);
        updateData.put("Mailing_City", input.Ciudad);

        resp = zoho.crm.updateRecord("Contacts", crmId, updateData);
    } else {
        // Create new in CRM
        newData = Map();
        newData.put("First_Name", input.Nombre);
        newData.put("Email",      input.Email);
        newData.put("Phone",      input.Telefono);

        resp = zoho.crm.createRecord("Contacts", newData);
        crmId = resp.get("data").get(0).get("id");

        // Save the CRM ID back in Creator
        update Reporte_Contactos[ID == input.ID] [
            CRM_ID = crmId
        ];
    }
}
```

### CRM → Books Pattern (Invoicing)

```deluge
// Create an invoice in Books when a Deal is closed in CRM
dealId   = crmRecord.get("id");
dealName = crmRecord.get("Deal_Name");
amount   = crmRecord.get("Amount");
accountId = crmRecord.get("Account_Name").get("id");

// Look up the Books contact by CRM Account ID (Books API v3)
booksContactResp = invokeUrl [
    url:        "https://www.zohoapis.com/books/v3/crm/account/" + accountId + "/import?organization_id=" + booksOrgId
    type:       POST
    headers:    {"Content-Type": "application/json"}
    connection: "zoho_books_conn"
];

booksContactId = booksContactResp.get("contact").get("contact_id");

// Create the invoice
lineItems = list();
item = Map();
item.put("name",  dealName);
item.put("rate",  amount);
item.put("quantity", 1);
lineItems.add(item);

invoiceData = Map();
invoiceData.put("customer_id", booksContactId);
invoiceData.put("line_items",  lineItems);
invoiceData.put("due_date",    addDays(today(), 30).toString("yyyy-MM-dd"));

resp = zoho.books.createRecord("invoices", booksOrgId, invoiceData);
invoiceId = resp.get("invoice").get("invoice_id");
invoiceUrl = resp.get("invoice").get("invoice_url");

// Update the Deal with the invoice ID
updateDeal = Map();
updateDeal.put("Factura_Books_ID", invoiceId);
updateDeal.put("Factura_URL",      invoiceUrl);
zoho.crm.updateRecord("Deals", dealId, updateDeal);
```

---

## 5. Structured Log Pattern

```deluge
// Reusable log function (in Creator)
void escribirLog(string modulo, string operacion, string nivel, string mensaje, string recordId) {
    insert into App_Logs [
        Modulo      = modulo
        Operacion   = operacion
        Nivel       = nivel        // "INFO", "WARN", "ERROR"
        Mensaje     = mensaje
        Record_ID   = recordId
        Fecha_Hora  = now()
        Usuario     = zoho.loginuser
        Entorno     = thisapp.environment
    ];
}

// Use in any workflow
try {
    resp = zoho.crm.createRecord("Contacts", datos);
    thisapp.escribirLog("CRM", "Crear_Contacto", "INFO", "Contacto creado: " + resp.get("data").get(0).get("id"), "");
} catch (e) {
    thisapp.escribirLog("CRM", "Crear_Contacto", "ERROR", e.toString(), recordId);
    // Do not rethrow the error if the log is sufficient
}
```

---

## 6. Environment Detection in Creator

```deluge
// thisapp.environment available since 2025
entorno = thisapp.environment;  // "Development" | "Stage" | "Production"

if (entorno == "Production") {
    webhookUrl = "https://hooks.empresa.com/prod/zoho";
    slackChannel = "#alertas-prod";
} else if (entorno == "Stage") {
    webhookUrl = "https://hooks.empresa.com/stage/zoho";
    slackChannel = "#alertas-stage";
} else {
    // Development — do not send external notifications
    info "Modo Development — integración externa omitida";
    return;
}

// Send webhook only in Stage/Prod
resp = invokeUrl [
    url:     webhookUrl
    type:    POST
    body:    {"event": "nuevo_registro", "id": recordId}.toText()
    headers: {"Content-Type": "application/json"}
];
```

---

## 7. Error Handling in invokeUrl

```deluge
try {
    resp = invokeUrl [
        url:        "https://api.externa.com/endpoint"
        type:       POST
        body:       payload.toText()
        headers:    headers
        connection: "mi_conexion"
    ];

    // Verify that the response has the expected format
    if (resp == null) {
        throw "La API no devolvió respuesta";
    }

    if (!resp.containsKey("data")) {
        statusCode = ifNull(resp.get("status"), "desconocido");
        errorMsg   = ifNull(resp.get("message"), resp.toString());
        throw "Error en API: " + statusCode + " — " + errorMsg;
    }

    return resp.get("data");

} catch (e) {
    thisapp.escribirLog("API_Externa", "POST_Endpoint", "ERROR", e.toString(), "");
    // Send a Cliq alert to the errors channel
    alerta = Map();
    alerta.put("text", "🚨 Error en integración: " + e.toString());
    zoho.cliq.postToChannel("alertas-dev", alerta);
    return null;
}
```
