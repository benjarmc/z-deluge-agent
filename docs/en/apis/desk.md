# Zoho Desk API v1 — Complete Reference

> Language: English. Spanish: see the matching path under docs/es/.

> **Official source:** https://desk.zoho.com/DeskAPIDocument  
> **OpenAPI:** https://github.com/zoho/zohodesk-oas (`v1.0`, reviewed 2026-11-06)
> **Base URL:** `https://desk.zoho.com/api/v1/`  
> **Required headers:** `Authorization: Zoho-oauthtoken {token}` + `orgId: {org_id}`  
> **Limit:** API credits by edition (see credits section)

---

## OAuth Scopes

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
GET    /organizations                           ← list organizations
GET    /organizations/{org_id}                  ← get org
GET    /organizations/accessible               ← accessible orgs
PUT    /organizations/{org_id}                  ← update
GET    /organizations/{org_id}/logo
POST   /organizations/{org_id}/logo             ← upload logo
PUT    /organizations/{org_id}/logo
DELETE /organizations/{org_id}/logo
GET    /organizations/{org_id}/favicon
POST   /organizations/default                   ← update default org
```

## Departments

```
GET    /departments                             ← list departments
GET    /departments/{dept_id}                  ← get department
GET    /departments/{dept_id}/agents           ← department agents
GET    /departments/count
GET    /departments/details                    ← by multiple IDs
POST   /departments                            ← create department
PUT    /departments/{dept_id}                  ← update
POST   /departments/{dept_id}/disable
POST   /departments/{dept_id}/enable
POST   /departments/{dept_id}/agents           ← associate agents
DELETE /departments/{dept_id}/agents/{agent_id}
GET    /departments/{dept_id}/logo
POST   /departments/{dept_id}/logo
DELETE /departments/{dept_id}/logo
```

## Agents

```
GET    /agents                                  ← list agents
GET    /agents/{agent_id}                      ← get agent
GET    /agents/count
GET    /agents/myinfo                          ← current agent info
POST   /agents/{agent_id}/activate
POST   /agents/{agent_id}/deactivate
POST   /agents/{agent_id}/reinvite
POST   /agents                                 ← add agent
PUT    /agents/{agent_id}                      ← update
DELETE /agents/{agent_id}/unconfirmed          ← delete unconfirmed
DELETE /agents/{agent_id}                      ← delete confirmed
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
POST   /teams                                  ← create team
PUT    /teams/{team_id}                        ← update
DELETE /teams/{team_id}
GET    /teams/{team_id}
GET    /teams                                  ← all teams
GET    /teams/{team_id}/agents                 ← team members
```

## Roles

```
GET    /roles                                  ← list roles
GET    /roles/{role_id}
GET    /roles/count
GET    /roles/personal
POST   /roles
PUT    /roles/{role_id}
DELETE /roles/{role_id}
GET    /roles/{role_id}/agents
```

## Tickets (the main module)

```
POST   /tickets                                ← create ticket
GET    /tickets                                ← list tickets
GET    /tickets/{ticket_id}                   ← get ticket
PUT    /tickets/{ticket_id}                   ← update
DELETE /tickets/{ticket_id}                   ← move to trash
DELETE /tickets/spam                          ← delete spam
POST   /tickets/merge                         ← merge tickets
POST   /tickets/{ticket_id}/move             ← move to department
POST   /tickets/{ticket_id}/split            ← split ticket
POST   /tickets/bulkUpdate                   ← update multiple
POST   /tickets/close                        ← close multiple
POST   /tickets/{ticket_id}/markAsRead
POST   /tickets/{ticket_id}/markAsUnread
POST   /tickets/{ticket_id}/markAsSpam
GET    /tickets/views/count                  ← count by views
GET    /tickets/agents/count                 ← count by agent
GET    /tickets/{ticket_id}/history
GET    /tickets/{ticket_id}/resolution
GET    /tickets/{ticket_id}/resolutionhistory
PUT    /tickets/{ticket_id}/resolution
DELETE /tickets/{ticket_id}/resolution
GET    /tickets/{ticket_id}/metrics          ← ticket metrics
POST   /tickets/spam/empty                   ← empty spam
POST   /tickets/{ticket_id}/assignSkill
POST   /tickets/{ticket_id}/recalculateSkill
GET    /tickets/{ticket_id}/suggestArticles  ← AI: suggested articles

GET    /tickets/archivedlist                 ← archived tickets
GET    /tickets/associated                   ← associated tickets
```

## Threads (Ticket Conversations)

```
GET    /tickets/{ticket_id}/threads
GET    /tickets/{ticket_id}/threads/{thread_id}
GET    /tickets/{ticket_id}/threads/latestthread
GET    /tickets/{ticket_id}/threads/{thread_id}/originalmail
POST   /tickets/{ticket_id}/threads/{thread_id}/sendForReview
GET    /tickets/{ticket_id}/conversations    ← list conversations
POST   /tickets/{ticket_id}/sendReply       ← reply via custom channel
POST   /tickets/{ticket_id}/sendEmail       ← reply by email
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
POST   /tickets/{ticket_id}/comments        ← add comment
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
POST   /contacts                               ← create contact
GET    /contacts                               ← list
GET    /contacts/{contact_id}                 ← get
PUT    /contacts/{contact_id}                 ← update
DELETE /contacts/{contact_id}                 ← move to trash
DELETE /contacts/spam
POST   /contacts/merge
GET    /contacts/{contact_id}/tickets
GET    /contacts/count
GET    /contacts/fields                       ← available fields
POST   /contacts/{contact_id}/photo
GET    /contacts/{contact_id}/photo
DELETE /contacts/{contact_id}/photo
```

## Accounts (Companies)

```
POST   /accounts                               ← create account
GET    /accounts                               ← list
GET    /accounts/{account_id}                 ← get
PUT    /accounts/{account_id}                 ← update
DELETE /accounts/{account_id}
POST   /accounts/merge
GET    /accounts/{account_id}/tickets
GET    /accounts/{account_id}/contacts
GET    /accounts/count
GET    /accounts/fields
```

## Products

```
POST   /products                               ← create product
GET    /products                               ← list
GET    /products/{product_id}                 ← get
PUT    /products/{product_id}                 ← update
DELETE /products                              ← move to trash
GET    /products/{product_id}/tickets
GET    /products/{product_id}/accounts
GET    /products/{product_id}/contacts
POST   /products/{product_id}/contacts       ← associate contacts
POST   /products/{product_id}/accounts       ← associate accounts
```

## Tasks

```
POST   /tasks                                  ← create task
GET    /tasks                                  ← list
GET    /tasks/{task_id}                       ← get
PUT    /tasks/{task_id}                       ← update
DELETE /tasks/{task_id}
GET    /tickets/{ticket_id}/tasks
POST   /tickets/{ticket_id}/tasks
```

## Events

```
POST   /events                                 ← create event
GET    /events                                 ← list
GET    /events/{event_id}                     ← get
PUT    /events/{event_id}                     ← update
DELETE /events/{event_id}
```

## Calls

```
POST   /calls                                  ← log call
GET    /calls                                  ← list
GET    /calls/{call_id}                       ← get
PUT    /calls/{call_id}                       ← update
DELETE /calls/{call_id}
```

## Knowledge Base (Articles)

```
POST   /articles                               ← create article
GET    /articles                               ← list
GET    /articles/{article_id}                 ← get
PUT    /articles/{article_id}                 ← update
DELETE /articles/{article_id}
POST   /articles/{article_id}/publish
POST   /articles/{article_id}/unpublish
GET    /articles/{article_id}/feedback
GET    /categories                             ← categories
POST   /categories
GET    /categories/{category_id}
PUT    /categories/{category_id}
DELETE /categories/{category_id}
GET    /categories/{category_id}/sections
POST   /categories/{category_id}/sections
```

## Search

```
GET    /search?module={module}&query={query}   ← general search
GET    /tickets/search?query={query}
GET    /contacts/search?query={query}
GET    /accounts/search?query={query}
```

## Automation

```
GET    /automations                            ← list rules
POST   /automations                            ← create rule
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
GET    /modules                                ← list all modules
POST   /modules                                ← create custom module
PUT    /modules/{module_id}                   ← edit module
GET    /modules/{module_id}
GET    /modules/accessible                    ← accessible to current profile

POST   /{module_api}/records                  ← create record
GET    /{module_api}/records                  ← list
GET    /{module_api}/records/{record_id}      ← get
PUT    /{module_api}/records/{record_id}      ← update
DELETE /{module_api}/records                  ← move to trash
PUT    /{module_api}/records/bulk             ← update multiple
GET    /{module_api}/records/count
```

## Webhooks

```
GET    /webhooks
POST   /webhooks                               ← register webhook
GET    /webhooks/{webhook_id}
PUT    /webhooks/{webhook_id}
DELETE /webhooks/{webhook_id}
```

Available events: `ticket.created`, `ticket.updated`, `ticket.statusChanged`, `contact.created`, `contact.updated`, `agent.created`, `agent.updated`, `account.created`

---

## Also in zohodesk-oas

Same base `/api/v1/` and the `orgId` header. The repo has ~180 resources; these are the ones not listed above.

```
GET    /contracts
POST   /contracts
GET    /sla
POST   /sla
GET    /blueprints
GET    /roundRobinRules
GET    /directAssignmentRules
GET    /channels
POST   /recycleBin/restore
GET    /ticketTags
POST   /closeTickets
GET    /myPendingApprovals
GET    /myActiveTimers
POST   /bulkExport

GET    /communityTopics
GET    /communityCategory
GET    /helpCenters
GET    /im/sessions
GET    /sandbox
GET    /zohoFinance/{financeService}/invoices
```

Ticket, contact, account, agent, article, and activity paths above stay the working set. Use the OAS when you need comments, attachments, followers, or time entries on those modules.

## invokeUrl example from Deluge

```deluge
// Create a ticket in Desk from a CRM function
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

// Or using the native Deluge task
deskResp = zoho.desk.create("tickets", orgId, ticketData);
ticketId = deskResp.get("id");
```

## Skills and Assignment

```
POST   /skilltypes                             ← create skill type
GET    /skilltypes
PUT    /skilltypes/{skilltype_id}
DELETE /skilltypes/{skilltype_id}
POST   /skills
GET    /skills
PUT    /skills/{skill_id}
DELETE /skills/{skill_id}
GET    /agents/{agent_id}/skills              ← agent skills
POST   /departments/{dept_id}/skills/{skill_id}/agents  ← assign
```

## Business Hours and Holidays

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

## Layouts and Custom Fields

```
GET    /layouts                                ← list layouts
GET    /layouts/{layout_id}
POST   /layouts
PUT    /layouts/{layout_id}
DELETE /layouts/{layout_id}
POST   /layouts/{layout_id}/clone
GET    /layouts/{layout_id}/profiles
PUT    /layouts/{layout_id}/profiles

GET    /fields?module={module}                ← module fields
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
