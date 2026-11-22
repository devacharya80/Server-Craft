# ServerCraft

**Build real backend applications. Practice real engineering.**

ServerCraft is a backend engineering practice platform designed to help developers improve their backend development skills through practical, hands-on challenges.

Developers build complete backend applications directly in a browser-based IDE, submit their implementations, and receive automated feedback through isolated execution environments and hidden HTTP tests.

The focus is on understanding and implementing real backend engineering concepts rather than solving isolated programming exercises.

## What We're Building

- **Practical Challenges:** Build REST APIs, authentication systems, validation layers, caching mechanisms, and other backend features.
- **Browser-Based IDE:** Develop complete multi-file applications using Monaco Editor.
- **Automated Evaluation:** Execute submitted applications inside isolated Docker containers and validate their behaviour using hidden HTTP tests.
- **Asynchronous Processing:** Handle submissions through event-driven communication.
- **Real-Time Feedback:** Receive execution status and test results through WebSockets.
- **Draft Persistence:** Save unfinished work and restore it when returning to a problem.
- **AI Learning Assistance:** Planned contextual hints, code explanations, and improvement suggestions.
- **Progress Tracking:** Planned submission analytics, activity tracking, and performance statistics.

## Technology Stack

| Category | Technologies |
|---|---|
| Frontend | React, Vite, TypeScript |
| Code Editor | Monaco Editor |
| Backend | Node.js, Express, TypeScript |
| AI | Python, FastAPI |
| Database | PostgreSQL |
| Messaging | Apache Kafka |
| Cache | Redis |
| Execution | Docker |
| Authentication | Google OAuth, GitHub OAuth, JWT |
| Real-Time | WebSockets |
| Logging | Pino |
| Observability | OpenTelemetry |
| CI/CD | GitHub Actions |
| Development | Docker Compose |

## Architecture

ServerCraft follows a microservices architecture with independently managed service responsibilities.

| Service | Responsibility |
|---|---|
| API Gateway | Request routing, authentication, rate limiting, WebSockets |
| Auth Service | Authentication and user management |
| Email Service | Event-driven email processing |
| Problem Service | Problem definitions, runtime configurations, hidden tests |
| Submission Service | Submissions, execution results, drafts |
| Runner Service | Docker execution and automated evaluation |
| Frontend | Problem library and browser-based IDE |
| Shared | Common contracts, types, and infrastructure utilities |

Future development will introduce dedicated AI and Analytics services.

## Development Roadmap

<details>
<summary><strong>MVP 1 — Core Platform</strong></summary>

- Public problem library.
- Multi-file browser IDE.
- JavaScript and Express support.
- Google and GitHub OAuth.
- Submission management.
- Kafka-based asynchronous execution.
- Docker-based runner and hidden HTTP tests.
- WebSocket updates.
- Submission history and draft persistence.
- Email event processing.
- Logging and trace instrumentation.

</details>

<details>
<summary><strong>MVP 2 — Intelligence and Analytics</strong></summary>

- AI-powered code analysis and hints.
- Problem-specific AI assistant.
- User profiles and submission statistics.
- Activity heatmaps and daily streaks.
- Analytics Service.
- Personal submission notes.

</details>

<details>
<summary><strong>MVP 3 — Learning Experience and Infrastructure</strong></summary>

- Short video recordings and uploads.
- Runtime and memory comparisons.
- Leaderboards and expanded profile features.
- Additional frameworks and problem categories.
- Advanced observability dashboards.
- Improved cloud deployment.
- Complete public landing page and product demonstration.

</details>

## Project Documentation

For a better understanding of the project and its implementation:

- [Project Overview](docs/overview.md) — Purpose, architecture, technology stack, and long-term vision.
- [MVP 1 Specification](docs/mvp1.md) — Initial scope, service responsibilities, workflows, infrastructure, security, and completion criteria.

## Development Status

<text-placeholder/>

This project is currently in the planning and initial repository setup stage. Development will proceed incrementally, building and testing individual components before connecting them into the complete platform.

## Project Philosophy

**Learn backend engineering by building real backend applications, not just solving isolated coding problems.**