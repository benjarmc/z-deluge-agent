# Complete Documentation: Zoho Analytics SQL

> Language: English. Spanish: see the matching path under docs/es/.

This documentation provides a comprehensive guide to SQL queries in Zoho Analytics, including all available functions, syntax, limitations, and practical examples for effective data analysis.

---

## Table of Contents

1. [Introduction to Zoho Analytics SQL](#introduction-to-zoho-analytics-sql)
2. [Basic SQL Syntax](#basic-sql-syntax)
3. [Date and Time Functions](#date-and-time-functions)
4. [String Functions](#string-functions)
5. [Numeric Functions](#numeric-functions)
6. [Aggregate Functions](#aggregate-functions)
7. [Conditional Functions](#conditional-functions)
8. [Set Operations](#set-operations)
9. [JOINs](#joins)
10. [Window Functions](#window-functions)
11. [Query Tables](#query-tables)
12. [Limitations and Restrictions](#limitations-and-restrictions)
13. [API for Running Queries](#api-for-running-queries)
14. [Practical Examples](#practical-examples)
15. [Best Practices](#best-practices)

---

## Introduction to Zoho Analytics SQL

Zoho Analytics supports SQL queries written in multiple dialects, including:
- **ANSI SQL** (recommended)
- Oracle
- SQL Server
- IBM DB2
- MySQL
- Sybase
- Informix
- PostgreSQL

### Key Features

- **Query Tables**: Create tables based on SQL queries
- **Advanced Analysis**: Aggregate functions and window functions
- **Integration**: Connect to multiple data sources
- **API**: Run queries via REST API
- **JDBC/ODBC**: Standard connectivity for applications

### Using Query Tables

Query Tables let you create virtual tables based on SQL queries that update dynamically.

**Steps to create a Query Table:**
1. Go to your workspace in Zoho Analytics
2. Click **Create** > **Query Table**
3. Write the SQL query
4. Click **Execute Query** to preview
5. Save the Query Table

---

## Basic SQL Syntax

### SELECT Statement

```sql
-- Seleccionar todas las columnas
SELECT * FROM "Sales";

-- Seleccionar columnas específicas
SELECT "Customer Name", "Amount", "Date" 
FROM "Sales";

-- Seleccionar con alias
SELECT 
    "Customer Name" AS Cliente,
    "Amount" AS Monto,
    "Date" AS Fecha
FROM "Sales";

-- Seleccionar con expresiones
SELECT 
    "Product",
    "Quantity" * "Price" AS "Total"
FROM "Orders";
```

### WHERE Clause

```sql
-- Filtro simple
SELECT * FROM "Sales" 
WHERE "Amount" > 1000;

-- Múltiples condiciones con AND
SELECT * FROM "Sales" 
WHERE "Amount" > 1000 
  AND "Region" = 'East';

-- Múltiples condiciones con OR
SELECT * FROM "Sales" 
WHERE "Region" = 'East' 
   OR "Region" = 'West';

-- Usar IN en lugar de múltiples OR (mejor rendimiento)
SELECT * FROM "Sales" 
WHERE "Region" IN ('East', 'West', 'North');

-- Filtro con LIKE
SELECT * FROM "Customers" 
WHERE "Name" LIKE 'John%';

-- Filtro con BETWEEN
SELECT * FROM "Sales" 
WHERE "Amount" BETWEEN 1000 AND 5000;

-- Filtro con IS NULL / IS NOT NULL
SELECT * FROM "Customers" 
WHERE "Email" IS NOT NULL;
```

### ORDER BY

```sql
-- Ordenar ascendente
SELECT * FROM "Sales" 
ORDER BY "Amount" ASC;

-- Ordenar descendente
SELECT * FROM "Sales" 
ORDER BY "Amount" DESC;

-- Ordenar por múltiples columnas
SELECT * FROM "Sales" 
ORDER BY "Region" ASC, "Amount" DESC;

-- Ordenar por posición de columna
SELECT "Customer Name", "Amount" 
FROM "Sales" 
ORDER BY 2 DESC;  -- Ordena por "Amount"
```

### LIMIT

```sql
-- Limitar número de filas
SELECT * FROM "Sales" 
ORDER BY "Amount" DESC 
LIMIT 10;

-- Limitar con offset (paginación)
SELECT * FROM "Sales" 
ORDER BY "Date" DESC 
LIMIT 20 OFFSET 40;  -- Filas 41-60
```

### DISTINCT

```sql
-- Valores únicos
SELECT DISTINCT "Region" FROM "Sales";

-- Múltiples columnas únicas
SELECT DISTINCT "Region", "Product" FROM "Sales";

-- Nota: Usar con precaución en grandes datasets
```

---

## Date and Time Functions

### Basic Functions

```sql
-- Fecha actual
SELECT CURDATE();  -- '2024-01-15'

-- Hora actual
SELECT CURTIME();  -- '14:30:00'

-- Fecha y hora actual
SELECT NOW();  -- '2024-01-15 14:30:00'
```

### Extract Date Components

```sql
-- Año
SELECT YEAR("Date") AS Año FROM "Sales";
-- Ejemplo: YEAR('2024-01-15') = 2024

-- Mes
SELECT MONTH("Date") AS Mes FROM "Sales";
-- Ejemplo: MONTH('2024-01-15') = 1

-- Día
SELECT DAY("Date") AS Día FROM "Sales";
-- Ejemplo: DAY('2024-01-15') = 15

-- Día de la semana (1=Monday, 7=Sunday)
SELECT DAYOFWEEK("Date") AS DiaSemana FROM "Sales";

-- Día del año (1-366)
SELECT DAYOFYEAR("Date") AS DiaAño FROM "Sales";

-- Semana del año
SELECT WEEK("Date") AS Semana FROM "Sales";

-- Trimestre
SELECT QUARTER("Date") AS Trimestre FROM "Sales";
```

### Date Operations

```sql
-- Agregar días
SELECT ADDDATE("Date", 30) AS FechaFutura FROM "Sales";
-- Ejemplo: ADDDATE('2024-01-15', 30) = '2024-02-14'

-- Agregar meses
SELECT ADDMONTH("Date", 3) AS FechaFutura FROM "Sales";

-- Agregar años
SELECT ADDYEAR("Date", 1) AS FechaFutura FROM "Sales";

-- Diferencia en días
SELECT DATEDIFF('2024-01-20', "Date") AS DiasDiferencia FROM "Sales";
-- Ejemplo: DATEDIFF('2024-01-20', '2024-01-15') = 5

-- Diferencia en meses
SELECT MONTHS_BETWEEN('2024-06-15', "Date") AS MesesDiferencia FROM "Sales";

-- Extraer solo la fecha de un datetime
SELECT DATE("Created_Time") AS SoloFecha FROM "Sales";
-- Ejemplo: DATE('2024-01-15 14:30:00') = '2024-01-15'

-- Extraer solo la hora de un datetime
SELECT TIME("Created_Time") AS SoloHora FROM "Sales";
-- Ejemplo: TIME('2024-01-15 14:30:00') = '14:30:00'
```

### Format Dates

```sql
-- Formatear fecha
SELECT DATE_FORMAT("Date", '%Y-%m-%d') AS FechaFormateada FROM "Sales";
-- Ejemplo: DATE_FORMAT('2024-01-15', '%Y-%m-%d') = '2024-01-15'

-- Formatos comunes:
-- %Y: Año 4 dígitos (2024)
-- %y: Año 2 dígitos (24)
-- %m: Mes numérico (01-12)
-- %d: Día del mes (01-31)
-- %M: Nombre del mes (January)
-- %b: Abreviatura del mes (Jan)
-- %W: Nombre del día (Monday)
-- %w: Día de la semana (0-6)
-- %H: Hora 24h (00-23)
-- %h: Hora 12h (01-12)
-- %i: Minutos (00-59)
-- %s: Segundos (00-59)

-- Ejemplos de formato
SELECT DATE_FORMAT("Date", '%d/%m/%Y') AS FechaEspañol FROM "Sales";
-- Resultado: '15/01/2024'

SELECT DATE_FORMAT("Date", '%M %d, %Y') AS FechaTexto FROM "Sales";
-- Resultado: 'January 15, 2024'
```

### Advanced Date Functions

```sql
-- Agregar tiempo
SELECT ADDTIME("Time", '02:30:00') AS NuevaHora FROM "Sales";
-- Ejemplo: ADDTIME('12:00:00', '02:30:00') = '14:30:00'

-- Convertir zona horaria
SELECT CONVERT_TZ("Created_Time", '+00:00', '-05:00') AS HoraLocal FROM "Sales";
-- Convierte de UTC a EST

-- Timestamp diff (diferencia en unidades específicas)
SELECT TIMESTAMPDIFF(DAY, "Start_Date", "End_Date") AS Dias FROM "Projects";
-- Unidades: YEAR, MONTH, DAY, HOUR, MINUTE, SECOND

-- Timestamp add (agregar intervalo)
SELECT TIMESTAMPADD(DAY, 7, "Date") AS SemanaSiguiente FROM "Sales";
-- Unidades: YEAR, MONTH, DAY, HOUR, MINUTE, SECOND

-- Primer día del mes
SELECT DATE_SUB("Date", INTERVAL DAY("Date")-1 DAY) AS PrimerDiaMes FROM "Sales";

-- Último día del mes
SELECT LAST_DAY("Date") AS UltimoDiaMes FROM "Sales";
```

---

## String Functions

### Concatenation

```sql
-- Concatenar strings
SELECT CONCAT("First_Name", ' ', "Last_Name") AS NombreCompleto FROM "Contacts";
-- Ejemplo: CONCAT('Juan', ' ', 'Pérez') = 'Juan Pérez'

-- Concatenar con separador
SELECT CONCAT_WS('-', "Year", "Month", "Day") AS Fecha FROM "Sales";
-- Ejemplo: CONCAT_WS('-', '2024', '01', '15') = '2024-01-15'

-- Concatenar múltiples valores
SELECT CONCAT("Name", ' (', "Code", ')') AS NombreCodigo FROM "Products";
```

### Case Transformation

```sql
-- Convertir a minúsculas
SELECT LOWER("Name") AS NombreMinuscula FROM "Contacts";
-- Ejemplo: LOWER('JUAN') = 'juan'

-- Convertir a mayúsculas
SELECT UPPER("Name") AS NombreMayuscula FROM "Contacts";
-- Ejemplo: UPPER('juan') = 'JUAN'
```

### Substring Extraction

```sql
-- Substring desde posición
SELECT SUBSTRING("Email", 1, 5) AS Prefijo FROM "Contacts";
-- Ejemplo: SUBSTRING('[email protected]', 1, 5) = 'juan@'

-- Substring desde posición hasta el final
SELECT SUBSTRING("Email", 6) AS Sufijo FROM "Contacts";
-- Ejemplo: SUBSTRING('[email protected]', 6) = 'example.com'

-- Left (primeros N caracteres)
SELECT LEFT("Name", 3) AS Primeros3 FROM "Contacts";
-- Ejemplo: LEFT('Juan', 3) = 'Jua'

-- Right (últimos N caracteres)
SELECT RIGHT("Email", 4) AS Extension FROM "Contacts";
-- Ejemplo: RIGHT('[email protected]', 4) = '.com'
```

### Search and Replace

```sql
-- Buscar posición de substring
SELECT LOCATE('@', "Email") AS PosicionArroba FROM "Contacts";
-- Ejemplo: LOCATE('@', '[email protected]') = 5

-- Reemplazar texto
SELECT REPLACE("Description", 'old', 'new') AS DescripcionNueva FROM "Products";
-- Ejemplo: REPLACE('Hello old world', 'old', 'new') = 'Hello new world'

-- Insertar texto en posición
SELECT INSERT("Name", 1, 0, 'Mr. ') AS NombreConTitulo FROM "Contacts";
-- Ejemplo: INSERT('Juan', 1, 0, 'Mr. ') = 'Mr. Juan'
```

### Length and Trimming

```sql
-- Longitud de string
SELECT LENGTH("Name") AS LongitudNombre FROM "Contacts";
-- Ejemplo: LENGTH('Juan') = 4

-- Eliminar espacios al inicio y final
SELECT TRIM("Name") AS NombreSinEspacios FROM "Contacts";
-- Ejemplo: TRIM('  Juan  ') = 'Juan'

-- Eliminar espacios al inicio
SELECT LTRIM("Name") AS NombreSinEspaciosInicio FROM "Contacts";

-- Eliminar espacios al final
SELECT RTRIM("Name") AS NombreSinEspaciosFinal FROM "Contacts";
```

### Advanced Functions

```sql
-- Repetir string
SELECT REPEAT('*', 5) AS Asteriscos;  -- '*****'

-- Invertir string
SELECT REVERSE("Name") AS NombreInvertido FROM "Contacts";
-- Ejemplo: REVERSE('Juan') = 'nauJ'

-- Comparar strings (case-insensitive)
SELECT STRCMP("Name1", "Name2") AS Comparacion FROM "Table";
-- Retorna: -1 (menor), 0 (igual), 1 (mayor)

-- Extraer dominio de email
SELECT SUBSTRING("Email", LOCATE('@', "Email") + 1) AS Dominio FROM "Contacts";
```

---

## Numeric Functions

### Basic Functions

```sql
-- Valor absoluto
SELECT ABS(-10) AS Absoluto;  -- 10

-- Redondear
SELECT ROUND(10.567, 2) AS Redondeado;  -- 10.57

-- Techo (redondear hacia arriba)
SELECT CEIL(10.2) AS Techo;  -- 11

-- Piso (redondear hacia abajo)
SELECT FLOOR(10.8) AS Piso;  -- 10

-- Truncar
SELECT TRUNCATE(10.567, 2) AS Truncado;  -- 10.56
```

### Mathematical Functions

```sql
-- Potencia
SELECT POWER(2, 3) AS Potencia;  -- 8

-- Raíz cuadrada
SELECT SQRT(16) AS RaizCuadrada;  -- 4

-- Módulo (resto de división)
SELECT MOD(10, 3) AS Resto;  -- 1

-- Logaritmo natural
SELECT LOG(10) AS Logaritmo;  -- ~2.302585

-- Logaritmo base 10
SELECT LOG10(100) AS Log10;  -- 2

-- Exponencial (e^x)
SELECT EXP(2) AS Exponencial;  -- ~7.389056

-- Número aleatorio (0-1)
SELECT RAND() AS Aleatorio;
```

### Comparison Functions

```sql
-- Máximo
SELECT MAX("Amount") AS MontoMaximo FROM "Sales";

-- Mínimo
SELECT MIN("Amount") AS MontoMinimo FROM "Sales";

-- Signo del número
SELECT SIGN(-10) AS Signo;  -- -1
SELECT SIGN(10) AS Signo;   -- 1
SELECT SIGN(0) AS Signo;    -- 0
```

---

## Aggregate Functions

### Basic Functions

```sql
-- Suma
SELECT SUM("Amount") AS TotalVentas FROM "Sales";

-- Contar filas
SELECT COUNT(*) AS TotalRegistros FROM "Sales";

-- Contar valores no nulos
SELECT COUNT("Email") AS ContactosConEmail FROM "Contacts";

-- Promedio
SELECT AVG("Amount") AS PromedioVentas FROM "Sales";

-- Máximo
SELECT MAX("Amount") AS VentaMaxima FROM "Sales";

-- Mínimo
SELECT MIN("Amount") AS VentaMinima FROM "Sales";
```

### GROUP BY

```sql
-- Agrupar por una columna
SELECT "Region", SUM("Amount") AS TotalVentas
FROM "Sales"
GROUP BY "Region";

-- Agrupar por múltiples columnas
SELECT "Region", "Product", SUM("Amount") AS TotalVentas
FROM "Sales"
GROUP BY "Region", "Product";

-- Agrupar con múltiples agregados
SELECT 
    "Region",
    COUNT(*) AS NumeroVentas,
    SUM("Amount") AS TotalVentas,
    AVG("Amount") AS PromedioVentas,
    MAX("Amount") AS VentaMaxima,
    MIN("Amount") AS VentaMinima
FROM "Sales"
GROUP BY "Region";
```

### HAVING

```sql
-- Filtrar grupos (después de GROUP BY)
SELECT "Region", SUM("Amount") AS TotalVentas
FROM "Sales"
GROUP BY "Region"
HAVING SUM("Amount") > 10000;

-- Nota: No se pueden usar alias en HAVING en Zoho Analytics
-- ❌ HAVING TotalVentas > 10000  (incorrecto)
-- ✅ HAVING SUM("Amount") > 10000  (correcto)
```

### Advanced Examples

```sql
-- Ventas por mes
SELECT 
    YEAR("Date") AS Año,
    MONTH("Date") AS Mes,
    SUM("Amount") AS TotalVentas,
    COUNT(*) AS NumeroVentas
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date")
ORDER BY Año, Mes;

-- Top 5 productos por ventas
SELECT 
    "Product",
    SUM("Amount") AS TotalVentas
FROM "Sales"
GROUP BY "Product"
ORDER BY TotalVentas DESC
LIMIT 5;

-- Ventas por región y categoría
SELECT 
    "Region",
    "Category",
    SUM("Amount") AS Total,
    AVG("Amount") AS Promedio
FROM "Sales"
GROUP BY "Region", "Category"
ORDER BY "Region", Total DESC;
```

---

## Conditional Functions

### CASE WHEN

```sql
-- CASE simple
SELECT 
    "Amount",
    CASE 
        WHEN "Amount" > 10000 THEN 'Alto'
        WHEN "Amount" > 5000 THEN 'Medio'
        ELSE 'Bajo'
    END AS Categoria
FROM "Sales";

-- CASE con múltiples condiciones
SELECT 
    "Customer Type",
    "Amount",
    CASE 
        WHEN "Customer Type" = 'Enterprise' AND "Amount" > 50000 THEN 'VIP Enterprise'
        WHEN "Customer Type" = 'Enterprise' THEN 'Enterprise'
        WHEN "Customer Type" = 'SMB' AND "Amount" > 10000 THEN 'SMB Premium'
        WHEN "Customer Type" = 'SMB' THEN 'SMB'
        ELSE 'Otro'
    END AS Segmento
FROM "Sales";

-- CASE en SELECT con agregados
SELECT 
    "Region",
    SUM(CASE WHEN "Amount" > 10000 THEN "Amount" ELSE 0 END) AS VentasAltas,
    SUM(CASE WHEN "Amount" <= 10000 THEN "Amount" ELSE 0 END) AS VentasBajas
FROM "Sales"
GROUP BY "Region";
```

### IF (Conditional Function)

```sql
-- IF simple (no estándar SQL, verificar disponibilidad)
-- En Zoho Analytics, usar CASE WHEN en su lugar
```

### COALESCE and NULLIF

```sql
-- COALESCE: Retorna el primer valor no nulo
SELECT COALESCE("Phone", "Mobile", "N/A") AS Telefono FROM "Contacts";
-- Si Phone es NULL, usa Mobile; si ambos son NULL, usa 'N/A'

-- NULLIF: Retorna NULL si dos valores son iguales
SELECT NULLIF("Amount", 0) AS MontoNoCero FROM "Sales";
-- Si Amount = 0, retorna NULL; si no, retorna Amount
```

---

## Set Operations

### UNION

```sql
-- Combinar resultados (elimina duplicados)
SELECT "Name", "Email" FROM "Contacts"
UNION
SELECT "Name", "Email" FROM "Leads";

-- UNION ALL (incluye duplicados - mejor rendimiento)
SELECT "Name", "Email" FROM "Contacts"
UNION ALL
SELECT "Name", "Email" FROM "Leads";

-- Nota: Preferir UNION ALL sobre UNION cuando sea posible
```

### INTERSECT

```sql
-- Filas comunes en ambos SELECT
SELECT "Email" FROM "Contacts"
INTERSECT
SELECT "Email" FROM "Leads";
-- Retorna emails que están en ambas tablas

-- INTERSECT ALL (incluye duplicados)
SELECT "Email" FROM "Contacts"
INTERSECT ALL
SELECT "Email" FROM "Leads";
```

### EXCEPT

```sql
-- Filas del primer SELECT que no están en el segundo
SELECT "Email" FROM "Contacts"
EXCEPT
SELECT "Email" FROM "Leads";
-- Retorna emails de Contacts que NO están en Leads

-- EXCEPT ALL (incluye duplicados)
SELECT "Email" FROM "Contacts"
EXCEPT ALL
SELECT "Email" FROM "Leads";
```

### Requirements for Set Operations

- Same number of columns in both SELECT statements
- Compatible data types
- Same column order

```sql
-- ✅ Correcto
SELECT "Name", "Email" FROM "Table1"
UNION
SELECT "Name", "Email" FROM "Table2";

-- ❌ Incorrecto (diferente número de columnas)
SELECT "Name", "Email", "Phone" FROM "Table1"
UNION
SELECT "Name", "Email" FROM "Table2";
```

---

## JOINs

### INNER JOIN

```sql
-- Join básico
SELECT 
    e."Name",
    d."DepartmentName"
FROM "Employees" e
INNER JOIN "Departments" d ON e."DepartmentID" = d."DepartmentID";

-- Múltiples joins
SELECT 
    o."OrderID",
    c."CustomerName",
    p."ProductName",
    oi."Quantity",
    oi."Price"
FROM "Orders" o
INNER JOIN "Customers" c ON o."CustomerID" = c."CustomerID"
INNER JOIN "OrderItems" oi ON o."OrderID" = oi."OrderID"
INNER JOIN "Products" p ON oi."ProductID" = p."ProductID";
```

### LEFT JOIN

```sql
-- Incluir todas las filas de la tabla izquierda
SELECT 
    c."CustomerName",
    o."OrderID",
    o."OrderDate"
FROM "Customers" c
LEFT JOIN "Orders" o ON c."CustomerID" = o."CustomerID";
-- Incluye clientes sin órdenes (OrderID será NULL)
```

### RIGHT JOIN

```sql
-- Incluir todas las filas de la tabla derecha
SELECT 
    o."OrderID",
    p."ProductName"
FROM "Orders" o
RIGHT JOIN "Products" p ON o."ProductID" = p."ProductID";
-- Incluye productos sin órdenes
```

### FULL OUTER JOIN

```sql
-- Incluir todas las filas de ambas tablas
-- Nota: Verificar soporte en Zoho Analytics
SELECT 
    c."CustomerName",
    o."OrderID"
FROM "Customers" c
FULL OUTER JOIN "Orders" o ON c."CustomerID" = o."CustomerID";
```

### Self JOIN

```sql
-- Join de una tabla consigo misma
SELECT 
    e1."Name" AS Empleado,
    e2."Name" AS Supervisor
FROM "Employees" e1
LEFT JOIN "Employees" e2 ON e1."SupervisorID" = e2."EmployeeID";
```

### JOIN Best Practices

```sql
-- ✅ Usar condiciones de join apropiadas (1:1, 1:N, N:1)
-- ❌ Evitar Cartesian joins (N:N)

-- ✅ Especificar alias de tabla
SELECT e."Name" FROM "Employees" e;

-- ✅ Usar índices en columnas de join para mejor rendimiento
```

---

## Window Functions

Zoho Analytics provides custom window functions:

### Window_Sum

```sql
-- Suma acumulada
SELECT 
    "Date",
    "Amount",
    window_sum(sum("Amount"), 0, 0) AS SumaAcumulada
FROM "Sales"
GROUP BY "Date", "Amount"
ORDER BY "Date";

-- Suma móvil (3 días)
SELECT 
    "Date",
    "Amount",
    window_sum(sum("Amount"), 1, 1) AS SumaMovil3Dias
FROM "Sales"
GROUP BY "Date", "Amount"
ORDER BY "Date";
```

**Parameters:**
- `AggExpr`: Aggregate expression (SUM, AVG, COUNT, etc.)
- `Start`: Number of rows above the current row
- `End`: Number of rows below the current row

### Window_Avg

```sql
-- Promedio móvil
SELECT 
    "Date",
    "Amount",
    window_avg(sum("Amount"), 2, 2) AS PromedioMovil
FROM "Sales"
GROUP BY "Date", "Amount"
ORDER BY "Date";
```

### Window_Count

```sql
-- Conteo en ventana
SELECT 
    "Date",
    "Amount",
    window_count(count("Amount"), 1, 1) AS ConteoVentana
FROM "Sales"
GROUP BY "Date", "Amount"
ORDER BY "Date";
```

---

## Query Tables

### Create a Query Table

1. **From the UI:**
   - Go to your workspace
   - Click **Create** > **Query Table**
   - Write the SQL query
   - Execute and save

2. **Query Table example:**

```sql
-- Query Table: Ventas por Región y Producto
SELECT 
    "Region",
    "Product",
    SUM("Amount") AS TotalVentas,
    COUNT(*) AS NumeroVentas,
    AVG("Amount") AS PromedioVentas
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY "Region", "Product"
ORDER BY TotalVentas DESC;
```

### Query Table Advantages

- **Automatic Refresh**: They update when source data changes
- **Reuse**: Can be used as a source for reports
- **Optimization**: Zoho Analytics optimizes the queries
- **Integration**: They integrate with dashboards and reports

---

## Limitations and Restrictions

### Common Table Expressions (CTEs)

```sql
-- ✅ CTEs no recursivos soportados
WITH VentasPorRegion AS (
    SELECT "Region", SUM("Amount") AS Total
    FROM "Sales"
    GROUP BY "Region"
)
SELECT * FROM VentasPorRegion;

-- ❌ CTEs recursivos NO soportados
-- ❌ Subqueries dentro de CTEs NO soportados
-- ❌ CTEs dentro de subqueries NO soportados
-- ❌ PIVOT/UNPIVOT con CTEs NO permitido
-- ✅ Máximo 3 CTEs por consulta
```

### Subqueries

```sql
-- ✅ Subqueries simples soportados
SELECT * FROM "Sales"
WHERE "Amount" > (SELECT AVG("Amount") FROM "Sales");

-- ❌ Subqueries correlacionados NO soportados
-- Ejemplo NO soportado:
-- SELECT * FROM "Sales" s1
-- WHERE "Amount" > (SELECT AVG("Amount") FROM "Sales" s2 
--                   WHERE s2."Region" = s1."Region");
```

### Query Tables — Limitations

```sql
-- ❌ Evitar SELECT * en tablas grandes
-- ✅ Seleccionar solo columnas necesarias
SELECT "Name", "Amount" FROM "Sales";  -- ✅ Mejor
SELECT * FROM "Sales";  -- ❌ Evitar

-- ❌ Evitar Cartesian joins (N*N)
-- ✅ Usar joins apropiados (1:1, 1:N, N:1)

-- ❌ No usar alias en HAVING
-- ✅ Usar expresión completa en HAVING
SELECT "Region", SUM("Amount") AS Total
FROM "Sales"
GROUP BY "Region"
HAVING SUM("Amount") > 10000;  -- ✅ Correcto
-- HAVING Total > 10000;  -- ❌ Incorrecto

-- ⚠️ Limitar uso de DISTINCT en grandes datasets
-- ✅ Preferir UNION ALL sobre UNION

-- ✅ Usar IN en lugar de múltiples OR
WHERE "Region" IN ('East', 'West', 'North');  -- ✅ Mejor
WHERE "Region" = 'East' OR "Region" = 'West' OR "Region" = 'North';  -- ❌ Evitar
```

### Data Limits

- **Free/Trial version**: Maximum 1,000,000 rows per table
- **Individual tables**: Recommended to keep under 200 million rows
- **Workspaces**: Recommended to keep under 500 million rows
- **Performance**: For large datasets, use Zoho Databridge

### GROUP BY — Limitations

```sql
-- ✅ GROUP BY solo con columnas no agregadas
SELECT "Region", SUM("Amount")
FROM "Sales"
GROUP BY "Region";  -- ✅ Correcto

-- ❌ No usar funciones agregadas en GROUP BY
-- ❌ No usar demasiadas columnas en GROUP BY
```

---

## API for Running Queries

### CloudSQL API

#### Endpoint

```
https://<ZohoAnalytics_Server_URI>/api/<OwnerEmail>/<WorkspaceName>
```

#### Parameters

- `ZOHO_ACTION`: `EXPORT`
- `ZOHO_SQLQUERY`: SQL query (URL-encoded)

#### Example with cURL

```bash
curl -X POST "https://analytics.zoho.com/api/[email protected]/MiWorkspace" \
  -d "ZOHO_ACTION=EXPORT" \
  -d "ZOHO_SQLQUERY=SELECT * FROM Sales WHERE Region='East'" \
  -H "Authorization: Zoho-oauthtoken YOUR_TOKEN"
```

#### Example with JavaScript

```javascript
// Ejecutar consulta SQL
const query = "SELECT * FROM Sales WHERE Region='East'";
const encodedQuery = encodeURIComponent(query);

fetch(`https://analytics.zoho.com/api/[email protected]/MiWorkspace?ZOHO_ACTION=EXPORT&ZOHO_SQLQUERY=${encodedQuery}`, {
    method: 'POST',
    headers: {
        'Authorization': 'Zoho-oauthtoken YOUR_TOKEN'
    }
})
.then(response => response.json())
.then(data => {
    console.log(data);
})
.catch(error => {
    console.error('Error:', error);
});
```

#### Response Formats

- **CSV**: `ZOHO_OUTPUT_FORMAT=CSV`
- **JSON**: `ZOHO_OUTPUT_FORMAT=JSON`
- **XML**: `ZOHO_OUTPUT_FORMAT=XML`

### JDBC Driver

```java
// Ejemplo Java con JDBC
import java.sql.*;

public class ZohoAnalyticsQuery {
    public static void main(String[] args) {
        String url = "jdbc:zohoanalytics://analytics.zoho.com:443/[email protected]/MiWorkspace";
        String user = "YOUR_EMAIL";
        String password = "YOUR_PASSWORD";
        
        try (Connection conn = DriverManager.getConnection(url, user, password);
             Statement stmt = conn.createStatement()) {
            
            String sql = "SELECT Region, SUM(Amount) AS Total FROM Sales GROUP BY Region";
            ResultSet rs = stmt.executeQuery(sql);
            
            while (rs.next()) {
                System.out.println(rs.getString("Region") + ": " + rs.getDouble("Total"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

## Practical Examples

### Example 1: Sales Analysis by Period

```sql
-- Ventas mensuales con comparación año anterior
SELECT 
    YEAR("Date") AS Año,
    MONTH("Date") AS Mes,
    SUM("Amount") AS VentasActuales,
    LAG(SUM("Amount"), 12) OVER (ORDER BY YEAR("Date"), MONTH("Date")) AS VentasAnoAnterior,
    SUM("Amount") - LAG(SUM("Amount"), 12) OVER (ORDER BY YEAR("Date"), MONTH("Date")) AS Diferencia
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date")
ORDER BY Año, Mes;
```

### Example 2: Top N by Category

```sql
-- Top 3 productos por región
SELECT 
    "Region",
    "Product",
    "Amount",
    ROW_NUMBER() OVER (PARTITION BY "Region" ORDER BY "Amount" DESC) AS Ranking
FROM "Sales"
WHERE ROW_NUMBER() OVER (PARTITION BY "Region" ORDER BY "Amount" DESC) <= 3;
```

### Example 3: Cohort Analysis

```sql
-- Clientes por mes de primera compra
SELECT 
    DATE_FORMAT(MIN("Date"), '%Y-%m') AS MesCohorte,
    COUNT(DISTINCT "CustomerID") AS NuevosClientes
FROM "Sales"
GROUP BY "CustomerID"
HAVING MIN("Date") >= DATE_SUB(CURDATE(), INTERVAL 12 MONTH);
```

### Example 4: Business Metric Calculation

```sql
-- Métricas de ventas: Total, Promedio, Mediana, Desviación
SELECT 
    "Region",
    COUNT(*) AS NumeroVentas,
    SUM("Amount") AS TotalVentas,
    AVG("Amount") AS PromedioVentas,
    MAX("Amount") AS VentaMaxima,
    MIN("Amount") AS VentaMinima,
    STDDEV("Amount") AS DesviacionEstandar
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)
GROUP BY "Region"
ORDER BY TotalVentas DESC;
```

### Example 5: Trend Analysis

```sql
-- Tendencias de ventas con promedio móvil
SELECT 
    "Date",
    SUM("Amount") AS VentasDiarias,
    window_avg(SUM("Amount"), 6, 0) AS PromedioMovil7Dias,
    window_avg(SUM("Amount"), 13, 0) AS PromedioMovil14Dias
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 3 MONTH)
GROUP BY "Date"
ORDER BY "Date";
```

### Example 6: Customer Segmentation

```sql
-- Segmentar clientes por valor de compra
SELECT 
    "CustomerID",
    "CustomerName",
    SUM("Amount") AS ValorTotal,
    COUNT(*) AS NumeroCompras,
    CASE 
        WHEN SUM("Amount") > 50000 THEN 'VIP'
        WHEN SUM("Amount") > 20000 THEN 'Premium'
        WHEN SUM("Amount") > 5000 THEN 'Regular'
        ELSE 'Básico'
    END AS Segmento
FROM "Sales"
GROUP BY "CustomerID", "CustomerName"
ORDER BY ValorTotal DESC;
```

---

## Best Practices

### 1. Query Optimization

```sql
-- ✅ Seleccionar solo columnas necesarias
SELECT "Name", "Amount" FROM "Sales";  -- ✅ Mejor

-- ❌ Evitar SELECT *
SELECT * FROM "Sales";  -- ❌ Menos eficiente

-- ✅ Usar WHERE para filtrar temprano
SELECT * FROM "Sales" WHERE "Date" >= '2024-01-01';  -- ✅ Filtra antes de procesar

-- ✅ Usar índices en columnas de WHERE y JOIN
```

### 2. Using Aggregates

```sql
-- ✅ Incluir todas las columnas no agregadas en GROUP BY
SELECT "Region", SUM("Amount") FROM "Sales" GROUP BY "Region";  -- ✅ Correcto

-- ❌ No usar funciones agregadas en GROUP BY
-- ❌ No olvidar columnas no agregadas en GROUP BY
```

### 3. Efficient JOINs

```sql
-- ✅ Usar condiciones de join apropiadas
SELECT * FROM "Orders" o
INNER JOIN "Customers" c ON o."CustomerID" = c."CustomerID";  -- ✅ 1:N

-- ❌ Evitar Cartesian joins
-- SELECT * FROM "Table1", "Table2";  -- ❌ Evitar
```

### 4. NULL Handling

```sql
-- ✅ Usar COALESCE para valores por defecto
SELECT COALESCE("Phone", "Mobile", "N/A") AS Telefono FROM "Contacts";

-- ✅ Usar IS NULL / IS NOT NULL explícitamente
SELECT * FROM "Contacts" WHERE "Email" IS NOT NULL;
```

### 5. Format and Readability

```sql
-- ✅ Usar alias descriptivos
SELECT 
    "Customer Name" AS Cliente,
    SUM("Amount") AS TotalVentas
FROM "Sales"
GROUP BY Cliente;

-- ✅ Formatear consultas complejas
SELECT 
    "Region",
    "Product",
    SUM("Amount") AS Total
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY "Region", "Product"
HAVING SUM("Amount") > 10000
ORDER BY Total DESC
LIMIT 10;
```

### 6. Comments in Queries

```sql
-- Query para análisis de ventas mensuales
-- Filtra últimos 12 meses
-- Agrupa por región y producto
SELECT 
    "Region",
    "Product",
    SUM("Amount") AS Total
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 12 MONTH)
GROUP BY "Region", "Product";
```

---

## Additional Resources

### Official Documentation

- **Supported SQL**: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/supported-sql.html
- **Query Tables**: https://www.zoho.com/analytics/help/query-tables.html
- **CloudSQL API**: https://www.zoho.com/analytics/api/v1/zoho-cloudsql/cloudsql-api-specification.html
- **JDBC Driver**: https://www.zoho.com/analytics/api/v2/zoho-analytics-cloudsql/sample-codes.html

### Supported SQL Dialects

- ANSI SQL (recommended)
- Oracle
- SQL Server
- MySQL
- PostgreSQL
- IBM DB2
- Sybase
- Informix

---

## Final Notes

- **Use ANSI SQL** whenever possible for maximum compatibility
- **Table and column names** must be in double quotes if they contain spaces or special characters
- **Case-sensitive**: Table and column names are case-sensitive
- **API Credits**: Queries via API consume credits
- **Performance**: Optimize queries for large data volumes
- **Testing**: Test queries in Query Tables before using them in production

---

*Documentation generated for use with language models (ML)*
*Last updated: 2024*
