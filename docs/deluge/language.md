# Documentación Completa: Zoho Deluge

Esta documentación proporciona una guía exhaustiva sobre el lenguaje de scripting Zoho Deluge, incluyendo todas las estructuras de datos, funciones, operadores, y acciones disponibles para automatizar workflows y crear lógica personalizada en aplicaciones Zoho.

---

## Tabla de Contenidos

1. [Introducción a Deluge](#introducción-a-deluge)
2. [Tipos de Datos y Variables](#tipos-de-datos-y-variables)
3. [Estructuras de Datos](#estructuras-de-datos)
4. [Operadores](#operadores)
5. [Estructuras de Control](#estructuras-de-control)
6. [Funciones de String](#funciones-de-string)
7. [Funciones de Fecha y Hora](#funciones-de-fecha-y-hora)
8. [Funciones Matemáticas](#funciones-matemáticas)
9. [Funciones de Base de Datos](#funciones-de-base-de-datos)
10. [Funciones de Integración CRM](#funciones-de-integración-crm)
11. [Funciones de Integración Creator](#funciones-de-integración-creator)
12. [Funciones de Comunicación](#funciones-de-comunicación)
13. [Funciones Personalizadas](#funciones-personalizadas)
14. [Manejo de Errores](#manejo-de-errores)
15. [Integración con APIs Externas](#integración-con-apis-externas)
16. [Ejemplos Prácticos](#ejemplos-prácticos)

---

## Introducción a Deluge

**Deluge** (Data Enriched Language for the Universal Grid Environment) es el lenguaje de scripting propietario de Zoho diseñado para agregar lógica y automatizar workflows en aplicaciones Zoho.

### Características Principales

- **Sintaxis Amigable**: Fácil de aprender para desarrolladores y no desarrolladores
- **Lenguaje de Consulta Integrado**: Combina programación de alto nivel con manipulación de datos
- **Modelo de Datos Relacional**: Aplicaciones con modelos de datos completamente normalizados
- **Integración Nativa**: Conecta fácilmente con servicios Zoho y aplicaciones externas

### Dónde se Usa Deluge

- **Zoho CRM**: Workflows, funciones personalizadas, automatizaciones
- **Zoho Creator**: Scripts de formularios, workflows, integraciones
- **Zoho Books**: Automatizaciones contables
- **Zoho Projects**: Gestión de proyectos automatizada
- **Otros productos Zoho**: Cualquier aplicación que soporte scripting

---

## Tipos de Datos y Variables

### Tipos de Datos Básicos

#### 1. Text (Texto/String)

Secuencias de caracteres encerradas entre comillas dobles.

```deluge
nombre = "Juan Pérez";
email = "[email protected]";
mensaje = "Hola, bienvenido a Zoho";
```

#### 2. Number (Número Entero)

Valores enteros sin decimales.

```deluge
edad = 30;
cantidad = 100;
id = 12345;
```

#### 3. Decimal (Número Decimal)

Números con punto decimal para cálculos precisos.

```deluge
precio = 19.99;
descuento = 0.15;
total = 1234.56;
```

#### 4. Boolean (Booleano)

Valores lógicos: `true` o `false` (sin comillas).

```deluge
activo = true;
completado = false;
esValido = true;
```

#### 5. Date-Time (Fecha y Hora)

Valores de fecha y hora encerrados entre comillas simples.

```deluge
fechaCreacion = '2024-01-15';
fechaHora = '2024-01-15 14:30:00';
hoy = today();
ahora = now();
```

### Declaración de Variables

```deluge
// Variables simples
nombre = "Juan";
edad = 25;
activo = true;

// Variables con tipos explícitos (en funciones)
string nombreCompleto = "Juan Pérez";
int cantidad = 10;
boolean esValido = true;
date fechaInicio = '2024-01-01';
```

---

## Estructuras de Datos

### 1. List (Lista)

Colección ordenada de elementos accesibles por índice (comienza en 0).

#### Crear Lista

```deluge
// Lista de productos
productos = {"Creator", "CRM", "Projects", "Books"};

// Lista de números
numeros = {1, 2, 3, 4, 5};

// Lista mixta
mixta = {"Texto", 123, true, '2024-01-01'};
```

#### Operaciones con Listas

```deluge
productos = {"Creator", "CRM", "Projects"};

// Obtener elemento por índice
primerProducto = productos.get(0);  // "Creator"

// Agregar elemento
productos.add("Books");

// Eliminar elemento por índice
productos.remove(2);  // Elimina "Projects"

// Tamaño de la lista
tamaño = productos.size();

// Verificar si contiene elemento
contiene = productos.contains("CRM");  // true

// Obtener índice de elemento
indice = productos.indexOf("CRM");  // 1

// Convertir a texto
texto = productos.toString();  // "{Creator, CRM, Projects, Books}"

// Iterar sobre lista
for each producto in productos {
    info producto;
}
```

### 2. Map (Mapa)

Colección de pares clave-valor donde cada clave es única.

#### Crear Map

```deluge
// Mapa de detalles de producto
producto = Map();
producto.put("nombre", "Zoho CRM");
producto.put("precio", 29.99);
producto.put("activo", true);

// O crear directamente
producto = {
    "nombre": "Zoho CRM",
    "precio": 29.99,
    "activo": true
};
```

#### Operaciones con Maps

```deluge
producto = Map();
producto.put("nombre", "Zoho CRM");
producto.put("precio", 29.99);

// Obtener valor por clave
nombre = producto.get("nombre");  // "Zoho CRM"

// Verificar si contiene clave
tienePrecio = producto.containsKey("precio");  // true

// Obtener todas las claves
claves = producto.keys();  // Lista de claves

// Obtener todos los valores
valores = producto.values();  // Lista de valores

// Eliminar par clave-valor
producto.remove("precio");

// Tamaño del mapa
tamaño = producto.size();

// Convertir a texto
texto = producto.toString();
```

### 3. Collection (Colección)

Estructura flexible que puede funcionar como lista o mapa según su uso.

#### Crear Collection desde Texto

```deluge
// Desde texto de mapa
textoMapa = "{company:Zoho, product:Deluge}";
coleccion = textoMapa.toCollection();
empresa = coleccion.get("company");  // "Zoho"

// Desde texto de lista
textoLista = "{company, Zoho, product, Deluge}";
coleccionLista = textoLista.toCollection();
primerElemento = coleccionLista.get(0);  // "company"
```

#### Operaciones con Collections

```deluge
// Crear collection vacía
coleccion = Collection();

// Agregar elementos
coleccion.add("elemento1");
coleccion.add("elemento2");

// Obtener por índice o clave
valor = coleccion.get(0);
valor = coleccion.get("clave");

// Convertir a lista
lista = coleccion.toList();

// Convertir a mapa
mapa = coleccion.toMap();
```

---

## Operadores

### Operadores Aritméticos

```deluge
a = 10;
b = 3;

suma = a + b;        // 13
resta = a - b;       // 7
multiplicacion = a * b;  // 30
division = a / b;    // 3.333...
modulo = a % b;      // 1 (resto de la división)
potencia = pow(a, b); // 1000
```

### Operadores Relacionales

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

### Operadores Lógicos

```deluge
a = true;
b = false;

y = (a && b);      // false (AND)
o = (a || b);      // true (OR)
no = !a;           // false (NOT)
```

### Operadores de String

```deluge
nombre = "Juan";
apellido = "Pérez";

// Concatenación
nombreCompleto = nombre + " " + apellido;  // "Juan Pérez"

// Verificar igualdad
esIgual = (nombre == "Juan");  // true
```

---

## Estructuras de Control

### 1. If-Else

```deluge
// If simple
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
// Iterar sobre lista
productos = {"Creator", "CRM", "Projects"};
for each producto in productos {
    info producto;
}

// Iterar sobre mapa
detalles = Map();
detalles.put("nombre", "Juan");
detalles.put("edad", 30);

for each clave in detalles.keys() {
    valor = detalles.get(clave);
    info clave + ": " + valor;
}

// Iterar sobre registros
for each registro in Empleados[Estado == "Activo"] {
    info registro.Nombre;
    // Actualizar registro
    update registro
    [
        Ultima_Revision = today()
    ];
}
```

### 3. Emular While Loop

Deluge no tiene `while` nativo, pero se puede emular:

```deluge
// Función para crear lista de longitud específica
list getListOfLength(int length) {
    return if(length < 1, list(), "".leftpad(length - 1).toList(""));
}

// Usar para emular while
for each index in getListOfLength(10) {
    // Código que se ejecuta 10 veces
    info "Iteración: " + index;
}
```

### 4. Operador Ternario (If Condicional)

```deluge
// Sintaxis: if(condicion, valorSiVerdadero, valorSiFalso)
edad = 20;
mensaje = if(edad >= 18, "Mayor de edad", "Menor de edad");

// Equivale a:
if (edad >= 18) {
    mensaje = "Mayor de edad";
} else {
    mensaje = "Menor de edad";
}
```

---

## Funciones de String

### Funciones Básicas

```deluge
texto = "Hola Mundo";

// Longitud
longitud = length(texto);  // 10

// Subcadena
sub = substring(texto, 0, 4);  // "Hola"

// Reemplazar
nuevo = replace(texto, "Mundo", "Zoho");  // "Hola Zoho"

// Minúsculas
minusculas = toLowerCase(texto);  // "hola mundo"

// Mayúsculas
mayusculas = toUpperCase(texto);  // "HOLA MUNDO"

// Eliminar espacios
sinEspacios = trim("  Hola  ");  // "Hola"
```

### Funciones de Búsqueda

```deluge
texto = "Zoho Creator es genial";

// Contiene
contiene = contains(texto, "Creator");  // true

// Comienza con
comienza = startsWith(texto, "Zoho");  // true

// Termina con
termina = endsWith(texto, "genial");  // true

// Buscar posición
posicion = find(texto, "Creator");  // 5

// Izquierda
izquierda = left(texto, 4);  // "Zoho"

// Derecha
derecha = right(texto, 6);  // "genial"
```

### Funciones de División y Unión

```deluge
// Dividir string
texto = "Juan,Pérez,30";
partes = split(texto, ",");  // Lista: {"Juan", "Pérez", "30"}

// Unir lista en string
nombres = {"Juan", "María", "Pedro"};
unido = nombres.toString(", ");  // "Juan, María, Pedro"
```

### Conversiones

```deluge
// Número a texto
numero = 123;
texto = numero.toString();  // "123"

// Texto a número
texto = "456";
numero = texto.toLong();  // 456

// Texto a decimal
texto = "123.45";
decimal = texto.toDecimal();  // 123.45
```

---

## Funciones de Fecha y Hora

### Funciones de Fecha Actual

```deluge
// Fecha actual
hoy = today();  // '2024-01-15'

// Fecha y hora actual
ahora = now();  // '2024-01-15 14:30:00'
```

### Operaciones con Fechas

```deluge
fecha = '2024-01-15';

// Agregar días
futuro = addDays(fecha, 7);  // '2024-01-22'

// Agregar semanas
futuro = addWeeks(fecha, 2);  // '2024-01-29'

// Agregar meses
futuro = addMonths(fecha, 1);  // '2024-02-15'

// Agregar años
futuro = addYears(fecha, 1);  // '2025-01-15'

// Días entre fechas
fecha1 = '2024-01-01';
fecha2 = '2024-01-15';
dias = daysBetween(fecha1, fecha2);  // 14
```

### Extraer Componentes de Fecha

```deluge
fecha = '2024-01-15 14:30:00';

// Año
año = year(fecha);  // 2024

// Mes
mes = month(fecha);  // 1

// Día
dia = day(fecha);  // 15

// Hora
hora = hour(fecha);  // 14

// Minuto
minuto = minute(fecha);  // 30
```

### Formatear Fechas

```deluge
fecha = '2024-01-15';

// Formatear fecha
formateada = format(fecha, "dd-MMM-yyyy");  // "15-Jan-2024"
formateada = format(fecha, "dd/MM/yyyy");    // "15/01/2024"
formateada = format(fecha, "yyyy-MM-dd");   // "2024-01-15"

// Convertir a fecha
texto = "15-01-2024";
fecha = toDate(texto, "dd-MM-yyyy");

// Obtener fecha como texto
fechaTexto = getDate(fecha);
```

---

## Funciones Matemáticas

### Funciones Básicas

```deluge
numero = 25.7;

// Valor absoluto
absoluto = abs(-25.7);  // 25.7

// Redondear
redondeado = round(25.7);  // 26

// Techo (redondear hacia arriba)
techo = ceil(25.3);  // 26

// Piso (redondear hacia abajo)
piso = floor(25.7);  // 25

// Máximo
maximo = max(10, 20);  // 20

// Mínimo
minimo = min(10, 20);  // 10
```

### Funciones Avanzadas

```deluge
// Raíz cuadrada
raiz = sqrt(25);  // 5.0

// Potencia
potencia = pow(2, 3);  // 8

// Logaritmo natural
logNat = log(10);  // ~2.302

// Logaritmo base 10
log10 = log10(100);  // 2.0

// Exponencial
exp = exp(2);  // ~7.389

// Módulo (resto)
resto = mod(10, 3);  // 1
```

### Funciones Trigonométricas

```deluge
angulo = 1.5708;  // ~90 grados en radianes

// Seno
seno = sin(angulo);  // ~1.0

// Coseno
coseno = cos(angulo);  // ~0.0

// Tangente
tangente = tan(angulo);

// Arcoseno
arcseno = asin(0.5);

// Arcocoseno
arcocoseno = acos(0.5);

// Arcotangente
arcotangente = atan(1.0);

// Arcotangente de dos argumentos
arcotangente2 = atan2(1.0, 1.0);
```

### Números Aleatorios

```deluge
// Número aleatorio entre 0 y 1
aleatorio = random();  // 0.0 a 1.0

// Generar número aleatorio en rango
minimo = 1;
maximo = 100;
aleatorio = floor(random() * (maximo - minimo + 1)) + minimo;
```

---

## Funciones de Base de Datos

### Insertar Registro

```deluge
// Insertar un registro
response = insert into Productos
[
    Nombre = "Libro"
    Precio = 150.00
    Codigo = "PROD-001"
    En_Stock = 20
    Activo = true
];

// El response contiene el ID del registro creado
idRegistro = response.ID;
info "Registro creado con ID: " + idRegistro;
```

### Obtener Registros

```deluge
// Obtener un registro específico por ID
empleado = Empleados[ID == 12345];
info empleado.Nombre;

// Obtener registro con criterio
empleado = Empleados[Email == "[email protected]"];
info empleado.Nombre;

// Obtener múltiples registros con criterio
empleadosActivos = Empleados[Estado == "Activo"];
for each emp in empleadosActivos {
    info emp.Nombre;
}

// Obtener todos los registros
todosEmpleados = Empleados;
for each emp in todosEmpleados {
    info emp.Nombre;
}

// Múltiples criterios
empleados = Empleados[(Estado == "Activo") && (Edad > 25)];
```

### Actualizar Registros

```deluge
// Actualizar un registro específico
empleado = Empleados[ID == 12345];
update empleado
[
    Estado = "Inactivo"
    Fecha_Salida = today()
];

// Actualizar múltiples registros
for each emp in Empleados[Estado == "Activo"] {
    update emp
    [
        Ultima_Revision = today()
        Salario = emp.Salario * 1.1  // Aumento del 10%
    ];
}
```

### Eliminar Registros

```deluge
// Eliminar registros con criterio
delete from Empleados[Estado == "Resignado"];

// Eliminar un registro específico
empleado = Empleados[ID == 12345];
delete empleado;
```

### Operaciones con Relaciones

```deluge
// Obtener registros relacionados
cliente = Clientes[ID == 12345];
pedidos = cliente.Pedidos;  // Obtener pedidos relacionados

for each pedido in pedidos {
    info pedido.Numero_Pedido;
    info pedido.Total;
}

// Crear registro relacionado
cliente = Clientes[ID == 12345];
nuevoPedido = insert into Pedidos
[
    Cliente = cliente
    Fecha = today()
    Total = 500.00
];
```

---

## Funciones de Integración CRM

### Obtener Registros

```deluge
// Obtener registros de un módulo
response = zoho.crm.getRecords("Contacts");
registros = response.get("data");

for each registro in registros {
    info registro.get("First_Name");
}

// Obtener registro por ID
response = zoho.crm.getRecordById("Contacts", "66359000000015039");
registro = response.get("data").get(0);
info registro.get("First_Name");

// Buscar registros
response = zoho.crm.searchRecords("Contacts", "First_Name:Juan");
registros = response.get("data");
```

### Crear Registro

```deluge
// Crear un registro
datos = Map();
datos.put("First_Name", "Juan");
datos.put("Last_Name", "Pérez");
datos.put("Email", "[email protected]");

response = zoho.crm.createRecord("Contacts", datos);
idRegistro = response.get("data").get(0).get("id");
info "Registro creado: " + idRegistro;
```

### Actualizar Registro

```deluge
// Actualizar registro
datos = Map();
datos.put("Phone", "+1234567890");
datos.put("Mailing_City", "Madrid");

response = zoho.crm.updateRecord("Contacts", "66359000000015039", datos);
info "Registro actualizado";
```

### Eliminar Registro

```deluge
// Eliminar registro
response = zoho.crm.deleteRecord("Contacts", "66359000000015039");
info "Registro eliminado";
```

### Obtener Registros Relacionados

```deluge
// Obtener registros relacionados
response = zoho.crm.getRelatedRecords("Contacts", "66359000000015039", "Deals");
deals = response.get("data");

for each deal in deals {
    info deal.get("Deal_Name");
}
```

### Convertir Lead

```deluge
// Convertir lead a contacto, cuenta y oportunidad
response = zoho.crm.convertLead("66359000000015039");
info "Lead convertido";
```

### Operaciones en Lote

```deluge
// Crear múltiples registros
registros = list();
for each i in {1, 2, 3} {
    datos = Map();
    datos.put("First_Name", "Contacto " + i);
    datos.put("Email", "contacto" + i + "@example.com");
    registros.add(datos);
}

response = zoho.crm.bulkCreateRecords("Contacts", registros);
info "Registros creados en lote";

// Actualizar múltiples registros
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

### Upsert (Actualizar o Crear)

```deluge
// Upsert: actualiza si existe, crea si no existe
datos = Map();
datos.put("Email", "[email protected]");
datos.put("First_Name", "Juan");
datos.put("Last_Name", "Pérez");

response = zoho.crm.upsertRecord("Contacts", datos);
info "Registro upserted";
```

### Adjuntar Archivo

```deluge
// Adjuntar archivo a registro
response = zoho.crm.attachFile("Contacts", "66359000000015039", "archivo.pdf");
info "Archivo adjuntado";
```

---

## Funciones de Integración Creator

### Crear Registro

```deluge
// Crear un registro
response = zoho.creator.createRecord("Mi_Aplicacion", "Formulario_Contactos", {
    "Nombre": "Juan Pérez",
    "Email": "[email protected]",
    "Telefono": "+1234567890"
});

idRegistro = response.get("ID");
info "Registro creado: " + idRegistro;
```

### Crear Múltiples Registros

```deluge
// Crear múltiples registros
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

### Obtener Registro por ID

```deluge
// Obtener registro específico
response = zoho.creator.getRecordById("Mi_Aplicacion", "Reporte_Contactos", "66359000000015039");
registro = response.get("data");
info registro.get("Nombre");
```

### Obtener Registros

```deluge
// Obtener todos los registros
response = zoho.creator.getRecords("Mi_Aplicacion", "Reporte_Contactos");
registros = response.get("data");

for each registro in registros {
    info registro.get("Nombre");
}
```

### Actualizar Registro

```deluge
// Actualizar registro por ID
response = zoho.creator.updateRecordById("Mi_Aplicacion", "Reporte_Contactos", "66359000000015039", {
    "Telefono": "+9876543210",
    "Ciudad": "Barcelona"
});

info "Registro actualizado";
```

### Actualizar Múltiples Registros

```deluge
// Actualizar múltiples registros
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

## Funciones de Comunicación

### Enviar Email (sendmail)

```deluge
// Email simple
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Bienvenido a Zoho"
    message: "Gracias por unirte a nuestra plataforma."
];

// Email con CC y BCC
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    cc: "[email protected]"
    bcc: "[email protected]"
    subject: "Reporte Mensual"
    message: "Adjunto encontrarás el reporte del mes."
];

// Email HTML
mensajeHTML = "<h1>Bienvenido</h1><p>Gracias por registrarte.</p>";
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Bienvenido"
    message: mensajeHTML
    content type: "text/html"
];

// Email con adjuntos
sendmail
[
    from: zoho.adminuserid
    to: "[email protected]"
    subject: "Documento Adjunto"
    message: "Por favor revisa el documento adjunto."
    attachments: "documento.pdf"
];
```

### Enviar SMS (sendSms)

```deluge
// SMS con Twilio
sendSms
[
    to: "+1234567890"
    message: "Tu pedido ha sido enviado. Número de seguimiento: 12345"
    connection: "twilio_connection"
];

// SMS con Clickatell
sendSms
[
    to: "+1234567890"
    message: "Recordatorio: Tu cita es mañana a las 10:00 AM"
    connection: "clickatell_connection"
];
```

---

## Funciones Personalizadas

### Definir Función

```deluge
// Función que retorna un valor
int CalcularDias(date fechaInicio, date fechaFin) {
    dias = daysBetween(fechaInicio, fechaFin);
    return dias;
}

// Función sin retorno
void EnviarNotificacion(string email, string mensaje) {
    sendmail
    [
        from: zoho.adminuserid
        to: email
        subject: "Notificación"
        message: mensaje
    ];
}

// Función con múltiples parámetros
string FormatearNombre(string nombre, string apellido) {
    nombreCompleto = nombre + " " + apellido;
    return toUpperCase(nombreCompleto);
}
```

### Llamar Función

```deluge
// Desde la misma aplicación
dias = thisapp.CalcularDias('2024-01-01', '2024-01-15');
info "Días transcurridos: " + dias;

// Desde otra aplicación
dias = OtraAplicacion.CalcularDias('2024-01-01', '2024-01-15');

// Función sin retorno
thisapp.EnviarNotificacion("[email protected]", "Mensaje de prueba");
```

### Ejemplo Completo de Función

```deluge
// Función para calcular descuento
decimal CalcularDescuento(decimal precio, decimal porcentaje) {
    if (porcentaje > 100 || porcentaje < 0) {
        return 0;
    }
    descuento = precio * (porcentaje / 100);
    return descuento;
}

// Usar la función
precioOriginal = 100.00;
descuento = thisapp.CalcularDescuento(precioOriginal, 15);
precioFinal = precioOriginal - descuento;
info "Precio final: " + precioFinal;
```

---

## Manejo de Errores

### Try-Catch

```deluge
// Manejo básico de errores
try {
    // Código que puede fallar
    response = invokeUrl
    [
        url: "https://api.example.com/data"
        type: GET
    ];
    info "Éxito: " + response;
} catch (e) {
    // Manejar el error
    info "Error ocurrido: " + e.toString();
}

// Try-Catch anidado
try {
    // Operación principal
    registro = Clientes[ID == 12345];
    
    try {
        // Operación secundaria
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

### Validación Antes de Ejecutar

```deluge
// Validar antes de ejecutar
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

### Logging de Errores

```deluge
// Función para logging de errores
void LogError(string operacion, string error) {
    fechaHora = now();
    log = fechaHora + " - " + operacion + " - Error: " + error;
    
    // Guardar en registro de errores
    insert into Log_Errores
    [
        Fecha_Hora = fechaHora
        Operacion = operacion
        Error = error
    ];
}

// Usar logging
try {
    response = zoho.crm.getRecordById("Contacts", "12345");
} catch (e) {
    thisapp.LogError("Obtener Contacto", e.toString());
    info "Error registrado en log";
}
```

---

## Integración con APIs Externas

### invokeUrl - GET Request

```deluge
// GET simple
response = invokeUrl
[
    url: "https://api.example.com/data"
    type: GET
];

info response;

// GET con parámetros
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
// POST con datos JSON
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

// POST con autenticación
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
// PUT para actualizar
datos = Map();
datos.put("nombre", "Juan Actualizado");

response = invokeUrl
[
    url: "https://api.example.com/contactos/123"
    type: PUT
    body: datos.toString()
    headers: {"Content-Type": "application/json"}
];

// PATCH para actualización parcial
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

### invokeUrl con Conexiones

```deluge
// Usar conexión configurada (OAuth, etc.)
response = invokeUrl
[
    url: "https://api.dropbox.com/2/files/list_folder"
    type: POST
    body: datos.toString()
    connection: "dropbox_connection"
];

info response;
```

### Manejo de Respuestas JSON

```deluge
// Parsear respuesta JSON
response = invokeUrl
[
    url: "https://api.example.com/data"
    type: GET
];

// La respuesta puede ser un Map o List
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

## Ejemplos Prácticos

### Ejemplo 1: Sincronizar Contactos CRM a Creator

```deluge
// Obtener contactos de CRM
crmContacts = zoho.crm.getRecords("Contacts");
contactos = crmContacts.get("data");

// Crear en Creator
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

### Ejemplo 2: Calcular Comisiones

```deluge
// Función para calcular comisión
decimal CalcularComision(decimal venta, decimal porcentaje) {
    if (venta > 10000) {
        porcentaje = porcentaje * 1.1;  // Bonificación del 10%
    }
    comision = venta * (porcentaje / 100);
    return comision;
}

// Calcular comisiones de vendedores
vendedores = Empleados[Rol == "Vendedor"];
for each vendedor in vendedores {
    ventas = vendedor.Ventas;
    totalVentas = 0;
    
    for each venta in ventas {
        totalVentas = totalVentas + venta.Monto;
    }
    
    comision = thisapp.CalcularComision(totalVentas, 5.0);
    
    // Actualizar comisión
    update vendedor
    [
        Comision_Mes = comision
        Total_Ventas = totalVentas
    ];
}
```

### Ejemplo 3: Enviar Recordatorios Automáticos

```deluge
// Obtener citas de hoy
hoy = today();
citasHoy = Citas[Fecha == hoy];

for each cita in citasHoy {
    cliente = cita.Cliente;
    horaCita = hour(cita.Fecha_Hora);
    horaActual = hour(now());
    
    // Enviar recordatorio 1 hora antes
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

### Ejemplo 4: Procesar Archivo CSV

```deluge
// Obtener archivo CSV
archivoCSV = Archivos[ID == 12345].Archivo;

// Leer contenido (asumiendo que está en texto)
lineas = split(archivoCSV, "\n");

// Procesar cada línea
for each linea in lineas {
    if (linea != null && linea != "") {
        campos = split(linea, ",");
        
        if (campos.size() >= 3) {
            nombre = trim(campos.get(0));
            email = trim(campos.get(1));
            telefono = trim(campos.get(2));
            
            // Crear registro
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

### Ejemplo 5: Integración con API Externa

```deluge
// Obtener datos de API externa
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
    
    // Guardar en base de datos
    insert into Clima
    [
        Ciudad = ciudad
        Temperatura = temperatura
        Fecha = today()
        Hora = now()
    ];
    
    // Enviar alerta si hace mucho calor
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

## Mejores Prácticas

### 1. Nomenclatura

```deluge
// Usar nombres descriptivos
nombreCompleto = "Juan Pérez";  // ✅ Bueno
nc = "Juan Pérez";  // ❌ Malo

// Funciones con verbos
CalcularTotal();  // ✅ Bueno
Total();  // ❌ Malo
```

### 2. Manejo de Errores

```deluge
// Siempre usar try-catch en operaciones críticas
try {
    response = zoho.crm.createRecord("Contacts", datos);
} catch (e) {
    info "Error: " + e.toString();
    // Manejar error apropiadamente
}
```

### 3. Validación de Datos

```deluge
// Validar antes de procesar
if (email != null && email != "" && contains(email, "@")) {
    // Procesar email
} else {
    info "Email inválido";
}
```

### 4. Optimización de Consultas

```deluge
// Evitar consultas en loops
// ❌ Malo
for each cliente in Clientes {
    pedidos = cliente.Pedidos;  // Consulta en cada iteración
}

// ✅ Bueno
pedidos = Pedidos;  // Una sola consulta
for each pedido in pedidos {
    cliente = pedido.Cliente;
    // Procesar
}
```

### 5. Comentarios

```deluge
// Comentar código complejo
// Calcular descuento basado en volumen de compra
if (cantidad > 100) {
    descuento = 0.15;  // 15% de descuento
} else if (cantidad > 50) {
    descuento = 0.10;  // 10% de descuento
} else {
    descuento = 0.05;  // 5% de descuento
}
```

### 6. Reutilización de Código

```deluge
// Crear funciones reutilizables
string FormatearTelefono(string telefono) {
    // Eliminar caracteres no numéricos
    telefono = replace(telefono, "-", "");
    telefono = replace(telefono, " ", "");
    telefono = replace(telefono, "(", "");
    telefono = replace(telefono, ")", "");
    return telefono;
}

// Usar en múltiples lugares
telefono1 = thisapp.FormatearTelefono("+1 (234) 567-8900");
telefono2 = thisapp.FormatearTelefono("123-456-7890");
```

---

## Recursos Adicionales

### Documentación Oficial

- **Zoho Deluge Help**: https://www.zoho.com/deluge/help/
- **Zoho Creator Scripting**: https://www.zoho.com/creator/help/script/
- **Zoho CRM Functions**: https://www.zoho.com/crm/developer/docs/functions/
- **Deluge Function Reference**: https://www.zoho.com/deluge/help/built-in-functions.html

### Aprender Deluge

- **Learn Deluge**: https://deluge.zoho.com/learndeluge
- **Deluge Editor**: https://www.zoho.com/deluge/help/deluge-editor.html

### Comunidad

- **Zoho Community**: https://help.zoho.com/portal/en/community
- **Zoho Creator Forum**: https://help.zoho.com/portal/en/community/categories/zoho-creator

---

## Notas Finales

- Deluge es case-sensitive: `Nombre` es diferente de `nombre`
- Los nombres de formularios y reportes deben coincidir exactamente
- Las fechas deben estar en formato 'YYYY-MM-DD' o 'YYYY-MM-DD HH:MM:SS'
- Siempre validar datos antes de procesarlos
- Usar try-catch para operaciones que pueden fallar
- Optimizar consultas para evitar problemas de rendimiento
- Documentar funciones complejas con comentarios

---

*Documentación generada para uso con modelos de lenguaje (ML)*
*Última actualización: 2024*
