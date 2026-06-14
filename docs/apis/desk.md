# Zoho Desk API v1 — Referencia Completa

> **Fuente oficial:** https://desk.zoho.com/DeskAPIDocument  
> **URL Base:** `https://desk.zoho.com/api/v1/`  
> **Headers obligatorios:** `Authorization: Zoho-oauthtoken {token}` + `orgId: {org_id}`  
> **Límite:** créditos de API por edición (ver sección de créditos)

---

## Scopes OAuth

```
Desk.tickets.READ / CREATE / UPDATE / DELETE / ALL
Desk.contacts.READ / CREATE / UPDATE / DELETE / ALL
Desk.accounts.READ / CREATE / UPDATE / DELETE / ALL
Desk.agents.READ / CREATE / UPDATE / DELETE / ALL
Desk.search.READ
Desk.settings.READ / CREATE / UPDATE / DELETE / ALL
Desk.basic.READ / CREATE / UPDATE / DELETE / ALL
Desk.articles.READ / CREATE / UPDATE / DELETE / ALL
Desk.tasks.READ / CREATE / UPDATE / DELETE / ALL
Desk.calls.READ / CREATE / UPDATE / DELETE / ALL
Desk.events.READ / CREATE / UPDATE / DELETE / ALL
```

---

## Organizations

```
GET    /organizations                           ← lista organizaciones
GET    /organizations/{org_id}                  ← obtiene org
GET    /organizations/accessible               ← orgs accesibles
PUT    /organizations/{org_id}                  ← actualiza
GET    /organizations/{org_id}/logo
POST   /organizations/{org_id}/logo             ← sube logo
PUT    /organizations/{org_id}/logo
DELETE /organizations/{org_id}/logo
GET    /organizations/{org_id}/favicon
POST   /organizations/default                   ← actualiza org default
```

## Departments

```
GET    /departments                             ← lista departamentos
GET    /departments/{dept_id}                  ← obtiene depto
GET    /departments/{dept_id}/agents           ← agentes del depto
GET    /departments/count
GET    /departments/details                    ← por IDs múltiples
POST   /departments                            ← crea depto
PUT    /departments/{dept_id}                  ← actualiza
POST   /departments/{dept_id}/disable
POST   /departments/{dept_id}/enable
POST   /departments/{dept_id}/agents           ← asocia agentes
DELETE /departments/{dept_id}/agents/{agent_id}
GET    /departments/{dept_id}/logo
POST   /departments/{dept_id}/logo
DELETE /departments/{dept_id}/logo
```

## Agents

```
GET    /agents                                  ← lista agentes
GET    /agents/{agent_id}                      ← obtiene agente
GET    /agents/count
GET    /agents/myinfo                          ← info del agente actual
POST   /agents/{agent_id}/activate
POST   /agents/{agent_id}/deactivate
POST   /agents/{agent_id}/reinvite
POST   /agents                                 ← añade agente
PUT    /agents/{agent_id}                      ← actualiza
DELETE /agents/{agent_id}/unconfirmed          ← elimina no confirmado
DELETE /agents/{agent_id}                      ← elimina confirmado
POST   /agents/{agent_id}/anonymize
GET    /agents/me/photo
POST   /agents/me/photo
DELETE /agents/me/photo
GET    /agents/me/preferences
PUT    /agents/me/preferences
GET    /agents/me/entitypreferences
PUT    /agents/me/entitypreferences
GET    /agents/{email}/agentbyemail
POST   /agents/{agent_id}/reassignment
```

## Agent Availability

```
PUT    /agents/availability
GET    /agents/availability
GET    /agents/me/availability
GET    /agents/online
GET    /agents/offline
```

## Teams

```
POST   /teams                                  ← crea equipo
PUT    /teams/{team_id}                        ← actualiza
DELETE /teams/{team_id}
GET    /teams/{team_id}
GET    /teams                                  ← todos los equipos
GET    /teams/{team_id}/agents                 ← miembros del equipo
```

## Roles

```
GET    /roles                                  ← lista roles
GET    /roles/{role_id}
GET    /roles/count
GET    /roles/personal
POST   /roles
PUT    /roles/{role_id}
DELETE /roles/{role_id}
GET    /roles/{role_id}/agents
```

## Tickets (el módulo principal)

```
POST   /tickets                                ← crear ticket
GET    /tickets                                ← listar tickets
GET    /tickets/{ticket_id}                   ← obtener ticket
PUT    /tickets/{ticket_id}                   ← actualizar
DELETE /tickets/{ticket_id}                   ← mover a papelera
DELETE /tickets/spam                          ← eliminar spam
POST   /tickets/merge                         ← fusionar tickets
POST   /tickets/{ticket_id}/move             ← mover a depto
POST   /tickets/{ticket_id}/split            ← dividir ticket
POST   /tickets/bulkUpdate                   ← actualizar múltiples
POST   /tickets/close                        ← cerrar múltiples
POST   /tickets/{ticket_id}/markAsRead
POST   /tickets/{ticket_id}/markAsUnread
POST   /tickets/{ticket_id}/markAsSpam
GET    /tickets/views/count                  ← conteo por vistas
GET    /tickets/agents/count                 ← conteo por agente
GET    /tickets/{ticket_id}/history
GET    /tickets/{ticket_id}/resolution
GET    /tickets/{ticket_id}/resolutionhistory
PUT    /tickets/{ticket_id}/resolution
DELETE /tickets/{ticket_id}/resolution
GET    /tickets/{ticket_id}/metrics          ← métricas del ticket
POST   /tickets/spam/empty                   ← vaciar spam
POST   /tickets/{ticket_id}/assignSkill
POST   /tickets/{ticket_id}/recalculateSkill
GET    /tickets/{ticket_id}/suggestArticles  ← IA: artículos sugeridos

GET    /tickets/archivedlist                 ← tickets archivados
GET    /tickets/associated                   ← tickets asociados
```

## Threads (Conversaciones del Ticket)

```
GET    /tickets/{ticket_id}/threads
GET    /tickets/{ticket_id}/threads/{thread_id}
GET    /tickets/{ticket_id}/threads/latestthread
GET    /tickets/{ticket_id}/threads/{thread_id}/originalmail
POST   /tickets/{ticket_id}/threads/{thread_id}/sendForReview
GET    /tickets/{ticket_id}/conversations    ← lista conversaciones
POST   /tickets/{ticket_id}/sendReply       ← responder via canal custom
POST   /tickets/{ticket_id}/sendEmail       ← responder por email
POST   /tickets/{ticket_id}/sendFacebookReply
POST   /tickets/{ticket_id}/sendTwitterReply
POST   /tickets/{ticket_id}/sendForumReply
POST   /tickets/{ticket_id}/draftEmailReply
POST   /tickets/{ticket_id}/draftFacebookReply
POST   /tickets/{ticket_id}/draftForumReply
PUT    /tickets/{ticket_id}/threads/{thread_id}/draft
DELETE /tickets/{ticket_id}/threads/{thread_id}/attachments/{attach_id}
```

## Ticket Comments

```
GET    /tickets/{ticket_id}/comments
POST   /tickets/{ticket_id}/comments        ← agregar comentario
GET    /tickets/{ticket_id}/comments/{comment_id}
PUT    /tickets/{ticket_id}/comments/{comment_id}
DELETE /tickets/{ticket_id}/comments/{comment_id}
```

## Ticket Attachments

```
GET    /tickets/{ticket_id}/attachments
POST   /tickets/{ticket_id}/attachments
GET    /tickets/{ticket_id}/attachments/{attach_id}
DELETE /tickets/{ticket_id}/attachments/{attach_id}
```

## Contacts

```
POST   /contacts                               ← crear contacto
GET    /contacts                               ← listar
GET    /contacts/{contact_id}                 ← obtener
PUT    /contacts/{contact_id}                 ← actualizar
DELETE /contacts/{contact_id}                 ← mover a papelera
DELETE /contacts/spam
POST   /contacts/merge
GET    /contacts/{contact_id}/tickets
GET    /contacts/count
GET    /contacts/fields                       ← campos disponibles
POST   /contacts/{contact_id}/photo
GET    /contacts/{contact_id}/photo
DELETE /contacts/{contact_id}/photo
```

## Accounts (Cuentas/Empresas)

```
POST   /accounts                               ← crear cuenta
GET    /accounts                               ← listar
GET    /accounts/{account_id}                 ← obtener
PUT    /accounts/{account_id}                 ← actualizar
DELETE /accounts/{account_id}
POST   /accounts/merge
GET    /accounts/{account_id}/tickets
GET    /accounts/{account_id}/contacts
GET    /accounts/count
GET    /accounts/fields
```

## Products

```
POST   /products                               ← crear producto
GET    /products                               ← listar
GET    /products/{product_id}                 ← obtener
PUT    /products/{product_id}                 ← actualizar
DELETE /products                              ← mover a papelera
GET    /products/{product_id}/tickets
GET    /products/{product_id}/accounts
GET    /products/{product_id}/contacts
POST   /products/{product_id}/contacts       ← asociar contactos
POST   /products/{product_id}/accounts       ← asociar cuentas
```

## Tasks

```
POST   /tasks                                  ← crear tarea
GET    /tasks                                  ← listar
GET    /tasks/{task_id}                       ← obtener
PUT    /tasks/{task_id}                       ← actualizar
DELETE /tasks/{task_id}
GET    /tickets/{ticket_id}/tasks
POST   /tickets/{ticket_id}/tasks
```

## Events

```
POST   /events                                 ← crear evento
GET    /events                                 ← listar
GET    /events/{event_id}                     ← obtener
PUT    /events/{event_id}                     ← actualizar
DELETE /events/{event_id}
```

## Calls

```
POST   /calls                                  ← registrar llamada
GET    /calls                                  ← listar
GET    /calls/{call_id}                       ← obtener
PUT    /calls/{call_id}                       ← actualizar
DELETE /calls/{call_id}
```

## Knowledge Base (Articles)

```
POST   /articles                               ← crear artículo
GET    /articles                               ← listar
GET    /articles/{article_id}                 ← obtener
PUT    /articles/{article_id}                 ← actualizar
DELETE /articles/{article_id}
POST   /articles/{article_id}/publish
POST   /articles/{article_id}/unpublish
GET    /articles/{article_id}/feedback
GET    /categories                             ← categorías
POST   /categories
GET    /categories/{category_id}
PUT    /categories/{category_id}
DELETE /categories/{category_id}
GET    /categories/{category_id}/sections
POST   /categories/{category_id}/sections
```

## Search

```
GET    /search?module={module}&query={query}   ← búsqueda general
GET    /tickets/search?query={query}
GET    /contacts/search?query={query}
GET    /accounts/search?query={query}
```

## Automation

```
GET    /automations                            ← listar reglas
POST   /automations                            ← crear regla
GET    /automations/{rule_id}
PUT    /automations/{rule_id}
DELETE /automations/{rule_id}
GET    /workflows
POST   /workflows
GET    /workflows/{wf_id}
PUT    /workflows/{wf_id}
DELETE /workflows/{wf_id}
```

## Custom Modules

```
GET    /modules                                ← lista todos los módulos
POST   /modules                                ← crea módulo custom
PUT    /modules/{module_id}                   ← edita módulo
GET    /modules/{module_id}
GET    /modules/accessible                    ← accesibles al perfil actual

POST   /{module_api}/records                  ← crear registro
GET    /{module_api}/records                  ← listar
GET    /{module_api}/records/{record_id}      ← obtener
PUT    /{module_api}/records/{record_id}      ← actualizar
DELETE /{module_api}/records                  ← mover a papelera
PUT    /{module_api}/records/bulk             ← actualizar múltiples
GET    /{module_api}/records/count
```

## Webhooks

```
GET    /webhooks
POST   /webhooks                               ← registrar webhook
GET    /webhooks/{webhook_id}
PUT    /webhooks/{webhook_id}
DELETE /webhooks/{webhook_id}
```

Eventos disponibles: `ticket.created`, `ticket.updated`, `ticket.statusChanged`, `contact.created`, `contact.updated`, `agent.created`, `agent.updated`, `account.created`

---

## Ejemplo invokeUrl desde Deluge

```deluge
// Crear ticket en Desk desde una función de CRM
orgId = "2389290";

ticketData = Map();
ticketData.put("subject", "Problema con factura #INV-001");
ticketData.put("departmentId", "1892000000006907");
ticketData.put("contactId", contactId);
ticketData.put("description", "El cliente reporta error en monto.");
ticketData.put("priority", "High");
ticketData.put("channel", "Web");
ticketData.put("status", "Open");

headers = Map();
headers.put("Authorization", "Zoho-oauthtoken " + token);
headers.put("orgId", orgId);
headers.put("Content-Type", "application/json");

response = invokeUrl [
    url:        "https://desk.zoho.com/api/v1/tickets"
    type:       POST
    body:       ticketData.toText()
    headers:    headers
    connection: "zoho_desk_conn"
];

ticketId = response.get("id");

// O usando la Deluge task nativa
deskResp = zoho.desk.create("tickets", orgId, ticketData);
ticketId = deskResp.get("id");
```

## Skills y Asignación

```
POST   /skilltypes                             ← crear tipo de habilidad
GET    /skilltypes
PUT    /skilltypes/{skilltype_id}
DELETE /skilltypes/{skilltype_id}
POST   /skills
GET    /skills
PUT    /skills/{skill_id}
DELETE /skills/{skill_id}
GET    /agents/{agent_id}/skills              ← habilidades del agente
POST   /departments/{dept_id}/skills/{skill_id}/agents  ← asignar
```

## Business Hours y Holidays

```
POST   /businesshours
GET    /businesshours
GET    /businesshours/{bh_id}
PUT    /businesshours/{bh_id}
DELETE /businesshours/{bh_id}
GET    /businesshours/{bh_id}/automationrules

POST   /holidaylists
GET    /holidaylists
GET    /holidaylists/{hl_id}
PUT    /holidaylists/{hl_id}
DELETE /holidaylists/{hl_id}
```

## Layouts y Campos Personalizados

```
GET    /layouts                                ← listar layouts
GET    /layouts/{layout_id}
POST   /layouts
PUT    /layouts/{layout_id}
DELETE /layouts/{layout_id}
POST   /layouts/{layout_id}/clone
GET    /layouts/{layout_id}/profiles
PUT    /layouts/{layout_id}/profiles

GET    /fields?module={module}                ← campos del módulo
POST   /fields
PUT    /fields/{field_id}
GET    /fields/{field_id}
DELETE /fields/{field_id}
```

## Data Sharing Rules

```
GET    /datasharingrules
PUT    /datasharingrules
```
