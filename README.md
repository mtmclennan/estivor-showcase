# Estivor

**Estimating, customer follow-up, and job workflow software built for excavation contractors and small field-service businesses.**

Estivor is a working SaaS product designed around the way small excavation companies actually operate: phone calls, inquiries, estimates, callbacks, jobs, and customer history.

Rather than adapting a large generic CRM to a small contractor, Estivor simplifies the workflow around the work contractors already do.

**Website:** estivor.com
**Status:** Working production pilot · Active development
**Production source:** Private

---

## The Problem

Small excavation contractors often manage customer work across a mixture of:

* phone calls and text messages
* notebooks
* spreadsheets
* email
* estimating documents
* disconnected accounting or CRM tools

That creates predictable problems:

* missed calls become missed opportunities
* follow-up depends on someone remembering
* estimate information gets recreated later
* customer history is scattered
* jobs are disconnected from the original inquiry and estimate

Estivor brings those pieces into one focused workflow.

---

## The Workflow

```text
Inquiry
   ↓
Customer
   ↓
Estimate
   ↓
Proposal
   ↓
Accepted Work
   ↓
Job
   ↓
Customer / Job History
```

Estivor also maintains a **Needs Attention** workflow so callbacks, overdue actions, follow-up, and other important work do not disappear into someone's inbox or memory.

---

## Current Product Capabilities

### Customer and inquiry management

* Customer and contact records
* Inquiry tracking
* Customer activity history
* Opportunity lifecycle
* Workspace-scoped data

### Estimating

* Estimate creation
* Quantities and costing
* Contractor-specific estimate structure
* Branded customer-facing documents
* Estimate-to-job workflow

### Follow-up and task management

* Needs Attention queue
* Return-call tasks
* Scheduled notifications
* Follow-up reminders
* Outbound communication queue

### Phone and lead intake

Estivor can receive missed-call events from a business phone system and turn them into actionable work.

```text
Incoming Call
     ↓
Ooma Office
     ↓
Zapier
     ↓
Estivor
     ↓
Contact matched or created
     ↓
Inquiry created or reused
     ↓
Return Call task
     ↓
Needs Attention
     ↓
Acknowledgement queued
```

The integration is designed to be idempotent so retries do not create duplicate customers, tasks, or messages.

---

## Why I Built It

Estivor grew from observing a real excavation company where estimating and customer follow-up were being handled across phone calls, email, spreadsheets, and individual memory.

The goal was not to build another general-purpose CRM.

The goal was to build a smaller system around the actual lifecycle of contractor work:

**inquiry → estimate → follow-up → accepted work → job**

The product is currently being piloted using real excavation-company workflows.

---

## Technology

### Application

* Next.js
* React
* TypeScript
* Prisma
* PostgreSQL
* Clerk authentication

### Production infrastructure

* DigitalOcean
* Neon PostgreSQL
* Cloudflare
* Cloudflare Tunnel
* Nginx
* systemd
* Linux

### Testing

* Vitest
* PostgreSQL integration testing
* Playwright
* CI

---

## Production Architecture

```text
                    Internet
                       │
                       ▼
                  Cloudflare
             ┌─────────┴─────────┐
             │                   │
        Web Traffic           SSH Access
             │                   │
             ▼                   ▼
        Cloudflare Tunnel
             │
             ▼
     ┌──────────────────┐
     │ DigitalOcean     │
     │ Production VM    │
     └────────┬─────────┘
              │
         Nginx Proxy
              │
              ▼
        Next.js / Estivor
              │
       ┌──────┴───────┐
       │              │
       ▼              ▼
   Neon Postgres    Clerk
```

The production origin does not expose the application, reverse proxy, or SSH daemon directly to the public Internet.

Web and SSH traffic enter through Cloudflare Tunnel, while the underlying services listen only on local interfaces.

---

## Engineering Considerations

Estivor has been built as a production application rather than only a UI prototype.

Some of the engineering work includes:

* multi-tenant workspace isolation
* server-side authorization
* idempotent external integrations
* database migrations
* production environment validation
* secure secret management
* outbound-only production networking
* scheduler health monitoring
* retry-safe lifecycle events
* notification and message queues
* production logging
* automated application testing
* deployment and recovery procedures

---

## Background Processing

Estivor uses a recurring production scheduler to process due work.

```text
systemd timer
     ↓
Scheduler service
     ↓
Estivor scheduler API
     ↓
Due notifications / messages / tasks
     ↓
Scheduler heartbeat
     ↓
System health monitoring
```

The application records scheduler health so a stopped background process becomes visible instead of silently failing.

---

## Security Approach

The production environment is intentionally designed to minimize exposed infrastructure.

* Cloudflare Tunnel instead of public application ports
* Cloudflare Access protecting SSH
* SSH bound to localhost
* Next.js bound to localhost
* Nginx bound to localhost
* firewall default-deny for inbound traffic
* production secrets stored outside the repository
* Clerk used for authentication
* application-level company membership used for authorization
* workspace isolation tested at the server layer

---

## Product Direction

Estivor is beginning with excavation contractors, but the underlying idea is broader:

**take powerful business workflows that normally require complicated CRM or operations software and simplify them for smaller field-service companies.**

Future development may expand the workflow from estimating into job planning, execution, and additional contractor operations.

---

## Project Status

**Working production pilot / active development**

The public Estivor website and product are under active development.

The production application source code remains private. This repository exists as a technical and product case study of the system, architecture, and engineering work behind Estivor.

