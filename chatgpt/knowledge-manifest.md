# Manifest de Knowledge — Custom GPT / Proyecto ChatGPT

Lista de archivos del repositorio para subir como **Knowledge** en un Custom GPT o adjuntar a un **Proyecto** de ChatGPT.

Límite Custom GPT: **20 archivos**. Este manifest usa **14** (margen para notas propias).

---

## Archivos a subir (en este orden)

| # | Ruta en repo | Dominio | Prioridad |
|---|--------------|---------|-----------|
| 1 | `docs/INDEX.md` | Índice maestro | ⭐ Esencial |
| 2 | `docs/patterns/production.md` | Patrones / troubleshooting | ⭐ Esencial |
| 3 | `docs/deluge/language.md` | Deluge general | ⭐ Esencial |
| 4 | `docs/creator/deluge-in-creator.md` | Deluge Creator | Alta |
| 5 | `docs/client-script/crm.md` | Client Scripts CRM | Alta |
| 6 | `docs/coql/crm-coql.md` | COQL | Alta |
| 7 | `docs/widgets/crm.md` | Widgets CRM | Media |
| 8 | `docs/widgets/creator.md` | Widgets Creator | Media |
| 9 | `docs/integration-tasks/index.md` | Integration tasks (24 servicios) | Alta |
| 10 | `docs/apis/crm-v8.md` | REST CRM v8 | Alta |
| 11 | `docs/apis/books.md` | REST Books | Media |
| 12 | `docs/apis/desk.md` | REST Desk | Media |
| 13 | `docs/apis/other-products.md` | REST otros productos | Media |
| 14 | `docs/analytics/sql.md` | Analytics SQL | Media |

### No subir (legacy / redundante)

| Archivo | Motivo |
|---------|--------|
| `docs/widgets/crm-creator.md` | Reemplazado por `widgets/crm.md` + `widgets/creator.md` |
| `plugins/**/SKILL.md` | Ya condensados en docs/ + gpt-system-prompt.md |
| `zoho-deluge-toolkit/` | Referencia externa, no parte del proyecto |

---

## Setup paso a paso — Custom GPT

1. ChatGPT → **Explore GPTs** → **Create**
2. **Name:** `Zoho Deluge Agent`
3. **Description:** Experto en Deluge, Creator, Client Scripts CRM, COQL, Widgets, APIs Zoho y Analytics SQL.
4. **Instructions:** copiar contenido de [`gpt-system-prompt.md`](gpt-system-prompt.md) (desde "Eres **Zoho Deluge Agent**...")
5. **Knowledge:** subir los 14 archivos de la tabla (arrastrar desde `docs/`)
6. **Capabilities:** activar **Code Interpreter** si quieres validar JSON/SQL; **Web Browsing** opcional para docs oficiales recientes
7. **Actions:** no requeridas (Knowledge es estático)
8. Guardar como **Only me** o **Anyone with link** según prefieras

---

## Setup — Proyecto ChatGPT (Plus/Pro)

1. Crear proyecto **Zoho Deluge Agent**
2. Custom instructions del proyecto: pegar `gpt-system-prompt.md`
3. Adjuntar los 14 `.md` al proyecto (quedan como contexto persistente)
4. En cada chat nuevo del proyecto, el contexto ya incluye los docs

---

## Si te quedas sin slots (20 archivos)

Prioridad mínima viable (7 archivos):

1. `docs/INDEX.md`
2. `docs/patterns/production.md`
3. `docs/deluge/language.md`
4. `docs/creator/deluge-in-creator.md`
5. `docs/client-script/crm.md`
6. `docs/integration-tasks/index.md`
7. `docs/apis/crm-v8.md`

Añade `coql/crm-coql.md` y `widgets/*.md` según tu uso principal.

---

## Actualizar Knowledge

Cuando actualices el repo:

1. Regenera o re-sube los `.md` modificados en el Custom GPT (Edit → Knowledge → replace file)
2. Los Custom GPT **no** se sincronizan solos con GitHub — hay que subir manualmente o vía API de Assistants

---

## Alternativa: API OpenAI Assistants

Para sync programático, usa la [Assistants API](https://platform.openai.com/docs/assistants/overview) con `file_ids` apuntando a los mismos 14 archivos. Requiere backend propio; no es el flujo UI de Custom GPT.
