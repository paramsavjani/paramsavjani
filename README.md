<div align="center">

<img src="./assets/banner.svg" width="100%" alt="Param Savjani — Backend · Distributed Systems · System Design"/>

<a href="https://portfolio.paramsavjani.in">
  <img src="https://readme-typing-svg.demolab.com/?font=Fira+Code&weight=500&size=20&pause=1200&color=58A6FF&center=true&vCenter=true&width=620&height=45&lines=Backend+engineer+who+designs+for+failure;Kotlin+%C2%B7+Spring+Boot+%C2%B7+PostgreSQL;Idempotent+writes.+Async+workers.+Boring+infra.;Making+p99+latency+boring;Wiring+LLMs+into+production+systems" alt="Typing SVG"/>
</a>

<p>
  <img src="https://komarev.com/ghpvc/?username=ParamSavjani&label=Profile%20views&color=58A6FF&style=flat-square" alt="Profile views"/>
  <a href="https://github.com/ParamSavjani?tab=followers"><img src="https://img.shields.io/github/followers/ParamSavjani?label=Followers&style=flat-square&color=58A6FF" alt="GitHub followers"/></a>
  <a href="https://portfolio.paramsavjani.in"><img src="https://img.shields.io/badge/Portfolio-paramsavjani.in-58A6FF?style=flat-square" alt="Portfolio"/></a>
  <a href="mailto:paramsavjani3010@gmail.com"><img src="https://img.shields.io/badge/Email-say_hi-58A6FF?style=flat-square&logo=gmail&logoColor=white" alt="Email"/></a>
</p>

</div>

## 🧾 `cat ~/.config/param.yaml`

```yaml
apiVersion: engineer.param.dev/v1
kind: BackendEngineer
metadata:
  name: param-savjani
  labels:
    role: backend
    specialty: system-design
    timezone: IST (UTC+05:30)
spec:
  replicas: 1                     # vertically scaled with chai ☕
  languages: [kotlin, java, python, typescript, rust]
  runtime: [spring-boot, node.js]
  storage:
    relational: postgresql        # the source of truth
    document: mongodb
    objects: minio                # s3-compatible
  patterns: [state-machines, async-workers, event-driven, caching]
  observability: [grafana, structured-logs, metrics]
  ai: [openai-api, llm-pipelines]
  designPrinciples:
    - every network call fails eventually; retry with backoff + jitter
    - writes are idempotent, or they are bugs waiting to happen
    - anything with a status column deserves an explicit state machine
    - measure first, then fix the p99, not the average
    - boring technology, interesting problems
status:
  phase: Running
  exploring: [distributed-systems, event-driven-architecture, llm-infra]
  readinessProbe: always-shipping
```

## 📈 Impact

<img src="./assets/metrics.svg" width="100%" alt="Impact: 60% lower query latency at Fleetway, 1,000+ installs and 2,300+ students on Attendance IO, 16-action async OpenAI connector at UnifyApps"/>

## 💼 `git log --oneline career`

| | Where | What I engineered |
|:-:|---|---|
| 🧩 | **UnifyApps**<br/><sub>Intern</sub> | Integrations for a workflow-automation engine: **Microsoft Entra ID connectors** and an **async OpenAI connector** exposing **16 actions**. |
| 🚚 | **Fleetway**<br/><sub>Core Backend Intern · 10 months</sub> | Fuel-allocation algorithms, trip-lifecycle **state machines**, and a **PostgreSQL + MinIO migration** that cut query latency by **60%**. |
| 🤖 | **Quantseer**<br/><sub>AI platform</sub> | An **AI compliance monitoring platform** that scans call-center conversations for regulatory violations using LLM APIs. |

> 📄 Full write-up of roles and projects: **[portfolio.paramsavjani.in](https://portfolio.paramsavjani.in)**

## 🧠 How I Design Systems

The shape most of my backends converge to: boring where it should be, async where it matters, observable everywhere.

```mermaid
flowchart LR
    U(["📱 Clients<br/>web · mobile"]) -->|HTTPS| GW["🛡️ API Gateway<br/>auth · rate limiting"]
    GW --> API["⚙️ Stateless Services<br/>Kotlin · Spring Boot"]
    API <-->|cache-aside| C[("⚡ Cache")]
    API -->|ACID txns| DB[("🐘 PostgreSQL<br/>primary + replicas")]
    API -->|outbox → events| Q[["📨 Message Queue"]]
    Q --> W["🧵 Async Workers<br/>retries · backoff · DLQ"]
    W --> S3[("🗄️ Object Storage<br/>MinIO / S3")]
    W --> AI{{"🤖 LLM APIs"}}
    W -.->|results| DB
    API -.->|metrics · logs| O["📈 Observability<br/>Grafana"]

    classDef ingress stroke:#58a6ff,stroke-width:2px
    classDef compute stroke:#3fb950,stroke-width:2px
    classDef store stroke:#f0883e,stroke-width:2px
    classDef background stroke:#bc8cff,stroke-width:2px
    class U,GW ingress
    class API,O compute
    class C,DB,S3 store
    class Q,W,AI background
```

| Concern | My default approach |
|---|---|
| **Reliability** | Timeouts on every call, retries with exponential backoff + jitter, circuit breakers, dead-letter queues |
| **Correctness** | Idempotency keys on writes, database constraints as the last line of defense, transactional outbox for events |
| **Data** | Normalize first, index for real query patterns, cursor pagination, zero-downtime migrations |
| **Performance** | Measure → profile → fix the p99; cache-aside with explicit TTLs; pooled connections |
| **Lifecycle** | Explicit state machines for anything with a status: trips, jobs, workflows |
| **Async & LLMs** | Slow or flaky work (LLM calls, file processing) goes to queues and workers, with timeouts, structured outputs and cost guardrails |
| **Operability** | Structured logs, RED metrics, dashboards and alerts before the first incident, not after |

<details>
<summary><b>🔁 Bonus: how I model an async job</b> (the same pattern behind trip lifecycles at Fleetway)</summary>
<br/>

```mermaid
stateDiagram-v2
    direction LR
    [*] --> QUEUED
    QUEUED --> RUNNING: worker claims job
    RUNNING --> SUCCEEDED: ok
    RUNNING --> RETRYING: transient error
    RETRYING --> RUNNING: backoff elapsed
    RETRYING --> DEAD_LETTERED: max attempts
    RUNNING --> FAILED: permanent error
    SUCCEEDED --> [*]
    FAILED --> [*]
    DEAD_LETTERED --> [*]
```

Every transition is explicit, persisted and idempotent, so a crashed worker can pick the job back up without double-processing.

</details>

## 🚀 Systems I've Built

<table>
<tr>
<td width="50%" valign="top">

**📲 [Attendance IO](https://github.com/ParamSavjani/Attendance_IO_Backend)**<br/>
Campus attendance platform running in production: auto-generated timetables, one-tap marking, live analytics.<br/>
**1,000+** Play Store installs · **2,300+** students tracked<br/>
`Kotlin` `Spring Boot` `PostgreSQL` `React` `Firebase`<br/>
[Backend](https://github.com/ParamSavjani/Attendance_IO_Backend) · [Frontend](https://github.com/ParamSavjani/Attendance_IO_Frontend)

</td>
<td width="50%" valign="top">

**🎮 Dots & Boxes**<br/>
Cross-platform, real-time multiplayer game with game state synced over WebSockets across web and mobile clients.<br/>
`TypeScript` `WebSockets`<br/>
[Backend](https://github.com/ParamSavjani/Dots-Boxes_Backend) · [Frontend](https://github.com/ParamSavjani/Dots-Boxes_Frontend)

</td>
</tr>
<tr>
<td width="50%" valign="top">

**💰 [Hisaab Kitab](https://github.com/ParamSavjani/HisaabKitab)**<br/>
Real-time expense tracker with bill splitting and friend-wise balance calculation (a ledger problem in disguise).<br/>
`JavaScript` `Node.js` `MongoDB`

</td>
<td width="50%" valign="top">

**🗳️ Decentralized IPL Voting**<br/>
On-chain voting platform with ERC-20 tracking and Merkle-tree claim verification.<br/>
`Solidity` `ERC-20` `Merkle proofs`

</td>
</tr>
<tr>
<td width="50%" valign="top">

**🌐 [Decentralized Reddit](https://github.com/ParamSavjani/Backend_DReddit)**<br/>
Reddit-style community platform on a decentralized backend.<br/>
`Rust` `TypeScript`<br/>
[Backend](https://github.com/ParamSavjani/Backend_DReddit) · [Frontend](https://github.com/ParamSavjani/Reddit-decentralize)

</td>
<td width="50%" valign="top">

**📶 [DAU Wi-Fi Auto-Login](https://github.com/ParamSavjani/daiict-wifi-auto-login)**<br/>
Daemon that handles login/logout on DAU's captive Wi-Fi portal automatically.<br/>
`Python` `systemd`

</td>
</tr>
</table>

<details>
<summary>More repos</summary>
<br/>

| Repo | Stack | Description |
|---|---|---|
| [telehealthcare](https://github.com/ParamSavjani/telehealthcare) | TypeScript | Telehealth appointment and consultation platform |
| [chatting-app-param](https://github.com/ParamSavjani/chatting-app-param) | TypeScript | Real-time chat application |
| [University-Management](https://github.com/ParamSavjani/University-Management) | Java | College management system |
| [Python-ML](https://github.com/ParamSavjani/Python-ML) | Python | Machine learning experiments |
| [Electronics_Hobby_Club](https://github.com/ParamSavjani/Electronics_Hobby_Club) | TypeScript | Site for the college electronics club |

</details>

## 🛠️ Toolbox

| Layer | Tools |
|---|---|
| **Languages** | <img src="https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white"/> <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white"/> <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/> <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white"/> <img src="https://img.shields.io/badge/Rust-000000?style=for-the-badge&logo=rust&logoColor=white"/> |
| **Backend** | <img src="https://img.shields.io/badge/Spring_Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white"/> <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white"/> <img src="https://img.shields.io/badge/WebSockets-010101?style=for-the-badge&logo=socketdotio&logoColor=white"/> <img src="https://img.shields.io/badge/REST_APIs-0D1117?style=for-the-badge&logo=openapiinitiative&logoColor=white"/> |
| **Data** | <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white"/> <img src="https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white"/> <img src="https://img.shields.io/badge/MinIO-C72E49?style=for-the-badge&logo=minio&logoColor=white"/> <img src="https://img.shields.io/badge/Firebase-DD2C00?style=for-the-badge&logo=firebase&logoColor=white"/> |
| **Infra & Ops** | <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white"/> <img src="https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white"/> <img src="https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white"/> <img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black"/> |
| **AI & Integrations** | <img src="https://img.shields.io/badge/OpenAI_API-412991?style=for-the-badge&logo=openai&logoColor=white"/> <img src="https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white"/> |
| **Frontend** | <img src="https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black"/> <img src="https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white"/> <img src="https://img.shields.io/badge/Capacitor-119EFF?style=for-the-badge&logo=capacitor&logoColor=white"/> |
| **Web3** | <img src="https://img.shields.io/badge/Solidity-363636?style=for-the-badge&logo=solidity&logoColor=white"/> <img src="https://img.shields.io/badge/Polkadot_SDK-E6007A?style=for-the-badge&logo=polkadot&logoColor=white"/> |

## 📊 GitHub Activity

<p align="center">
  <img src="https://streak-stats.demolab.com?user=ParamSavjani&background=0D1117&border=30363D&stroke=30363D&ring=58A6FF&fire=58A6FF&currStreakNum=F0F6FC&currStreakLabel=58A6FF&sideNums=F0F6FC&sideLabels=C9D1D9&dates=7D8590&border_radius=12" alt="GitHub streak"/>
</p>

<div align="center">

## 📫 Let's Talk Systems

<a href="mailto:paramsavjani3010@gmail.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/></a>
<a href="https://portfolio.paramsavjani.in"><img src="https://img.shields.io/badge/Portfolio-000000?style=for-the-badge&logo=vercel&logoColor=white" alt="Portfolio"/></a>
<a href="https://github.com/ParamSavjani"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/></a>

<sub>Happy to talk about idempotent APIs, Postgres performance, state machines, or running LLMs in production.</sub>

<br/><br/>

<code>HTTP/1.1 200 OK</code> · <code>Connection: keep-alive</code> · thanks for stopping by 👋

</div>
