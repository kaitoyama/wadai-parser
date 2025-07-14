# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Build & Run
- `make build` - Build the Go application
- `go run .` - Run the application directly
- `make db-up` - Start Docker containers (MariaDB)
- `make db-down` - Stop Docker containers
- `make init` - Initialize development environment (starts DB, migrates, generates code)

### Testing & Quality
- `make test` - Run all tests with verbose output
- `make lint` - Run golangci-lint
- `make fmt` - Format Go code

### Database Operations
- `make migrate` - Apply database migrations using mysqldef
- `make migrate-dry-run` - Preview migration changes
- `make wait-db` - Wait for database to be ready
- `make clean-docker` - Stop and remove all containers with volumes

### Code Generation
- `make generate-sqlc` - Generate SQL code using sqlc
- `make oapi` - Generate OpenAPI server and models
- `make generate-server` - Generate server code from OpenAPI spec
- `make generate-models` - Generate model code from OpenAPI spec

## Architecture

This is a Go web service following Clean Architecture principles with these layers:

### Project Structure
- `internal/domain/` - Domain entities and interfaces
- `internal/usecase/` - Business logic layer
- `internal/interface/handler/` - HTTP handlers (presentation layer)
- `internal/infrastructure/` - External dependencies (DB, config)
- `openapi/` - Generated OpenAPI server and models
- `db/` - Database schema and queries

### Key Technologies
- **Web Framework**: Echo v4 with middleware (CORS, logging, recovery)
- **Database**: MariaDB/MySQL with sqlc for type-safe queries
- **OpenAPI**: oapi-codegen for server and model generation
- **Logging**: zerolog with structured logging
- **Containerization**: Docker Compose for local development

### Configuration
Environment variables with defaults in `internal/infrastructure/config/config.go`:
- `NS_MARIADB_HOSTNAME` (default: localhost)
- `NS_MARIADB_PORT` (default: 3306)  
- `NS_MARIADB_USER` (default: user)
- `NS_MARIADB_PASSWORD` (default: password)
- `NS_MARIADB_DATABASE` (default: template_db)
- `PORT` (default: 8080)

### Code Generation Workflow
1. Modify `openapi/swagger.yml` for API changes
2. Run `make oapi` to regenerate server and models
3. Modify `db/schema.sql` for database changes  
4. Run `make migrate` to apply schema changes
5. Modify `db/queries.sql` for new queries
6. Run `make generate-sqlc` to regenerate query code

### Server Setup
- Main server setup in `router.go` with embedded OpenAPI spec
- Health check endpoint at `/api/health`
- Database connectivity validation on startup
- Graceful error handling with structured logging