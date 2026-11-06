# Manifest de Knowledge — Custom GPT / Proyecto ChatGPT

Archivos para subir como **Knowledge**. Límite Custom GPT: **20 archivos**. Esta lista usa **15** (elige **un idioma**).

[English](knowledge-manifest.md)

Sube `docs/es/` **o** `docs/en/` — no ambos (superarías el límite).

---

## Archivos a subir (este orden)

Sustituye `LANG` por `es` o `en`.

| # | Ruta | Dominio | Prioridad |
|---|------|---------|-----------|
| 1 | `docs/INDEX.md` | Índice maestro (bilingüe) | Esencial |
| 2 | `docs/LANG/patterns/production.md` | Patrones / troubleshooting | Esencial |
| 3 | `docs/LANG/deluge/language.md` | Deluge general | Esencial |
| 4 | `docs/LANG/creator/deluge-in-creator.md` | Deluge Creator | Alta |
| 5 | `docs/LANG/client-script/crm.md` | Client Scripts CRM | Alta |
| 6 | `docs/LANG/coql/crm-coql.md` | COQL | Alta |
| 7 | `docs/LANG/widgets/crm.md` | Widgets CRM | Media |
| 8 | `docs/LANG/widgets/creator.md` | Widgets Creator | Media |
| 9 | `docs/LANG/integration-tasks/index.md` | Integration tasks (24 servicios) | Alta |
| 10 | `docs/LANG/apis/crm-v8.md` | REST CRM v8 | Alta |
| 11 | `docs/LANG/apis/crm-rest-versions.md` | REST CRM v2–v8 y GraphQL | Alta |
| 12 | `docs/LANG/apis/books.md` | REST Books | Media |
| 13 | `docs/LANG/apis/desk.md` | REST Desk | Media |
| 14 | `docs/LANG/apis/other-products.md` | REST otros productos | Media |
| 15 | `docs/LANG/analytics/sql.md` | Analytics SQL | Media |

### No subir

| Archivo | Motivo |
|---------|--------|
| `docs/es/widgets/crm-creator.md` | Legacy — usar `widgets/crm.md` + `widgets/creator.md` |
| `plugins/**/SKILL.md` | Ya condensados en docs + system prompt |
| `zoho-deluge-toolkit/` | Referencia externa |

---

## Setup Custom GPT

1. ChatGPT → **Explore GPTs** → **Create**
2. **Name:** `Zoho Deluge Agent`
3. **Description:** Experto en Deluge, Creator, Client Scripts CRM, COQL, Widgets, APIs Zoho y Analytics SQL (EN/ES).
4. **Instructions:** pegar [`gpt-system-prompt.es.md`](gpt-system-prompt.es.md) (español) o [`gpt-system-prompt.md`](gpt-system-prompt.md) (inglés)
5. **Knowledge:** subir los 15 archivos de **un** idioma
6. Guardar

## Setup Proyecto ChatGPT

1. Crear proyecto **Zoho Deluge Agent**
2. Instrucciones: pegar el system prompt
3. Adjuntar los 15 `.md`
4. Los chats nuevos heredan el conocimiento

## Si solo caben 7 archivos

1. `docs/INDEX.md`
2. `docs/LANG/patterns/production.md`
3. `docs/LANG/deluge/language.md`
4. `docs/LANG/creator/deluge-in-creator.md`
5. `docs/LANG/client-script/crm.md`
6. `docs/LANG/integration-tasks/index.md`
7. `docs/LANG/apis/crm-v8.md`

## Actualizar Knowledge

Vuelve a subir los `.md` modificados. Los Custom GPT **no** se sincronizan solos con GitHub.
