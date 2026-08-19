# Guía de uso en ChatGPT

Este directorio adapta **z-deluge-agent** para ChatGPT, que no soporta skills de Cursor (`.cursor/skills/`) ni plugins de Claude Code (`.claude-plugin/`).

[English](instructions.md)

## Archivos

| Archivo | Uso |
|---------|-----|
| [`gpt-system-prompt.es.md`](gpt-system-prompt.es.md) | Instructions en español para el Custom GPT |
| [`gpt-system-prompt.md`](gpt-system-prompt.md) | Instructions en inglés |
| [`knowledge-manifest.es.md`](knowledge-manifest.es.md) | Qué archivos de `docs/es/` o `docs/en/` subir |

## Comparativa de plataformas

| | Cursor | Claude Code | ChatGPT |
|--|--------|-------------|---------|
| Formato | `.cursor/skills/` | Plugin marketplace | Custom GPT / Proyecto |
| Auto-carga | Al abrir repo | `/plugin install` | Config manual |
| Knowledge | Skills + docs | Plugin skills | Subir `.md` a Knowledge |
| Enrutamiento 10 dominios | Automático | Automático | Via system prompt |
| Idiomas | EN/ES (idioma del usuario) | EN/ES | EN/ES |

## Flujo recomendado

```
1. Crear Custom GPT "Zoho Deluge Agent"
2. Pegar gpt-system-prompt.es.md → Instructions
3. Subir 14 archivos de docs/es/ O docs/en/ (ver knowledge-manifest)
4. Probar: "Escribe un Custom API Creator que cree un pedido desde JSON"
```

## Tips

- **Deluge Creator:** pregunta explícita "en Zoho Creator" ayuda al enrutamiento.
- **Client Script:** es JavaScript — el GPT no debe mezclarlo con Deluge.
- **Archivos grandes:** `deluge/language.md` e `integration-tasks/index.md` son los más pesados.
- **Versión desactualizada:** si Zoho lanza API nueva, actualiza el `.md` en el repo y re-sube Knowledge.

## Fuente de verdad

- `docs/en/` y `docs/es/` — referencia exhaustiva
- `plugins/z-deluge-agent/skills/` — skills para Cursor/Claude

Cuando amplíes un skill, actualiza el `.md` en `docs/en/` y `docs/es/` y vuelve a subirlo al Custom GPT.
