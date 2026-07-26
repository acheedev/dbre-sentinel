# DBRE Sentinel

**An AI-assisted database reliability engineering platform for investigating incidents, explaining root causes, and accelerating safe response.**

> **Status:** Design and architecture phase. This repository defines the project’s mission, operating principles, safety model, and implementation roadmap before the production system is built.

## Why This Exists

Database incidents are rarely caused by one obvious failure. They emerge from interactions across workloads, application behavior, schema changes, resource pressure, jobs, locks, replication, configuration, and human decisions.

The evidence is usually fragmented. The people responding are under pressure. The cost of a wrong action can be far greater than the cost of a slow one.

General-purpose AI can summarize logs and suggest fixes, but production database reliability requires more:

- evidence must be tied to the actual environment
- conclusions must distinguish fact from inference
- permissions must be explicit
- proposed actions must be reversible and reviewable
- uncertainty must be surfaced rather than hidden
- every meaningful decision must be auditable

DBRE Sentinel exists to help engineering teams move from scattered signals to a defensible diagnosis and a safe response plan.

## The Vision

DBRE Sentinel is an AI database reliability engineer that works alongside DBAs, DBREs, SREs, developers, and incident commanders.

It will gather evidence from approved sources, construct a timeline, generate and test hypotheses, explain likely causes, assess risk, and recommend the next safest action.

The system is not designed to replace accountable engineers or autonomously control production databases. It is designed to increase the speed, consistency, and quality of human-led incident response while preserving operational control.

## Who It Is For

DBRE Sentinel is intended for:

- database reliability engineers and DBAs
- site reliability and platform engineering teams
- application teams responsible for database-backed services
- incident commanders coordinating complex outages
- enterprises that need governed AI assistance in sensitive production environments

## Core Capabilities

### Evidence Collection

Collect approved operational evidence such as:

- database health and wait-state metrics
- active sessions, blocking chains, and transaction state
- execution plans and workload changes
- schema, configuration, and deployment history
- alert, job, backup, and replication status
- application errors and relevant infrastructure telemetry

Every observation should retain its source, collection time, and scope.

### Incident Timeline

Build a time-ordered account of:

- when symptoms began
- what changed before and during the incident
- which systems and workloads were affected
- which actions responders already took
- how the environment reacted

The timeline becomes shared operational context rather than another opaque AI summary.

### Hypothesis-Driven Investigation

Generate competing explanations, identify the evidence each would require, and test them systematically.

A useful investigation should show:

- confirmed facts
- working hypotheses
- contradictory evidence
- missing information
- confidence and uncertainty
- the next diagnostic action with the highest expected value

### Root-Cause Explanation

Translate technical evidence into explanations appropriate for different audiences:

- responder-level technical diagnosis
- incident-command summary
- application-team impact explanation
- executive or customer-facing summary

The explanation must remain traceable to evidence and clearly label inference.

### Safe Response Planning

Recommend actions with:

- expected benefit
- operational risk
- prerequisites
- blast radius
- validation steps
- rollback or recovery plan
- required approval level

The safest useful action should be preferred over the most dramatic action.

### Learning and Prevention

Convert incident evidence into durable improvements:

- post-incident timelines
- contributing-factor analysis
- runbook updates
- monitoring gaps
- regression tests
- reliability backlog items
- benchmark scenarios for future AI evaluation

## Operating Modes

DBRE Sentinel will separate observation, recommendation, and execution.

### Observe

Read approved telemetry and database metadata. Produce evidence-backed findings without changing the environment.

### Investigate

Run approved diagnostic queries and tests inside defined boundaries. Record every operation and result.

### Recommend

Produce ranked response options with risk, validation, and rollback guidance. A human remains responsible for execution.

### Approval-Gated Action

Where an organization explicitly enables it, execute narrowly scoped, pre-approved actions through controlled tooling with full auditability and immediate validation.

Autonomous unrestricted production control is not a project goal.

## Safety Model

Safety is part of the architecture, not a warning added to the user interface.

### Least Privilege

The system should receive only the access needed for its current mode and task.

### Evidence Before Action

Recommendations must cite observed evidence and identify assumptions.

### Human Accountability

Material production changes require an accountable human decision unless an organization has explicitly approved a narrowly bounded automated action.

### Reversibility

Changes should have a tested rollback or recovery path whenever possible.

### Blast-Radius Awareness

The system must evaluate affected databases, applications, users, replicas, and dependent services before proposing action.

### Independent Validation

After any action, the system must check whether the expected effect occurred and whether new harm was introduced.

### Complete Audit Trail

Evidence, hypotheses, tool calls, recommendations, approvals, actions, and outcomes must be retained in a reviewable trace.

### Fail Closed

Missing permissions, ambiguous scope, contradictory evidence, or unavailable rollback should stop execution rather than encourage improvisation.

## Trust Model

DBRE Sentinel must earn trust through behavior that can be inspected.

Every material conclusion should answer:

- What do we know?
- How do we know it?
- What are we inferring?
- What evidence contradicts the leading hypothesis?
- How confident are we?
- What information is still missing?
- What is the safest next step?

Confidence without evidence is not reliability.

## Example Incident Domains

Initial investigation playbooks are expected to cover:

1. **Blocking and lock contention**  
   Identify blockers, victims, transaction age, workload origin, and safe intervention options.

2. **Query performance regression**  
   Compare plans, statistics, indexes, bind behavior, workload shifts, and deployment history.

3. **Resource saturation**  
   Investigate CPU, memory, I/O, temp usage, connection pressure, and runaway workloads.

4. **Failed jobs and data pipelines**  
   Trace scheduler failures, dependency breaks, partial processing, retries, and data consistency impact.

5. **Replication and availability degradation**  
   Analyze lag, transport failures, apply errors, recovery state, and failover risk.

6. **Schema and deployment incidents**  
   Correlate migrations, invalid objects, incompatible application changes, and rollback readiness.

7. **Data integrity incidents**  
   Detect corruption symptoms, broken invariants, duplicate processing, reconciliation failures, and scope of impact.

8. **Capacity and growth anomalies**  
   Identify unexpected storage, workload, partition, index, or retention growth before service failure.

## High-Level Architecture

```text
Approved Evidence Sources
          |
          v
Collection and Normalization Layer
          |
          v
Incident Context and Timeline Store
          |
          v
Investigation Orchestrator <----> Policy and Permission Engine
          |                                  |
          v                                  v
Hypothesis and Evidence Graph       Controlled Tool Gateway
          |                                  |
          +----------------+-----------------+
                           v
              Recommendation and Risk Engine
                           |
                           v
             Human Approval / Bounded Action
                           |
                           v
               Validation and Audit Record
```

Planned components:

- **connectors and collectors** — approved database, observability, deployment, and incident-management sources
- **normalization layer** — common evidence model with provenance and timestamps
- **incident context store** — timeline, entities, symptoms, actions, and environment scope
- **investigation orchestrator** — hypothesis generation, evidence requests, and diagnostic sequencing
- **policy engine** — permissions, operating modes, approval requirements, and prohibited actions
- **controlled tool gateway** — audited queries and operational actions with enforced boundaries
- **recommendation engine** — ranked response options with risk and rollback analysis
- **validation engine** — checks expected outcomes and detects unintended effects
- **audit and reporting layer** — complete investigation trace and audience-specific summaries

## Relationship to DB-Agent Bench

[DB-Agent Bench](https://github.com/acheedev/db-agent-bench) is the evaluation layer for the capabilities DBRE Sentinel depends on.

Sentinel should not be trusted because its explanations sound plausible. Its investigation, safety, recovery, and uncertainty behavior should be tested against reproducible database incidents before those capabilities are introduced into sensitive environments.

Incidents and failure patterns discovered through Sentinel can also become sanitized benchmark scenarios, creating a feedback loop between real operational needs and measurable agent improvement.

## Relationship to the Larger Ecosystem

DBRE Sentinel is the operational intelligence layer of a broader platform for trustworthy AI in enterprise data engineering:

- **Seedwright** creates realistic synthetic database environments.
- **DB-Agent Bench** evaluates AI agents against reproducible engineering and incident scenarios.
- **AI SQL Assistant** demonstrates governed interaction with enterprise databases.
- **DBRE Sentinel** applies evaluated capabilities to reliability engineering and incident response.

Together, the projects address the data, evaluation, governance, and operational layers required to move database AI from demonstrations toward defensible enterprise use.

## Guiding Principles

### Facts Before Fluency

A polished explanation is worthless if it is not grounded in current evidence.

### Assist the Operator

The system should strengthen human judgment and coordination, not obscure responsibility.

### Read-Only First

Observation and diagnosis should deliver value before any write or control capability is considered.

### Smallest Safe Action

Prefer targeted, reversible interventions over broad changes.

### No Hidden Autonomy

The system’s permissions, actions, and approval boundaries must be visible and enforceable.

### Uncertainty Is Operational Data

Unknowns and conflicting evidence should be surfaced because they change the risk of action.

### Validate Every Intervention

An action is incomplete until its effect and side effects are checked.

### Learn From Every Incident

Each investigation should improve runbooks, monitoring, testing, and future evaluation.

## Roadmap

### Phase 0 — Operating Model and Safety Specification

- define evidence, incident, hypothesis, recommendation, and action models
- define operating modes and permission boundaries
- define audit and provenance requirements
- define approval, rollback, and validation contracts
- select the first supported database and incident domains

### Phase 1 — Read-Only Investigation Assistant

- collect database evidence from a local or lab environment
- build an incident timeline
- generate evidence-backed hypotheses
- recommend additional diagnostics
- produce technical and incident-command summaries

### Phase 2 — Controlled Diagnostic Orchestration

- run approved diagnostic queries through a tool gateway
- enforce query and resource boundaries
- rank hypotheses using collected evidence
- capture complete investigation traces
- integrate initial DB-Agent Bench incident scenarios

### Phase 3 — Response Planning

- generate ranked mitigation options
- assess risk, prerequisites, and blast radius
- produce validation and rollback procedures
- require explicit human approval for material actions

### Phase 4 — Approval-Gated Operations

- implement narrowly scoped operational actions
- enforce policy and approval workflows
- validate outcomes automatically
- stop and escalate on ambiguity or unexpected effects

### Phase 5 — Enterprise Integration

- observability and incident-management connectors
- multi-database support
- organizational policy packs
- post-incident learning workflows
- deployment, access-control, and audit hardening

## Non-Goals

DBRE Sentinel is not intended to be:

- an unrestricted autonomous database administrator
- a generic chatbot attached to production telemetry
- a replacement for DBAs, DBREs, SREs, or incident commanders
- a system that executes material changes based only on model confidence
- an excuse to bypass change management, access control, or operational ownership
- a guarantee that AI can eliminate database incidents

## What Success Looks Like

DBRE Sentinel succeeds when teams can:

- reduce time spent assembling fragmented incident evidence
- test competing hypotheses systematically
- reach root cause faster without sacrificing rigor
- choose safer and more reversible interventions
- communicate incident state clearly across technical and business teams
- preserve a complete record of why decisions were made
- turn incidents into better runbooks, tests, monitoring, and benchmarks

## Contributing

The project is in its design phase. Early contributions are especially valuable in:

- database incident-response workflows
- DBRE and SRE operating models
- safety and approval architecture
- observability integration
- evidence provenance and audit design
- agent evaluation and failure analysis
- realistic incident scenarios

Contributions should favor inspectable, testable operational behavior over impressive but unverifiable autonomy.

## License

License selection will be finalized before the first implementation release.
