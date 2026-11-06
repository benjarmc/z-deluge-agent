---
name: zoho-deluge-core
description: Writes, reviews, and debugs Deluge scripts in any Zoho product. Use for Deluge, invokeUrl, invokeapi, Map/List, dates, for each, break, continue, return, try-catch, sendmail, zoho.currentdate, custom functions, server-side workflows in CRM, Creator, Books, Desk. / Escribe, revisa y depura scripts Deluge en cualquier producto Zoho. Usar para Deluge, invokeUrl, invokeapi, Map/List, fechas, for each, break, continue, return, try-catch, sendmail, funciones personalizadas, workflows server-side.
---

# Zoho Deluge — Lenguaje base

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

## Alcance

Deluge server-side en **cualquier** producto Zoho. Este skill cubre el **lenguaje** completo: tipos, operadores, estructuras de control, funciones por categoría, invokeUrl y variables del sistema.

Para Creator específico → skill `zoho-deluge-creator`.  
Para tasks `zoho.*` → skill `zoho-integration-tasks`.  
Para REST OAuth → skill `zoho-public-apis`.

**Referencia completa:** `docs/en/deluge/language.md` · `docs/es/deluge/language.md`  
**Patrones:** `docs/en/patterns/production.md` · `docs/es/patterns/production.md`

---

## Tipos de dato

### Text (string)

```deluge
nombre = "Juan Pérez";
email = "maria@empresa.com";
concat = nombre + " - " + email;
```

### Number (entero)

```deluge
cantidad = 100;
id = 12345;
suma = cantidad + 50;
```

### Decimal

```deluge
precio = 19.99;
descuento = precio * 0.15;
total = precio - descuento;
```

### Boolean

```deluge
activo = true;
completado = false;
if(activo && !completado) { info "En progreso"; }
```

### Date / DateTime

```deluge
fecha = '2024-01-15';
fechaHora = '2024-01-15 14:30:00';
hoy = today();
ahora = now();
zohoDate = zoho.currentdate;
zohoTime = zoho.currenttime;
```

Formatos comunes:
- Creator criterios: `fecha.toString("dd-MMM-yyyy")`
- APIs REST: `fecha.toString("yyyy-MM-dd")`
- ISO datetime: `ahora.toString("yyyy-MM-dd'T'HH:mm:ssXXX")`

### Time

Hora independiente de fecha (12h o 24h). Entre comillas simples.

```deluge
hora = '14:30:00';
```

### File (FILE)

Archivo desde web/nube (`invokeUrl`, adjuntos). **No** archivos locales. Las File functions **no** aplican a campos File de Creator vía `fetch`/`input` (nombre: `actualName`).

`null` es constante built-in **sin** tipo.

---

## List (lista)

```deluge
productos = {"Creator", "CRM", "Books"};
numeros = {1, 2, 3};

// Operaciones
primer = productos.get(0);
productos.add("Desk");
productos.remove(1);
size = productos.size();
existe = productos.contains("CRM");
idx = productos.indexOf("CRM");

for each p in productos { info p; }

// Desde string
lista = "a,b,c".toList(",");
```

---

## Map (mapa)

```deluge
m = Map();
m.put("nombre", "Zoho CRM");
m.put("precio", 29.99);

// Literal
m2 = {"nombre": "Books", "activo": true};

// Operaciones
val = m.get("nombre");
tiene = m.containsKey("precio");
claves = m.keys();
m.remove("precio");
size = m.size();

// JSON para APIs — CRÍTICO
jsonBody = m.toText();    // ✅ JSON válido
badJson = m.toString();   // ❌ {nombre=Zoho} — NO usar en invokeUrl
```

---

## Collection

Un Collection actúa **o** como lista **o** como mapa; no mezcla elementos y pares clave-valor en el mismo objeto.

```deluge
textoMapa = "{company:Zoho, product:Deluge}";
col = textoMapa.toCollection();
empresa = col.get("company");

col2 = Collection();
col2.add("item1");
lista = col2.toList();
```

---

## File functions

Índice: https://www.zoho.com/deluge/help/file-methods.html  
Operan sobre FILE (`invokeUrl`). No sobre `fetch`/`input` de Creator.

| Función | Uso |
|---------|-----|
| `f.getFileName()` | Nombre |
| `f.getFileSize()` | Tamaño |
| `f.getFileType()` | MIME / tipo |
| `f.getFileContent()` | Contenido |
| `f.isFile()` | ¿es FILE? |
| `f.setFileName(name)` | Renombrar |
| `f.setFileType(type)` | Tipo |
| `f.setCharset(cs)` | Charset |
| `f.setFilePassword(pwd)` | Password |
| `files.compress([name])` | ZIP (máx. ~2 MB) |
| `zip.extract()` | Extraer ZIP |
| `value.toFile(fileName)` | Envolver en FILE (Creator ≤5 MB) |
| `f.convertToPDF()` | A PDF |

```deluge
f = invokeUrl [ url: "https://ejemplo.com/a.png" type: GET ];
nombre = f.getFileName();
zip = f.compress("paquete");
notas = "hola".toFile("notas.txt");
```

---

## Operadores

Índice oficial: Arithmetic, Assignment, Logical, Relational. `+` concatena Text (no hay sección “string operators” aparte). **No hay ternario.**

### Aritméticos
`+`, `-`, `*`, `/`, `%` · `pow(a, b)`

### Asignación
`=` · compuestos: `+=`, `-=`, `*=`, `/=`

### Relacionales
`==`, `!=`, `>`, `<`, `>=`, `<=`

### Lógicos
`&&`, `||`, `!`

---

## Control de flujo

### if / else if / else

```deluge
if(puntuacion >= 90) { grado = "A"; }
else if(puntuacion >= 80) { grado = "B"; }
else { grado = "C"; }
```

### for each

Deluge **no tiene `while`**. Los dos patrones que funcionan de forma fiable:

**1. Estático — N conocido y fijo** (constantes, reintentos, pasos fijos):

```deluge
for each i in {1,2,3,4,5,6}
{
    info i;
}
```

**2. Dinámico — N calculado** (paginación, batches, tamaño de lista):

```deluge
batchSize = 100;
total = fullList.toList().size();
numOfPages = (total / batchSize).ceil();
lst_pageLoop = "0,".repeat(numOfPages).toList(",");
for each index pageNumber in lst_pageLoop
{
    offset = pageNumber * batchSize;
    // procesar el lote pageNumber
}
```

`repeat` + `toList(",")` genera exactamente `numOfPages` iteraciones. `for each index` da el índice 0-based (`pageNumber`).

```deluge
// Iterar una lista o query (cuando el conjunto ya existe)
for each item in lista {
    info item;
}
```

### continue — saltar la iteración actual

Oficial: [continue](https://www.zoho.com/deluge/help/misc-statements/continue.html). No termina el loop; solo omite el resto de **esta** iteración.

```deluge
colors = List();
colors.add("Green");
colors.add("Red");
colors.add("Blue");
colors.add("Red");
colors.add("Teal");

for each rec in colors
{
    if(rec == "Red")
    {
        continue;
    }
    info rec;
}
// Green, Blue, Teal
```

Disponible en On Load/Validate/Success, On User input, subform add/delete row, schedules, approval, custom functions y action items de reporte.

### break — salir del loop

Oficial: [break](https://www.zoho.com/deluge/help/misc-statements/break.html). Termina el `for each` y sigue con la primera sentencia **después** del loop.

```deluge
for each technician in technicians
{
    if(technician.get("status") == "available")
    {
        info technician.get("name") + " is available";
        break;
    }
}
```

`break` vs `continue`: `break` mata el loop; `continue` mata solo la iteración.

**No hay `while`.** Para N fijo o calculado usar los dos patrones de `for each` de arriba; dentro de ellos sí usar `break`/`continue`.

### return — valor de función

Oficial: [return](https://www.zoho.com/deluge/help/misc-statements/return.html). Solo en **custom functions** que declaran tipo de retorno. El tipo de `<expression>` debe coincidir o hay error en runtime.

```deluge
int thisapp.CalculateDays(date sdate, date edate)
{
    days = (((edate - sdate) / 86400000)).toLong();
    return days;
}
```

No aplica en On Load/Validate/Success de formulario ni en action items de reporte. Funciones `void` no usan `return` con valor.

---

## Built-in functions (índice oficial)

Cada tipo tiene su set. File functions van aparte: https://www.zoho.com/deluge/help/file-methods.html

| Categoría | Help |
|-----------|------|
| Collection | https://www.zoho.com/deluge/help/functions/collection.html |
| Text | https://www.zoho.com/deluge/help/functions/text.html |
| Number | https://www.zoho.com/deluge/help/functions/number.html |
| Date-time | https://www.zoho.com/deluge/help/functions/date-time.html |
| Time | https://www.zoho.com/deluge/help/functions/time.html |
| Encryption | https://www.zoho.com/deluge/help/encryption-tasks.html |
| Logical | https://www.zoho.com/deluge/help/functions/logical.html |
| Boolean-returning | https://www.zoho.com/deluge/help/functions-returning-boolean.html |
| Type check | https://www.zoho.com/deluge/help/functions/type-check.html |
| Type conversion | https://www.zoho.com/deluge/help/functions/conversion.html |
| Utilities | https://www.zoho.com/deluge/help/functions/utilities.html |
| XML | https://www.zoho.com/deluge/help/functions/xml.html |
| List | https://www.zoho.com/deluge/help/functions/list.html |
| Map (Key-Value) | https://www.zoho.com/deluge/help/functions/key-value.html |
| File | https://www.zoho.com/deluge/help/file-methods.html |

Abajo: Text, Date-time, Number, Collection/List/Map. Encryption, XML, type-check/conversion y Time: ver help.

---

## Funciones String

| Función | Uso |
|---------|-----|
| `len(s)` | Longitud |
| `toUpperCase(s)` / `toLowerCase(s)` | Case |
| `trim(s)` / `ltrim` / `rtrim` | Espacios |
| `subString(s, start, [end])` | Subcadena |
| `contains(s, sub)` | Contiene |
| `startsWith(s, prefix)` / `endsWith` | Prefijo/sufijo |
| `replaceAll(s, old, new, ignoreCase)` | Reemplazo |
| `indexOf(s, sub)` | Posición |
| `split(s, delimiter)` | → List |
| `leftpad(s, n)` / `rightpad` | Padding |
| `ifNull(val, default)` | Null coalescing |
| `getJSON(jsonStr, path)` | Parse JSON path |

```deluge
email = "maria@empresa.com";
dominio = subString(email, indexOf(email, "@") + 1);
partes = email.split("@");
```

---

## Funciones Fecha y Hora

| Función | Descripción |
|---------|-------------|
| `today()` | Fecha actual (TZ org) |
| `now()` | DateTime actual |
| `zoho.currentdate` | Variable sistema |
| `zoho.currenttime` | Variable sistema |
| `addDay(d, n)` | Sumar días |
| `addMonth(d, n)` | Sumar meses (⚠️ limitado en Creator) |
| `addYear(d, n)` | Sumar años |
| `daysBetween(d1, d2)` | Diferencia días |
| `monthsBetween(d1, d2)` | Diferencia meses |
| `year(d)` / `month(d)` / `day(d)` | Componentes |
| `hour(dt)` / `minute` / `second` | Hora |
| `toTimeZone(dt, "America/Mexico_City")` | TZ |
| `d.toString("dd-MMM-yyyy")` | Formato |

```deluge
hoy = zoho.currentdate;
manana = addDay(hoy, 1);
fmtCreator = hoy.toString("dd-MMM-yyyy");
fmtAPI = hoy.toString("yyyy-MM-dd");
```

---

## Funciones Matemáticas

| Función | Descripción |
|---------|-------------|
| `abs(n)` | Valor absoluto |
| `ceil(n)` / `floor(n)` / `round(n, dec)` | Redondeo |
| `max(a,b)` / `min(a,b)` | Comparación |
| `sqrt(n)` / `pow(base, exp)` | Raíz/potencia |
| `randomNumber(min, max)` | Aleatorio entero |

---

## Funciones Collection / List / Map

| Función | Descripción |
|---------|-------------|
| `list.size()` | Tamaño |
| `list.add(item)` | Agregar |
| `list.remove(index)` | Eliminar |
| `list.contains(item)` | Contiene |
| `list.indexOf(item)` | Índice |
| `list.distinct()` | Únicos |
| `list.sort()` | Ordenar |
| `map.keys()` / `map.values()` | Claves/valores |
| `map.containsKey(k)` | Tiene clave |
| `map.toText()` | → JSON |

---

## Variables del sistema `zoho.*`

| Variable | Descripción |
|----------|-------------|
| `zoho.currentdate` | Fecha actual org |
| `zoho.currenttime` | DateTime actual org |
| `zoho.loginuserid` | Email usuario activo |
| `zoho.adminuserid` | Email admin org |
| `zoho.loginuser` | Alias loginuserid |
| `zoho.ipaddress` | IP del request (contextos web) |
| `zoho.appuri` | URI app (Creator) |
| `zoho.appname` | Nombre app (Creator) |

CRM Vertical Solutions / extensiones (no es CRM V8):
```deluge
orgVar = zoho.crm.getOrgVariable("nombre_variable");
```
Índice Vertical: skill `zoho-integration-tasks`. CRUD CRM estándar: `zoho.crm.v8.*`.

---

## Funciones personalizadas

```deluge
string FormatearNombre(string nombre, string apellido) {
    return toUpperCase(nombre + " " + apellido);
}

void LogError(string op, string err) {
    info op + ": " + err;
}

// Llamar
nombre = thisapp.FormatearNombre("Juan", "Pérez");
// Otra app
val = OtraApp.MiFuncion(arg1);
```

Tipos de retorno: `void`, `int`, `string`, `boolean`, `date`, `decimal`, `map`, `list`.

---

## sendmail y sendSms

```deluge
sendmail [
    from: zoho.adminuserid
    to: "destino@empresa.com"
    subject: "Asunto"
    message: "<h1>HTML</h1>"
    content type: "text/html"
    attachments: fileVar
];

sendSms [
    to: "+525512345678"
    message: "Tu pedido fue enviado"
    connection: "twilio_connection"
];
```

---

## try-catch

Oficial: [try-catch](https://www.zoho.com/deluge/help/misc-statements/try-catch.html). El `catch` solo corre si hay error en runtime dentro de `try`. El código **después** del bloque try-catch se ejecuta igual (el error no aborta toda la función).

```deluge
try
{
    products = {"Creator","CRM","Cliq"};
    products = products.get(10);
}
catch(e)
{
    info e.lineNo;   // línea que falló
    info e.message;  // mensaje
    info e;          // ambos: "Error at line : 6, Given index 10 is greater than the list size"
}
```

Disponible en los mismos eventos de workflow que `break`/`continue` (Load, Validate, Success, User input, subform, schedule, approval, custom function, report action).

Usar try-catch alrededor de `invokeUrl`/`invokeapi`, índices de lista y cálculos que puedan recibir vacío.

---

## invokeUrl — integración HTTP

```deluge
// GET simple
resp = invokeUrl [ url: "https://api.ejemplo.com/data" type: GET ];

// GET con query params
params = Map();
params.put("page", "1");
resp = invokeUrl [ url: url type: GET parameters: params ];

// POST JSON — SIEMPRE toText()
headers = Map();
headers.put("Content-Type", "application/json");
body = Map();
body.put("campo", "valor");
resp = invokeUrl [
    url: "https://api.ejemplo.com"
    type: POST
    body: body.toText()
    headers: headers
];

// OAuth Zoho (conexión configurada)
resp = invokeUrl [
    url: "https://www.zohoapis.com/crm/v8/Contacts"
    type: GET
    connection: "zoho_crm_oauth"
];

// connection SIEMPRE literal. Una variable no compila:
// connName = "zoho_crm_oauth";
// connection: connName

// PUT / PATCH / DELETE
resp = invokeUrl [ url: url type: PUT body: body.toText() headers: headers ];
```

### Respuesta invokeUrl segura

```deluge
respStr = resp.toString();
if(respStr.contains(""data"")) {
    data = resp.get("data");
}
// NO usar .containsKey() directo en respuesta invokeUrl
```

### Map anidado en body

Cuando `.toText()` no genera estructura anidada correcta, construir JSON manualmente:
```deluge
jsonStr = "{\"data\": [{\"id\": \"" + recordId + "\", \"Stage\": \"Closed\"}]}";
```

---

## invokeAPI — HTTP entre servicios Zoho

Oficial: [invokeAPI](https://www.zoho.com/deluge/help/webhook/invokeapitask.html). Cliente HTTP para APIs **Zoho↔Zoho**. A diferencia de `invokeUrl`, solo se indica el **path**; el dominio se resuelve según el data center. Cada ejecución consume 1 external call.

```deluge
response = invokeapi
[
    service: zohocrm
    path: "/crm/v8/Contacts"
    type: GET
    connection: "zoho_crm_oauth"
];

info response.status;   // HTTP code, p.ej. 200
info response.headers;
```

| Parámetro | Uso |
|-----------|-----|
| `service` | `zohocrm`, `zohobooks`, `zohoinvoice`, `zohobilling`, `zohoinventory`, `zohobookings`, `zohocreator`, `zohoworkdrive` |
| `path` | Path sin base URL: `/crm/v3/Leads` (no `https://www.zohoapis.com/...`) |
| `type` | GET (default), POST, PUT, PATCH, DELETE, OPTIONS |
| `parameters` | Query string (TEXT o Map) |
| `body` | TEXT / FILE / Map — JSON: `body: data.toString()` + header `application/json` |
| `headers` | Map |
| `connection` | Nombre de conexión, literal entre comillas (`"connection_name"`). Una variable no compila. En Creator a menudo obligatorio |
| `response-format` | FILE, COLLECTION, STRING, NONE, DEFAULT |
| `response-decoding` | Default UTF-8 |

```deluge
// GET Bookings
response = invokeapi
[
    service: ZohoBookings
    path: "bookings/v1/json/services"
    type: GET
    connection: "bookings_connection"
];

// PUT Inventory — body JSON
address_map = Map();
address_map.put("shipping_address", Address);
response = invokeapi
[
    service: ZohoInventory
    path: "/inventory/v1/contacts/" + contactId
    type: PUT
    parameters: {"organization_id": orgId}
    body: address_map.toString()
    headers: {"content-type": "application/json"}
    connection: "zohoinventory_connection"
];
```

Límites: socket timeout 40s; Creator download máx 5 MB (otros servicios 15 MB). `for each` con invokeAPI dentro = N external calls.

**Cuándo `invokeapi` vs `invokeUrl`:** Zoho→Zoho con path relativo → `invokeapi`. URL completa, API externa, o header/path que invokeAPI no cubre → `invokeUrl`.

---

## Deluge tasks (categorías oficiales)

Índice: [Deluge Tasks](https://www.zoho.com/deluge/help/tasks.html)

| Categoría | Help |
|-----------|------|
| Artificial Intelligence Tasks | tasks AI Creator |
| Creator Blueprint Tasks | transiciones Blueprint |
| Conditions | `if`, `else`, `if()` expression |
| Data Access | `insert`, `update`, `delete`, `Form[criteria]` (Creator) |
| Composite fields | Name, Address, etc. |
| Client Functions | JS del lado cliente Creator |
| Miscellaneous | `break`, `continue`, `return`, `info`, `cancel submit` |
| Debug | `info` |
| List Manipulations | `for each`, `add`, `get` |
| Map Manipulations | `put`, `get` |
| Subform Tasks | insert/update fila |
| File upload field tasks | atributos de campo File |
| User roles & properties | roles/perfiles Creator |
| XML manipulation | parse XML |
| openUrl | navegación Creator |
| Integration `zoho.*` | skill `zoho-integration-tasks` |

---

## Diferencias por producto

| Aspecto | CRM | Creator | Books |
|---------|-----|---------|-------|
| Null check | `!= null` | `!= ""` | `containsKey()` |
| Query records | `zoho.crm.*` / REST | `Form[criteria]` | `zoho.books.*` |
| Empty set | N/A (API null) | Guard `.count() > 0` | N/A |
| Ternario | ❌ | ❌ | ❌ |
| while | ❌ | ❌ | ❌ |
| break / continue (en `for each`) | ✅ oficial | ✅ oficial | ✅ oficial |
| input. | CRM functions | Workflows | N/A |

---

## Gotchas universales

| Problema | Solución |
|----------|----------|
| JSON inválido POST | `map.toText()` no `toString()` |
| `.containsKey()` en resp API | `.toString().contains("key")` primero |
| Sin ternario | `if/else` |
| Sin while | Estático `{1,2,3}` o dinámico `"0,".repeat(n).toList(",")` |
| Saltar iteración / salir del loop | `continue;` / `break;` dentro de `for each` |
| IDs CRM | Siempre string `.toString()` |
| Lookup update CRM | `{"id": "66359..."}` |
| External calls en loop | 1 call/iteración — paginar |
| `variable = null` | Flag boolean + default |

---

## Troubleshooting

| Error | Causa | Fix |
|-------|-------|-----|
| Improper Statement | Ternario/while/null rvalue | if/else, for each |
| Comparison of non numeric | Date en criterio Creator | string dd-MMM-yyyy |
| Function returned null | Cadena .get() en null | ifNull / guard |
| INVALID_OAUTHTOKEN | Token expirado | Refresh OAuth connection |
| Type mismatch | Number vs string ID | `.toString()` |

Tabla extendida → skill `zoho-patterns`.

---

## Funciones String — referencia extendida

```deluge
texto = "Zoho Creator es genial";
length(texto);           // 21
subString(texto, 0, 4);  // "Zoho"
replaceAll(texto, "Creator", "CRM", false);
contains(texto, "Creator");   // true
startsWith(texto, "Zoho");    // true
endsWith(texto, "genial");    // true
indexOf(texto, "Creator");    // 5
left(texto, 4);               // "Zoho"
right(texto, 6);              // "genial"
trim("  hola  ");             // "hola"
split("a,b,c", ",");          // List {"a","b","c"}
"123".toLong();               // 123
"19.99".toDecimal();          // 19.99
getJSON('{"a":1}', "$.a");    // parse path
e = replaceAll(input.Email, "'", "\\'", false);  // escapar SQL/COQL
```

---

## Funciones Fecha — referencia extendida

```deluge
hoy = today();
ahora = now();
addDay(hoy, 7);
addWeek(hoy, 2);
addMonth(hoy, 1);   // ⚠️ inconsistente en Creator — preferir addDay
addYear(hoy, 1);
daysBetween('2024-01-01', '2024-01-15');  // 14
monthsBetween(f1, f2);
year(hoy); month(hoy); day(hoy);
hour(ahora); minute(ahora); second(ahora);
workDaysBetween(f1, f2);  // días hábiles (CRM)
hoy.toString("dd-MMM-yyyy");
hoy.toString("yyyy-MM-dd");
toTimeZone(ahora, "America/Mexico_City");
toDate("15-01-2024", "dd-MM-yyyy");
```

---

## Funciones Matemáticas — referencia extendida

```deluge
abs(-10); ceil(10.2); floor(10.8); round(10.567, 2);
max(10, 20); min(10, 20);
sqrt(16); pow(2, 8); mod(10, 3);
sin(0); cos(0); tan(0); asin(0.5); acos(0.5); atan(1);
log(10); exp(2);
randomNumber(1, 100);
```

---

## Operaciones de base de datos (Creator)

Sintaxis SQL-like en Creator forms:

```deluge
// Insert
insert into Clientes [ Nombre = "Acme" Status = "Activo" ];

// Update
update Clientes[ID == recId] [ Email = "nuevo@email.com" ];

// Delete
delete from Clientes[ID == recId];

// Fetch con criterio
rec = Clientes[Email == "a@acme.com"];
cnt = Clientes[Status == "Open"].count();

// Agregar a subform relacionado
insert into Pedidos.Lineas [ Pedido = recId SKU = "X1" Qty = 2 ];
```

En CRM Deluge functions usar `zoho.crm.*` tasks o REST — no syntax `Form[criteria]`.

---

## info, return, cancel

```deluge
info "Debug: " + variable.toString();  // log ejecución
return "valor";   // solo custom function con tipo de retorno
cancel submit;    // Creator On Validate — abortar submit
```

`return` no está disponible en eventos de formulario (On Load/Validate/Success). Ver sección return arriba.

---

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Lenguaje Deluge | `docs/en/deluge/language.md` · `docs/es/deluge/language.md` |
| Integration tasks | `docs/en/integration-tasks/index.md` · `docs/es/integration-tasks/index.md` |
| Patrones producción | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
| Índice docs | `docs/INDEX.md` |

