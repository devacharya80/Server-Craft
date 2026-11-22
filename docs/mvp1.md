# ServerCraft — MVP 1 Specification

## 1. Overview

MVP 1 is the first functional version of ServerCraft.

Its primary objective is to establish a complete end-to-end workflow where developers can write backend applications in a browser-based IDE, submit their implementations, execute them inside isolated environments, and receive automated evaluation results.

MVP 1 will initially support plain JavaScript with Node.js and Express.

The initial problem library will contain three problems, with a target of approximately ten by the completion of MVP 1.

Every problem will have a predefined technology stack and runtime configuration. Users will not select their own framework or configure the execution environment.

## 2. Scope and Features

### Included

- Simple home page and public problem library.
- Problem descriptions, API specifications, and starter files.
- Monaco Editor with multi-file project support.
- JavaScript and Express execution environment.
- Google and GitHub OAuth authentication.
- JWT authentication using HTTP-only cookies.
- Verified-email account linking.
- Submission management and history.
- Redis-based per-user submission rate limiting.
- PostgreSQL-backed submission and draft storage.
- Kafka-based asynchronous processing.
- Docker-based application execution.
- Automated hidden HTTP test evaluation.
- WebSocket-based live execution updates.
- One-time WebSocket authentication tickets.
- Console output and individual test results.
- Browser-based draft autosave.
- Email processing through registration events.
- Pino structured logging and OpenTelemetry instrumentation.
- Internal service authentication.
- Basic submission recovery and idempotent event processing.
- GitHub Actions CI.

### Excluded

The following features are intentionally deferred:

- AI-powered code analysis and chatbot.
- Analytics Service and user activity tracking.
- User profiles, streaks, and leaderboards.
- Notes and video recordings.
- Multiple programming languages and frameworks.
- Admin interface for problem creation.
- User-accessible terminal or separate Run button.
- User-installed dependencies.
- Kubernetes deployment.
- Advanced observability dashboards.
- Account deletion workflow.

## 3. Supported Execution Environment

MVP 1 supports the following environment:

| Component | Configuration |
|---|---|
| Language | Plain JavaScript |
| Runtime | Node.js |
| Framework | Express |
| Execution | Docker |
| Communication | HTTP |

Each problem will define a runtime manifest containing its stack identifier, runtime version, framework version, entry file, application port, allowed packages, and execution configuration.

Dependencies will be preinstalled in the runner images. Users will not install packages or execute setup commands manually.

The first three problems will use in-memory data and will not require PostgreSQL, Redis, or external services during execution.

Initial problem categories:

1. In-Memory CRUD API.
2. Request Validation API.
3. Simple Authentication API.

Problems will be created through seed scripts. An administrative problem-authoring interface is not part of MVP 1.

## 4. Application Workflow

The main user journey follows this sequence:

1. **Browse:** Visitors explore the public problem library without authentication.
2. **Open:** Users select a problem and review its requirements, API specifications, constraints, and starter files.
3. **Develop:** Users implement their solution using the browser-based multi-file IDE.
4. **Authenticate:** Authentication is required when submitting. Unfinished code must survive the authentication redirect.
5. **Submit:** The frontend sends project files through the API Gateway. The Submission Service validates and persists the submission.
6. **Queue:** The Submission Service publishes a submission job to Kafka.
7. **Execute:** The Runner Service retrieves the source code and hidden tests through authenticated internal APIs and starts an isolated Docker container.
8. **Evaluate:** The runner executes hidden HTTP tests and collects results, logs, runtime, and memory information where feasible.
9. **Persist:** The Submission Service consumes the final execution result and saves it.
10. **Notify:** After successfully persisting the result, the Submission Service publishes a completion event.
11. **Display:** The API Gateway delivers the final result through WebSockets.

The user can review individual test outcomes, errors, execution status, and submission history.

Refreshing the browser during execution must not lose the submission status. The frontend will retrieve the latest status through HTTP and reconnect to receive subsequent updates.

## 5. Microservices

### API Gateway

**Technology:** Node.js, Express, TypeScript.

Responsibilities:
- Single public entry point for frontend requests.
- Route requests to internal services.
- Validate access tokens.
- Apply Redis-based rate limiting.
- Enforce per-user submission limits.
- Manage WebSocket connections.
- Issue and validate one-time WebSocket tickets.
- Deliver submission status and completion events.
- Protect internal services from public access.

Only the API Gateway will be publicly exposed in the initial architecture.

### Auth Service

**Technology:** Node.js, Express, TypeScript, PostgreSQL.

Responsibilities:
- Google and GitHub OAuth.
- User registration and account management.
- Verified-email identity linking.
- Access and refresh token management.
- Session management and roles.
- Publishing user registration events.

Authentication tokens will be stored in secure HTTP-only cookies, with CSRF protection for state-changing requests.

Password-based authentication and account deletion are excluded from MVP 1.

### Email Service

**Technology:** Node.js worker, Kafka.

Responsibilities:
- Consume user registration events.
- Process welcome emails.
- Handle email delivery failures.
- Expose a minimal health-check endpoint.

During local development, email content can be logged instead of sending actual emails. Amazon SES may be evaluated for cloud deployment.

### Problem Service

**Technology:** Node.js, Express, TypeScript, PostgreSQL.

Responsibilities:
- Store problem descriptions and metadata.
- Manage difficulty levels and tags.
- Store starter files and stack configurations.
- Maintain runtime manifests.
- Manage hidden test suites and versions.
- Expose public problem APIs.
- Provide authenticated internal APIs for the runner.

Hidden tests and private execution configurations must never be exposed through public endpoints.

### Submission Service

**Technology:** Node.js, Express, TypeScript, PostgreSQL, Kafka.

Responsibilities:
- Receive and validate submitted source code.
- Verify problem information and test suite versions.
- Persist submissions and execution results.
- Publish submission jobs.
- Consume runner status and result events.
- Maintain submission lifecycle states.
- Publish completion events after persistence.
- Provide authenticated internal code retrieval.
- Manage submission history and user drafts.
- Enforce submission ownership.

Submission-related data belongs to this service. User activity analytics belong to the future Analytics Service.

### Runner Service

**Technology:** Node.js worker, Docker, Kafka.

Responsibilities:
- Consume submission jobs.
- Retrieve submitted code and hidden tests through authenticated internal APIs.
- Read problem runtime manifests.
- Select the appropriate prebuilt Docker image.
- Start submitted applications.
- Execute hidden HTTP tests.
- Collect test outcomes, logs, and execution metrics.
- Publish status and result events.
- Enforce resource limits and clean up execution containers.

Runner concurrency will initially be limited to one or two jobs.

The runner will execute as a host process during local development and use the local Docker daemon. It will not run as a normal application container with unrestricted Docker socket access.

For the cloud demonstration, it will run on a separate dedicated VM.

### Frontend

**Technology:** React, Vite, TypeScript, Monaco Editor.

Responsibilities:
- Home page and public problem browsing.
- Problem descriptions.
- Multi-file browser IDE.
- File explorer and code editing.
- Draft autosave.
- Google and GitHub authentication.
- Submission interface.
- WebSocket connection management.
- Console output and test result display.
- Submission history.

### Future Services

The Analytics Service and AI Service are planned for MVP 2 and will not be implemented in MVP 1.

## 6. Communication and Event Architecture

The system will use synchronous HTTP communication and asynchronous Kafka messaging.

**HTTP communication:**
- Frontend to API Gateway.
- Gateway to internal services.
- Submission Service to Problem Service.
- Runner Service to Submission Service.
- Runner Service to Problem Service.

Internal service communication must be authenticated.

**Initial Kafka events:**

| Event | Publisher | Consumer |
|---|---|---|
| user.registered | Auth Service | Email Service |
| submission.queued | Submission Service | Runner Service |
| submission.status | Runner Service | Submission Service, Gateway |
| submission.result | Runner Service | Submission Service |
| submission.completed | Submission Service | Gateway |

A future `user.deleted` event is reserved for account deletion and downstream cleanup.

### Submission Lifecycle

The submission lifecycle follows this ownership model:

1. Submission Service persists a submission with `QUEUED` status.
2. Submission Service publishes `submission.queued`.
3. Runner Service consumes the job and begins execution.
4. Runner Service publishes `submission.status` with `RUNNING`.
5. Submission Service persists the running status.
6. Gateway can deliver the temporary running update.
7. Runner Service executes the hidden tests.
8. Runner Service publishes `submission.result`.
9. Submission Service validates and persists the final result.
10. Submission Service publishes `submission.completed`.
11. Gateway delivers the final result to the correct authenticated user.

The final completion notification must only be sent after the result has been persisted.

Every submission event must carry the submission ID and authenticated user ID. The gateway must never trust a browser-supplied user ID when routing results.

### Reliability

The platform will progressively introduce:

- Idempotent event consumers.
- Duplicate message handling.
- Forward-only submission status transitions.
- Retry policies and dead-letter topics.
- Stuck submission detection and recovery.
- Transactional outbox publishing.
- Trace and correlation ID propagation.
- Appropriate Kafka consumer handling for long-running jobs.

Allowed status transitions:

`QUEUED → RUNNING → PASSED / FAILED / ERROR / TIMEOUT`

Late or duplicate events must not overwrite an already finalized submission.

Reliability mechanisms will be implemented incrementally, beginning with a working end-to-end submission flow.

## 7. Database and Infrastructure

### PostgreSQL

One PostgreSQL server will initially be used with separate schemas and database users for individual services.

- Auth Service owns authentication data.
- Problem Service owns problem metadata and hidden tests.
- Submission Service owns submissions, results, and drafts.

Services must not directly access another service's database schema.

Submitted source code will initially be stored in PostgreSQL.

### Kafka

Apache Kafka will handle asynchronous jobs and events.

The local environment will use single-node KRaft mode without ZooKeeper.

Messaging will sit behind a shared abstraction so that a lighter broker, such as Redis Streams or Amazon SQS, can be evaluated for cloud deployment.

### Redis

Redis will initially support gateway rate limiting and per-user submission limits. It may support distributed WebSocket event delivery in a later phase.

It will not be used as the primary persistent storage for submissions or drafts.

### Docker Compose

The default local infrastructure will include:

- PostgreSQL.
- Kafka.
- Redis.

Application services will be started as their implementations become available. The Runner Service will run on the host during local development.

## 8. Security and Execution Isolation

Submitted applications are untrusted code. The runner must enforce strict execution restrictions.

Initial protections include:

- Non-root container execution.
- No privileged containers.
- CPU and memory limits.
- Execution timeouts.
- Process and output size limits.
- Restricted filesystem access.
- No internet access for submitted applications.
- No access to platform databases or internal service networks.
- Container cleanup after execution.

The test harness must be able to reach the submitted application through a controlled internal communication path without exposing hidden tests or platform infrastructure.

Docker is the initial execution technology, not a guarantee of complete isolation. Stronger technologies such as gVisor, Kata Containers, or Firecracker may be evaluated before production workloads.

The Runner Service will be isolated from application and database infrastructure in the cloud deployment.

## 9. Logging and Observability

MVP 1 will establish:

- Pino for structured JSON logging.
- OpenTelemetry instrumentation.
- Trace propagation across HTTP requests.
- Trace context propagation through Kafka messages.
- Trace ID as the correlation identifier.
- Shared observability initialization.

Jaeger, Prometheus, Grafana, and OpenTelemetry Collector are deferred to MVP 3 and will be introduced through an optional Docker Compose profile.

## 10. Development and Deployment

### Local Development

Docker Compose will provide PostgreSQL, Kafka, and Redis.

Application services will be developed and started incrementally. The runner will operate as a host process using the local Docker daemon.

### CI/CD

GitHub Actions will handle:

- Linting.
- Automated tests.
- Type checking.
- Build validation.
- Container image validation where applicable.

Workflow files belong in `.github/workflows/` at the repository root.

The `infra/` directory will contain infrastructure configuration, scripts, database and Kafka configuration, and future deployment files.

### Cloud Demonstration

A cost-conscious AWS deployment will be planned against the available account credits.

The actual credit expiry date must be verified in the AWS console. The current planning estimate is around May 2027 and is not confirmed.

The runner will use a dedicated VM. Budget alerts and resource shutdown procedures must be established before deploying paid infrastructure.

A lighter messaging solution may be considered if Kafka's deployment cost is not justified for the demonstration.

Kubernetes will initially be explored locally using kind or Minikube. Managed cloud Kubernetes is outside MVP 1.

## 11. Completion Criteria

MVP 1 will be considered complete when:

1. Visitors can browse problems and access the editor without authentication.
2. Google and GitHub authentication works with verified-email account linking.
3. Users can write multi-file JavaScript Express applications.
4. Unfinished code survives browser reloads and authentication redirects.
5. Submissions are persisted and processed asynchronously.
6. The runner executes applications inside restricted Docker containers.
7. Hidden HTTP tests correctly evaluate submitted applications.
8. Submission statuses and final results are persisted correctly.
9. WebSockets deliver updates only to the appropriate authenticated user.
10. Duplicate and out-of-order events are handled safely.
11. Stuck submissions can be detected and recovered.
12. Submission history and individual test results are available.
13. Email registration events are processed.
14. Local infrastructure can be started through Docker Compose.
15. GitHub Actions CI passes.
16. Structured logging and trace instrumentation are established.
17. The platform launches with three problems and expands to approximately ten.

## 12. Development Approach

MVP 1 will be implemented incrementally rather than building every service and feature simultaneously.

Each component will first be developed and tested independently. Once its basic functionality is established, it will be connected to the relevant components to build the complete workflow.

The priority is to understand the responsibilities, communication patterns, and failure scenarios of each component while maintaining a working application throughout development.

**MVP 1 Objective:** A developer should be able to build a backend application, submit it, have it executed automatically in an isolated environment, and receive reliable evaluation results after they have been persisted.