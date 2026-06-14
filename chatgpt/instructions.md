# Guía de uso en ChatGPT

Este directorio adapta **z-deluge-agent** para ChatGPT, que no soporta el formato nativo de skills de Cursor (`.cursor/skills/`) ni plugins de Claude Code (`.claude-plugin/`).

## Archivos incluidos

| Archivo | Uso |
|---------|-----|
| [`gpt-system-prompt.md`](gpt-system-prompt.md) | Prompt listo para copiar en **Instructions** del Custom GPT |
| [`knowledge-manifest.md`](knowledge-manifest.md) | Qué archivos de `docs/` subir y cómo configurar |

## Comparativa de plataformas

| | Cursor | Claude Code | ChatGPT |
|--|--------|-------------|---------|
| Formato | `.cursor/skills/` | Plugin marketplace | Custom GPT / Proyecto |
| Auto-carga | ✅ Al abrir repo | ✅ `/plugin install` | ❌ Config manual |
| Knowledge | Skills + docs en repo | Plugin skills | Subir `.md` a Knowledge |
| Enrutamiento 10 dominios | Automático | Automático | Via system prompt |

## Flujo recomendado

```
1. Crear Custom GPT "Zoho Deluge Agent"
2. Pegar gpt-system-prompt.md → Instructions
3. Subir 14 archivos de docs/ → Knowledge (ver knowledge-manifest.md)
4. Probar: "Escribe un Custom API Creator que cree un pedido desde JSON"
```

## Tips

- **Deluge Creator:** pregunta explícita "en Zoho Creator" ayuda al enrutamiento.
- **Client Script:** recuerda que es JavaScript — el GPT debe no mezclar con Deluge.
- **Archivos grandes:** `deluge/language.md` e `integration-tasks/index.md` son los más pesados; ChatGPT los indexa bien pero respuestas muy específicas pueden requerir citar el fragmento en el chat.
- **Versión desactualizada:** si Zoho lanza API nueva, actualiza el `.md` en el repo y re-sube a Knowledge.

## Mantenimiento

El contenido fuente de verdad sigue siendo:

- `docs/` — referencia exhaustiva
- `plugins/z-deluge-agent/skills/` — skills para Cursor/Claude

Cuando amplíes un skill en Cursor, actualiza el `.md` correspondiente en `docs/` y vuelve a subirlo al Custom GPT.
