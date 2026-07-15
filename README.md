# Foldr

A full-stack document management and search platform, currently in early development.

Foldr aims to let authenticated users upload documents, organize them, and retrieve them through full-text search over their contents. It is being built as a REST API backed by MySQL, with a React single-page application as the client.

The project is organized around three core concerns: an authentication and authorization boundary, a file storage and streaming pipeline, and an indexed full-text search layer. The details below describe the intended direction of the project. They are subject to change as development progresses.

## Status

Early development. The initial focus is on project scaffolding, database setup, and the authentication layer. Features described in this document are planned or in progress rather than complete, and the design may evolve.

## Goals

- Stateless, token-based authentication.
- Per-user ownership and authorization of documents.
- Multipart file upload with validation and size limits.
- Streaming download of stored documents.
- Organization of documents into folders or categories.
- Full-text search over extracted document contents, with filtering.
- Asynchronous text extraction from common document formats, so uploads are not blocked by parsing.
- Paginated listing endpoints.
- A documented API contract via OpenAPI.

## Intended Architecture

Foldr is planned as a two-tier application. A Spring Boot service will expose a REST API and own persistence, storage, and search logic. A React client will consume that API.

The backend is intended to be stateless, carrying identity on each request as a signed token validated before requests reach application logic. Document bytes are intended to be written through a storage abstraction rather than directly from controller code, so the storage backend can be changed later without rewriting upload and download logic. Text extraction is intended to run asynchronously after upload, with extracted content indexed for retrieval.

This describes the target shape of the system. The concrete design will be settled as the corresponding parts are built.

## Technology Stack

**Backend**

- Java with Spring Boot
- Spring Web, Spring Data JPA, Spring Security, Spring Validation
- JWT for authentication
- Apache Tika for document text extraction
- Flyway for database migrations

**Frontend**

- React

**Database**

- MySQL 8.4

**Tooling**

- Maven
- Docker Compose for the local database

The exact set of dependencies will grow as features are added.

## Getting Started

### Prerequisites

- JDK (version as declared in `pom.xml`)
- Maven, or the bundled Maven wrapper (`./mvnw`)
- Docker Desktop, for the local MySQL instance
- Node.js, for the frontend client

### Start the database

The local MySQL instance is provisioned through Docker Compose. From the project root:

```bash
docker compose up -d
```

This creates the `foldr` database and persists data in a named volume. Confirm the container is running:

```bash
docker compose ps
```

The container currently maps host port `3307` to the container's internal `3306`, to avoid a conflict with another local MySQL instance. This may change; update the datasource URL to match if you change the mapping.

### Run the backend

```bash
./mvnw spring-boot:run
```

### Run the frontend

From the client directory:

```bash
npm install
npm run dev
```

## Configuration

Application configuration lives in `src/main/resources`. Local datasource settings point at the Dockerized MySQL instance. Credentials should be supplied through environment variables outside of local development; no production credentials are committed to the repository.

The database schema is managed through Flyway migrations. Schema changes are made by adding versioned migration files.

## Project Structure

The repository contains a Spring Boot backend and a React frontend, with local database provisioning via Docker Compose. The internal package layout will take shape as the application is built and is not fixed at this stage.

## License

Released under the MIT License. See the `LICENSE` file for details.