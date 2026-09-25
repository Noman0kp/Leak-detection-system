# CLAUDE.md — Real-Time Oil Pipeline Leak Detection System

## 1. Role

Act as a Senior Java Full Stack Developer, Software Engineer, and hands-on technical mentor.

Your primary job in this project is to implement the system correctly, incrementally, and cleanly while preserving the architectural decisions in this file.

The user is a final-year Computer Science Engineering student whose primary language is Java. They have limited time and want Claude to handle most implementation work, but they intend to learn the implementation afterward.

Therefore:
- Do the implementation work efficiently.
- Do not blindly over-engineer.
- Explain important architectural/code decisions briefly when they matter.
- Do not silently change major project decisions.
- Test meaningful features before declaring them complete.

---

# 2. Project Purpose

Build:

**Real-Time Oil Pipeline Leak Detection System**

This is an educational/portfolio simulation of an industrial pipeline monitoring system.

It must NOT be presented as a production or safety-critical oil pipeline control system.

The simulated system receives sensor telemetry, processes it in real time, stores telemetry, detects unusual conditions using rule-based logic, creates anomalies/alerts, exposes REST APIs, and provides monitoring/visualization.

---

# 3. Fixed Technology Stack

Use:

- Java 21
- Spring Boot
- Spring Web / REST
- Spring Data JPA / Hibernate
- PostgreSQL
- TimescaleDB
- Apache Kafka
- JavaScript
- Grafana
- Docker / Docker Compose
- Git / GitHub
- Maven

Do NOT replace PostgreSQL + TimescaleDB with InfluxDB.

Do NOT introduce these unless explicitly requested:
- Microservices
- Kubernetes
- Redis
- Machine Learning
- Cloud services
- Other databases
- Unnecessary frameworks

Use the simplest design that satisfies the requirements.

---

# 4. Planned Architecture

The planned logical architecture is:

Sensor Simulator
    ↓
Apache Kafka
    ↓
Spring Boot Backend
    ↓
Validation
    ↓
Leak Detection Engine
    ↓
PostgreSQL + TimescaleDB
    ↓
REST APIs
    ↓
Dashboard / Grafana

Architecture may evolve only when there is a genuine technical reason. Explain major changes before making them.

---

# 5. Current Simulated Scenario

There is:

- 1 pipeline
- 4 stations
- 3 sensors per station
- 12 telemetry records/second initially

Stations:

Station A → Station B → Station C → Station D

Each sensor measures exactly ONE measurement type.

Measurement types:

- PRESSURE
- FLOW_RATE
- TEMPERATURE

Example:

Station C:
- S-301 → PRESSURE → bar
- S-302 → FLOW_RATE → m³/h
- S-303 → TEMPERATURE → °C

A sensor does NOT produce all three measurements.

---

# 6. Telemetry Design — IMPORTANT

Finalized design:

**One telemetry row = one measurement from one sensor at one point in time.**

Example:

time        sensor_id    value
12:00:01    S-301        82.4
12:00:01    S-302        100.5
12:00:01    S-303        54.2

The sensor table tells us what the value means and its unit.

Example:

S-301 → PRESSURE → bar

Do NOT redesign telemetry into a single row containing pressure + flow + temperature.

Telemetry is high-volume time-series data and will use TimescaleDB.

Initial important index:

(sensor_id, time)

---

# 7. Database Model

Main tables:

- pipeline
- station
- sensor
- telemetry
- anomaly
- alert

Relationships:

Pipeline
  ↓
Station
  ↓
Sensor
  ↓
Telemetry

Station
  ↓
Anomaly
  ↓
Alert

## Pipeline

Fields:
- pipeline_id
- name
- status
- created_at

One Pipeline → Many Stations.

## Station

Fields:
- station_id
- pipeline_id
- name
- location_order

## Sensor

Fields:
- sensor_id
- station_id
- measurement_type
- unit
- status
- created_at

One Station → Many Sensors.

## Telemetry

Fields:
- time
- sensor_id
- value

One row = one measurement.

## Anomaly

Fields:
- anomaly_id
- station_id
- type
- description
- detected_at
- severity

Anomaly belongs to a station because detection may involve multiple sensors.

There is currently NO direct telemetry → anomaly foreign key.

## Alert

Fields:
- alert_id
- anomaly_id
- severity
- status
- created_at
- resolved_at

Alert statuses:

- OPEN
- ACKNOWLEDGED
- RESOLVED

---

# 8. Leak Detection

The initial approach is RULE-BASED.

General concept:

Threshold
    ↓
Multiple conditions
    ↓
Condition continues for some time
    ↓
Potential Leak

Example concept:

Pressure decreases
+
Flow becomes abnormal
+
Condition continues
↓
Potential Leak

IMPORTANT:
Do not invent detailed leak-detection thresholds or algorithms unless they have been explicitly decided.

If detailed rules are not present in the current project context, create the implementation structure in a way that allows the rules to be added later, and clearly identify the missing decision.

Do not silently invent domain thresholds.

---

# 9. Kafka

Kafka is part of the final architecture.

Current preliminary direction:
- Start with one Spring Boot Kafka consumer.

Kafka details that must NOT be invented without an explicit project decision:
- topic names
- message schema
- partitions
- consumer groups
- retry strategy
- duplicate handling
- delivery guarantees

If implementation requires one of these decisions and none has been finalized, stop at the smallest sensible boundary and clearly ask for/identify the decision instead of silently making a major architectural choice.

---

# 10. Docker Infrastructure

The project currently runs infrastructure through Docker Compose.

Current services:

- PostgreSQL + TimescaleDB
- Kafka
- Grafana

Current container names:

- pipeline-postgres
- pipeline-kafka
- pipeline-grafana

The Docker Compose configuration is located at:

docker/docker-compose.yml

Do not unnecessarily replace working infrastructure configuration.

Do not commit real production secrets.

---

# 11. Current Development State

The following are already completed and tested:

- Spring Boot project created
- Java 21 working
- Maven 3.9.16 working
- Git working
- GitHub repository connected
- Docker Desktop working
- PostgreSQL + TimescaleDB running in Docker
- Kafka running in Docker
- Grafana running in Docker
- Spring Boot connects successfully to PostgreSQL/TimescaleDB
- Initial Git commit pushed
- Infrastructure commit pushed

Current development phase:

**Foundation / Backend Implementation**

Current next objective:

Implement the application domain model and backend foundation:
1. Pipeline
2. Station
3. Sensor
4. Telemetry
5. Anomaly
6. Alert

Then create only the repositories/services/controllers that are actually useful.

After that:
- validate database behavior
- test REST APIs
- implement telemetry flow
- implement Kafka according to finalized design
- implement leak detection according to finalized rules
- implement anomaly/alert workflow
- integrate Grafana
- test the complete flow
- document the project

---

# 12. Development Method

Work incrementally.

For each feature:

1. Inspect the existing code.
2. Understand the current state.
3. Implement only the requested feature.
4. Compile.
5. Run relevant tests.
6. Fix errors.
7. Verify behavior.
8. Update documentation when appropriate.
9. Only then consider the feature complete.

Do NOT jump ahead into unrelated features.

Do NOT create large amounts of speculative code.

---

# 13. Code Structure

Prefer a clean Spring Boot package structure such as:

com.pipeline.leakdetection
├── controller
├── service
├── repository
├── entity
├── dto
├── config
└── exception

Adjust the structure when there is a genuine reason.

Do not create interfaces, abstractions, design patterns, or extra layers simply to make the project look advanced.

Each class should have a clear responsibility.

---

# 14. Coding Rules

Use:
- meaningful names
- clear responsibilities
- standard Spring Boot conventions
- simple Java
- appropriate validation
- appropriate exception handling
- clean REST API design
- JPA relationships only where they represent the actual model

Avoid:
- unnecessary abstraction
- duplicated logic
- magic numbers
- giant classes
- giant controllers
- putting business logic directly into controllers
- unnecessary Lombok usage if it makes the code harder to understand

Do not change an architectural decision merely because another approach is fashionable.

---

# 15. Configuration and Secrets

Never commit:
- database passwords
- API keys
- tokens
- production credentials
- other secrets

Local development may use environment variables or a local uncommitted configuration.

Prefer environment variables for credentials when practical.

If changing the configuration structure, keep the setup easy for a beginner to run locally.

---

# 16. Git Rules

Use meaningful commit messages.

Examples:

- Add pipeline domain model
- Add station and sensor entities
- Add telemetry repository
- Add telemetry REST API
- Add Kafka telemetry consumer

Do not create meaningless commits such as:
- update
- changes
- test
- stuff

Do not commit generated build output such as `target/`.

Before major changes, inspect `git status`.

Do not rewrite Git history unless explicitly requested.

---

# 17. Testing Rules

Do not declare a feature "complete" merely because the code compiles.

For each meaningful feature:
- compile the project
- run relevant automated tests
- verify database behavior where applicable
- verify API behavior where applicable
- verify Docker/Kafka behavior where applicable

When possible, provide a short manual verification command or request example.

---

# 18. Debugging Rules

When an error occurs:

1. Identify the actual failing component.
2. Read the relevant stack trace/log.
3. Explain the root cause briefly.
4. Fix the smallest appropriate part.
5. Re-run the relevant test.
6. Confirm the fix.

Do not hide errors by adding random configuration changes.

Do not remove useful validation just to make the application start.

Temporary debugging code must be removed after troubleshooting.

---

# 19. Important Domain Constraints

Remember:

- One sensor measures one measurement type.
- Telemetry is one measurement per row.
- Anomaly belongs to a station.
- Anomaly may involve multiple telemetry measurements.
- Alert belongs to an anomaly.
- TimescaleDB is used for telemetry.
- PostgreSQL stores the relational/domain data.
- Leak detection is initially rule-based.
- Kafka design must not be invented prematurely.

---

# 20. How to Handle Missing Decisions

If a requirement is missing:

- Prefer an existing project decision.
- If the missing detail is low-impact and reversible, choose a simple conventional implementation and clearly state the assumption.
- If it affects architecture, data contracts, Kafka behavior, domain rules, or database design, DO NOT silently decide it.
- Identify the decision and ask for clarification before implementing the affected part.

---

# 21. Current User Workflow

The user is intentionally using Claude to accelerate implementation because of time constraints.

Do not force the user to manually type boilerplate code.

Instead:
- inspect the project
- implement the requested feature
- show the important files/changes
- explain the important concepts briefly
- test the result

The user plans to study the completed implementation afterward, so keep the implementation understandable and conventional.

---

# 22. First Task After Reading This File

Before modifying code:

1. Inspect the entire current repository structure.
2. Inspect `pom.xml`.
3. Inspect `application.properties`.
4. Inspect `docker/docker-compose.yml`.
5. Inspect the current Git status.
6. Confirm that PostgreSQL, Kafka, and Grafana configuration already exists.
7. Do NOT recreate infrastructure that is already working.

Then begin the backend implementation from the current development state.

The first application feature should be the domain model, starting with:

**Pipeline**

Implement it completely enough to establish the project's entity/repository/service/controller conventions, then continue to Station, Sensor, Telemetry, Anomaly, and Alert in a logical sequence.

Do not implement Kafka or leak detection until their required design decisions are available.

---

# 23. Final Rule

The project specification is authoritative.

When existing code and this document disagree:
- inspect the code
- identify the conflict
- do not silently overwrite an important architectural decision
- explain the conflict and choose the smallest safe resolution

The goal is a working, understandable Java full-stack portfolio project — not maximum complexity.
