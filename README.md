# go-micro

A small Go microservices example made up of a broker service, an authentication service, and a simple frontend used to exercise the APIs.

## Project overview

This repository demonstrates a basic service-to-service flow:

1. The frontend sends a request to the broker service.
2. The broker service routes authentication requests to the auth service.
3. The auth service validates the user against PostgreSQL and returns the result.

## Services

### Frontend
- Location: `/home/runner/work/go-micro/go-micro/front-end`
- Purpose: serves a test page with buttons that call the broker endpoints
- Default port: `80`

### Broker service
- Location: `/home/runner/work/go-micro/go-micro/broker-service`
- Purpose: entry point for API requests and request routing
- Default port: `8002` on the host (`80` in the container)
- Key endpoints:
  - `POST /` health-style broker test
  - `POST /handle` request dispatcher

### Auth service
- Location: `/home/runner/work/go-micro/go-micro/auth-service`
- Purpose: validates user credentials against PostgreSQL
- Default port: `8081` on the host (`80` in the container)
- Key endpoint:
  - `POST /authentication`

### Supporting service
- PostgreSQL runs through Docker Compose and is used by the auth service.

## Repository structure

```text
go-micro/
├── auth-service/
├── broker-service/
├── front-end/
├── project/
│   ├── Makefile
│   └── docker-compose.yml
└── request.http
```

## Prerequisites

Install the following before starting:

- Go 1.19 or newer
- Docker and Docker Compose
- GNU Make

## Setup guide

### 1. Build and start backend services

From `/home/runner/work/go-micro/go-micro/project`, run:

```bash
make up_build
```

This target:
- builds the broker binary
- builds the auth binary
- starts the broker, auth service, and PostgreSQL with Docker Compose

To stop the containers later:

```bash
make down
```

### 2. Start the frontend

From `/home/runner/work/go-micro/go-micro/project`, run:

```bash
make start
```

This builds the frontend binary and starts the web server locally.

To stop the frontend:

```bash
make stop
```

### 3. Open the app

Visit:

```text
http://localhost
```

Use the page buttons to test:
- broker connectivity
- authentication flow through the broker

## Database notes

The auth service expects a PostgreSQL database named `users` and a `users` table with columns used by the application, including:

- `id`
- `email`
- `first_name`
- `last_name`
- `password`
- `user_active`
- `created_at`
- `updated_at`

The frontend sends the sample credentials below when the **Test Auth** button is clicked:

- email: `admin@example.com`
- password: `verysecret`

Make sure your local database contains a matching user record if you want the demo authentication flow to succeed.

## Helpful commands

Run these from `/home/runner/work/go-micro/go-micro/project`:

```bash
make build_broker
make build_auth
make build_front
make up
make up_build
make down
make start
make stop
```

## Manual API testing

You can also test the broker directly:

```http
POST http://localhost:8002
```

And submit an authentication request:

```http
POST http://localhost:8002/handle
Content-Type: application/json

{
  "action": "auth",
  "auth": {
    "email": "admin@example.com",
    "password": "verysecret"
  }
}
```

## Troubleshooting

- If the auth service cannot connect, confirm PostgreSQL is running and reachable from Docker Compose.
- If authentication fails, verify the user exists in the database and the password matches.
- If the frontend does not load, confirm nothing else is already using port `80`.
