# ServerCraft — Project Overview

## 1. Introduction

ServerCraft is a backend engineering practice platform where developers improve their skills by building complete backend applications through practical challenges.

Instead of focusing on isolated programming exercises, the platform focuses on real-world backend development concepts such as REST APIs, authentication, authorization, database operations, caching, request validation, and asynchronous processing.

Developers write their solutions directly in a browser-based IDE using a multi-file code editor. Submitted applications are executed inside isolated environments and evaluated automatically using predefined HTTP test cases.

The goal is to provide a practical environment where developers learn backend engineering by building and testing actual applications.

## 2. Core Capabilities

- **Problem Library:** Browse practical backend engineering challenges with requirements, API specifications, and starter files.
- **Browser-Based IDE:** Write complete multi-file backend applications using Monaco Editor.
- **Automated Evaluation:** Execute submitted applications in isolated Docker containers and validate their behaviour using hidden HTTP tests.
- **Submission Management:** Track submissions, execution statuses, test results, and submission history.
- **Authentication:** Support Google and GitHub OAuth with secure session management.
- **Real-Time Updates:** Deliver execution status and results through WebSockets.
- **Draft Persistence:** Automatically save unfinished work locally and synchronize authenticated users' drafts with the backend.
- **AI Learning Assistance:** Planned code explanations, contextual hints, and improvement suggestions.
- **Progress Tracking:** Planned submission analytics, activity tracking, streaks, and leaderboards.

## 3. Architecture

ServerCraft follows a microservices architecture with clearly separated responsibilities.

The initial architecture consists of:

| Component | Responsibility |
|---|---|
| API Gateway | Request routing, authentication validation, rate limiting, and WebSocket communication |
| Auth Service | User authentication, OAuth integration, and account management |
| Email Service | Asynchronous email processing |
| Problem Service | Problem metadata, starter files, runtime configurations, and hidden tests |
| Submission Service | Submission lifecycle, source code, results, and drafts |
| Runner Service | Isolated application execution and automated evaluation |
| Frontend | User interface, problem browsing, and browser-based IDE |
| Shared | Event contracts, common types, messaging abstractions, and infrastructure utilities |

Future phases will introduce an AI Service and Analytics Service.

Services communicate through HTTP for synchronous operations and asynchronous messaging for background processing.

## 4. Technology Stack

| Area | Technology |
|---|---|
| Frontend | React, Vite, TypeScript |
| Code Editor | Monaco Editor |
| Backend | Node.js, Express, TypeScript |
| AI Service | Python, FastAPI |
| Database | PostgreSQL |
| Message Broker | Apache Kafka initially |
| Cache and Rate Limiting | Redis |
| Code Execution | Docker |
| Authentication | Google OAuth, GitHub OAuth, JWT |
| Real-Time Communication | WebSockets |
| Logging | Pino |
| Instrumentation | OpenTelemetry |
| CI/CD | GitHub Actions |
| Local Infrastructure | Docker Compose |

The messaging layer will use a shared abstraction to allow the underlying broker to be replaced when necessary.

## 5. Development Roadmap

### MVP 1 — Core Platform

Establish the complete problem-solving and submission workflow.

- Public problem library.
- Multi-file browser IDE.
- JavaScript and Express support.
- Google and GitHub authentication.
- Asynchronous submission processing.
- Docker-based execution and hidden HTTP testing.
- Real-time submission updates.
- Submission history and draft persistence.
- Email event processing.
- Structured logging and trace instrumentation.

### MVP 2 — Intelligence and Analytics

Introduce learning assistance and progress tracking.

- AI-powered code analysis and contextual hints.
- Problem-specific AI assistant.
- User profiles and submission statistics.
- Activity heatmaps and daily streaks.
- Analytics Service.
- Personal notes attached to submissions.

### MVP 3 — Learning Experience and Infrastructure

Expand the learning experience and improve operational visibility.

- Short video recordings and uploads.
- Runtime and memory comparisons.
- Leaderboards and additional profile features.
- More frameworks and problem categories.
- Prometheus, Grafana, Jaeger, and OpenTelemetry Collector.
- Improved cloud deployment and infrastructure.
- Complete public landing page and product demonstration.

## 6. Long-Term Vision

The long-term objective is to build a comprehensive platform for practical backend engineering education.

Future development will expand programming language and framework support, introduce advanced distributed systems challenges, and provide personalized learning experiences through AI-powered assistance and performance analytics.

**Core Philosophy:** Learn backend engineering by building real backend applications, not just solving isolated coding problems.