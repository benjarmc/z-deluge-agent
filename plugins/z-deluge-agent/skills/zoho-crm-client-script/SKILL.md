---
name: zoho-crm-client-script
description: Escribe, revisa y depura Client Scripts de Zoho CRM en JavaScript del lado del cliente. Usar para page.onLoad, page.onSave, field.onChange, subform.onRowAdd/onCellChange, ZDK.Page, ZDK.Client, zoho.crm.* Web API, ZRC, validación en tiempo real, UI formularios CRM, Canvas, Wizard o cuando el usuario menciona Client Script, zohocrm.dev o scripting frontend CRM.
---

# Client Scripts — Zoho CRM (JavaScript)

Scripts **JavaScript** en el navegador. **No es Deluge.**

**Referencia completa:** `docs/client-script/crm.md`  
**Docs oficiales:** https://www.zohocrm.dev/explore/client-script/

---

## Alcance

- Páginas: Create, Edit, Clone, Detail (Standard + Canvas), List (Standard), Wizard
- Setup: Developer Hub → Client Script → +New Script → Module + Page + Event
- Runtime: browser del usuario — respeta permisos de su perfil

---

## Event Dictionary — Page Events

### page.onLoad
Dispara al cargar Create/Edit/Clone/Detail.

```javascript
page.onLoad = function() {
    var acc = ZDK.Page.getField("Account_Type");
    acc.setValue("Wholesaler");
    acc.setReadOnly(true);
    ZDK.Page.getField("Campo_Oculto").hide();
    ZDK.Page.getField("Email").setMandatory(true);
};
```

### page.onSave
Validación antes de guardar. Retornar `false` bloquea save.

```javascript
page.onSave = function() {
    var email = ZDK.Page.getField("Email").getValue();
    if(!email || !email.includes("@")) {
        ZDK.Client.showMessage("Email inválido", {type: "error"});
        return false;
    }
    return true;
};
```

### page.onChange
Dispara cuando cambia cualquier campo.

```javascript
page.onChange = function(fieldName, fieldValue) {
    if(fieldName === "Industry" && fieldValue === "Technology") {
        ZDK.Page.getField("Priority").setValue("High");
    }
};
```

---

## Event Dictionary — Field Events

### field.{API_NAME}.onChange

```javascript
field.Email.onChange = function(fieldValue) {
    var f = ZDK.Page.getField("Email");
    if(fieldValue && !fieldValue.includes("@")) {
        f.showError("Email inválido");
    } else {
        f.clearError();
    }
};
```

### field.onBeforeSave (validación campo)

```javascript
field.Phone.onBeforeSave = function(value) {
    if(value && value.length < 10) {
        return {valid: false, message: "Mínimo 10 dígitos"};
    }
    return {valid: true};
};
```

---

## Event Dictionary — Subform Events

### subform.onRowAdd

```javascript
subform.onRowAdd = function(rowIndex) {
    var sf = ZDK.Page.getSubform("Productos");
    sf.getRow(rowIndex).getField("Cantidad").setValue(1);
};
```

### subform.onRowDelete

```javascript
subform.onRowDelete = function(rowIndex) {
    recalcularTotal();
};
```

### subform.onCellChange

```javascript
subform.onCellChange = function(rowIndex, fieldName, value) {
    if(fieldName === "Cantidad" || fieldName === "Precio") {
        recalcularFila(rowIndex);
    }
};
```

---

## ZDK.Page — Client API

### getField(fieldName)

```javascript
var field = ZDK.Page.getField("Email");
field.getValue();
field.setValue("nuevo@email.com");
field.setReadOnly(true);
field.setMandatory(true);
field.setVisibility(false);  // o .hide() / .show()
field.showError("mensaje");
field.clearError();
field.getLabel();
field.getType();
```

### getForm()

```javascript
var form = ZDK.Page.getForm();
var all = form.getValues();
form.setValues({
    'Account_Type': 'Enterprise',
    'Status': 'Active'
});
```

### getSubform(subformName)

```javascript
var sf = ZDK.Page.getSubform("Lineas");
var rows = sf.getRows();
var count = sf.getRowCount();
sf.addRow();
sf.deleteRow(0);
sf.getRow(0).setValues({'Quantity': 1, 'Price': 100});
```

### getRecordContext()

```javascript
var ctx = ZDK.Page.getRecordContext();
var recordId = ctx.getId();
var module = ctx.getModuleName();
var pageType = ctx.getPageType(); // create, edit, detail
var isNew = ctx.isNew();
```

---

## ZDK.Client — UI

```javascript
ZDK.Client.showMessage("Guardado", {type: "success"});
ZDK.Client.showMessage("Error", {type: "error"});
ZDK.Client.showMessage("Aviso", {type: "warning"});

ZDK.Client.showAlert("Operación completada", "Éxito", "OK");

var ok = ZDK.Client.showConfirmation("¿Eliminar?", "Sí", "No");
if(ok) { /* confirmado */ }

ZDK.Client.showLoader();
ZDK.Client.hideLoader();
```

---

## Web API — zoho.crm.*

Desde Client Script, acceso REST con permisos del usuario activo.

### getRecords

```javascript
var resp = zoho.crm.getRecords("Deals", 1, 50);
for(var i = 0; i < resp.length; i++) {
    var rec = resp[i];
    console.log(rec.get("Deal_Name"));
}
```

### getRecord

```javascript
var rec = zoho.crm.getRecord("Contacts", recordId);
console.log(rec.get("Email"));
```

### searchRecords

```javascript
var results = zoho.crm.searchRecords("Contacts", "(Email:equals:maria@empresa.com)");
```

### createRecord / updateRecord / deleteRecord

```javascript
var data = {"Last_Name": "García", "Email": "maria@empresa.com"};
var created = zoho.crm.createRecord("Leads", data);

zoho.crm.updateRecord("Contacts", recordId, {"Phone": "+525512345678"});
zoho.crm.deleteRecord("Tasks", taskId);
```

**Límites:** max 200/página, respeta perfil usuario, no expone OAuth secrets.

---

## ZRC — Zoho Request Client

Proxy HTTP desde Client Script (evita CORS).

### ZRC.get / post / put / delete

```javascript
ZRC.get("https://api.ejemplo.com/data", {param: "value"}, {"Authorization": "Bearer TOKEN"})
.then(function(resp) {
    if(resp.status === 200) {
        var data = JSON.parse(resp.body);
    }
});

ZRC.post("https://api.ejemplo.com/validar", JSON.stringify({rfc: rfcVal}), {
    "Content-Type": "application/json"
}).then(function(resp) { /* ... */ });
```

Configurar conexión en CRM para credenciales server-side.

---

## Patrones paste-ready

### Show/hide condicional

```javascript
page.onLoad = function() { toggleCampos(); };
field.Tipo_Cuenta.onChange = function() { toggleCampos(); };

function toggleCampos() {
    var tipo = ZDK.Page.getField("Tipo_Cuenta").getValue();
    var esEmpresa = (tipo === "Empresa");
    ZDK.Page.getField("RFC").setVisibility(esEmpresa);
    ZDK.Page.getField("Razon_Social").setMandatory(esEmpresa);
}
```

### Validación cruzada onSave

```javascript
page.onSave = function() {
    var ini = ZDK.Page.getField("Fecha_Inicio").getValue();
    var fin = ZDK.Page.getField("Fecha_Fin").getValue();
    if(ini && fin && new Date(fin) < new Date(ini)) {
        return {valid: false, message: "Fin debe ser posterior a inicio"};
    }
    return {valid: true};
};
```

### Prefill desde URL (Create)

```javascript
page.onLoad = function() {
    var params = ZDK.Page.getQueryParams();
    if(params.campaign_id) {
        ZDK.Page.getField("Campaign_Source").setValue(params.campaign_id);
    }
};
```

### Duplicados email

```javascript
field.Email.onChange = function(val) {
    if(!val) return;
    var dupes = zoho.crm.searchRecords("Contacts", "(Email:equals:" + val + ")");
    if(dupes.length > 0) {
        ZDK.Client.showMessage("Email ya existe en Contacts", {type: "warning"});
    }
};
```

### Total subform

```javascript
subform.onCellChange = function(rowIdx, field, val) {
    recalcTotal();
};
function recalcTotal() {
    var sf = ZDK.Page.getSubform("Lineas");
    var total = 0;
    for(var i = 0; i < sf.getRowCount(); i++) {
        var row = sf.getRow(i);
        var qty = parseFloat(row.getField("Cantidad").getValue()) || 0;
        var price = parseFloat(row.getField("Precio").getValue()) || 0;
        total += qty * price;
    }
    ZDK.Page.getField("Total").setValue(total);
}
```

---

## Client Script vs Deluge (server)

| | Client Script | Deluge Function |
|--|---------------|-----------------|
| Runtime | Browser | Servidor |
| Lenguaje | JavaScript | Deluge |
| UI inmediata | ✅ | ❌ |
| Secretos OAuth | ❌ | ✅ |
| Límites | Perfil usuario | Integration limits |

UX → Client Script. Integraciones pesadas/sensibles → Deluge.

---

## Gotchas

| Problema | Fix |
|----------|-----|
| UI congelada | Evitar loops síncronos largos |
| undefined en getValue | Siempre null-check |
| Picklist hardcoded | Usar API names |
| Canvas Detail | Verificar selectores layout |
| CORS en fetch | Usar ZRC |
| API name typo | Setup → Fields → API Name |

---

## Troubleshooting

| Síntoma | Causa | Fix |
|---------|-------|-----|
| Script no ejecuta | Evento/página incorrectos | Verificar Module + Page + Event |
| Field not found | API name incorrecto | Revisar API Name en Setup |
| Save no bloquea | onSave no retorna false | return false explícito |
| Web API 403 | Sin permiso perfil | Ajustar perfil o usar Deluge |
| ZRC fail | Conexión no configurada | Setup → Connections |

---

## Referencia completa

| Tema | Archivo |
|------|---------|
| Client Scripts CRM | `docs/client-script/crm.md` |
| Patrones | `docs/patterns/production.md` |

