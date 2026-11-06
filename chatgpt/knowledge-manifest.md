# Knowledge manifest — Custom GPT / ChatGPT Project

Files to upload as **Knowledge**. Custom GPT limit: **20 files**. This list uses **15** (pick **one language**).

[Español](knowledge-manifest.es.md)

Upload either `docs/en/` **or** `docs/es/` — not both (that would exceed the 20-file cap).

---

## Files to upload (this order)

Replace `LANG` with `en` or `es`.

| # | Path | Domain | Priority |
|---|------|--------|----------|
| 1 | `docs/INDEX.md` | Master index (bilingual) | Essential |
| 2 | `docs/LANG/patterns/production.md` | Patterns / troubleshooting | Essential |
| 3 | `docs/LANG/deluge/language.md` | Deluge core | Essential |
| 4 | `docs/LANG/creator/deluge-in-creator.md` | Creator Deluge | High |
| 5 | `docs/LANG/client-script/crm.md` | CRM Client Scripts | High |
| 6 | `docs/LANG/coql/crm-coql.md` | COQL | High |
| 7 | `docs/LANG/widgets/crm.md` | CRM widgets | Medium |
| 8 | `docs/LANG/widgets/creator.md` | Creator widgets | Medium |
| 9 | `docs/LANG/integration-tasks/index.md` | Integration tasks (24 services) | High |
| 10 | `docs/LANG/apis/crm-v8.md` | CRM v8 REST | High |
| 11 | `docs/LANG/apis/crm-rest-versions.md` | CRM REST v2–v8 and GraphQL | High |
| 12 | `docs/LANG/apis/books.md` | Books REST | Medium |
| 13 | `docs/LANG/apis/desk.md` | Desk REST | Medium |
| 14 | `docs/LANG/apis/other-products.md` | Other products REST | Medium |
| 15 | `docs/LANG/analytics/sql.md` | Analytics SQL | Medium |

### Do not upload

| File | Why |
|------|-----|
| `docs/es/widgets/crm-creator.md` | Legacy — use `widgets/crm.md` + `widgets/creator.md` |
| `plugins/**/SKILL.md` | Already condensed in docs + system prompt |
| `zoho-deluge-toolkit/` | External reference |

---

## Custom GPT setup

1. ChatGPT → **Explore GPTs** → **Create**
2. **Name:** `Zoho Deluge Agent`
3. **Description:** Expert in Deluge, Creator, CRM Client Scripts, COQL, Widgets, Zoho APIs, and Analytics SQL (EN/ES).
4. **Instructions:** paste [`gpt-system-prompt.md`](gpt-system-prompt.md) (English) or [`gpt-system-prompt.es.md`](gpt-system-prompt.es.md) (Spanish)
5. **Knowledge:** upload the 15 files for **one** language
6. **Capabilities:** Code Interpreter optional; Web Browsing optional for official docs
7. Save

## ChatGPT Project setup

1. Create project **Zoho Deluge Agent**
2. Project instructions: paste the matching system prompt
3. Attach the 15 `.md` files
4. New chats in the project inherit that knowledge

## If you only have 7 slots

1. `docs/INDEX.md`
2. `docs/LANG/patterns/production.md`
3. `docs/LANG/deluge/language.md`
4. `docs/LANG/creator/deluge-in-creator.md`
5. `docs/LANG/client-script/crm.md`
6. `docs/LANG/integration-tasks/index.md`
7. `docs/LANG/apis/crm-v8.md`

## Updating Knowledge

Re-upload changed `.md` files. Custom GPTs do **not** auto-sync from GitHub.
