# CI & CD with Practices

![CI](https://github.com/oghuzkhandev/learn-ci_cd/actions/workflows/ci.yml/badge.svg)

This repository contains a production-style backend application written in Go, designed to demonstrate **real-world CI/CD practices**, **database migrations**, and **secure cloud deployment**.

The project is based on Boot.dev’s Notely application and extended to include modern DevOps workflows using GitHub Actions, Google Cloud Run, Turso (libSQL), Goose migrations, and secret management best practices.

---

## 🚀 Project Overview

It is intentionally structured to reflect how real production systems are built, deployed, and maintained. It covers the full lifecycle of a backend service, including:

- **Continuous Integration (CI):** Automated validation via GitHub Actions.
- **Continuous Deployment (CD):** Automated delivery to Google Cloud.
- **Infrastructure:** Containerized deployment using Docker and Google Cloud Run.
- **Database:** Serverless SQLite-compatible database using Turso (libSQL).
- **Security:** Secret management using Google Cloud Secret Manager and IAM roles.

---

## 🏗 Architecture & Flow

The deployment pipeline ensures that code moves safely from a local environment to the cloud:

1. **Push:** Developer pushes code or opens a Pull Request.
2. **CI Pipeline:** Code is formatted, linted, and tested.
3. **CD Pipeline:** Triggered on merge to `main`.
4. **Build:** Docker image is built via Google Cloud Build and stored in Artifact Registry.
5. **Migrate:** Database migrations are applied using Goose.
6. **Deploy:** The container is deployed to Google Cloud Run as a new immutable revision.

---

## 🛠 Tech Stack

| Category | Technology |
| :--- | :--- |
| **Language** | Go (1.22+) |
| **CI/CD** | GitHub Actions |
| **Cloud Platform** | Google Cloud Platform (GCP) |
| **Containerization** | Docker / Google Cloud Build |
| **Registry** | Google Artifact Registry |
| **Database** | Turso (libSQL) |
| **Migrations** | Goose |
| **Secrets** | Google Cloud Secret Manager |

---

## 🔄 Workflow Details

### 1. Continuous Integration (CI)
Runs on every push and pull request to ensure code quality:
* **Go Environment:** Sets up a consistent Go workspace.
* **Format & Lint:** Runs `go fmt` and static analysis to catch idiomatic errors.
* **Testing:** Executes unit tests and measures coverage to ensure logic integrity.

### 2. Continuous Deployment (CD)
Triggered automatically on merges to the `main` branch:
* **Production Build:** Creates a deterministic Go binary via `buildprod.sh`.
* **GCP Auth:** Authenticates using a service account with minimal required permissions.
* **Database Migrations:** Before the new code goes live, Goose executes pending migrations to `sql/schema/` to ensure the database schema matches the new application version.
* **Service Deployment:** Updates the Cloud Run service to point to the newly built image.

---

## 🔒 Secret Management

This project follows the **Principle of Least Privilege**:
- Sensitive values like `DATABASE_URL` are stored in **Google Cloud Secret Manager**.
- Secrets are injected into the Cloud Run environment at runtime.
- **IAM** policies restrict access so only the deployment service account can read specific secrets.

---

## 💾 Database Migrations

Migrations are versioned and committed under `sql/schema/`. This ensures the database schema is reproducible across all environments: local, CI, and production.

```bash
# Example migration command used in the pipeline
goose turso $DATABASE_URL up
