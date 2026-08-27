# MicroCRM Full-Stack DevOps

Full-stack CRM application built with **Spring Boot 3.5, Angular 19 and Java 21**.

The project is organized as a monorepo and focuses on **software delivery, quality, security, containerization and observability**.

## ✨ Project Overview

MicroCRM provides a simplified CRM used to manage:

- individuals
- organizations
- application data through a Spring Boot REST API
- a web interface built with Angular

The repository is designed to demonstrate a complete engineering workflow around a Full-Stack application.

## 🛠️ Tech Stack

### Backend
- Java 21
- Spring Boot 3.5
- Gradle
- REST API

### Frontend
- Angular 19
- TypeScript
- Node.js 22

### DevOps & Quality
- Docker / Docker Compose
- GitHub Actions
- SonarCloud
- npm audit
- semantic-release
- GitHub Releases
- GitHub Container Registry (GHCR)
- Conventional Commits

### Observability
- Elasticsearch
- Logstash
- Kibana
- Caddy access logs
- structured Spring Boot logs

## 🏗️ Monorepo Architecture

```text
.
├── front/                  # Angular application
├── back/                   # Spring Boot API
├── elk/                    # ELK configuration and Kibana saved objects
├── misc/docker/            # Caddy / supervisor configuration
├── .github/workflows/      # CI/CD workflows
├── Dockerfile              # Multi-stage build targets
├── docker-compose.yml      # Application orchestration
└── docker-compose-elk.yml  # Optional local observability stack
```

## ⚙️ CI Pipeline

The GitHub Actions CI workflow is triggered on:

- pushes
- pull requests
- scheduled nightly runs
- manual execution

### Backend checks

The backend pipeline includes:

- Gradle tests
- application build
- SonarCloud analysis

### Frontend checks

The frontend pipeline includes:

- dependency installation with `npm ci`
- runtime dependency security gate with `npm audit --omit=dev --audit-level=high`
- Angular unit tests in headless mode
- Angular production build
- SonarCloud analysis

The runtime dependency audit is executed before tests and build in order to **fail fast** on blocking High/Critical vulnerabilities.

### Docker Compose smoke test

Pull requests also run an integration smoke test that:

- builds the stack with Docker Compose
- starts the backend and frontend
- checks that the API responds
- checks that the frontend responds and redirects to HTTPS
- cleans up containers and volumes

This helps detect containerization and orchestration issues before merge.

## 🔐 Security & Quality Gates

The project includes:

- dependency vulnerability checks
- SonarCloud quality analysis
- CI checks on pull requests
- GitHub Secrets for sensitive values
- smoke tests before merge

Secrets such as Sonar tokens and release tokens are stored in GitHub Secrets and are not exposed in repository configuration.

## 🐳 Docker

The root Dockerfile supports multiple build targets.

### Frontend image

```bash
docker build --target front -t orion-microcrm-front:latest .
```

### Backend image

```bash
docker build --target back -t orion-microcrm-back:latest .
```

### Standalone image

A standalone image can package the frontend and backend together:

```bash
docker build --target standalone -t orion-microcrm-standalone:latest .
```

## 🚀 Run with Docker Compose

Start the application:

```bash
docker compose up --build
```

Detached mode:

```bash
docker compose up -d --build
docker compose ps
```

Stop the application:

```bash
docker compose down
```

Main endpoints:

```text
Backend API: http://localhost:8080
Frontend:    https://localhost
```

The frontend redirects HTTP traffic to HTTPS.

## 🧪 Run Tests Locally

### Backend

```bash
cd back
./gradlew clean test
```

### Frontend

```bash
cd front
npm install
npx ng test --watch=false --browsers=ChromeHeadlessNoSandbox
```

Runtime dependency audit:

```bash
npm audit --omit=dev --audit-level=high
```

## 📦 Automated Versioning & Releases

The project uses **semantic-release** with **Conventional Commits**.

Version changes are derived automatically from commit history:

- `fix:` → PATCH
- `feat:` → MINOR
- breaking changes → MAJOR

The release workflow updates:

- Angular package versions
- backend Gradle version
- `CHANGELOG.md`

A version tag then triggers:

- GitHub Release creation
- release artifact publication
- Docker image builds
- image publication to GHCR

## 📈 ELK Observability

An optional local ELK stack is provided for log centralization and analysis.

Start the application with ELK:

```bash
docker compose -f docker-compose.yml -f docker-compose-elk.yml up --build
```

Services:

```text
Elasticsearch: http://localhost:9200
Kibana:        http://localhost:5601
```

### Collected logs

The monitoring stack can centralize:

- structured Spring Boot application logs
- Caddy HTTP access logs

This enables analysis of:

- errors over time
- request volume
- activity peaks
- log trends

Kibana saved objects can be exported and imported to make dashboards reproducible across environments.

## 📌 Project Focus

This repository demonstrates a complete Full-Stack engineering workflow around a Spring Boot and Angular application:

- Full-Stack development
- monorepo organization
- automated testing
- CI/CD with GitHub Actions
- security gates
- SonarCloud quality analysis
- Docker containerization
- pull-request smoke tests
- automated semantic versioning
- GitHub Releases
- GHCR image publishing
- local observability with ELK
