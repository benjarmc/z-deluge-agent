# Subir plugin local en Claude (app gráfica)

Claude Desktop / Cowork permite **Subir plugin local** como archivo `.zip`.

[English](upload-local-plugin.md)

## 1. Generar el ZIP

Desde la raíz del repo:

```bash
cd plugins/z-deluge-agent
zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

O usa el ZIP ya generado: **`dist/z-deluge-agent.zip`**

> **Importante:** el ZIP debe contener en la raíz `.claude-plugin/` y `skills/`, **no** una carpeta padre extra.

Estructura correcta dentro del zip:

```
z-deluge-agent.zip
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    ├── zoho-deluge-core/
    ├── zoho-deluge-creator/
    ├── ... (10 skills)
```

## 2. Subir en Claude

1. Abre Claude (app gráfica / Cowork).
2. **Personalizar** → **Plugins personales** → **Subir plugin local**.
3. Arrastra **`dist/z-deluge-agent.zip`** (solo extensión `.zip`, no `.plugin`).
4. Confirma la advertencia de seguridad (plugin no verificado por Anthropic).
5. Pulsa **Subir**.

## 3. Activar el plugin

Tras instalar, activa **z-deluge-agent** en la lista de plugins personales.

## 4. Probar

Invoca un skill directamente:

```
/z-deluge-agent:zoho-deluge-creator
```

O pregunta en inglés o español:

- *"Custom API Creator que cree un pedido desde JSON"*
- *"COQL de Contacts con email no nulo"*
- *"Client Script para validar RFC en page.onSave"*

El agente responde en el idioma del usuario.

## 5. Actualizar tras cambios

Si editas skills en el repo:

```bash
cd plugins/z-deluge-agent && zip -r ../../dist/z-deluge-agent.zip . -x "*.DS_Store"
```

Vuelve a subir el zip (o desinstala la versión anterior e instala la nueva).

---

## Notas

| Tema | Detalle |
|------|---------|
| Formato | Solo `.zip` — si tienes `.plugin`, renómbralo a `.zip` |
| Docs `docs/` | No van en el zip; el contenido operativo está en cada `SKILL.md`. Para referencia larga, usa un Proyecto con `docs/es/` o `docs/en/` |
| Marketplace GitHub | Alternativa: `/plugin marketplace add benjarmc/z-deluge-agent` en Claude Code |
| 10 skills | Ver tabla en `README.es.md` |

## Troubleshooting

| Error | Solución |
|-------|----------|
| Upload failed | Verifica que sea `.zip` y que la raíz del zip tenga `.claude-plugin/` |
| Skills no aparecen | Re-sube zip; reinicia Claude |
| Respuestas genéricas | Invoca `/z-deluge-agent:zoho-patterns` o el skill concreto |
