# Zoho CRM API v8 — Referencia Completa

> **Fuente oficial:** https://www.zoho.com/crm/developer/docs/api/v8/  
> **URL Base:** `https://www.zohoapis.com/crm/v8/`  
> **Autenticación:** OAuth 2.0 (`Authorization: Zoho-oauthtoken {token}`)  
> **Versión actual:** v8 (2024-2025) — también disponibles v2, v5, v6, v7

---

## Scopes OAuth — CRM v8

```
ZohoCRM.modules.ALL        ← acceso completo a módulos
ZohoCRM.modules.READ
ZohoCRM.modules.CREATE
ZohoCRM.modules.UPDATE
ZohoCRM.modules.DELETE

ZohoCRM.settings.ALL       ← configuración completa
ZohoCRM.settings.modules
ZohoCRM.settings.fields
ZohoCRM.settings.layouts
ZohoCRM.settings.territories
ZohoCRM.settings.custom_views
ZohoCRM.settings.related_lists
ZohoCRM.settings.variables
ZohoCRM.settings.tags
ZohoCRM.settings.tab_groups
ZohoCRM.settings.macros
ZohoCRM.settings.custom_links
ZohoCRM.settings.custom_buttons
ZohoCRM.settings.roles
ZohoCRM.settings.profiles
ZohoCRM.settings.currencies

ZohoCRM.users.ALL
ZohoCRM.org.ALL
ZohoCRM.bulk.ALL           ← bulk read/write
ZohoCRM.notifications.ALL  ← notificaciones push
ZohoCRM.coql.READ          ← COQL queries

// Módulos específicos (ejemplos):
ZohoCRM.modules.leads.ALL
ZohoCRM.modules.contacts.ALL
ZohoCRM.modules.deals.ALL
ZohoCRM.modules.accounts.ALL
```

---

## Módulos Estándar de CRM

| Módulo API Name | Descripción |
|----------------|-------------|
| `Leads` | Prospectos / leads |
| `Contacts` | Contactos |
| `Accounts` | Cuentas / empresas |
| `Deals` | Oportunidades |
| `Campaigns` | Campañas de marketing |
| `Tasks` | Tareas |
| `Cases` | Casos de soporte |
| `Events` | Eventos / actividades |
| `Calls` | Llamadas |
| `Solutions` | Soluciones / base de conocimiento |
| `Products` | Productos |
| `Vendors` | Proveedores |
| `PriceBooks` | Listas de precios |
| `Quotes` | Cotizaciones |
| `SalesOrders` | Órdenes de venta |
| `PurchaseOrders` | Órdenes de compra |
| `Invoices` | Facturas |
| `Activities` | Actividades (Tasks + Events + Calls) |
| `Notes` | Notas |
| `Attachments` | Archivos adjuntos |
| `Users` | Usuarios de CRM |
| `Services` | Servicios (Field Service) |
| `Appointments` | Citas (Field Service) |
| `Appointments_Rescheduled_History` | Historial de reprogramaciones |

---

## Records — Endpoints Principales

### CRUD de Registros

```
GET    /{module}                               ← listar registros
GET    /{module}/{record_id}                  ← obtener por ID
POST   /{module}                               ← crear registro(s)
PUT    /{module}                               ← actualizar registro(s)
DELETE /{module}                               ← eliminar registro(s)
POST   /{module}/upsert                       ← crear o actualizar
```

### Parámetros de Listado

```
page={n}                    ← paginación (default: 1)
per_page={n}                ← registros por página (máx: 200)
sort_by={field_api_name}    ← campo de ordenamiento
sort_order=asc|desc
fields={field1,field2}     ← campos específicos
converted=true|false        ← solo para Leads
approved=true|false
territory_id={id}
include=owner,created_by,modified_by
```

### Búsqueda

```
GET    /{module}/search?criteria=({field}:{operator}:{value})
GET    /{module}/search?word={searchTerm}
GET    /{module}/search?email={email}
GET    /{module}/search?phone={phone}
GET    /{module}/search?cvid={custom_view_id}
```

Operadores: `equals`, `not_equals`, `contains`, `starts_with`, `ends_with`, `greater_than`, `less_than`, `between`, `not_between`, `in`, `not_in`, `is_empty`, `is_not_empty`

### Registros Relacionados (Submodules)

```
GET    /{module}/{record_id}/{related_module}
POST   /{module}/{record_id}/{related_module}
PUT    /{module}/{record_id}/{related_module}/{related_id}
DELETE /{module}/{record_id}/{related_module}/{related_id}
```

### Conversión de Lead

```
POST   /Leads/{lead_id}/actions/convert
```

### Archivos Adjuntos

```
GET    /{module}/{record_id}/Attachments
POST   /{module}/{record_id}/Attachments   ← subir archivo
GET    /{module}/{record_id}/Attachments/{attach_id}
DELETE /{module}/{record_id}/Attachments/{attach_id}
```

### Notas

```
GET    /{module}/{record_id}/Notes
POST   /{module}/{record_id}/Notes
PUT    /Notes/{note_id}
DELETE /Notes/{note_id}
```

---

## COQL — CRM Object Query Language

> **Documentación completa:** [`docs/coql/crm-coql.md`](../coql/crm-coql.md)  
> **Skill:** `zoho-crm-coql`

```
POST   /coql
```

Body mínimo:
```json
{
  "select_query": "select Last_Name, Email from Contacts where Email is not null limit 200"
}
```

Scope: `ZohoCRM.coql.READ` — Límite: **200 registros/request**, paginar con OFFSET.

Para sintaxis completa, agregados, joins lookup, ejemplos Deluge y troubleshooting → ver `docs/coql/crm-coql.md`.

## Bulk Read / Write

### Bulk Read (exportación masiva)

```
POST   /read                                   ← crear job de lectura masiva
GET    /read/{job_id}                          ← estado del job
GET    /read/{job_id}/result                   ← descargar resultado (ZIP/CSV)
DELETE /read/{job_id}                          ← cancelar job
```

Body POST:
```json
{
  "query": {
    "module": {"api_name": "Leads"},
    "fields": [{"api_name": "Last_Name"}, {"api_name": "Email"}],
    "criteria": {
      "group_operator": "and",
      "group": [{"field": {"api_name": "Created_Time"}, "comparator": "between", "value": ["2024-01-01T00:00:00+00:00", "2024-12-31T23:59:59+00:00"]}]
    },
    "callback": {"url": "https://tu-webhook.com/notif", "method": "post"}
  }
}
```

### Bulk Write (importación masiva)

```
POST   /write                                  ← crear job de escritura
GET    /write/{job_id}
DELETE /write/{job_id}
```

---

## Users

```
GET    /users                                  ← listar usuarios
GET    /users/{user_id}                       ← obtener usuario
GET    /users/search?criteria=...
POST   /users                                  ← crear usuario
PUT    /users                                  ← actualizar
DELETE /users/{user_id}
GET    /users?type=ActiveUsers|DeactiveUsers|AdminUsers|CurrentUser
```

---

## Settings

### Modules

```
GET    /settings/modules
GET    /settings/modules/{module_api_name}
POST   /settings/modules                       ← crear módulo custom
PUT    /settings/modules/{module_api_name}
```

### Fields

```
GET    /settings/fields?module={module}        ← campos del módulo
GET    /settings/fields/{field_id}?module={module}
POST   /settings/fields?module={module}        ← crear campo
PUT    /settings/fields?module={module}
DELETE /settings/fields?module={module}
```

### Layouts

```
GET    /settings/layouts?module={module}
GET    /settings/layouts/{layout_id}?module={module}
POST   /settings/layouts?module={module}
PUT    /settings/layouts/{layout_id}?module={module}
DELETE /settings/layouts/{layout_id}?module={module}
```

### Custom Views

```
GET    /settings/custom_views?module={module}
GET    /settings/custom_views/{cv_id}?module={module}
POST   /settings/custom_views?module={module}
PUT    /settings/custom_views/{cv_id}?module={module}
DELETE /settings/custom_views/{cv_id}?module={module}
```

### Roles

```
GET    /settings/roles
GET    /settings/roles/{role_id}
POST   /settings/roles
PUT    /settings/roles/{role_id}
DELETE /settings/roles/{role_id}
```

### Profiles

```
GET    /settings/profiles
GET    /settings/profiles/{profile_id}
POST   /settings/profiles
PUT    /settings/profiles/{profile_id}
DELETE /settings/profiles/{profile_id}
```

### Variables de Organización

```
GET    /settings/variables
GET    /settings/variables/{var_id}
POST   /settings/variables
PUT    /settings/variables/{var_id}
DELETE /settings/variables/{var_id}
```

### Pipelines

```
GET    /settings/pipelines?module={module}
GET    /settings/pipelines/{pipeline_id}?module={module}
POST   /settings/pipelines?module={module}
PUT    /settings/pipelines/{pipeline_id}?module={module}
DELETE /settings/pipelines/{pipeline_id}?module={module}
```

### Tags

```
GET    /settings/tags?module={module}
POST   /settings/tags?module={module}
PUT    /settings/tags/{tag_id}?module={module}
DELETE /settings/tags/{tag_id}?module={module}
POST   /{module}/{record_id}/tags              ← agregar tags a registro
GET    /{module}/tags?tag_ids={id1,id2}        ← registros por tag
DELETE /{module}/{record_id}/tags
```

---

## Notifications (Push / Webhooks)

```
POST   /actions/watch                          ← crear suscripción
GET    /actions/watch                          ← listar suscripciones
PATCH  /actions/watch                          ← actualizar
DELETE /actions/watch                          ← cancelar
```

Body:
```json
{
  "watch": [{
    "channel_id": "1000000068002",
    "events": ["Leads.create", "Contacts.edit", "Accounts.delete", "Tasks.all"],
    "channel_expiry": "2024-12-31T00:00:00+05:30",
    "token": "TOKEN_SECRETO",
    "notify_url": "https://tu-webhook.com/zoho"
  }]
}
```

---

## Functions (Standalone — REST API)

```
POST   /functions/{function_name}/actions/execute?auth_type=apikey&zapikey={key}
```

Body:
```json
{
  "arguments": "{\"recordId\":\"123456\",\"parametro\":\"valor\"}"
}
```

Desde Deluge, para llamar una función standalone:
```deluge
req_data = {
    "arguments": JSON.stringify({"recordId": recordId})
};
result = ZOHO.CRM.FUNCTIONS.execute("nombre_funcion", req_data);
output = result.get("details").get("output");
```

---

## Org

```
GET    /org                                    ← info de la organización
PUT    /org                                    ← actualizar org
GET    /org/businesshours
PUT    /org/businesshours
GET    /org/currencies
POST   /org/currencies
PUT    /org/currencies/{currency_id}
POST   /org/currencies/{currency_id}/enable_home_currency
```

---

## Ejemplo completo invokeUrl desde Deluge

```deluge
// Buscar contactos por email y actualizar en CRM v8
email = input.Email;

// COQL search
searchQuery = "select id, Last_Name, Email from Contacts where Email = '" + email + "' limit 1";
coqlBody = Map();
coqlBody.put("select_query", searchQuery);

headers = Map();
headers.put("Content-Type", "application/json");

searchResp = invokeUrl [
    url:        "https://www.zohoapis.com/crm/v8/coql"
    type:       POST
    body:       coqlBody.toText()
    headers:    headers
    connection: "zoho_crm_v8_conn"
];

data = searchResp.get("data");
if (data != null && data.size() > 0) {
    contactId = data.get(0).get("id");

    // Actualizar el contacto
    updateData = list();
    rec = Map();
    rec.put("id", contactId);
    rec.put("Lead_Status", "Calificado");
    updateData.add(rec);

    updateBody = Map();
    updateBody.put("data", updateData);

    updateResp = invokeUrl [
        url:        "https://www.zohoapis.com/crm/v8/Contacts"
        type:       PUT
        body:       updateBody.toText()
        headers:    headers
        connection: "zoho_crm_v8_conn"
    ];
}
```

---

## SDKs Oficiales

| Lenguaje | Repositorio |
|----------|-------------|
| Java | zohocrm/zohocrm-java-sdk-v8 |
| Python | zohocrm/zohocrm-python-sdk-v8 |
| PHP | zohocrm/zohocrm-php-sdk-v8 |
| Node.js | zohocrm/zohocrm-nodejs-sdk-v8 |
| C# | zohocrm/zohocrm-csharp-sdk-v8 |
| Ruby | zohocrm/zohocrm-ruby-sdk-v8 |
| JavaScript (Browser) | zohocrm/zohocrm-js-sdk |

---

## Notas Importantes v8

- La mayoría de respuestas usan el campo `data` (array) como raíz
- IDs son strings largos numéricos (ej: `"66359000000015039"`)
- Fechas en ISO 8601: `"2024-01-15T14:30:00+05:30"`
- Lookup fields devuelven `{"id": "...", "name": "..."}` — para actualizar, enviar solo `{"id": "..."}`
- El scope `ZohoCRM.modules.ALL` da acceso a todos los módulos incluyendo los custom
- Para módulos custom usar su API name (ej: `Pago_Facturas`, `Mi_Modulo_Custom`)
