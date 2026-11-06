---
name: zoho-deluge-core
description: Writes, reviews, and debugs Deluge scripts in any Zoho product. Use for Deluge, invokeUrl, invokeapi, Map/List, dates, for each, break, continue, return, try-catch, sendmail, zoho.currentdate, custom functions, server-side workflows in CRM, Creator, Books, Desk. / Escribe, revisa y depura scripts Deluge en cualquier producto Zoho. Usar para Deluge, invokeUrl, invokeapi, Map/List, fechas, for each, break, continue, return, try-catch, sendmail, funciones personalizadas, workflows server-side.
---

# Zoho Deluge — Core language

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

## Scope

Server-side Deluge in **any** Zoho product. This skill covers the full **language**: types, operators, control structures, functions by category, invokeUrl, and system variables.

For Creator-specific work → skill `zoho-deluge-creator`.  
For `zoho.*` tasks → skill `zoho-integration-tasks`.  
For REST OAuth → skill `zoho-public-apis`.

**Full reference:** `docs/en/deluge/language.md` · `docs/es/deluge/language.md`  
**Patterns:** `docs/en/patterns/production.md` · `docs/es/patterns/production.md`

---

## Data types

### Text (string)

```deluge
nombre = "Juan Pérez";
email = "maria@empresa.com";
concat = nombre + " - " + email;
```

### Number (integer)

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

Common formats:
- Creator criteria: `fecha.toString("dd-MMM-yyyy")`
- REST APIs: `fecha.toString("yyyy-MM-dd")`
- ISO datetime: `ahora.toString("yyyy-MM-dd'T'HH:mm:ssXXX")`

### Time

Time independent of a date (12h or 24h). Enclosed in single quotes.

```deluge
hora = '14:30:00';
```

### File (FILE)

File from the web/cloud (`invokeUrl`, attachments). **Not** local files. File functions **do not** apply to Creator File fields via `fetch`/`input` (use `actualName` for the name).

`null` is a built-in constant with **no** data type.

---

## List

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

## Map

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

A Collection acts as **either** a list **or** a map; it does not mix list elements and key-value pairs in the same object.

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

Index: https://www.zoho.com/deluge/help/file-methods.html  
Operate on FILE (`invokeUrl`). Not on Creator `fetch`/`input`.

| Function | Use |
|----------|-----|
| `f.getFileName()` | Name |
| `f.getFileSize()` | Size |
| `f.getFileType()` | MIME / type |
| `f.getFileContent()` | Content |
| `f.isFile()` | Is FILE? |
| `f.setFileName(name)` | Rename |
| `f.setFileType(type)` | Type |
| `f.setCharset(cs)` | Charset |
| `f.setFilePassword(pwd)` | Password |
| `files.compress([name])` | ZIP (max ~2 MB) |
| `zip.extract()` | Extract ZIP |
| `value.toFile(fileName)` | Wrap as FILE (Creator ≤5 MB) |
| `f.convertToPDF()` | To PDF |

```deluge
f = invokeUrl [ url: "https://ejemplo.com/a.png" type: GET ];
nombre = f.getFileName();
zip = f.compress("paquete");
notas = "hola".toFile("notas.txt");
```

---

## Operators

Official index: Arithmetic, Assignment, Logical, Relational. `+` concatenates Text (no separate “string operators”). **No ternary.**

### Arithmetic
`+`, `-`, `*`, `/`, `%` · `pow(a, b)`

### Assignment
`=` · compound: `+=`, `-=`, `*=`, `/=`

### Relational
`==`, `!=`, `>`, `<`, `>=`, `<=`

### Logical
`&&`, `||`, `!`

---

## Control flow

### if / else if / else

```deluge
if(puntuacion >= 90) { grado = "A"; }
else if(puntuacion >= 80) { grado = "B"; }
else { grado = "C"; }
```

### for each

Deluge **has no `while`**. The two patterns that work reliably:

**1. Static — N is known and fixed** (constants, retries, fixed steps):

```deluge
for each i in {1,2,3,4,5,6}
{
    info i;
}
```

**2. Dynamic — N is calculated** (pagination, batches, list size):

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

`repeat` + `toList(",")` yields exactly `numOfPages` iterations. `for each index` is 0-based (`pageNumber`).

```deluge
// Iterate a list or query (when the collection already exists)
for each item in lista {
    info item;
}
```

### continue — skip the current iteration

Official: [continue](https://www.zoho.com/deluge/help/misc-statements/continue.html). Does not end the loop; only skips the rest of **this** iteration.

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

Available in On Load/Validate/Success, On User input, subform add/delete row, schedules, approval, custom functions, and report action items.

### break — exit the loop

Official: [break](https://www.zoho.com/deluge/help/misc-statements/break.html). Ends the `for each` and resumes at the first statement **after** the loop.

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

`break` vs `continue`: `break` kills the loop; `continue` kills only the iteration.

**There is no `while`.** For a fixed or calculated N use the two `for each` patterns above; inside them, use `break`/`continue`.

### return — function value

Official: [return](https://www.zoho.com/deluge/help/misc-statements/return.html). Only in **custom functions** that declare a return type. The type of `<expression>` must match or runtime throws.

```deluge
int thisapp.CalculateDays(date sdate, date edate)
{
    days = (((edate - sdate) / 86400000)).toLong();
    return days;
}
```

Not available in form On Load/Validate/Success or report action items. `void` functions do not return a value.

---

## Built-in functions (official index)

Each data type has its own set. File functions are separate: https://www.zoho.com/deluge/help/file-methods.html

| Category | Help |
|----------|------|
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

Below: Text, Date-time, Number, Collection/List/Map. Encryption, XML, type-check/conversion, and Time: see help.

---

## String functions

| Function | Use |
|---------|-----|
| `len(s)` | Length |
| `toUpperCase(s)` / `toLowerCase(s)` | Case |
| `trim(s)` / `ltrim` / `rtrim` | Whitespace |
| `subString(s, start, [end])` | Substring |
| `contains(s, sub)` | Contains |
| `startsWith(s, prefix)` / `endsWith` | Prefix/suffix |
| `replaceAll(s, old, new, ignoreCase)` | Replace |
| `indexOf(s, sub)` | Position |
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

## Date and time functions

| Function | Description |
|---------|-------------|
| `today()` | Current date (org TZ) |
| `now()` | Current DateTime |
| `zoho.currentdate` | System variable |
| `zoho.currenttime` | System variable |
| `addDay(d, n)` | Add days |
| `addMonth(d, n)` | Add months (⚠️ limited in Creator) |
| `addYear(d, n)` | Add years |
| `daysBetween(d1, d2)` | Difference in days |
| `monthsBetween(d1, d2)` | Difference in months |
| `year(d)` / `month(d)` / `day(d)` | Components |
| `hour(dt)` / `minute` / `second` | Time |
| `toTimeZone(dt, "America/Mexico_City")` | TZ |
| `d.toString("dd-MMM-yyyy")` | Format |

```deluge
hoy = zoho.currentdate;
manana = addDay(hoy, 1);
fmtCreator = hoy.toString("dd-MMM-yyyy");
fmtAPI = hoy.toString("yyyy-MM-dd");
```

---

## Math functions

| Function | Description |
|---------|-------------|
| `abs(n)` | Absolute value |
| `ceil(n)` / `floor(n)` / `round(n, dec)` | Rounding |
| `max(a,b)` / `min(a,b)` | Comparison |
| `sqrt(n)` / `pow(base, exp)` | Root/power |
| `randomNumber(min, max)` | Random integer |

---

## Collection / List / Map functions

| Function | Description |
|---------|-------------|
| `list.size()` | Size |
| `list.add(item)` | Add |
| `list.remove(index)` | Remove |
| `list.contains(item)` | Contains |
| `list.indexOf(item)` | Index |
| `list.distinct()` | Unique |
| `list.sort()` | Sort |
| `map.keys()` / `map.values()` | Keys/values |
| `map.containsKey(k)` | Has key |
| `map.toText()` | → JSON |

---

## System variables `zoho.*`

| Variable | Description |
|----------|-------------|
| `zoho.currentdate` | Current org date |
| `zoho.currenttime` | Current org DateTime |
| `zoho.loginuserid` | Active user email |
| `zoho.adminuserid` | Org admin email |
| `zoho.loginuser` | Alias of loginuserid |
| `zoho.ipaddress` | Request IP (web contexts) |
| `zoho.appuri` | App URI (Creator) |
| `zoho.appname` | App name (Creator) |

CRM Vertical Solutions / extensions (not CRM V8):
```deluge
orgVar = zoho.crm.getOrgVariable("nombre_variable");
```
Vertical index: skill `zoho-integration-tasks`. Standard CRM CRUD: `zoho.crm.v8.*`.

---

## Custom functions

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

Return types: `void`, `int`, `string`, `boolean`, `date`, `decimal`, `map`, `list`.

---

## sendmail and sendSms

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

Official: [try-catch](https://www.zoho.com/deluge/help/misc-statements/try-catch.html). `catch` runs only if a runtime error occurs in `try`. Code **after** the try-catch still runs (the error does not abort the whole function).

```deluge
try
{
    products = {"Creator","CRM","Cliq"};
    products = products.get(10);
}
catch(e)
{
    info e.lineNo;   // failing line
    info e.message;  // message
    info e;          // both: "Error at line : 6, Given index 10 is greater than the list size"
}
```

Available in the same workflow events as `break`/`continue`. Wrap `invokeUrl`/`invokeapi`, list indexes, and calculations that may receive empty values.

---

## invokeUrl — HTTP integration

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

// connection is ALWAYS a literal. A variable does not compile:
// connName = "zoho_crm_oauth";
// connection: connName

// PUT / PATCH / DELETE
resp = invokeUrl [ url: url type: PUT body: body.toText() headers: headers ];
```

### Safe invokeUrl response

```deluge
respStr = resp.toString();
if(respStr.contains(""data"")) {
    data = resp.get("data");
}
// NO usar .containsKey() directo en respuesta invokeUrl
```

### Nested Map in body

When `.toText()` does not produce the correct nested structure, build JSON manually:
```deluge
jsonStr = "{\"data\": [{\"id\": \"" + recordId + "\", \"Stage\": \"Closed\"}]}";
```

---

## invokeAPI — HTTP between Zoho services

Official: [invokeAPI](https://www.zoho.com/deluge/help/webhook/invokeapitask.html). HTTP client for **Zoho↔Zoho** APIs. Unlike `invokeUrl`, you pass only the **path**; the domain is resolved from the data center. Each execution consumes 1 external call.

```deluge
response = invokeapi
[
    service: zohocrm
    path: "/crm/v8/Contacts"
    type: GET
    connection: "zoho_crm_oauth"
];

info response.status;   // HTTP code, e.g. 200
info response.headers;
```

| Parameter | Use |
|-----------|-----|
| `service` | `zohocrm`, `zohobooks`, `zohoinvoice`, `zohobilling`, `zohoinventory`, `zohobookings`, `zohocreator`, `zohoworkdrive` |
| `path` | Path without base URL: `/crm/v3/Leads` |
| `type` | GET (default), POST, PUT, PATCH, DELETE, OPTIONS |
| `parameters` | Query string (TEXT or Map) |
| `body` | TEXT / FILE / Map — JSON: `body: data.toString()` + `application/json` header |
| `headers` | Map |
| `connection` | Connection name, quoted literal (`"connection_name"`). A variable does not compile. Often required in Creator |
| `response-format` | FILE, COLLECTION, STRING, NONE, DEFAULT |
| `response-decoding` | Default UTF-8 |

**When `invokeapi` vs `invokeUrl`:** Zoho→Zoho with a relative path → `invokeapi`. Full URL, external API, or headers/path invokeAPI does not cover → `invokeUrl`.

Limits: 40s socket timeout; Creator download max 5 MB (other services 15 MB). `for each` containing invokeAPI = N external calls.

---

## Deluge tasks (official categories)

Index: [Deluge Tasks](https://www.zoho.com/deluge/help/tasks.html)

| Category | Help |
|----------|------|
| Artificial Intelligence Tasks | Creator AI tasks |
| Creator Blueprint Tasks | Blueprint transitions |
| Conditions | `if`, `else`, `if()` expression |
| Data Access | `insert`, `update`, `delete`, `Form[criteria]` (Creator) |
| Composite fields | Name, Address, etc. |
| Client Functions | Creator client-side JS |
| Miscellaneous | `break`, `continue`, `return`, `info`, `cancel submit` |
| Debug | `info` |
| List Manipulations | `for each`, `add`, `get` |
| Map Manipulations | `put`, `get` |
| Subform Tasks | insert/update row |
| File upload field tasks | File field attributes |
| User roles & properties | Creator roles/profiles |
| XML manipulation | parse XML |
| openUrl | Creator navigation |
| Integration `zoho.*` | skill `zoho-integration-tasks` |

---

## Differences by product

| Aspect | CRM | Creator | Books |
|---------|-----|---------|-------|
| Null check | `!= null` | `!= ""` | `containsKey()` |
| Query records | `zoho.crm.*` / REST | `Form[criteria]` | `zoho.books.*` |
| Empty set | N/A (API null) | Guard `.count() > 0` | N/A |
| Ternary | ❌ | ❌ | ❌ |
| while | ❌ | ❌ | ❌ |
| break / continue (in `for each`) | ✅ official | ✅ official | ✅ official |
| input. | CRM functions | Workflows | N/A |

---

## Universal gotchas

| Problem | Solution |
|----------|----------|
| Invalid JSON POST | `map.toText()` not `toString()` |
| `.containsKey()` on API resp | `.toString().contains("key")` first |
| No ternary | `if/else` |
| No while | Static `{1,2,3}` or dynamic `"0,".repeat(n).toList(",")` |
| Skip iteration / exit loop | `continue;` / `break;` inside `for each` |
| CRM IDs | Always string `.toString()` |
| Lookup update CRM | `{"id": "66359..."}` |
| External calls in loop | 1 call/iteration — paginate |
| `variable = null` | Boolean flag + default |

---

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| Improper Statement | Ternary/while/null rvalue | if/else, for each |
| Comparison of non numeric | Date in Creator criteria | string dd-MMM-yyyy |
| Function returned null | `.get()` chain on null | ifNull / guard |
| INVALID_OAUTHTOKEN | Expired token | Refresh OAuth connection |
| Type mismatch | Number vs string ID | `.toString()` |

Extended table → skill `zoho-patterns`.

---

## String functions — extended reference

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

## Date functions — extended reference

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

## Math functions — extended reference

```deluge
abs(-10); ceil(10.2); floor(10.8); round(10.567, 2);
max(10, 20); min(10, 20);
sqrt(16); pow(2, 8); mod(10, 3);
sin(0); cos(0); tan(0); asin(0.5); acos(0.5); atan(1);
log(10); exp(2);
randomNumber(1, 100);
```

---

## Database operations (Creator)

SQL-like syntax in Creator forms:

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

In CRM Deluge functions use `zoho.crm.*` tasks or REST — not `Form[criteria]` syntax.

---

## info, return, cancel

```deluge
info "Debug: " + variable.toString();  // execution log
return "valor";   // custom function with a return type only
cancel submit;    // Creator On Validate — abort submit
```

`return` is not available on form On Load/Validate/Success. See the return section above.

---

---

## Full reference

| Topic | File |
|------|---------|
| Deluge language | `docs/en/deluge/language.md` · `docs/es/deluge/language.md` |
| Integration tasks | `docs/en/integration-tasks/index.md` · `docs/es/integration-tasks/index.md` |
| Production patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
| Docs index | `docs/INDEX.md` |
