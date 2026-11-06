# Contexto del proyecto: z-deluge-agent

_Última actualización: 2026-11-06_

## Qué es

Toolkit de documentación y 10 skills de agente para escribir, revisar y depurar código del ecosistema Zoho (Deluge, Creator, CRM, COQL, widgets, APIs REST, Analytics SQL). Bilingüe EN/ES. Lo mantiene y lo usa solo el autor, en local (Cursor, plugin Claude, Custom GPT). No es una aplicación con runtime propio.

## Stack y estructura

- Contenido Markdown. Plugin Claude v1.2.0 (`plugins/z-deluge-agent/.claude-plugin/plugin.json`). Sin package.json, tests ni servidor.
- Fuente de los skills: `plugins/z-deluge-agent/skills/<nombre>/` — cada uno con `SKILL.md` (cuerpo en español + YAML bilingüe; el agente responde en el idioma del usuario), `SKILL.en.md` y `reference.md`.
- `.cursor/skills/` son symlinks absolutos a esa carpeta (Cursor los carga al abrir el repo).
- Referencia larga: `docs/en/` y `docs/es/`, índice en `docs/INDEX.md`. Código, keywords Deluge, APIs y nombres de producto se quedan en inglés en ambos idiomas.
- Distribución: marketplace Claude en `.claude-plugin/marketplace.json`; zip local `dist/z-deluge-agent.zip` (gitignored) según `claude/`; prompt y manifiesto en `chatgpt/`.
- `zoho-deluge-toolkit/` es referencia local, gitignored, no parte del proyecto.
- No hay salida de graphify.
- Repo: `main` en GitHub `benjarmc/z-deluge-agent`. No hay deploy: el “release” es el zip del plugin o el contenido del repo.

## Lógica de negocio

- 10 dominios, 1:1 skill ↔ carpeta de docs (mapa al final de `docs/INDEX.md`).
- Deluge el lenguaje (`zoho-deluge-core`) se separa de Creator (`zoho-deluge-creator`), de tasks `zoho.*` (`zoho-integration-tasks`) y de REST OAuth (`zoho-public-apis`). COQL no se trata en profundidad dentro de public APIs.
- Widgets CRM y Creator son skills y docs distintos. `docs/es/widgets/crm-creator.md` es el documento combinado original; la referencia vigente son `widgets/crm.md` y `widgets/creator.md`.
- Un solo mantenedor. Nadie depende de una versión publicada en el marketplace: el uso es local. El `marketplace.json` y las guías de Claude/ChatGPT son para instalarlo en las propias herramientas.

## Decisiones

- 2026-11-06 — `connection` en `invokeUrl` e `invokeapi` es un literal entre comillas (`connection: "connection_name"`). Deluge lo resuelve al guardar; una variable no compila.
- 2026-11-06 — Uso local, un solo mantenedor — no hay equipos ni consumidores de una versión publicada; se puede cambiar docs y skills sin coordinar un release.
- 2026-11-06 — El contexto de trabajo vive en `docs/CONTEXT.md` y la regla `.cursor/rules/project-context.mdc` — para no depender de chats largos de Cursor. Plantilla de `~/Proyectos/docs/cursor-context`.
- README único bilingüe (se eliminó un README solo en español) — una entrada al repo.
- Skills con cuerpo ES en `SKILL.md` y cuerpo EN en `SKILL.en.md` — el agente carga `SKILL.md`; la doc canónica larga está en `docs/en` y `docs/es`.

## Estado actual

Documentación y los 10 skills están en el repo y en `main`. El plugin declara versión 1.2.0. CRM REST v2–v8 y GraphQL están resumidos en `docs/en/apis/crm-rest-versions.md` (y `docs/es/`). No hay suite de tests ni CI visible en el árbol.

## Pendientes

- [ ] Sumar productos Zoho que aún no tienen skill ni docs.
- [ ] Ir actualizando skills y `docs/en` + `docs/es` cuando Zoho saque documentación o funcionalidades nuevas.

## Problemas conocidos / gotchas

- Los symlinks de `.cursor/skills/` son absolutos a esta máquina (`/Users/benjarmc/Proyectos/z-deluge-agent/...`). En otro checkout hay que recrearlos.
- Al empaquetar el plugin, el zip debe tener `.claude-plugin/` y `skills/` en la raíz, y solo `.zip` (no `.plugin`). Excluir `.DS_Store`. `dist/` no se commitea.
- No guardar secretos en este archivo. Zoho OAuth y tokens no viven aquí.

## Bitácora

- 2026-11-06 — Specs de Inventory (locations, shipmentorders, stock), WorkDrive (JSON:API, sin `/move`), Creator REST v2.1 (`/data`, `/meta`, `/bulk`) y Desk OAS (`zohodesk-oas`: contracts, SLA, community, IM).
- 2026-11-06 — Incorporadas las colecciones Postman de CRM (REST v2–v6 y GraphQL) como mapa de paths en `docs/*/apis/crm-rest-versions.md`. Regla nueva: `connection` de `invokeUrl` va entre comillas, no en una variable.
- 2026-11-06 — Confirmado: un solo mantenedor, uso local. Siguiente trabajo: productos Zoho nuevos y mantener docs y skills al día.
- 2026-11-06 — Activado el contexto persistente: regla `project-context.mdc` y este archivo, a partir del README, el índice de docs y el plugin v1.2.0.
