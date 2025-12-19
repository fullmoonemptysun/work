## 🧭 PHASE 1 — Understand the Problem Domain (Foundations)

**Goal:** Understand _what_ you’re building and _why_ before thinking _how_.

1. **Define core stakeholders & roles**
    
    - Who uses the system? (Clinicians, Agency Admins, Schedulers, Super Admins, Patients)
        
    - What are their goals?
        
    - What are their responsibilities?
        
    - What data do they access?
        
2. **Define use cases**
    
    - Start high-level: “A clinician creates a new patient chart.”
        
    - Don’t think about screens or DB tables yet — just _actions_ and _outcomes_.
        
    - Each use case should describe:
        
        - Actor (who)
            
        - Trigger (what starts it)
            
        - System behavior (what it must do)
            
        - Output (what changes or results)
            
3. **Define business rules**
    
    - E.g. “A scheduler cannot see clinical notes,” “A clinician can only access patients in their agency.”
        
    - These become security and RBAC requirements later.
        
4. **Define constraints**
    
    - Regulatory (HIPAA, data locality)
        
    - Availability (99.9% uptime)
        
    - Scalability (multi-tenant)
        
    - Auditability (immutable logs)
        
    - Extensibility (integrations, API-first design)
        

📘 **Deliverable:**  
A _Problem Definition Document_ or “System Requirements Specification” (SRS).  
You can make this in Google Docs, Notion, or Confluence.

---

## 🧩 PHASE 2 — Conceptual Design (The “Whiteboard” Phase)

**Goal:** Visualize how the system behaves, not how it’s built.

This is where you start **drawing**.

1. **System Context Diagram (Level 0 DFD)**
    
    - Show your system as a box.
        
    - Draw external actors around it: Clinician, Agency Admin, SSO Provider, File Storage, Payment Processor.
        
    - Show arrows (data flows).
        
    - Label arrows with what flows (e.g., “Upload PHI”, “Auth token”, “Schedule event”).
        
2. **Use Case Diagram**
    
    - Show relationships between actors and major use cases.
        
    - You’ll see natural groupings (e.g., clinician-centric, scheduler-centric).
        
3. **High-Level Component Diagram**
    
    - Inside your main “system box,” break it into components:
        
        - Auth Service
            
        - Patient Service
            
        - Scheduling Service
            
        - File Storage
            
        - Audit Service
            
        - API Gateway
            
    - Show how they talk to each other.
        

🧠 **Learning goal:** You’re identifying **boundaries** — where responsibilities start and stop.

📘 **Deliverables:** Context Diagram, Use Case Diagram, Component Diagram.  
Use **draw.io** or **Lucidchart** for this step.

---

## ⚙️ PHASE 3 — Logical Design (What the System Knows)

**Goal:** Define what data your system must _know_ and _manage._

This is when the **data model** comes in — **but only after** you understand the actors, flows, and boundaries.

1. **Entity Relationship Diagram (ERD)**
    
    - Identify entities (Patient, Clinician, Agency, Appointment, Chart, File, etc.).
        
    - Define relationships (1-to-N, N-to-M).
        
    - Add key attributes — just names and IDs for now.
        
    - Don’t add all fields yet; focus on structure.
        
2. **Refine relationships**
    
    - Add cardinalities (one-to-many, etc.)
        
    - Mark ownership: which entity “owns” data, which “references” it.
        
    - Define soft vs. hard deletion needs (e.g., “charts can never be deleted”).
        
3. **Map to use cases**
    
    - Every use case must map to one or more entities and relationships.
        
    - If it doesn’t, you’re missing something or have a redundant concept.
        

📘 **Deliverables:** ERD (Conceptual + Logical)  
👉 This is where your _core data model sketch_ lives.

💡 **Key insight:** The ERD is central because it connects everything —  
UI screens, APIs, permissions, and infrastructure all pivot around your data.

---

## 🏗️ PHASE 4 — Physical & Technical Design

**Goal:** Translate your logical model into a real system design.

1. **Database schema design**
    
    - Convert the ERD into real DDL (SQL tables).
        
    - Add keys, indexes, and constraints.
        
    - Apply **multi-tenancy** patterns (tenant_id or schema-per-tenant).
        
2. **Architecture Diagram**
    
    - Combine components, databases, storage, and network zones.
        
    - Show trust boundaries (important for HIPAA).
        
    - Example layers:
        
        - Presentation (React/Next.js)
            
        - API Gateway
            
        - Services (Node/FastAPI)
            
        - Data (Postgres, Redis, S3)
            
        - Security (IAM, KMS, Logging)
            
3. **Sequence Diagrams**
    
    - For each critical flow: login, upload file, create chart, schedule appointment.
        
    - Show actor interactions with services step-by-step.
        

📘 **Deliverables:**  
Physical ERD, Architecture Diagram, Sequence Diagrams, Security Zones Diagram.

---

## 🧱 PHASE 5 — Security & Compliance Layer

**Goal:** Ensure every design decision supports HIPAA & security.

You’ll add **annotations** on your diagrams for:

- Encryption at rest/in-transit
    
- Audit logging points
    
- Data retention boundaries
    
- RBAC boundaries
    
- PHI flows (highlighted in red)
    

📘 **Deliverables:**  
Threat Model, Security Data Flow Diagram, and Compliance Map (linking HIPAA safeguards to design features).

---

## 🧮 PHASE 6 — Implementation Planning

**Goal:** Turn your design into a roadmap.

1. **Break design into epics:**
    
    - Auth & Identity
        
    - Tenancy & RBAC
        
    - Patient Management
        
    - Scheduling
        
    - Charting & File Upload
        
    - Audit & Analytics
        
    - Compliance Layer
        
2. **Prioritize MVP**
    
    - Choose essential flows: login, patient CRUD, chart upload, scheduler view.
        
    - Build smallest viable version across the full vertical slice.
        
3. **Create sprint backlog**
    
    - Each epic → stories → tasks.
        
    - Use Jira or Linear.
        
    - Start writing acceptance criteria per user story.
        

📘 **Deliverables:**  
Project roadmap, backlog, definition of done (DoD), acceptance criteria.

---

## 🔁 PHASE 7 — Validation, Scaling, and Governance

**Goal:** Prove and maintain the system at scale.

- Add monitoring, alerting, audit verification.
    
- Perform compliance audits (HIPAA gap analysis).
    
- Stress test tenancy isolation.
    
- Iterate on architecture for performance and cost.
    

📘 **Deliverables:**  
Ops runbook, monitoring dashboards, compliance checklist, risk register.