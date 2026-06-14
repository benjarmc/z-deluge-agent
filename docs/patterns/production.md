# Patrones de Producción — Deluge y APIs Zoho

> Patterns battle-tested, gotchas conocidos y reglas de null safety para código Deluge en producción.

---

## 1. Null Safety — Reglas por App

Este es el error más frecuente en Deluge. Las reglas de validación difieren según la aplicación origen del dato.

### Tabla de Reglas

| App | Validar con | Ejemplo |
|-----|-------------|---------|
| Zoho CRM | `!= null` | `if (rec.get("Phone") != null)` |
| Zoho Creator | `!= ""` | `if (campo != "")` |
| Zoho Books | `containsKey()` | `if (rec.containsKey("custom_field_hash"))` |
| Zoho Inventory | `containsKey()` | igual que Books |
| Zoho Invoice | `containsKey()` | igual que Books |
| Zoho Desk | `!= null` | `if (rec.get("status") != null)` |
| Zoho People | `!= null && != ""` | doble validación recomendada |

### Patrón Seguro para Lookup Fields

```deluge
// ❌ MALO — falla si el lookup es null
accountId = rec.get("Account_Name").get("id");

// ✅ BUENO — escudo con ifNull
accountLookup = ifNull(rec.get("Account_Name"), {"id": "", "name": ""});
accountId = accountLookup.get("id");

// ✅ TAMBIÉN VÁLIDO — verificar antes
if (rec.get("Account_Name") != null) {
    accountId = rec.get("Account_Name").get("id");
}
```

### Patrón para Listas Vacías

```deluge
// Resultado de searchRecords o getRecords puede estar vacío
resultados = zoho.crm.searchRecords("Contacts", "(Email:equals:" + email + ")");

// ❌ MALO
primerContacto = resultados.get("data").get(0);

// ✅ BUENO
data = resultados.get("data");
if (data != null && data.size() > 0) {
    primerContacto = data.get(0);
}
```

### Patrón para Custom Fields de Finance

```deluge
// Books, Inventory, Invoice guardan custom fields en "custom_field_hash"
rec = zoho.books.getRecordById("Invoices", invoiceId, orgId);

if (rec.containsKey("custom_field_hash")) {
    cf = rec.get("custom_field_hash");
    rfc = ifNull(cf.get("cf_rfc"), "");
    folio = ifNull(cf.get("cf_folio_fiscal"), "");
}
```

---

## 2. Límites de API — Tabla de Referencia

### Por Aplicación

| App | Límite | Unidad | Error |
|-----|--------|--------|-------|
| Zoho Creator | 50 calls | /usuario/minuto | HTTP 429 code 2955 |
| Zoho CRM | 25,000 | /día (integration tasks) | HTTP 429 |
| Zoho Books | 100 calls | /minuto/org | HTTP 429 |
| Zoho Desk | según edición | créditos/día | HTTP 429 |
| Zoho Inventory | 100 calls | /minuto/org | HTTP 429 |
| Zoho Projects | 300 calls | /minuto | HTTP 429 |

### Límites de Statements Deluge por App

| App | Statements | Por ejecución |
|-----|-----------|---------------|
| Zoho CRM (función) | 2,000 – 10,000 | según plan |
| Zoho Creator (Free) | 1,000 | por función |
| Zoho Creator (Standard+) | 5,000 – 50,000 | según plan y entorno |

### Patrón de Paginación Universal

```deluge
// Para cualquier módulo con más de 200 registros
pagina   = 1;
porPagina = 200;
todos    = list();
hayMas   = true;

// Función de guardia para evitar loops infinitos (max 50 páginas = 10,000 registros)
list getGuardList() {
    return "".leftpad(49).toList("");
}

for each i in getGuardList() {
    if (!hayMas) { break; }

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

info "Total registros obtenidos: " + todos.size().toString();
```

---

## 3. Gotchas Frecuentes

### toString() vs toText() — Crítico para APIs

```deluge
mapa = {"nombre": "Juan Pérez", "edad": 30, "activo": true};

// toString() → representación Deluge, INVÁLIDA como JSON
mapa.toString()  // "{nombre=Juan Pérez, edad=30, activo=true}"

// toText()   → JSON válido para APIs externas
mapa.toText()    // '{"nombre":"Juan Pérez","edad":30,"activo":true}'

// ✅ SIEMPRE usar toText() en body de invokeUrl
resp = invokeUrl [
    url:     "https://api.externo.com/endpoint"
    type:    POST
    body:    mapa.toText()     // ← toText()
    headers: {"Content-Type": "application/json"}
];
```

### Actualización de Lookup Fields en CRM

```deluge
// ❌ MALO — no funciona, envía el nombre, no el ID
datos = Map();
datos.put("Account_Name", "Acme Corp");
zoho.crm.updateRecord("Contacts", recId, datos);

// ✅ BUENO — enviar objeto con el ID
datos = Map();
datos.put("Account_Name", {"id": "66359000000015039"});
zoho.crm.updateRecord("Contacts", recId, datos);
```

### IDs en CRM son Strings, no Números

```deluge
// ❌ MALO — comparación numérica falla
if (record.get("id") == 66359000000015039) { /* ... */ }

// ✅ BUENO — siempre convertir a string
recordId = record.get("id").toString();
if (recordId == "66359000000015039") { /* ... */ }
```

### Fechas y Zona Horaria

```deluge
// today() retorna tipo Date en zona horaria de la org
hoy = today();

// now() retorna DateTime en zona horaria de la org
ahora = now();

// Para enviar a API que espera ISO 8601
fechaISO = hoy.toString("yyyy-MM-dd");
datetimeISO = ahora.toString("yyyy-MM-dd'T'HH:mm:ssZ");

// Conversión de zona horaria
enCDMX = ahora.toTimeZone("America/Mexico_City");
enUTC  = ahora.toTimeZone("UTC");
```

### CRM: fields retorna Map, no valor directo

```deluge
// Al actualizar múltiples registros con bulkUpdateRecords,
// la respuesta contiene "data" que es una lista de Maps
resp = zoho.crm.bulkUpdateRecords("Contacts", updateList);
data = resp.get("data");

if (data != null) {
    for each item in data {
        status = item.get("status");  // "success" o "error"
        if (status == "error") {
            errorMsg = item.get("message");
            info "Error en registro " + item.get("details").get("id") + ": " + errorMsg;
        }
    }
}
```

### Creator: insert devuelve ID como Integer, no String

```deluge
// En Creator, el ID del nuevo registro viene como número
resp = insert into MiFormulario [Campo1 = "valor"];
nuevoId = resp.ID;  // es un número entero

// Para usarlo en otras tasks, convertir
idStr = nuevoId.toString();
```

### Loops y límite de statements

```deluge
// ❌ MALO — múltiples .get() dentro de loop cuentan cada uno
for each rec in registros {
    nombre  = rec.get("First_Name");   // +1 statement
    email   = rec.get("Email");        // +1 statement
    ciudad  = rec.get("City");         // +1 statement
    empresa = rec.get("Account_Name"); // +1 statement
    // ... 5 campos × 1000 registros = 5000 statements solo aquí
}

// ✅ MEJOR — reducir operaciones, pre-filtrar en la consulta
campos = {"id", "First_Name", "Email"};  // pedir solo lo necesario
```

---

## 4. Sincronización Cross-App

### Patrón CRM → Creator

```deluge
// Función en CRM (Button o Workflow) que sincroniza a Creator
// Usar cuando el registro se crea/actualiza en CRM y hay que reflejarlo en Creator

crmRecord = input;  // el registro de CRM que disparó la función

// 1. Buscar si ya existe en Creator
existente = zoho.creator.getRecords("mi_app", "Reporte_Sincronizados");
datos = existente.get("data");

creatorRecId = null;
for each rec in datos {
    if (rec.get("CRM_ID") == crmRecord.get("id").toString()) {
        creatorRecId = rec.get("ID");
        break;
    }
}

// 2. Crear o actualizar
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

### Patrón Creator → CRM (Bidireccional)

```deluge
// Workflow en Creator (On Submit) que actualiza CRM
// Prevenir loops: solo sincronizar si el cambio viene del usuario, no de la sync

if (input.Origen_Cambio != "Sync_Automatica") {
    crmId = input.CRM_ID;

    if (crmId != "") {
        // Actualizar en CRM
        updateData = Map();
        updateData.put("Phone",       input.Telefono);
        updateData.put("Mailing_City", input.Ciudad);

        resp = zoho.crm.updateRecord("Contacts", crmId, updateData);
    } else {
        // Crear nuevo en CRM
        newData = Map();
        newData.put("First_Name", input.Nombre);
        newData.put("Email",      input.Email);
        newData.put("Phone",      input.Telefono);

        resp = zoho.crm.createRecord("Contacts", newData);
        crmId = resp.get("data").get(0).get("id");

        // Guardar el CRM ID de vuelta en Creator
        update Reporte_Contactos[ID == input.ID] [
            CRM_ID = crmId
        ];
    }
}
```

### Patrón CRM → Books (Facturación)

```deluge
// Crear factura en Books cuando se cierra un Deal en CRM
dealId   = crmRecord.get("id");
dealName = crmRecord.get("Deal_Name");
amount   = crmRecord.get("Amount");
accountId = crmRecord.get("Account_Name").get("id");

// Buscar contacto de Books por CRM Account ID (API v3 Books)
booksContactResp = invokeUrl [
    url:        "https://www.zohoapis.com/books/v3/crm/account/" + accountId + "/import?organization_id=" + booksOrgId
    type:       POST
    headers:    {"Content-Type": "application/json"}
    connection: "zoho_books_conn"
];

booksContactId = booksContactResp.get("contact").get("contact_id");

// Crear la factura
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

// Actualizar el Deal con el ID de factura
updateDeal = Map();
updateDeal.put("Factura_Books_ID", invoiceId);
updateDeal.put("Factura_URL",      invoiceUrl);
zoho.crm.updateRecord("Deals", dealId, updateDeal);
```

---

## 5. Patrón de Log Estructurado

```deluge
// Función de log reutilizable (en Creator)
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

// Uso en cualquier workflow
try {
    resp = zoho.crm.createRecord("Contacts", datos);
    thisapp.escribirLog("CRM", "Crear_Contacto", "INFO", "Contacto creado: " + resp.get("data").get(0).get("id"), "");
} catch (e) {
    thisapp.escribirLog("CRM", "Crear_Contacto", "ERROR", e.toString(), recordId);
    // No relanzar el error si el log es suficiente
}
```

---

## 6. Detección de Entorno en Creator

```deluge
// thisapp.environment disponible desde 2025
entorno = thisapp.environment;  // "Development" | "Stage" | "Production"

if (entorno == "Production") {
    webhookUrl = "https://hooks.empresa.com/prod/zoho";
    slackChannel = "#alertas-prod";
} else if (entorno == "Stage") {
    webhookUrl = "https://hooks.empresa.com/stage/zoho";
    slackChannel = "#alertas-stage";
} else {
    // Development — no enviar notificaciones externas
    info "Modo Development — integración externa omitida";
    return;
}

// Enviar webhook solo en Stage/Prod
resp = invokeUrl [
    url:     webhookUrl
    type:    POST
    body:    {"event": "nuevo_registro", "id": recordId}.toText()
    headers: {"Content-Type": "application/json"}
];
```

---

## 7. Manejo de Errores en invokeUrl

```deluge
try {
    resp = invokeUrl [
        url:        "https://api.externa.com/endpoint"
        type:       POST
        body:       payload.toText()
        headers:    headers
        connection: "mi_conexion"
    ];

    // Verificar que la respuesta tiene el formato esperado
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
    // Enviar alerta por Cliq al canal de errores
    alerta = Map();
    alerta.put("text", "🚨 Error en integración: " + e.toString());
    zoho.cliq.postToChannel("alertas-dev", alerta);
    return null;
}
```
