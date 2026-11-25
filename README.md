<h1 align="center">
  <samp>Martial A. C. AHADJI</samp>
</h1>

<p align="center">
  <samp>Software Architect · Tech Lead · Data Systems · Founder of CleanSoft Solutions</samp>
</p>

<p align="center">
  <i>From C++ memory management to multi-tenant SaaS: I design whole systems, write the code, and ship them to real users in West Africa.</i>
</p>

<p align="center">
  <a href="https://www.linkedin.com/in/marsahadji"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white" alt="LinkedIn" /></a>
  <a href="https://substack.com/@marsahadji"><img src="https://img.shields.io/badge/Substack-FF6719?style=flat-square&logo=substack&logoColor=white" alt="Substack" /></a>
  <a href="https://twitter.com/marsahadji"><img src="https://img.shields.io/badge/X-000000?style=flat-square&logo=x&logoColor=white" alt="X" /></a>
  <a href="mailto:mars@soltg.com"><img src="https://img.shields.io/badge/mars@soltg.com-D14836?style=flat-square&logo=gmail&logoColor=white" alt="Email" /></a>
  <img src="https://img.shields.io/badge/Lomé,_Togo-🇹🇬-2E7D32?style=flat-square" alt="Lomé, Togo" />
</p>

---

## 🧭 Who I am, in one paragraph

I started in **C/C++ and Qt**, writing desktop software where every byte of memory mattered. Since then I've built and run production systems for distributors, notaries, real-estate platforms, schools and associations in Togo and the region. I've led a team of 21 and migrated a live platform from SQL to MongoDB without losing a single record. These days I hold **50+ MongoDB certifications**, build on **Next.js 16 / React 19 / TypeScript**, drive coding agents with my own prompt framework, and write about **open data and African digital sovereignty**.

This page shows how I think, how I build, and how I draw systems before I code them.

---

## 🧠 How I think

| Principle | What it means in practice |
|---|---|
| **The data model comes first** | I design schemas, relations and aggregation paths before any UI. Most bugs I've fixed in other people's code started as modeling mistakes. |
| **Build for the real environment** | Power cuts and unstable networks are normal here. My systems keep working in degraded or offline mode and sync once the connection is back. |
| **Server-first, type-safe** | Server Components by default. Server Actions for internal mutations. API routes only for webhooks and third parties. Zod at every boundary. |
| **Boring beats clever** | Singleton DB connections, explicit enums, predictable folder layouts. The next developer should understand the code in minutes. |
| **Go on site** | Before I write a spec, I sit with the operators who will use the system: depot managers, notary clerks, accountants. |

---

## 🛰️ How I work with AI agents: the "Control Room"

I don't "vibe code". I run coding agents like a small engineering team, with an architect, written rules and small checked deliveries. The method is published as a reusable prompt library in [**templatex**](https://github.com/marsahadji/templatex).

```mermaid
flowchart LR
    A["🧑🏾‍💻 Architect chat<br/>(me + planning AI)"] -->|"small YAML prompt<br/>≤ 7 tasks"| T["task.md"]
    subgraph CR["📁 Control files (kept lean)"]
        R["rules.md<br/>strict minimum"]
        S["ai_summary.md<br/>project state"]
        C["checkpoint.md<br/>≤ 15 lines"]
    end
    T --> AG["🤖 Coding agent"]
    R --> AG
    S --> AG
    AG -->|"report + diff"| RV{"Review"}
    RV -->|"approved"| M["✅ Merge"]
    RV -->|"fix needed"| A
    M --> C
    C --> A
```

**Rules I enforce:** one purpose per prompt, outline approved before the full prompt, the agent reads only what it needs, no tests that add nothing. This keeps token use low and makes the output predictable.

---

## 🏗️ Systems I've designed

### 1. NotarIs: a multi-tenant workflow SaaS for notary offices
`Next.js 16` `React 19` `TypeScript` `MongoDB/Mongoose` `NextAuth v5` `Zod` `TanStack Table` `Recharts` `Tailwind 4`

Notary offices run long procedures such as property sales or company creation. Each one is a chain of documents, payments, signatures and forms. I modeled it as a **template-driven workflow engine**: an office defines a procedure once, and every new case is created from that template.

```mermaid
flowchart TB
    subgraph APP["Next.js App Router, split by route groups"]
        MK["(marketing)<br/>public site"]
        AU["(auth)<br/>login"]
        DB["(dashboard)<br/>tenant workspace"]
        AD["admin<br/>platform owner"]
    end
    PX["proxy.ts<br/>NextAuth session guard"] --> AU & DB & AD
    DB --> SA["Server Actions"]
    AD --> API["Route handlers<br/>/api/admin/*"]
    SA & API --> ORM["Mongoose models"]
    ORM --> M[("MongoDB")]
    subgraph MODELS["Domain"]
        O["Organization"] --- U["User · roles[]"]
        O --- TPL["CaseTemplate"]
        TPL -->|"instantiates"| CA["Case + WorkflowState"]
        CA --- TK["Task"]
        CA --- ACT["CaseActivity<br/>audit timeline"]
        O --- SUB["Subscription / Invoice"]
    end
    ORM -.-> MODELS
```

Each step has a **type** (`DOCUMENT · PAYMENT · SIGNATURE · FORM · INFO`), a config inherited from the template, its own runtime data, and a `isCritical` flag that blocks the next steps until it's done:

```mermaid
stateDiagram-v2
    [*] --> PENDING
    PENDING --> IN_PROGRESS: clerk starts step
    IN_PROGRESS --> REVIEW: proof uploaded (doc, payment, signature)
    REVIEW --> COMPLETED: validated
    REVIEW --> REJECTED: invalid proof
    REJECTED --> IN_PROGRESS: resubmit
    COMPLETED --> [*]
    note right of REVIEW: a critical step blocks<br/>the rest of the case
```

The platform also has session-based multi-tenancy, a super-admin who can **impersonate** a tenant (with a visible banner), team invitations by email, and a system log.

---

### 2. AfricaHome / TogoHome: a regional PropTech ecosystem (2019 → today)
`V1 → V4` `SQL → MongoDB migration` `3 countries` `4 brands` `21-person team`

I've been CTO since 2019. I took the platform from V1 to V4, moved it off a crashing legacy SQL database to MongoDB **with zero data loss**, and turned it into one shared core that serves several brands.

```mermaid
flowchart LR
    subgraph CORE["Shared core (centralized backend)"]
        BL["Business logic<br/>isolated from clients"]
        DATA[("MongoDB")]
        BL --- DATA
    end
    subgraph BRANDS["Brands: Togo · Côte d'Ivoire · Burkina Faso"]
        TH["TogoHome"]
        AH["AfricaHome"]
        TW["Twins Digital"]
        AB["AirBM"]
    end
    subgraph MODS["Modules"]
        MP["Trust marketplace<br/>1,000+ certified listings"]
        PM["Property management"]
        PT["Construction / project tracking"]
        AI["AI assistants"]
        WA["WhatsApp API integration"]
    end
    BRANDS --> CORE
    CORE --> MODS
    NAT["📱 Lightweight native apps"] --> CORE
```

---

### 3. TogoBoissons: an ERP for a drinks distribution depot
`Offline-tolerant` `Inventory` `Delivery routes` `Real-time accounting` `1,500+ sales in one month`

This runs a real depot: stock, delivery rounds, cash and accounting. The constraint that shaped everything is that sales must continue during a power or network cut.

```mermaid
flowchart LR
    POS["🧾 Point of sale<br/>& depot staff"] --> Q["Local operations<br/>(degraded mode)"]
    Q -->|"sync when online"| API["Backend"]
    API --> INV["Inventory"]
    API --> RT["Delivery routes"]
    API --> ACC["Accounting"]
    INV & RT & ACC --> DB[("MongoDB")]
    DB --> DASH["📊 Manager dashboard"]
```

Also shipped: **FactuPro** (B2B invoicing, 300+ invoices a month in production), **NecroTogo** (B2C payments and donations with a vendor marketplace), **LYC Informatique V2** (e-commerce with WhatsApp checkout, OTP and B2B proformas), plus a school-management app and a writers' association platform.

---

### 4. From the metal up: my C++ / Qt toolkit
[`cpp-qt-css_library`](https://github.com/marsahadji/cpp-qt-css_library) · `C++` `Qt5` `qmake subdirs` `shared libraries (.dll / .a)`

Before the web, I built desktop business software in Qt. To avoid rewriting the same plumbing for every client, I split it into **separately compiled shared libraries** that every project linked against:

```mermaid
flowchart BT
    CORE["CssCore<br/>Qt core includes, files, crypto, processes"]
    SQL["CssSql<br/>DataConnect · MasterCrud"]
    GUI["CssGui<br/>delegates, dialogs, models"]
    NET["CssNetwork<br/>IP / subnet calculations"]
    SPACE["CssSpace<br/>settings, validation, FCFA formatting, audit trail"]
    CORE --> SQL
    CORE --> SPACE
    SQL --> SPACE
    GUI --> SPACE
    PRINT["CssPrint"] & CHARTS["CssCharts"] & MM["CssMultimedia"] & MATHS["CssMaths"]
```

A small generic CRUD layer over `QSqlQuery`, so business code never builds SQL strings by hand:

```cpp
namespace CssSql {
struct crudElt { QString field; QVariant value; };

class MasterCrud {
public:
    explicit MasterCrud(QString table);
    QSqlQuery readValue(crudElt e);
    bool insertValue(QList<crudElt> l);
    bool updateValue(QList<crudElt> l);
    bool deleteValue(crudElt e);
    static bool checkPresence(QString t, crudElt e);
};
}
```

It also has regex validators for local phone numbers, money formatting in FCFA, and an `actionWriter()` that logs every user action to the database. I was building audit trails long before NotarIs.

---

## 🗄️ Data is my home ground

- **MongoDB**: 50+ official certifications covering schema design, aggregation pipelines, indexing, replication, sharding, cluster performance, Vector Search & RAG
- **SQL**: PostgreSQL, MariaDB/MySQL, SQLite, SQL Server, Oracle (legacy). including a live SQL → MongoDB migration with zero data loss
- **Open data**: alumnus of the CAFDO Open Data Champions Network; certified training "Data and Innovation for Development" (2025)
- **Self-hosting**: my own VPS running **Coolify**, which hosts several production sites with external MongoDB and S3 storage

---

## 🛠️ Toolbox

**Today's stack**

<p>
  <img src="https://img.shields.io/badge/Next.js_16-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" />
  <img src="https://img.shields.io/badge/React_19-20232A?style=for-the-badge&logo=react&logoColor=61DAFB" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Tailwind_4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Mongoose-880000?style=for-the-badge&logo=mongoose&logoColor=white" />
  <img src="https://img.shields.io/badge/Auth.js-7C3AED?style=for-the-badge&logo=auth0&logoColor=white" />
  <img src="https://img.shields.io/badge/Zod-3E67B1?style=for-the-badge&logo=zod&logoColor=white" />
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" />
  <img src="https://img.shields.io/badge/pnpm-F69220?style=for-the-badge&logo=pnpm&logoColor=white" />
</p>

**Systems & roots**

<p>
  <img src="https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white" />
  <img src="https://img.shields.io/badge/C-A8B9CC?style=for-the-badge&logo=c&logoColor=black" />
  <img src="https://img.shields.io/badge/Qt-41CD52?style=for-the-badge&logo=qt&logoColor=white" />
  <img src="https://img.shields.io/badge/WebAssembly-654FF0?style=for-the-badge&logo=webassembly&logoColor=white" />
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/PHP-777BB4?style=for-the-badge&logo=php&logoColor=white" />
  <img src="https://img.shields.io/badge/C%23-512BD4?style=for-the-badge&logo=dotnet&logoColor=white" />
  <img src="https://img.shields.io/badge/Ruby-CC342D?style=for-the-badge&logo=ruby&logoColor=white" />
</p>

**Data & infrastructure**

<p>
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white" />
  <img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/Coolify-6B16ED?style=for-the-badge&logoColor=white" />
  <img src="https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white" />
  <img src="https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black" />
</p>

---

## 🗺️ The road so far

```mermaid
timeline
    2014 : DUT Computer Engineering (ELATSA)
         : Software, networks LAN/WAN
    2016 : Independent systems developer
         : C/C++ & Qt desktop business software
    2019 : CTO of TogoHome
         : Independent architect & consultant
    2020 : Technical writer & data trainer
    2022 : Founded CleanSoft Solutions
    2025 : CAFDO Open Data Champion
         : AI community events (Neuractif)
    2026 : NotarIs, TogoBoissons, AfricaHome V4
         : 50+ MongoDB certifications
```

---

## ✍️ Beyond code

- 📝 **Writing**: essays on open data, data governance and African digital sovereignty on [Substack](https://substack.com/@marsahadji). Author of *Au-delà du CFE*, a practical guide to OHADA business law (2026).
- 🎓 **Teaching**: I design data-analysis training programs and mentor junior developers and interns.
- 🎤 **Community**: organizer and panelist at AI events in Lomé.
- 🗣️ **Languages**: French & Ewe (native), English (C1/C2).

---

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=marsahadji&show_icons=true&count_private=true&hide_border=true&theme=transparent" height="160" alt="GitHub stats" />
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=marsahadji&layout=compact&hide_border=true&theme=transparent" height="160" alt="Top languages" />
</p>

<p align="center"><samp>Most of my production work lives in private client repositories. Ask me for a walkthrough.</samp></p>
