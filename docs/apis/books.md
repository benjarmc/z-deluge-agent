# Zoho Books API v3 — Referencia Completa

> **Fuente oficial:** https://www.zoho.com/books/api/v3/  
> **URL Base:** `https://www.zohoapis.com/books/v3/`  
> **Autenticación:** OAuth 2.0 (`Zoho-oauthtoken {token}`)  
> **Parámetro obligatorio en todos los endpoints:** `?organization_id={orgId}`  
> **Límite:** 100 requests/minuto por organización

---

## Scopes OAuth

```
ZohoBooks.settings.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.contacts.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.invoices.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.estimates.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.salesorders.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.purchaseorders.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.bills.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.expenses.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.creditnotes.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.customerpayments.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.vendorpayments.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.items.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.journals.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.accountants.READ / CREATE / UPDATE / DELETE / ALL
ZohoBooks.reports.READ
```

---

## Organizaciones

```
GET    /organizations                  ← lista organizaciones
GET    /organizations/{org_id}         ← obtiene una org
POST   /organizations                  ← crea organización
PUT    /organizations/{org_id}         ← actualiza
```

## Contacts (Clientes / Proveedores)

```
POST   /contacts                       ← crear contacto
GET    /contacts                       ← listar contactos
GET    /contacts/{contact_id}          ← obtener contacto
PUT    /contacts/{contact_id}          ← actualizar
DELETE /contacts/{contact_id}          ← eliminar

POST   /contacts/{contact_id}/active   ← marcar activo
POST   /contacts/{contact_id}/inactive ← marcar inactivo
POST   /contacts/{contact_id}/portal/enable   ← habilitar portal
POST   /contacts/{contact_id}/paymentreminder/enable
POST   /contacts/{contact_id}/paymentreminder/disable
POST   /contacts/{contact_id}/statements/email
GET    /contacts/{contact_id}/statements/mailcontent
POST   /contacts/{contact_id}/email
GET    /contacts/{contact_id}/comments
POST   /contacts/{contact_id}/address  ← agregar dirección
GET    /contacts/{contact_id}/addresses
PUT    /contacts/{contact_id}/address/{address_id}
DELETE /contacts/{contact_id}/address/{address_id}
GET    /contacts/{contact_id}/refunds
POST   /contacts/{contact_id}/track1099
POST   /contacts/{contact_id}/untrack1099
GET    /contacts/{contact_id}/unusedcredits
```

## Contact Persons

```
POST   /contacts/{contact_id}/contactpersons
PUT    /contacts/{contact_id}/contactpersons/{cp_id}
DELETE /contacts/{contact_id}/contactpersons/{cp_id}
GET    /contacts/{contact_id}/contactpersons
GET    /contacts/{contact_id}/contactpersons/{cp_id}
POST   /contacts/{contact_id}/contactpersons/{cp_id}/primary
```

## Estimates (Presupuestos)

```
POST   /estimates
GET    /estimates
GET    /estimates/{estimate_id}
PUT    /estimates/{estimate_id}
DELETE /estimates/{estimate_id}
POST   /estimates/{estimate_id}/status/sent
POST   /estimates/{estimate_id}/status/accepted
POST   /estimates/{estimate_id}/status/declined
POST   /estimates/{estimate_id}/submit
POST   /estimates/{estimate_id}/approve
POST   /estimates/{estimate_id}/email
GET    /estimates/{estimate_id}/email
POST   /estimates/email           ← email múltiples
POST   /estimates/exportpdf
POST   /estimates/print
PUT    /estimates/{estimate_id}/address/billing
PUT    /estimates/{estimate_id}/address/shipping
GET    /estimates/templates
PUT    /estimates/{estimate_id}/templates/{template_id}
POST   /estimates/{estimate_id}/comments
GET    /estimates/{estimate_id}/comments
PUT    /estimates/{estimate_id}/comments/{comment_id}
DELETE /estimates/{estimate_id}/comments/{comment_id}
```

## Sales Orders (Órdenes de Venta)

```
POST   /salesorders
GET    /salesorders
GET    /salesorders/{so_id}
PUT    /salesorders/{so_id}
DELETE /salesorders/{so_id}
POST   /salesorders/{so_id}/status/open
POST   /salesorders/{so_id}/status/void
PUT    /salesorders/{so_id}/substatus
POST   /salesorders/{so_id}/email
GET    /salesorders/{so_id}/email
POST   /salesorders/{so_id}/submit
POST   /salesorders/{so_id}/approve
POST   /salesorders/exportpdf
POST   /salesorders/print
PUT    /salesorders/{so_id}/address/billing
PUT    /salesorders/{so_id}/address/shipping
GET    /salesorders/templates
PUT    /salesorders/{so_id}/templates/{template_id}
POST   /salesorders/{so_id}/attachment
GET    /salesorders/{so_id}/attachment
DELETE /salesorders/{so_id}/attachment
POST   /salesorders/{so_id}/comments
GET    /salesorders/{so_id}/comments
PUT    /salesorders/{so_id}/comments/{comment_id}
DELETE /salesorders/{so_id}/comments/{comment_id}
```

## Invoices (Facturas)

```
POST   /invoices
GET    /invoices
GET    /invoices/{invoice_id}
PUT    /invoices/{invoice_id}
DELETE /invoices/{invoice_id}
POST   /invoices/{invoice_id}/status/sent
POST   /invoices/{invoice_id}/status/void
POST   /invoices/{invoice_id}/status/draft
POST   /invoices/email           ← email múltiples
POST   /invoices/{invoice_id}/email
GET    /invoices/{invoice_id}/email
POST   /invoices/{invoice_id}/paymentreminder
GET    /invoices/{invoice_id}/paymentreminder
POST   /invoices/paymentreminder/enable
POST   /invoices/paymentreminder/disable
POST   /invoices/exportpdf
POST   /invoices/print
POST   /invoices/{invoice_id}/writeoff
POST   /invoices/{invoice_id}/cancelwriteoff
PUT    /invoices/{invoice_id}/address/billing
PUT    /invoices/{invoice_id}/address/shipping
GET    /invoices/templates
PUT    /invoices/{invoice_id}/templates/{template_id}
GET    /invoices/{invoice_id}/payments
GET    /invoices/{invoice_id}/creditsapplied
POST   /invoices/{invoice_id}/credits
DELETE /invoices/{invoice_id}/payments/{payment_id}
DELETE /invoices/{invoice_id}/creditsapplied/{credit_id}
POST   /invoices/{invoice_id}/attachment
GET    /invoices/{invoice_id}/attachment
DELETE /invoices/{invoice_id}/attachment
POST   /invoices/{invoice_id}/comments
GET    /invoices/{invoice_id}/comments
PUT    /invoices/{invoice_id}/comments/{comment_id}
DELETE /invoices/{invoice_id}/comments/{comment_id}
POST   /invoices/{invoice_id}/paymentlink  ← generar link de pago
POST   /invoices/instantinvoices           ← crear factura instantánea
```

## Sales Receipts

```
POST   /salesreceipts
GET    /salesreceipts
GET    /salesreceipts/{sr_id}
PUT    /salesreceipts/{sr_id}
DELETE /salesreceipts/{sr_id}
POST   /salesreceipts/{sr_id}/email
```

## Credit Notes (Notas de Crédito)

```
POST   /creditnotes
GET    /creditnotes
GET    /creditnotes/{cn_id}
PUT    /creditnotes/{cn_id}
DELETE /creditnotes/{cn_id}
POST   /creditnotes/{cn_id}/status/open
POST   /creditnotes/{cn_id}/status/void
POST   /creditnotes/{cn_id}/email
GET    /creditnotes/{cn_id}/refunds
POST   /creditnotes/{cn_id}/refunds
PUT    /creditnotes/{cn_id}/refunds/{refund_id}
DELETE /creditnotes/{cn_id}/refunds/{refund_id}
GET    /creditnotes/{cn_id}/invoices
POST   /creditnotes/{cn_id}/invoices   ← aplicar crédito a facturas
DELETE /creditnotes/{cn_id}/invoices/{invoice_id}
```

## Customer Payments (Pagos de Clientes)

```
POST   /customerpayments
GET    /customerpayments
GET    /customerpayments/{payment_id}
PUT    /customerpayments/{payment_id}
DELETE /customerpayments/{payment_id}
POST   /customerpayments/{payment_id}/refund
```

## Purchase Orders (Órdenes de Compra)

```
POST   /purchaseorders
GET    /purchaseorders
GET    /purchaseorders/{po_id}
PUT    /purchaseorders/{po_id}
DELETE /purchaseorders/{po_id}
POST   /purchaseorders/{po_id}/status/open
POST   /purchaseorders/{po_id}/status/billed
POST   /purchaseorders/{po_id}/status/cancelled
POST   /purchaseorders/{po_id}/email
POST   /purchaseorders/{po_id}/submit
POST   /purchaseorders/{po_id}/approve
PUT    /purchaseorders/{po_id}/address/billing
PUT    /purchaseorders/{po_id}/address/shipping
POST   /purchaseorders/{po_id}/attachment
POST   /purchaseorders/{po_id}/comments
GET    /purchaseorders/{po_id}/comments
```

## Bills (Facturas de Proveedor)

```
POST   /bills
GET    /bills
GET    /bills/{bill_id}
PUT    /bills/{bill_id}
DELETE /bills/{bill_id}
POST   /bills/{bill_id}/status/open
POST   /bills/{bill_id}/status/void
POST   /bills/{bill_id}/submit
POST   /bills/{bill_id}/approve
GET    /bills/{bill_id}/payments
POST   /bills/{bill_id}/payments
DELETE /bills/{bill_id}/payments/{payment_id}
GET    /bills/{bill_id}/creditsapplied
POST   /bills/{bill_id}/credits
DELETE /bills/{bill_id}/creditsapplied/{credit_id}
POST   /bills/{bill_id}/attachment
POST   /bills/{bill_id}/comments
```

## Vendor Payments (Pagos a Proveedores)

```
POST   /vendorpayments
GET    /vendorpayments
GET    /vendorpayments/{payment_id}
PUT    /vendorpayments/{payment_id}
DELETE /vendorpayments/{payment_id}
```

## Vendor Credits (Créditos de Proveedor)

```
POST   /vendorcredits
GET    /vendorcredits
GET    /vendorcredits/{vc_id}
PUT    /vendorcredits/{vc_id}
DELETE /vendorcredits/{vc_id}
POST   /vendorcredits/{vc_id}/status/open
POST   /vendorcredits/{vc_id}/status/void
```

## Expenses (Gastos)

```
POST   /expenses
GET    /expenses
GET    /expenses/{expense_id}
PUT    /expenses/{expense_id}
DELETE /expenses/{expense_id}
POST   /expenses/{expense_id}/submit
POST   /expenses/{expense_id}/approve
POST   /expenses/{expense_id}/receipt
GET    /expenses/{expense_id}/receipt
DELETE /expenses/{expense_id}/receipt
POST   /expenses/{expense_id}/comments
GET    /expenses/{expense_id}/comments
```

## Items (Productos / Servicios)

```
POST   /items
GET    /items
GET    /items/{item_id}
GET    /itemdetails?item_ids={id1},{id2}  ← múltiples items
PUT    /items/{item_id}
DELETE /items/{item_id}
POST   /items/{item_id}/active
POST   /items/{item_id}/inactive
```

## Chart of Accounts (Plan de Cuentas)

```
POST   /chartofaccounts
GET    /chartofaccounts
GET    /chartofaccounts/{account_id}
PUT    /chartofaccounts/{account_id}
DELETE /chartofaccounts/{account_id}
POST   /chartofaccounts/{account_id}/active
POST   /chartofaccounts/{account_id}/inactive
GET    /chartofaccounts/transactions   ← transacciones de cuenta
```

## Journals (Asientos Contables)

```
POST   /journals
GET    /journals
GET    /journals/{journal_id}
PUT    /journals/{journal_id}
DELETE /journals/{journal_id}
```

## Bank Accounts

```
POST   /bankaccounts
GET    /bankaccounts
GET    /bankaccounts/{account_id}
PUT    /bankaccounts/{account_id}
DELETE /bankaccounts/{account_id}
POST   /bankaccounts/{account_id}/deactivate
POST   /bankaccounts/{account_id}/activate
GET    /bankaccounts/{account_id}/transactions
POST   /bankaccounts/{account_id}/transactions
GET    /bankaccounts/{account_id}/statement
POST   /bankaccounts/{account_id}/statement
```

## Bank Transactions

```
GET    /banktransactions
GET    /banktransactions/{transaction_id}
POST   /banktransactions/match         ← categorizar transacción
POST   /banktransactions/unmatch
POST   /banktransactions/exclude
POST   /banktransactions/restore
```

## Taxes

```
POST   /settings/taxes
GET    /settings/taxes
GET    /settings/taxes/{tax_id}
PUT    /settings/taxes/{tax_id}
DELETE /settings/taxes/{tax_id}
```

## Projects (dentro de Books)

```
POST   /projects
GET    /projects
GET    /projects/{project_id}
PUT    /projects/{project_id}
DELETE /projects/{project_id}
POST   /projects/{project_id}/active
POST   /projects/{project_id}/inactive
GET    /projects/{project_id}/tasks
POST   /projects/{project_id}/tasks
GET    /projects/{project_id}/users
POST   /projects/{project_id}/users
GET    /projects/{project_id}/timelogs
POST   /projects/{project_id}/timelogs
```

## Reports

```
GET    /reports/balancesheet
GET    /reports/profitloss
GET    /reports/cashflow
GET    /reports/trialbalance
GET    /reports/generalled
GET    /reports/accounttransactions
GET    /reports/salesbycustomer
GET    /reports/salesbyitem
GET    /reports/purchasebysupplier
GET    /reports/purchasebyitem
GET    /reports/invoicedetails
GET    /reports/receivablesummary
GET    /reports/payablesummary
GET    /reports/taxsummary
```

## Custom Modules

```
POST   /settings/modules                ← crear módulo personalizado
GET    /settings/modules                ← listar módulos
GET    /settings/modules/{module_api}   ← obtener módulo
PUT    /settings/modules/{module_api}   ← actualizar
POST   /{module_api}                    ← crear registro
GET    /{module_api}                    ← listar registros
GET    /{module_api}/{record_id}        ← obtener registro
PUT    /{module_api}/{record_id}        ← actualizar
DELETE /{module_api}/{record_id}        ← eliminar
```

## Custom Functions (API v3)

```
POST   /settings/automations/customfunctions
GET    /settings/automations/customfunctions
GET    /settings/automations/customfunctions/{function_id}
PUT    /settings/automations/customfunctions/{function_id}
DELETE /settings/automations/customfunctions/{function_id}
```

**Lenguajes soportados:** `deluge`, `nodejs`, `java`, `python`, `golang`

---

## Ejemplo invokeUrl desde Deluge

```deluge
// Crear factura en Books desde CRM function
orgId = "10234695";

lineItems = list();
item = Map();
item.put("item_id", "460000000044208");
item.put("quantity", 2);
item.put("rate", 100.00);
lineItems.add(item);

payload = Map();
payload.put("customer_id", "460000000026041");
payload.put("line_items", lineItems);
payload.put("due_date", "2024-03-31");

headers = Map();
headers.put("Authorization", "Zoho-oauthtoken " + accessToken);
headers.put("Content-Type", "application/json");

response = invokeUrl [
    url:        "https://www.zohoapis.com/books/v3/invoices?organization_id=" + orgId
    type:       POST
    body:       payload.toText()
    headers:    headers
    connection: "zoho_books_conn"
];

invoiceId = response.get("invoice").get("invoice_id");
```

## Manejo de Nulos en Books

```deluge
// Los campos custom están en custom_field_hash
rec = zoho.books.getRecordById("Contacts", contactId, orgId);

if (rec.containsKey("custom_field_hash")) {
    customFields = rec.get("custom_field_hash");
    rfc = ifNull(customFields.get("cf_rfc"), "");
}

// Estado del registro
status = ifNull(rec.get("status"), "");
if (status == "active") { /* ... */ }
```
