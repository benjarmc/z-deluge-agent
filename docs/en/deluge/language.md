# Complete Documentation: Zoho Deluge

> Language: English. Spanish: see the matching path under docs/es/.

This documentation provides a comprehensive guide to the Zoho Deluge scripting language, including all data structures, functions, operators, and actions available to automate workflows and create custom logic in Zoho applications.

---

## Table of Contents

1. [Introduction to Deluge](#introduction-to-deluge)
2. [Data Types and Variables](#data-types-and-variables)
3. [Data Structures](#data-structures)
4. [File functions](#file-functions)
5. [Operators](#operators)
5. [Control Structures](#control-structures)
6. [String Functions](#string-functions)
7. [Date and Time Functions](#date-and-time-functions)
8. [Mathematical Functions](#mathematical-functions)
9. [Database Functions](#database-functions)
10. [CRM Integration Functions](#crm-integration-functions)
11. [Creator Integration Functions](#creator-integration-functions)
12. [Communication Functions](#communication-functions)
13. [Custom Functions](#custom-functions)
14. [Error Handling](#error-handling)
15. [External API Integration](#external-api-integration)
16. [Practical Examples](#practical-examples)

---

## Introduction to Deluge

**Deluge** (Data Enriched Language for the Universal Grid Environment) is Zoho's proprietary scripting language designed to add logic and automate workflows in Zoho applications.

### Main Features

- **Friendly Syntax**: Easy to learn for developers and non-developers
- **Integrated Query Language**: Combines high-level programming with data manipulation
- **Relational Data Model**: Applications with fully normalized data models
- **Native Integration**: Connects easily with Zoho services and external applications

### Where Deluge Is Used

- **Zoho CRM**: Workflows, custom functions, automations
- **Zoho Creator**: Form scripts, workflows, integrations
- **Zoho Books**: Accounting automations
- **Zoho Projects**: Automated project management
- **Other Zoho products**: Any application that supports scripting

---

## Data Types and Variables

### Basic Data Types

#### 1. Text (String)

Character sequences enclosed in double quotes.

```deluge
nombre = "Juan Pérez";
email = "[email protected]";
mensaje = "Hola, bienvenido a Zoho";
```

#### 2. Number (Integer)

Integer values without decimals.

```deluge
edad = 30;
cantidad = 100;
id = 12345;
```

#### 3. Decimal

Numbers with a decimal point for precise calculations.

```deluge
precio = 19.99;
descuento = 0.15;
total = 1234.56;
```

#### 4. Boolean

Logical values: `true` or `false` (without quotes).

```deluge
activo = true;
completado = false;
esValido = true;
```

#### 5. Date-Time

Date and time values enclosed in single quotes.

```deluge
fechaCreacion = '2024-01-15';
fechaHora = '2024-01-15 14:30:00';
hoy = today();
ahora = now();
```

#### 6. Time

Time independent of a date (12h or 24h), enclosed in single quotes.

```deluge
hora = '14:30:00';
```

#### 7. File (FILE)

A file fetched from the web or a cloud service (`invokeUrl`, attachments). **Not** local files. [File functions](https://www.zoho.com/deluge/help/file-methods.html) operate on this type.

**Note:** `null` is a built-in constant with no value and **does not** belong to any data type.

### Variable Declaration

```deluge
// Simple variables
nombre = "Juan";
edad = 25;
activo = true;

// Variables with explicit types (in functions)
string nombreCompleto = "Juan Pérez";
int cantidad = 10;
boolean esValido = true;
date fechaInicio = '2024-01-01';
```

---

## Data Structures

### 1. List

Ordered collection of elements accessible by index (starts at 0).

#### Create List

```deluge
// Product list
productos = {"Creator", "CRM", "Projects", "Books"};

// Number list
numeros = {1, 2, 3, 4, 5};

// Mixed list
mixta = {"Texto", 123, true, '2024-01-01'};
```

#### List Operations

```deluge
productos = {"Creator", "CRM", "Projects"};

// Get element by index
primerProducto = productos.get(0);  // "Creator"

// Add element
productos.add("Books");

// Remove element by index
productos.remove(2);  // Removes "Projects"

// List size
tamaño = productos.size();

// Check if it contains an element
contiene = productos.contains("CRM");  // true

// Get element index
indice = productos.indexOf("CRM");  // 1

// Convert to text
texto = productos.toString();  // "{Creator, CRM, Projects, Books}"

// Iterate over list
for each producto in productos {
    info producto;
}
```

### 2. Map

Collection of key-value pairs where each key is unique.

#### Create Map

```deluge
// Product details map
producto = Map();
producto.put("nombre", "Zoho CRM");
producto.put("precio", 29.99);
producto.put("activo", true);

// Or create directly
producto = {
    "nombre": "Zoho CRM",
    "precio": 29.99,
    "activo": true
};
```

#### Map Operations

```deluge
producto = Map();
producto.put("nombre", "Zoho CRM");
producto.put("precio", 29.99);

// Get value by key
nombre = producto.get("nombre");  // "Zoho CRM"

// Check if it contains a key
tienePrecio = producto.containsKey("precio");  // true

// Get all keys
claves = producto.keys();  // List of keys

// Get all values
valores = producto.values();  // List of values

// Remove key-value pair
producto.remove("precio");

// Map size
tamaño = producto.size();

// Convert to text
texto = producto.toString();
```

### 3. Collection

Holds **either** a list of elements **or** key-value pairs. A single Collection **cannot** mix both at once.

#### Create Collection from Text

```deluge
// From map text
textoMapa = "{company:Zoho, product:Deluge}";
coleccion = textoMapa.toCollection();
empresa = coleccion.get("company");  // "Zoho"

// From list text
textoLista = "{company, Zoho, product, Deluge}";
coleccionLista = textoLista.toCollection();
primerElemento = coleccionLista.get(0);  // "company"
```

#### Collection Operations

```deluge
// Create empty collection
coleccion = Collection();

// Add elements
coleccion.add("elemento1");
coleccion.add("elemento2");

// Get by index or key
valor = coleccion.get(0);
valor = coleccion.get("clave");

// Convert to list
lista = coleccion.toList();

// Convert to map
mapa = coleccion.toMap();
```

---

## File functions

Index: https://www.zoho.com/deluge/help/file-methods.html

Operate on FILE (`invokeUrl`). **Do not** apply to Creator field files via `fetch` or `input` (upload name: `actualName`).

| Function | Use |
|----------|-----|
| `f.getFileName()` | Name |
| `f.getFileSize()` | Size |
| `f.getFileType()` | Type / MIME |
| `f.getFileContent()` | Content |
| `f.isFile()` | Is FILE? |
| `f.setFileName(name)` | Rename |
| `f.setFileType(type)` | Type |
| `f.setCharset(cs)` | Charset |
| `f.setFilePassword(pwd)` | Password |
| `files.compress([name])` | ZIP (max ~2 MB) |
| `zip.extract()` | Extract ZIP |
| `value.toFile(fileName)` | Wrap as FILE (Creator ≤5 MB; others ≤50 MB) |
| `f.convertToPDF()` | Convert to PDF |

```deluge
f = invokeUrl
[
    url: "https://ejemplo.com/sample.png"
    type: GET
];
nombre = f.getFileName();
zip = f.compress("paquete");
notas = "contenido".toFile("notas.txt");
```

---

## Operators

### Arithmetic Operators

```deluge
a = 10;
b = 3;

suma = a + b;        // 13
resta = a - b;       // 7
multiplicacion = a * b;  // 30
division = a / b;    // 3.333...
modulo = a % b;      // 1 (division remainder)
potencia = pow(a, b); // 1000
```

`+` also concatenates Text. Official types: Arithmetic, Assignment, Logical, Relational.

### Assignment Operators

```deluge
n = 10;
n += 5;   // 15
n -= 2;   // 13
n *= 2;   // 26
n /= 2;   // 13
```

### Relational Operators

```deluge
a = 10;
b = 5;

igual = (a == b);        // false
diferente = (a != b);    // true
mayor = (a > b);         // true
menor = (a < b);         // false
mayorIgual = (a >= b);   // true
menorIgual = (a <= b);   // false
```

### Logical Operators

```deluge
a = true;
b = false;

y = (a && b);      // false (AND)
o = (a || b);      // true (OR)
no = !a;           // false (NOT)
```

### String Operators

```deluge
nombre = "Juan";
apellido = "Pérez";

// Concatenation
nombreCompleto = nombre + " " + apellido;  // "Juan Pérez"

// Equality check
esIgual = (nombre == "Juan");  // true
```

Officially `+` on Text is an **arithmetic** operator, not a fifth family.

---

## Built-in functions (official index)

Each type has its own set: [Collection](https://www.zoho.com/deluge/help/functions/collection.html), [Text](https://www.zoho.com/deluge/help/functions/text.html), [Number](https://www.zoho.com/deluge/help/functions/number.html), [Date-time](https://www.zoho.com/deluge/help/functions/date-time.html), [Time](https://www.zoho.com/deluge/help/functions/time.html), [Encryption](https://www.zoho.com/deluge/help/encryption-tasks.html), [Logical](https://www.zoho.com/deluge/help/functions/logical.html), [Boolean-returning](https://www.zoho.com/deluge/help/functions-returning-boolean.html), [Type check](https://www.zoho.com/deluge/help/functions/type-check.html), [Type conversion](https://www.zoho.com/deluge/help/functions/conversion.html), [Utilities](https://www.zoho.com/deluge/help/functions/utilities.html), [XML](https://www.zoho.com/deluge/help/functions/xml.html), [List](https://www.zoho.com/deluge/help/functions/list.html), [Map](https://www.zoho.com/deluge/help/functions/key-value.html), [File](https://www.zoho.com/deluge/help/file-methods.html).

---

## Control Structures

### 1. If-Else

```deluge
// Simple If
if (edad >= 18) {
    info "Es mayor de edad";
}

// If-Else
if (estado == "Activo") {
    info "El registro está activo";
} else {
    info "El registro está inactivo";
}

// If-Else If-Else
if (puntuacion >= 90) {
    calificacion = "A";
} else if (puntuacion >= 80) {
    calificacion = "B";
} else if (puntuacion >= 70) {
    calificacion = "C";
} else {
    calificacion = "F";
}
```

### 2. For Each Loop

```deluge
// Iterate over list
productos = {"Creator", "CRM", "Projects"};
for each producto in productos {
    info producto;
}

// Iterate over map
detalles = Map();
detalles.put("nombre", "Juan");
detalles.put("edad", 30);

for each clave in detalles.keys() {
    valor = detalles.get(clave);
    info clave + ": " + valor;
}

// Iterate over records
for each registro in Empleados[Estado == "Activo"] {
    info registro.Nombre;
    // Update record
    update registro
    [
        Ultima_Revision = today()
    ];
}
```

### 3. Loops without `while` — static vs dynamic

Deluge **has no `while`**. Use `for each` with one of these two forms (the most reliable):

**Static — N is fixed and known** (steps, retries, a literal range):

```deluge
for each i in {1,2,3,4,5,6}
{
    info i;
}
```

**Dynamic — N is calculated** (pagination, batches, list size):

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

`"0,".repeat(numOfPages).toList(",")` yields exactly `numOfPages` items. `for each index` provides the 0-based index in `pageNumber`.

```deluge
// Iterate a collection that already exists
for each producto in {"Creator", "CRM", "Projects"} {
    info producto;
}
```

### continue / break / return

**`continue;`** skips the rest of the current iteration. **`break;`** ends the `for each`. There is no `while`. Docs: [continue](https://www.zoho.com/deluge/help/misc-statements/continue.html) · [break](https://www.zoho.com/deluge/help/misc-statements/break.html) · [return](https://www.zoho.com/deluge/help/misc-statements/return.html)

```deluge
for each rec in colors
{
    if(rec == "Red")
    {
        continue;
    }
    info rec;
}

for each technician in technicians
{
    if(technician.get("status") == "available")
    {
        info technician.get("name") + " is available";
        break;
    }
}

int thisapp.CalculateDays(date sdate, date edate)
{
    days = (((edate - sdate) / 86400000)).toLong();
    return days;
}
```

`return` only in custom functions with a declared return type. Not in form On Load/Validate/Success.

### 4. Ternary Operator (Conditional If)

```deluge
// Syntax: if(condicion, valorSiVerdadero, valorSiFalso)
edad = 20;
mensaje = if(edad >= 18, "Mayor de edad", "Menor de edad");

// Equivalent to:
if (edad >= 18) {
    mensaje = "Mayor de edad";
} else {
    mensaje = "Menor de edad";
}
```

---

## String Functions

### Basic Functions

```deluge
texto = "Hola Mundo";

// Length
longitud = length(texto);  // 10

// Substring
sub = substring(texto, 0, 4);  // "Hola"

// Replace
nuevo = replace(texto, "Mundo", "Zoho");  // "Hola Zoho"

// Lowercase
minusculas = toLowerCase(texto);  // "hola mundo"

// Uppercase
mayusculas = toUpperCase(texto);  // "HOLA MUNDO"

// Remove spaces
sinEspacios = trim("  Hola  ");  // "Hola"
```

### Search Functions

```deluge
texto = "Zoho Creator es genial";

// Contains
contiene = contains(texto, "Creator");  // true

// Starts with
comienza = startsWith(texto, "Zoho");  // true

// Ends with
termina = endsWith(texto, "genial");  // true

// Find position
posicion = find(texto, "Creator");  // 5

// Left
izquierda = left(texto, 4);  // "Zoho"

// Right
derecha = right(texto, 6);  // "genial"
```

### Split and Join Functions

```deluge
// Split string
texto = "Juan,Pérez,30";
partes = split(texto, ",");  // List: {"Juan", "Pérez", "30"}

// Join list into string
nombres = {"Juan", "María", "Pedro"};
unido = nombres.toString(", ");  // "Juan, María, Pedro"
```

### Conversions

```deluge
// Number to text
numero = 123;
texto = numero.toString();  // "123"

// Text to number
texto = "456";
numero = texto.toLong();  // 456

// Text to decimal
texto = "123.45";
decimal = texto.toDecimal();  // 123.45
```

---

## Date and Time Functions

### Current Date Functions

```deluge
// Current date
hoy = today();  // '2024-01-15'

// Current date and time
ahora = now();  // '2024-01-15 14:30:00'
```

### Date Operations

```deluge
fecha = '2024-01-15';

// Add days
futuro = addDays(fecha, 7);  // '2024-01-22'

// Add weeks
futuro = addWeeks(fecha, 2);  // '2024-01-29'

// Add months
futuro = addMonths(fecha, 1);  // '2024-02-15'

// Add years
futuro = addYears(fecha, 1);  // '2025-01-15'

// Days between dates
fecha1 = '2024-01-01';
fecha2 = '2024-01-15';
dias = daysBetween(fecha1, fecha2);  // 14
```

### Extract Date Components

```deluge
fecha = '2024-01-15 14:30:00';

// Year
año = year(fecha);  // 2024

// Month
mes = month(fecha);  // 1

// Day
dia = day(fecha);  // 15

// Hour
hora = hour(fecha);  // 14

// Minute
minuto = minute(fecha);  // 30
```

### Format Dates

```deluge
fecha = '2024-01-15';

// Format date
formateada = format(fecha, "dd-MMM-yyyy");  // "15-Jan-2024"
formateada = format(fecha, "dd/MM/yyyy");    // "15/01/2024"
formateada = format(fecha, "yyyy-MM-dd");   // "2024-01-15"

// Convert to date
texto = "15-01-2024";
fecha = toDate(texto, "dd-MM-yyyy");

// Get date as text
fechaTexto = getDate(fecha);
```

---

## Mathematical Functions

### Basic Functions

```deluge
numero = 25.7;

// Absolute value
absoluto = abs(-25.7);  // 25.7

// Round
redondeado = round(25.7);  // 26

// Ceiling (round up)
techo = ceil(25.3);  // 26

// Floor (round down)
piso = floor(25.7);  // 25

// Maximum
maximo = max(10, 20);  // 20

// Minimum
minimo = min(10, 20);  // 10
```

### Advanced Functions

```deluge
// Square root
raiz = sqrt(25);  // 5.0

// Power
potencia = pow(2, 3);  // 8

// Natural logarithm
logNat = log(10);  // ~2.302

// Base 10 logarithm
log10 = log10(100);  // 2.0

// Exponential
exp = exp(2);  // ~7.389

// Modulo (remainder)
resto = mod(10, 3);  // 1
```

### Trigonometric Functions

```deluge
angulo = 1.5708;  // ~90 degrees in radians

// Sine
seno = sin(angulo);  // ~1.0

// Cosine
coseno = cos(angulo);  // ~0.0

// Tangent
tangente = tan(angulo);

// Arcsine
arcseno = asin(0.5);

// Arccosine
arcocoseno = acos(0.5);

// Arctangent
arcotangente = atan(1.0);

// Two-argument arctangent
arcotangente2 = atan2(1.0, 1.0);
```

### Random Numbers

```deluge
// Random number between 0 and 1
aleatorio = random();  // 0.0 to 1.0

// Generate a random number in a range
minimo = 1;
maximo = 100;
aleatorio = floor(random() * (maximo - minimo + 1)) + minimo;
```

---

## Database Functions

### Insert Record

```deluge
// Insert a record
response = insert into Productos
[
    Nombre = "Libro"
    Precio = 150.00
    Codigo = "PROD-001"
    En_Stock = 20
    Activo = true
];

// The response contains the ID of the created record
idRegistro = response.ID;
info "Registro creado con ID: " + idRegistro;
```

### Get Records

```deluge
// Get a specific record by ID
empleado = Empleados[ID == 12345];
info empleado.Nombre;

// Get record with criterion
empleado = Empleados[Email == "[email protected]"];
info empleado.Nombre;

// Get multiple records with criterion
empleadosActivos = Empleados[Estado == "Activo"];
for each emp in empleadosActivos {
    info emp.Nombre;
}

// Get all records
todosEmpleados = Empleados;
for each emp in todosEmpleados {
    info emp.Nombre;
}

// Multiple criteria
empleados = Empleados[(Estado == "Activo") && (Edad > 25)];
```

### Update Records

```deluge
// Update a specific record
empleado = Empleados[ID == 12345];
update empleado
[
    Estado = "Inactivo"
    Fecha_Salida = today()
];

// Update multiple records
for each emp in Empleados[Estado == "Activo"] {
    update emp
    [
        Ultima_Revision = today()
        Salario = emp.Salario * 1.1  // 10% increase
    ];
}
```

### Delete Records

```deluge
// Delete records with criterion
delete from Empleados[Estado == "Resignado"];

// Delete a specific record
empleado = Empleados[ID == 12345];
delete empleado;
```

### Relationship Operations

```deluge
// Get related records
cliente = Clientes[ID == 12345];
pedidos = cliente.Pedidos;  // Get related orders

for each pedido in pedidos {
    info pedido.Numero_Pedido;
    info pedido.Total;
}

// Create related record
cliente = Clientes[ID == 12345];
nuevoPedido = insert into Pedidos
[
    Cliente = cliente
    Fecha = today()
    Total = 500.00
];
```

---

## CRM Integration Functions

There are **two** official indexes; do not mix them:

- **CRM V8** (`zoho.crm.v8.*`): CRUD between Zoho products and standard CRM. [V8 help](https://www.zoho.com/deluge/help/crm-integration-tasks-V8.html)
- **Vertical Solutions / extensions** (`getOrgVariable`, `invokeConnector`, `getRecords("users",…)`): Vertical CRM and extensions only. [Vertical help](https://www.zoho.com/deluge/help/crm-tasks.html)

Signatures and tables: `docs/en/integration-tasks/index.md`. Examples in this section use the legacy `zoho.crm.*` namespace (no `.v8`).

### Get Records

```deluge
// Get records from a module
response = zoho.crm.getRecords("Contacts");
registros = response.get("data");

for each registro in registros {
    info registro.get("First_Name");
}

// Get record by ID
response = zoho.crm.getRecordById("Contacts", "66359000000015039");
registro = response.get("data").get(0);
info registro.get("First_Name");

// Search records
response = zoho.crm.searchRecords("Contacts", "First_Name:Juan");
registros = response.get("data");
```

### Create Record

```deluge
// Create a record
datos = Map();
datos.put("First_Name", "Juan");
datos.put("Last_Name", "Pérez");
datos.put("Email", "[email protected]");

response = zoho.crm.createRecord("Contacts", datos);
idRegistro = response.get("data").get(0).get("id");
info "Registro creado: " + idRegistro;
```

### Update Record

```deluge
// Update record
datos = Map();
datos.put("Phone", "+1234567890");
datos.put("Mailing_City", "Madrid");

response = zoho.crm.updateRecord("Contacts", "66359000000015039", datos);
info "Registro actualizado";
```

### Delete Record

```deluge
// Delete record
response = zoho.crm.deleteRecord("Contacts", "66359000000015039");
info "Registro eliminado";
```

### Get Related Records

```deluge
// Get related records
response = zoho.crm.getRelatedRecords("Contacts", "66359000000015039", "Deals");
deals = response.get("data");

for each deal in deals {
    info deal.get("Deal_Name");
}
```

### Convert Lead

```deluge
// Convert lead to contact, account, and opportunity
response = zoho.crm.convertLead("66359000000015039");
info "Lead convertido";
```

### Bulk Operations

```deluge
// Create multiple records
registros = list();
for each i in {1, 2, 3} {
    datos = Map();
    datos.put("First_Name", "Contacto " + i);
    datos.put("Email", "contacto" + i + "@example.com");
    registros.add(datos);
}

response = zoho.crm.bulkCreateRecords("Contacts", registros);
info "Registros creados en lote";

// Update multiple records
updates = list();
for each registro in registrosExistentes {
    update = Map();
    update.put("id", registro.get("id"));
    update.put("Phone", "+1234567890");
    updates.add(update);
}

response = zoho.crm.bulkUpdateRecords("Contacts", updates);
info "Registros actualizados en lote";
```

### Upsert (Update or Create)

```deluge
// Upsert: updates if it exists, creates if it does not
datos = Map();
datos.put("Email", "[email protected]");
datos.put("First_Name", "Juan");
datos.put("Last_Name", "Pérez");

response = zoho.crm.upsertRecord("Contacts", datos);
info "Registro upserted";
```

### Attach File

```deluge
// Attach file to record
response = zoho.crm.attachFile("Contacts", "66359000000015039", "archivo.pdf");
info "Archivo adjuntado";
```

---

## Creator Integration Functions

### Create Record

```deluge
// Create a record
response = zoho.creator.createRecord("Mi_Aplicacion", "Formulario_Contactos", {
    "Nombre": "Juan Pérez",
    "Email": "[email protected]",
    "Telefono": "+1234567890"
});

idRegistro = response.get("ID");
info "Registro creado: " + idRegistro;
```

### Create Multiple Records

```deluge
// Create multiple records
registros = list();
for each i in {1, 2, 3} {
    registro = Map();
    registro.put("Nombre", "Contacto " + i);
    registro.put("Email", "contacto" + i + "@example.com");
    registros.add(registro);
}

response = zoho.creator.createRecords("Mi_Aplicacion", "Formulario_Contactos", registros);
info "Registros creados";
```

### Get Record by ID

```deluge
// Get specific record
response = zoho.creator.getRecordById("Mi_Aplicacion", "Reporte_Contactos", "66359000000015039");
registro = response.get("data");
info registro.get("Nombre");
```

### Get Records

```deluge
// Get all records
response = zoho.creator.getRecords("Mi_Aplicacion", "Reporte_Contactos");
registros = response.get("data");

for each registro in registros {
    info registro.get("Nombre");
}
```

### Update Record

```deluge
// Update record by ID
response = zoho.creator.updateRecordById("Mi_Aplicacion", "Reporte_Contactos", "66359000000015039", {
    "Telefono": "+9876543210",
    "Ciudad": "Barcelona"
});

info "Registro actualizado";
```

### Update Multiple Records

```deluge
// Update multiple records
updates = list();
for each registro in registrosExistentes {
    update = Map();
    update.put("ID", registro.get("ID"));
    update.put("Estado", "Actualizado");
    updates.add(update);
}

response = zoho.creator.updateRecords("Mi_Aplicacion", "Reporte_Contactos", updates);
info "Registros actualizados";
```

---

## Communication Functions

### Send Email (sendmail)

```deluge
// Simple email
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Bienvenido a Zoho"
    message: "Gracias por unirte a nuestra plataforma."
];

// Email with CC and BCC
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    cc: "[email protected]"
    bcc: "[email protected]"
    subject: "Reporte Mensual"
    message: "Adjunto encontrarás el reporte del mes."
];

// HTML email
mensajeHTML = "<h1>Bienvenido</h1><p>Gracias por registrarte.</p>";
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Bienvenido"
    message: mensajeHTML
    content type: "text/html"
];

// Email with attachments
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Documento Adjunto"
    message: "Por favor revisa el documento adjunto."
    attachments: "documento.pdf"
];
```

### Send SMS (sendSms)

```deluge
// SMS with Twilio
sendSms
[
    to: "+1234567890"
    message: "Tu pedido ha sido enviado. Número de seguimiento: 12345"
    connection: "twilio_connection"
];

// SMS with Clickatell
sendSms
[
    to: "+1234567890"
    message: "Recordatorio: Tu cita es mañana a las 10:00 AM"
    connection: "clickatell_connection"
];
```

---

## Custom Functions

### Define Function

```deluge
// Function that returns a value
int CalcularDias(date fechaInicio, date fechaFin) {
    dias = daysBetween(fechaInicio, fechaFin);
    return dias;
}

// Function with no return
void EnviarNotificacion(string email, string mensaje) {
    sendmail
    [
        from: zoho.adminuserid
        to: email
        subject: "Notificación"
        message: mensaje
    ];
}

// Function with multiple parameters
string FormatearNombre(string nombre, string apellido) {
    nombreCompleto = nombre + " " + apellido;
    return toUpperCase(nombreCompleto);
}
```

### Call Function

```deluge
// From the same application
dias = thisapp.CalcularDias('2024-01-01', '2024-01-15');
info "Días transcurridos: " + dias;

// From another application
dias = OtraAplicacion.CalcularDias('2024-01-01', '2024-01-15');

// Function with no return
thisapp.EnviarNotificacion("[email protected]", "Mensaje de prueba");
```

### Complete Function Example

```deluge
// Function to calculate discount
decimal CalcularDescuento(decimal precio, decimal porcentaje) {
    if (porcentaje > 100 || porcentaje < 0) {
        return 0;
    }
    descuento = precio * (porcentaje / 100);
    return descuento;
}

// Use the function
precioOriginal = 100.00;
descuento = thisapp.CalcularDescuento(precioOriginal, 15);
precioFinal = precioOriginal - descuento;
info "Precio final: " + precioFinal;
```

---

## Error Handling

### Try-Catch

```deluge
// Basic error handling
try {
    // Code that may fail
    response = invokeUrl
    [
        url: "https://api.example.com/data"
        type: GET
    ];
    info "Éxito: " + response;
} catch (e) {
    // Handle the error
    info e.lineNo;
    info e.message;
    info e;  // "Error at line : N, ..."
}

// Nested Try-Catch
try {
    // Main operation
    registro = Clientes[ID == 12345];
    
    try {
        // Secondary operation
        update registro
        [
            Estado = "Actualizado"
        ];
    } catch (e2) {
        info "Error al actualizar: " + e2.toString();
    }
    
} catch (e1) {
    info "Error al obtener registro: " + e1.toString();
}
```

### Validation Before Execution

```deluge
// Validate before executing
if (email != null && email != "") {
    if (contains(email, "@")) {
        try {
            sendmail
            [
                from: zoho.adminuserid
                to: email
                subject: "Test"
                message: "Mensaje de prueba"
            ];
            info "Email enviado correctamente";
        } catch (e) {
            info "Error al enviar email: " + e.toString();
        }
    } else {
        info "Email inválido";
    }
} else {
    info "Email no proporcionado";
}
```

### Error Logging

```deluge
// Function for error logging
void LogError(string operacion, string error) {
    fechaHora = now();
    log = fechaHora + " - " + operacion + " - Error: " + error;
    
    // Save in error log
    insert into Log_Errores
    [
        Fecha_Hora = fechaHora
        Operacion = operacion
        Error = error
    ];
}

// Use logging
try {
    response = zoho.crm.getRecordById("Contacts", "12345");
} catch (e) {
    thisapp.LogError("Obtener Contacto", e.toString());
    info "Error registrado en log";
}
```

---

## External API Integration

### invokeUrl - GET Request

```deluge
// Simple GET
response = invokeUrl
[
    url: "https://api.example.com/data"
    type: GET
];

info response;

// GET with parameters
params = Map();
params.put("api_key", "tu_api_key");
params.put("limit", "10");

response = invokeUrl
[
    url: "https://api.example.com/data"
    type: GET
    parameters: params
];

info response;
```

### invokeUrl - POST Request

```deluge
// POST with JSON data
datos = Map();
datos.put("nombre", "Juan");
datos.put("email", "[email protected]");

response = invokeUrl
[
    url: "https://api.example.com/contactos"
    type: POST
    body: datos.toString()
    headers: {"Content-Type": "application/json"}
];

info response;

// POST with authentication
headers = Map();
headers.put("Authorization", "Bearer tu_token");
headers.put("Content-Type", "application/json");

response = invokeUrl
[
    url: "https://api.example.com/data"
    type: POST
    body: datos.toString()
    headers: headers
];
```

### invokeUrl - PUT/PATCH Request

```deluge
// PUT to update
datos = Map();
datos.put("nombre", "Juan Actualizado");

response = invokeUrl
[
    url: "https://api.example.com/contactos/123"
    type: PUT
    body: datos.toString()
    headers: {"Content-Type": "application/json"}
];

// PATCH for partial update
response = invokeUrl
[
    url: "https://api.example.com/contactos/123"
    type: PATCH
    body: datos.toString()
    headers: {"Content-Type": "application/json"}
];
```

### invokeUrl - DELETE Request

```deluge
// DELETE
response = invokeUrl
[
    url: "https://api.example.com/contactos/123"
    type: DELETE
];

info response;
```

### invokeUrl with Connections

```deluge
// Use a configured connection (OAuth, etc.)
response = invokeUrl
[
    url: "https://api.dropbox.com/2/files/list_folder"
    type: POST
    body: datos.toString()
    connection: "dropbox_connection"
];

info response;
```

### invokeAPI — Zoho-to-Zoho APIs

Unlike `invokeUrl`, you pass only the **path**. The domain comes from the data center. [invokeAPI](https://www.zoho.com/deluge/help/webhook/invokeapitask.html)

```deluge
response = invokeapi
[
    service: zohocrm
    path: "/crm/v8/Contacts"
    type: GET
    connection: "zoho_crm_oauth"
];
info response.status;
info response.headers;
```

Services: `zohocrm`, `zohobooks`, `zohoinvoice`, `zohobilling`, `zohoinventory`, `zohobookings`, `zohocreator`, `zohoworkdrive`. Each execution = 1 external call. 40s timeout.

For a full URL or a non-Zoho API use `invokeUrl`.

### Handling JSON Responses

```deluge
// Parse JSON response
response = invokeUrl
[
    url: "https://api.example.com/data"
    type: GET
];

// The response may be a Map or a List
if (response != null) {
    if (response.containsKey("data")) {
        datos = response.get("data");
        for each item in datos {
            info item.get("nombre");
        }
    }
}
```

---

## Practical Examples

### Example 1: Sync CRM Contacts to Creator

```deluge
// Get contacts from CRM
crmContacts = zoho.crm.getRecords("Contacts");
contactos = crmContacts.get("data");

// Create in Creator
for each contacto in contactos {
    datos = Map();
    datos.put("Nombre", contacto.get("First_Name") + " " + contacto.get("Last_Name"));
    datos.put("Email", contacto.get("Email"));
    datos.put("Telefono", contacto.get("Phone"));
    
    try {
        response = zoho.creator.createRecord("Mi_App", "Formulario_Contactos", datos);
        info "Contacto sincronizado: " + datos.get("Nombre");
    } catch (e) {
        info "Error al sincronizar: " + e.toString();
    }
}
```

### Example 2: Calculate Commissions

```deluge
// Function to calculate commission
decimal CalcularComision(decimal venta, decimal porcentaje) {
    if (venta > 10000) {
        porcentaje = porcentaje * 1.1;  // 10% bonus
    }
    comision = venta * (porcentaje / 100);
    return comision;
}

// Calculate salesperson commissions
vendedores = Empleados[Rol == "Vendedor"];
for each vendedor in vendedores {
    ventas = vendedor.Ventas;
    totalVentas = 0;
    
    for each venta in ventas {
        totalVentas = totalVentas + venta.Monto;
    }
    
    comision = thisapp.CalcularComision(totalVentas, 5.0);
    
    // Update commission
    update vendedor
    [
        Comision_Mes = comision
        Total_Ventas = totalVentas
    ];
}
```

### Example 3: Send Automatic Reminders

```deluge
// Get today's appointments
hoy = today();
citasHoy = Citas[Fecha == hoy];

for each cita in citasHoy {
    cliente = cita.Cliente;
    horaCita = hour(cita.Fecha_Hora);
    horaActual = hour(now());
    
    // Send reminder 1 hour before
    if ((horaCita - horaActual) == 1) {
        mensaje = "Recordatorio: Tienes una cita en 1 hora con " + cliente.Nombre;
        
        sendmail
        [
            from: zoho.adminuserid
            to: cliente.Email
            subject: "Recordatorio de Cita"
            message: mensaje
        ];
        
        sendSms
        [
            to: cliente.Telefono
            message: mensaje
            connection: "twilio_connection"
        ];
    }
}
```

### Example 4: Process CSV File

```deluge
// Get CSV file
archivoCSV = Archivos[ID == 12345].Archivo;

// Read content (assuming it is in text)
lineas = split(archivoCSV, "\n");

// Process each line
for each linea in lineas {
    if (linea != null && linea != "") {
        campos = split(linea, ",");
        
        if (campos.size() >= 3) {
            nombre = trim(campos.get(0));
            email = trim(campos.get(1));
            telefono = trim(campos.get(2));
            
            // Create record
            insert into Contactos
            [
                Nombre = nombre
                Email = email
                Telefono = telefono
                Fecha_Importacion = today()
            ];
        }
    }
}
```

### Example 5: External API Integration

```deluge
// Get data from external API
response = invokeUrl
[
    url: "https://api.weather.com/current"
    type: GET
    parameters: {
        "city": "Madrid",
        "api_key": "tu_api_key"
    }
];

if (response != null && response.containsKey("temperature")) {
    temperatura = response.get("temperature");
    ciudad = response.get("city");
    
    // Save to database
    insert into Clima
    [
        Ciudad = ciudad
        Temperatura = temperatura
        Fecha = today()
        Hora = now()
    ];
    
    // Send alert if it is very hot
    if (temperatura > 35) {
        sendmail
        [
            from: zoho.adminuserid
            to: "[email protected]"
            subject: "Alerta de Temperatura"
            message: "La temperatura en " + ciudad + " es de " + temperatura + "°C"
        ];
    }
}
```

---

## Best Practices

### 1. Naming

```deluge
// Use descriptive names
nombreCompleto = "Juan Pérez";  // ✅ Good
nc = "Juan Pérez";  // ❌ Bad

// Functions with verbs
CalcularTotal();  // ✅ Good
Total();  // ❌ Bad
```

### 2. Error Handling

```deluge
// Always use try-catch in critical operations
try {
    response = zoho.crm.createRecord("Contacts", datos);
} catch (e) {
    info "Error: " + e.toString();
    // Handle error appropriately
}
```

### 3. Data Validation

```deluge
// Validate before processing
if (email != null && email != "" && contains(email, "@")) {
    // Process email
} else {
    info "Email inválido";
}
```

### 4. Query Optimization

```deluge
// Avoid queries in loops
// ❌ Bad
for each cliente in Clientes {
    pedidos = cliente.Pedidos;  // Query on every iteration
}

// ✅ Good
pedidos = Pedidos;  // A single query
for each pedido in pedidos {
    cliente = pedido.Cliente;
    // Process
}
```

### 5. Comments

```deluge
// Comment complex code
// Calculate discount based on purchase volume
if (cantidad > 100) {
    descuento = 0.15;  // 15% discount
} else if (cantidad > 50) {
    descuento = 0.10;  // 10% discount
} else {
    descuento = 0.05;  // 5% discount
}
```

### 6. Code Reuse

```deluge
// Create reusable functions
string FormatearTelefono(string telefono) {
    // Remove non-numeric characters
    telefono = replace(telefono, "-", "");
    telefono = replace(telefono, " ", "");
    telefono = replace(telefono, "(", "");
    telefono = replace(telefono, ")", "");
    return telefono;
}

// Use in multiple places
telefono1 = thisapp.FormatearTelefono("+1 (234) 567-8900");
telefono2 = thisapp.FormatearTelefono("123-456-7890");
```

---

## Additional Resources

### Official Documentation

- **Zoho Deluge Help**: https://www.zoho.com/deluge/help/
- **Zoho Creator Scripting**: https://www.zoho.com/creator/help/script/
- **Zoho CRM Functions**: https://www.zoho.com/crm/developer/docs/functions/
- **Deluge Function Reference**: https://www.zoho.com/deluge/help/built-in-functions.html

### Learn Deluge

- **Learn Deluge**: https://deluge.zoho.com/learndeluge
- **Deluge Editor**: https://www.zoho.com/deluge/help/deluge-editor.html

### Community

- **Zoho Community**: https://help.zoho.com/portal/en/community
- **Zoho Creator Forum**: https://help.zoho.com/portal/en/community/categories/zoho-creator

---

## Final Notes

- Deluge is case-sensitive: `Nombre` is different from `nombre`
- Form and report names must match exactly
- Dates must be in `'YYYY-MM-DD'` or `'YYYY-MM-DD HH:MM:SS'` format
- Always validate data before processing it
- Use try-catch for operations that can fail
- Optimize queries to avoid performance problems
- Document complex functions with comments

---

*Documentation generated for use with language models (ML)*
*Last update: 2024*
