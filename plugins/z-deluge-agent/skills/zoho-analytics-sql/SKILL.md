---
name: zoho-analytics-sql
description: Escribe, revisa y depura consultas SQL en Zoho Analytics: funciones fecha/string/numéricas, JOINs, window functions, Query Tables, limitaciones CTE/subqueries, CloudSQL API REST, JDBC. Usar cuando el usuario menciona Zoho Analytics SQL, Query Table, window_sum, CloudSQL API, ZOHO_SQLQUERY o análisis BI en Analytics.
---

# Zoho Analytics SQL

Consultas SQL en Zoho Analytics: Query Tables, reportes y CloudSQL API.

**Referencia completa:** `docs/analytics/sql.md`  
**Integration tasks Deluge:** `zoho.analytics.*` en skill `zoho-integration-tasks`  
**REST Analytics v2:** skill `zoho-public-apis`

---

## Dialectos soportados

ANSI SQL (recomendado), MySQL, Oracle, SQL Server, PostgreSQL, DB2, Sybase, Informix.

Usar **ANSI SQL** para máxima compatibilidad.

---

## Query Tables

1. Workspace → Create → Query Table
2. Escribir SQL → Execute Query → Save
3. Se actualizan dinámicamente con datos fuente

```sql
SELECT "Region", "Product", SUM("Amount") AS TotalVentas
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY "Region", "Product"
ORDER BY TotalVentas DESC;
```

**Reglas:** nombres con espacios entre comillas dobles `"Column Name"`.

---

## SELECT / WHERE / ORDER / LIMIT

```sql
SELECT "Customer Name", "Amount" FROM "Sales" WHERE "Amount" > 1000;

SELECT * FROM "Sales"
WHERE "Region" IN ('East', 'West')
ORDER BY "Amount" DESC
LIMIT 10 OFFSET 0;

SELECT DISTINCT "Region" FROM "Sales";
```

Preferir `IN` sobre múltiples `OR`. Evitar `SELECT *` en tablas grandes.

---

## Funciones de fecha

| Función | Uso |
|---------|-----|
| `CURDATE()` | Fecha actual |
| `NOW()` | DateTime actual |
| `YEAR(d)` / `MONTH(d)` / `DAY(d)` | Componentes |
| `ADDDATE(d, n)` / `ADDMONTH(d, n)` | Sumar |
| `DATEDIFF(d1, d2)` | Diferencia días |
| `DATE_FORMAT(d, '%Y-%m-%d')` | Formato |
| `DATE_SUB(CURDATE(), INTERVAL 12 MONTH)` | Rango relativo |
| `TIMESTAMPDIFF(DAY, d1, d2)` | Diff en unidades |
| `LAST_DAY(d)` | Último día mes |

```sql
SELECT YEAR("Date") AS Año, MONTH("Date") AS Mes, SUM("Amount") AS Total
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date");
```

---

## Funciones string

| Función | Uso |
|---------|-----|
| `CONCAT(a, b)` | Concatenar |
| `CONCAT_WS(sep, a, b)` | Con separador |
| `LOWER(s)` / `UPPER(s)` | Case |
| `SUBSTRING(s, start, len)` | Subcadena |
| `LEFT(s, n)` / `RIGHT(s, n)` | Extremos |
| `TRIM(s)` | Espacios |
| `LENGTH(s)` | Longitud |
| `REPLACE(s, old, new)` | Reemplazo |
| `LOCATE(sub, s)` | Posición |

---

## Funciones numéricas

`ABS`, `ROUND(n, dec)`, `CEIL`, `FLOOR`, `POWER`, `SQRT`, `MOD`, `RAND`

---

## Agregados y GROUP BY

```sql
SELECT "Region",
    COUNT(*) AS NumVentas,
    SUM("Amount") AS Total,
    AVG("Amount") AS Promedio,
    MAX("Amount") AS Maximo
FROM "Sales"
GROUP BY "Region"
HAVING SUM("Amount") > 10000;
```

**Gotcha:** no usar alias en HAVING — usar expresión completa `SUM("Amount")`.

---

## Condicionales

```sql
SELECT "Amount",
    CASE
        WHEN "Amount" > 10000 THEN 'Alto'
        WHEN "Amount" > 5000 THEN 'Medio'
        ELSE 'Bajo'
    END AS Categoria
FROM "Sales";

SELECT COALESCE("Phone", "Mobile", 'N/A') AS Telefono FROM "Contacts";
SELECT NULLIF("Amount", 0) FROM "Sales";
```

---

## JOINs

```sql
-- INNER JOIN
SELECT e."Name", d."DepartmentName"
FROM "Employees" e
INNER JOIN "Departments" d ON e."DepartmentID" = d."DepartmentID";

-- LEFT JOIN
SELECT c."CustomerName", o."OrderID"
FROM "Customers" c
LEFT JOIN "Orders" o ON c."CustomerID" = o."CustomerID";
```

Evitar Cartesian joins (N×N). Usar alias de tabla.

---

## Window Functions (Zoho Analytics)

Funciones propias: `window_sum`, `window_avg`, `window_count`

```sql
SELECT "Date", "Amount",
    window_sum(SUM("Amount"), 0, 0) AS SumaAcumulada
FROM "Sales"
GROUP BY "Date", "Amount"
ORDER BY "Date";

-- Promedio móvil 7 días
SELECT "Date",
    window_avg(SUM("Amount"), 6, 0) AS PromedioMovil7D
FROM "Sales"
GROUP BY "Date"
ORDER BY "Date";
```

Parámetros: `(AggExpr, Start, End)` — filas arriba/abajo de la actual.

---

## Operaciones de conjunto

```sql
SELECT "Email" FROM "Contacts"
UNION ALL
SELECT "Email" FROM "Leads";

SELECT "Email" FROM "Contacts"
INTERSECT
SELECT "Email" FROM "Leads";

SELECT "Email" FROM "Contacts"
EXCEPT
SELECT "Email" FROM "Leads";
```

Preferir `UNION ALL` sobre `UNION` (mejor rendimiento).

---

## Limitaciones

| Feature | Soporte |
|---------|---------|
| CTEs no recursivos | ✅ (máx 3 por query) |
| CTEs recursivos | ❌ |
| Subqueries simples | ✅ |
| Subqueries correlacionados | ❌ |
| Alias en HAVING | ❌ |
| PIVOT/UNPIVOT con CTE | ❌ |
| DISTINCT en datasets grandes | ⚠️ usar con cuidado |

Límites datos: Free/Trial 1M filas/tabla; recomendado <200M filas/tabla.

---

## CloudSQL API

```
POST https://analytics.zoho.com/api/{ownerEmail}/{workspaceName}
```

Parámetros:
- `ZOHO_ACTION=EXPORT`
- `ZOHO_SQLQUERY={query URL-encoded}`
- `ZOHO_OUTPUT_FORMAT=JSON|CSV|XML`

```bash
curl -X POST "https://analytics.zoho.com/api/user@domain.com/MiWorkspace" \
  -d "ZOHO_ACTION=EXPORT" \
  -d "ZOHO_SQLQUERY=SELECT * FROM Sales WHERE Region='East'" \
  -H "Authorization: Zoho-oauthtoken TOKEN"
```

Desde Deluge → invokeUrl con connection OAuth Analytics.

---

## Deluge integration tasks

```deluge
zoho.analytics.createRow(workspaceId, tableId, rowData);
zoho.analytics.updateData(workspaceId, tableId, criteria, updateData);
zoho.analytics.deleteRow(workspaceId, tableId, criteria);
```

Para queries SQL complejas → CloudSQL API o Query Tables, no tasks.

---

## Ejemplos prácticos

### Ventas mensuales

```sql
SELECT YEAR("Date") AS Año, MONTH("Date") AS Mes,
    SUM("Amount") AS Ventas, COUNT(*) AS NumTransacciones
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date")
ORDER BY Año, Mes;
```

### Top 5 productos

```sql
SELECT "Product", SUM("Amount") AS Total
FROM "Sales"
GROUP BY "Product"
ORDER BY Total DESC
LIMIT 5;
```

### Segmentación clientes

```sql
SELECT "CustomerID", SUM("Amount") AS ValorTotal,
    CASE
        WHEN SUM("Amount") > 50000 THEN 'VIP'
        WHEN SUM("Amount") > 20000 THEN 'Premium'
        ELSE 'Regular'
    END AS Segmento
FROM "Sales"
GROUP BY "CustomerID";
```

---

## Mejores prácticas

1. Seleccionar solo columnas necesarias
2. Filtrar temprano con WHERE
3. Usar IN en lugar de múltiples OR
4. UNION ALL > UNION
5. Probar en Query Table antes de producción
6. Comentarios en SQL complejo
7. API queries consumen créditos Analytics

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| Column not found | Comillas dobles, case-sensitive |
| HAVING alias error | Usar expresión agregada completa |
| Query timeout | Reducir dataset, agregar filtros |
| CTE limit | Máximo 3 CTEs |
| API 401 | Refresh OAuth token |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Analytics SQL | `docs/analytics/sql.md` |
| CloudSQL API | docs/analytics/sql.md § API |
| Patrones | `docs/patterns/production.md` |

