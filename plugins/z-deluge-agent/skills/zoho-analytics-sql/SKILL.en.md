---
name: zoho-analytics-sql
description: Writes, reviews, and debugs SQL in Zoho Analytics: date/string/numeric functions, JOINs, window functions, Query Tables, CTE/subquery limits, CloudSQL REST API, JDBC. Use for Zoho Analytics SQL, Query Table, window_sum, CloudSQL API, ZOHO_SQLQUERY. / Escribe, revisa y depura SQL en Zoho Analytics: funciones fecha/string/numéricas, JOINs, window functions, Query Tables, CloudSQL API, ZOHO_SQLQUERY.
---

# Zoho Analytics SQL

## Language / Idioma

Reply in the user's language (English or Spanish). Code identifiers, API names, and Zoho product names stay in English.
Responde en el idioma del usuario (inglés o español). Identificadores de código, nombres de API y productos Zoho permanecen en inglés.

Canonical docs: `docs/en/` (English) · `docs/es/` (Español)

SQL queries in Zoho Analytics: Query Tables, reports, and CloudSQL API.

**Full reference:** `docs/en/analytics/sql.md` · `docs/es/analytics/sql.md`  
**Deluge integration tasks:** `zoho.analytics.*` in skill `zoho-integration-tasks`  
**REST Analytics v2:** skill `zoho-public-apis`

---

## Supported dialects

ANSI SQL (recommended), MySQL, Oracle, SQL Server, PostgreSQL, DB2, Sybase, Informix.

Use **ANSI SQL** for maximum compatibility.

---

## Query Tables

1. Workspace → Create → Query Table
2. Write SQL → Execute Query → Save
3. They refresh dynamically with source data

```sql
SELECT "Region", "Product", SUM("Amount") AS TotalVentas
FROM "Sales"
WHERE "Date" >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY "Region", "Product"
ORDER BY TotalVentas DESC;
```

**Rules:** names with spaces go in double quotes `"Column Name"`.

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

Prefer `IN` over multiple `OR`. Avoid `SELECT *` on large tables.

---

## Date functions

| Function | Use |
|---------|-----|
| `CURDATE()` | Current date |
| `NOW()` | Current DateTime |
| `YEAR(d)` / `MONTH(d)` / `DAY(d)` | Components |
| `ADDDATE(d, n)` / `ADDMONTH(d, n)` | Add |
| `DATEDIFF(d1, d2)` | Difference in days |
| `DATE_FORMAT(d, '%Y-%m-%d')` | Format |
| `DATE_SUB(CURDATE(), INTERVAL 12 MONTH)` | Relative range |
| `TIMESTAMPDIFF(DAY, d1, d2)` | Diff in units |
| `LAST_DAY(d)` | Last day of month |

```sql
SELECT YEAR("Date") AS Año, MONTH("Date") AS Mes, SUM("Amount") AS Total
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date");
```

---

## String functions

| Function | Use |
|---------|-----|
| `CONCAT(a, b)` | Concatenate |
| `CONCAT_WS(sep, a, b)` | With separator |
| `LOWER(s)` / `UPPER(s)` | Case |
| `SUBSTRING(s, start, len)` | Substring |
| `LEFT(s, n)` / `RIGHT(s, n)` | Ends |
| `TRIM(s)` | Whitespace |
| `LENGTH(s)` | Length |
| `REPLACE(s, old, new)` | Replace |
| `LOCATE(sub, s)` | Position |

---

## Numeric functions

`ABS`, `ROUND(n, dec)`, `CEIL`, `FLOOR`, `POWER`, `SQRT`, `MOD`, `RAND`

---

## Aggregates and GROUP BY

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

**Gotcha:** do not use aliases in HAVING — use the full expression `SUM("Amount")`.

---

## Conditionals

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

Avoid Cartesian joins (N×N). Use table aliases.

---

## Window Functions (Zoho Analytics)

Custom functions: `window_sum`, `window_avg`, `window_count`

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

Parameters: `(AggExpr, Start, End)` — rows above/below the current one.

---

## Set operations

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

Prefer `UNION ALL` over `UNION` (better performance).

---

## Limitations

| Feature | Support |
|---------|---------|
| Non-recursive CTEs | ✅ (max 3 per query) |
| Recursive CTEs | ❌ |
| Simple subqueries | ✅ |
| Correlated subqueries | ❌ |
| Alias in HAVING | ❌ |
| PIVOT/UNPIVOT with CTE | ❌ |
| DISTINCT on large datasets | ⚠️ use with care |

Data limits: Free/Trial 1M rows/table; recommended <200M rows/table.

---

## CloudSQL API

```
POST https://analytics.zoho.com/api/{ownerEmail}/{workspaceName}
```

Parameters:
- `ZOHO_ACTION=EXPORT`
- `ZOHO_SQLQUERY={query URL-encoded}`
- `ZOHO_OUTPUT_FORMAT=JSON|CSV|XML`

```bash
curl -X POST "https://analytics.zoho.com/api/user@domain.com/MiWorkspace" \
  -d "ZOHO_ACTION=EXPORT" \
  -d "ZOHO_SQLQUERY=SELECT * FROM Sales WHERE Region='East'" \
  -H "Authorization: Zoho-oauthtoken TOKEN"
```

From Deluge → invokeUrl with an Analytics OAuth connection.

---

## Deluge integration tasks

```deluge
zoho.analytics.createRow(workspaceId, tableId, rowData);
zoho.analytics.updateData(workspaceId, tableId, criteria, updateData);
zoho.analytics.deleteRow(workspaceId, tableId, criteria);
```

For complex SQL queries → CloudSQL API or Query Tables, not tasks.

---

## Practical examples

### Monthly sales

```sql
SELECT YEAR("Date") AS Año, MONTH("Date") AS Mes,
    SUM("Amount") AS Ventas, COUNT(*) AS NumTransacciones
FROM "Sales"
GROUP BY YEAR("Date"), MONTH("Date")
ORDER BY Año, Mes;
```

### Top 5 products

```sql
SELECT "Product", SUM("Amount") AS Total
FROM "Sales"
GROUP BY "Product"
ORDER BY Total DESC
LIMIT 5;
```

### Customer segmentation

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

## Best practices

1. Select only the columns you need
2. Filter early with WHERE
3. Use IN instead of multiple OR
4. UNION ALL > UNION
5. Test in a Query Table before production
6. Comments in complex SQL
7. API queries consume Analytics credits

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| Column not found | Double quotes, case-sensitive |
| HAVING alias error | Use the full aggregate expression |
| Query timeout | Reduce dataset, add filters |
| CTE limit | Maximum 3 CTEs |
| API 401 | Refresh OAuth token |

---

## Full reference

| Topic | File |
|------|---------|
| Analytics SQL | `docs/en/analytics/sql.md` · `docs/es/analytics/sql.md` |
| CloudSQL API | `docs/en/analytics/sql.md` · `docs/es/analytics/sql.md` § API |
| Patterns | `docs/en/patterns/production.md` · `docs/es/patterns/production.md` |
