---
name: zoho-deluge-core
description: Escribe, revisa y depura scripts Deluge en cualquier producto Zoho. Usar cuando el usuario pide ayuda con Deluge, invokeUrl, Map/List, fechas, operadores, control de flujo, funciones string/fecha/math/collection, variables zoho.currentdate/zoho.loginuserid, funciones personalizadas, sendmail, try-catch, workflows server-side en CRM, Creator, Books, Desk u otras apps Zoho.
---

# Zoho Deluge — Lenguaje base

## Alcance

Deluge server-side en **cualquier** producto Zoho. Este skill cubre el **lenguaje** completo: tipos, operadores, estructuras de control, funciones por categoría, invokeUrl y variables del sistema.

Para Creator específico → skill `zoho-deluge-creator`.  
Para tasks `zoho.*` → skill `zoho-integration-tasks`.  
Para REST OAuth → skill `zoho-public-apis`.

**Referencia completa:** `docs/deluge/language.md`  
**Patrones:** `docs/patterns/production.md`

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

```deluge
textoMapa = "{company:Zoho, product:Deluge}";
col = textoMapa.toCollection();
empresa = col.get("company");

col2 = Collection();
col2.add("item1");
lista = col2.toList();
```

---

## Operadores

### Aritméticos
`+`, `-`, `*`, `/`, `%`, `pow(a, b)`

### Relacionales
`==`, `!=`, `>`, `<`, `>=`, `<=`

### Lógicos
`&&`, `||`, `!`

### String
Concatenación con `+`

**No existe operador ternario** — usar `if/else` explícito.

---

## Control de flujo

### if / else if / else

```deluge
if(puntuacion >= 90) { grado = "A"; }
else if(puntuacion >= 80) { grado = "B"; }
else { grado = "C"; }
```

### for each

```deluge
for each item in lista {
    info item;
}
```

### Emular while (Deluge no tiene while)

```deluge
// Máximo N iteraciones con lista guardia
for each i in "".leftpad(49).toList("") {
    if(!continuar) { break; }
    // lógica
}
```

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

CRM adicional:
```deluge
orgVar = zoho.crm.getOrgVariable("nombre_variable");
```

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

```deluge
try {
    resp = invokeUrl [ url: "https://api.ejemplo.com" type: GET ];
} catch(e) {
    info "Error: " + e.toString();
}
```

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

## Diferencias por producto

| Aspecto | CRM | Creator | Books |
|---------|-----|---------|-------|
| Null check | `!= null` | `!= ""` | `containsKey()` |
| Query records | `zoho.crm.*` / REST | `Form[criteria]` | `zoho.books.*` |
| Empty set | N/A (API null) | Guard `.count() > 0` | N/A |
| Ternario | ❌ | ❌ | ❌ |
| while | ❌ | ❌ | ❌ |
| input. | CRM functions | Workflows | N/A |

---

## Gotchas universales

| Problema | Solución |
|----------|----------|
| JSON inválido POST | `map.toText()` no `toString()` |
| `.containsKey()` en resp API | `.toString().contains("key")` primero |
| Sin ternario | `if/else` |
| Sin while | `for each` + guard list |
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
return "valor";   // funciones standalone / Custom API
cancel submit;    // Creator On Validate — abortar submit
```

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Lenguaje Deluge | `docs/deluge/language.md` |
| Integration tasks | `docs/integration-tasks/index.md` |
| Patrones producción | `docs/patterns/production.md` |
| Índice docs | `docs/INDEX.md` |

