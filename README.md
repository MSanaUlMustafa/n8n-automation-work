# Digital Banking Backend + Ops Automation

A digital banking backend and operations-automation capstone built with **n8n, Python, Supabase/PostgreSQL, Pinecone, RAG, and Gmail**.

The project implements an automated banking operations system where:

- **Supabase/PostgreSQL** is the banking source of truth and ledger.
- **n8n** orchestrates customer requests, scheduled jobs, approvals, notifications, and error handling.
- **Python** performs fraud scoring, interest calculation, and reconciliation.
- **Pinecone** provides semantic retrieval for approved banking policies and fraud-pattern references.
- **RAG** grounds customer-support drafts in retrieved policy context.
- **Gmail** is the customer-facing communication channel.

---

## 1. Project Overview

The objective of this project was to build a digital banking backend and operations automation platform capable of handling:

- Customer email intake
- Customer identification
- Intent classification
- Account management
- Joint-account authority rules
- Transfers
- Idempotency
- Transaction isolation
- Rollback
- Standing orders
- Fraud detection
- Fraud holds
- Customer disputes
- RAG-based customer support
- Human approval
- Interest calculation
- Nightly reconciliation
- Audit logging
- Operational error handling

The architecture separates responsibilities between workflow orchestration, financial state, computation, semantic search, and communication.

### Technology Responsibilities

| Technology | Responsibility |
|---|---|
| n8n | Workflow orchestration |
| Supabase | Database platform |
| PostgreSQL | Banking source of truth and ledger |
| Python | Fraud, interest and reconciliation computation |
| Pinecone | Semantic policy/fraud retrieval |
| RAG | Grounded customer support |
| Gmail | Customer and operations communication |

---

# 2. High-Level Architecture

```text
                         CUSTOMER
                            |
                            v
                          GMAIL
                            |
                            v
              +---------------------------+
              | WF-01 Gmail Intake        |
              | & Intent Router           |
              +---------------------------+
                            |
                            v
                    Normalize Email
                            |
                            v
                    Find Customer
                            |
                            v
                   Classify Intent
                            |
                            v
                      Route Intent
                            |
          +-----------------+------------------+
          |                 |                  |
          v                 v                  v
       WF-02             WF-03              WF-04
 Account Management   Atomic Transfer   Standing Orders
          |                 |                  |
          +-----------------+------------------+
                            |
                            v
                 +----------------------+
                 | Supabase/PostgreSQL  |
                 | Banking Source       |
                 | of Truth             |
                 +----------------------+
                            |
          +-----------------+------------------+
          |                 |                  |
          v                 v                  v
       WF-05             WF-06              WF-08
       Fraud          RAG Customer      Disputes/Closure
     Detection          Support
          |                 |                  |
          v                 v                  v
       Python           Pinecone          Approval
       Fraud           + Embeddings        Review
       Service
                            |
                            v
                         WF-07
                    Human Approval
                            |
                            v
                          Gmail


Scheduled workflows:

WF-04 → Standing Orders
WF-07 → Human Approval
WF-09 → Interest
WF-10 → Reconciliation

Operational workflow:

WF-11 → Error Handler
