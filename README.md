# Task Manager & Reminder Bot

> Automated WhatsApp task management and reminder system powered by **LLM**, **PostgreSQL**, and **scheduled background jobs**.

**Status:** `Architecture Spec` · `main`

---

## Architecture Overview

The system processes natural-language WhatsApp messages, extracts task information using an LLM, stores structured data in PostgreSQL, and automatically sends deadline reminders through scheduled background jobs.

```mermaid
flowchart LR
    U["👤 Registered User<br/>WhatsApp"] --> G["Node.js Gateway<br/>whatsapp-web.js"]

    G --> L["LLM API Engine<br/>Entity Extraction<br/>JSON Structuring"]
    L --> DB[("PostgreSQL<br/>Users / Tasks / Logs")]

    G --> DB

    C["Cron Job Worker<br/>node-cron"] --> DB
    C --> G

    G --> U
```

---

## System Architecture

| Layer                | Component                   | Responsibility                                                       |
| -------------------- | --------------------------- | -------------------------------------------------------------------- |
| **01 · Client**      | WhatsApp User               | Sends natural-language task messages                                 |
| **02 · Gateway**     | Node.js + `whatsapp-web.js` | Handles WhatsApp session, routing, and message delivery              |
| **03 · Processing**  | LLM API                     | Extracts entities and converts natural language into structured JSON |
| **04 · Persistence** | PostgreSQL                  | Stores users, tasks, statuses, and reminder logs                     |
| **05 · Background**  | `node-cron`                 | Periodically checks deadlines and triggers reminders                 |

---

# Data Flow

## Flow A — Ingestion & Natural Querying

```text
WhatsApp User
     │
     ▼
whatsapp-web.js
     │
     ▼
Node.js Message Router
     │
     ▼
LLM API Engine
     │
     ├── Entity Extraction
     ├── Date Detection
     ├── Priority Classification
     └── Structured JSON
     │
     ▼
PostgreSQL
     │
     ▼
Node.js Gateway
     │
     ▼
WhatsApp Response
```

### Example

**User input:**

```text
math excercise page 114, deadline oct 14 2026
```

**LLM output:**

```json
{
  "task": "math",
  "topics: excercise page 114",
  "due_date": "2026-14-10",
  "priority": "HIGH"
}
```

The structured result is then validated and persisted into PostgreSQL.

---

## Flow B — Scheduled Auto-Reminder

```text
             ┌──────────────────┐
             │   node-cron      │
             │ Background Worker│
             └────────┬─────────┘
                      │
                      ▼
              Check PostgreSQL
                      │
                      ▼
              Deadline Condition
                      │
             ┌────────┴────────┐
             │                 │
          No Match           Match
             │                 │
             ▼                 ▼
           Wait        Notification Dispatcher
                               │
                               ▼
                        WhatsApp User
```

The scheduler periodically checks active tasks and triggers notifications when a configured reminder condition is met.

---

# Core Modules

## 1. Natural Language Parsing

The bot accepts normal human language instead of requiring rigid commands.

### Input

```text
math excercise page 114, deadline oct 14 2026
```

### Processing

The LLM extracts:

* Task name
* Task Information
* Creation date
* Deadline
* Priority
* Other relevant entities

### Structured Output

```json
{
  "task": "math",
  "topics: excercise page 114",
  "due_date": "2026-14-10",
  "priority": "HIGH"
}
```

---

## 2. Persistence & Database

PostgreSQL is used as the primary relational database.

### Database Structure

```text
users
├── id
├── phone_number
├── name
├── timezone
└── created_at


tasks
├── id
├── user_id
├── title
├── description
├── created_at
├── due_date
├── due_time
├── status
├── priority
└── status

reminder_logs
├── id
├── task_id
├── reminder_type
├── sent_at
└── status
```

```text
H-2 Day
   │
   ▼
Send Reminder

H-1 Day
   │
   ▼
Send Reminder
```

Example:

```text
Deadline: 14 October 2026

13 October 2026
└── H-1 reminder

14 October 2026
└── H-3 hour reminder
```

The exact scheduling logic can be configured independently from the WhatsApp message-processing layer.

---

## 4. Interactive Priority Query

Users can request their active tasks at any time.

### Example

```text
informasi tugas
```

The system retrieves active tasks from PostgreSQL and returns them ordered by urgency.

### Example Response

```text
📌 DAFTAR TUGAS AKTIF

1. Math
   Deadline: 14 Okt
   Priority: HIGH

2. Operating System
   Deadline: 16 Okt
   Priority: MEDIUM
```

---

# Technology Stack

| Component           | Technology        | Function                                                                |
| ------------------- | ----------------- | ----------------------------------------------------------------------- |
| **WhatsApp Client** | `whatsapp-web.js` | Receive messages, maintain WhatsApp Web session, and send responses     |
| **Runtime**         | Node.js           | Main application runtime                                                |
| **AI Engine**       | LLM API           | Natural-language processing, entity extraction, and task classification |
| **Database**        | PostgreSQL        | Persistent storage for users, tasks, and reminder logs                  |
| **Scheduler**       | `node-cron`       | Periodic background deadline checking                                   |
| **Communication**   | WhatsApp          | User-facing interface                                                   |

---

# Logical Architecture

```text
┌───────────────────────────────────────────────────────────────┐
│                         USER LAYER                            │
│                                                               │
│                     WhatsApp User                             │
└──────────────────────────────┬────────────────────────────────┘
                               │
                               ▼
┌───────────────────────────────────────────────────────────────┐
│                       GATEWAY LAYER                           │
│                                                               │
│              Node.js + whatsapp-web.js                        │
│                                                               │
│   ┌─────────────────────┐    ┌──────────────────────────┐    │
│   │ Message Router      │    │ Notification Dispatcher  │    │
│   └─────────────────────┘    └──────────────────────────┘    │
└───────────────┬───────────────────────────────┬───────────────┘
                │                               │
                ▼                               │
┌──────────────────────────────┐                │
│       AI PROCESSING          │                │
│                              │                │
│        LLM API Engine        │                │
│                              │                │
│  • Entity Extraction         │                │
│  • Date Recognition          │                │
│  • Priority Classification   │                │
│  • JSON Structuring          │                │
└──────────────┬───────────────┘                │
               │                                │
               ▼                                │
┌───────────────────────────────────────────────┴───────────────┐
│                       DATA LAYER                              │
│                                                               │
│                        PostgreSQL                             │
│                                                               │
│       users  ─────  tasks  ─────  reminder_logs              │
└──────────────────────────────▲────────────────────────────────┘
                               │
                               │
┌──────────────────────────────┴────────────────────────────────┐
│                    BACKGROUND WORKER                          │
│                                                               │
│                       node-cron                               │
│                                                               │
│          Periodic deadline & reminder checker                │
└───────────────────────────────────────────────────────────────┘
```

---

# Request Lifecycle

### Incoming Task

```text
1. User sends WhatsApp message
          ↓
2. whatsapp-web.js receives message
          ↓
3. Node.js router identifies the request
          ↓
4. LLM parses natural language
          ↓
5. Structured JSON is validated
          ↓
6. Task is stored in PostgreSQL
          ↓
7. Bot confirms task creation
```

### Reminder

```text
1. node-cron triggers worker
          ↓
2. Worker queries active tasks
          ↓
3. Deadline condition is evaluated
          ↓
4. reminder_logs is checked
          ↓
5. Notification is dispatched
          ↓
6. Reminder result is logged
```

---

# Design Principles

### Natural Input

Users should not need to memorize commands.

```text
❌ /add math 2026-10-14 high

✅ mathematics deadline date 14
```

### Structured Processing

Natural-language input is converted into deterministic structured data before database operations.

```text
Natural Language
       ↓
      LLM
       ↓
Structured JSON
       ↓
Validation
       ↓
PostgreSQL
```

### Separation of Concerns

Each component has a focused responsibility:

```text
WhatsApp     → Communication
Node.js      → Application Logic
LLM          → Language Understanding
PostgreSQL   → Persistence
node-cron    → Scheduling
```

### Idempotent Reminders

Reminder execution should be tracked through `reminder_logs` so the same reminder is not sent repeatedly.

---

# Environment Configuration

Example environment variables:

```env
# WhatsApp
WHATSAPP_SESSION_NAME=task-manager

# LLM
LLM_API_KEY=your_api_key
LLM_MODEL=your_model

# PostgreSQL
DATABASE_URL=postgresql://user:password@localhost:5432/task_manager

# Scheduler
REMINDER_TIMEZONE=Asia/Jakarta
```

> Never commit `.env` or API keys to the repository.

---

# Future Improvements

* [ ] Multiple reminder intervals
* [ ] Task completion commands
* [ ] Task editing and deletion
* [ ] Recurring tasks
* [ ] User-specific timezone
* [ ] Priority customization
* [ ] Rich WhatsApp message formatting
* [ ] Reminder retry mechanism
* [ ] LLM response validation
* [ ] Database migration system
* [ ] Error monitoring and logging
* [ ] Docker deployment
* [ ] Web-based administration dashboard

---
