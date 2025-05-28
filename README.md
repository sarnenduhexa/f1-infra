# f1-infra
Repository for the Infra for the F1 app

## Architecture Overview

This project uses Docker Compose to orchestrate four main services:

### Services

1. **PostgreSQL Database (`postgres`)**
   - Uses PostgreSQL 16 Alpine for a lightweight database
   - Features:
     - Persistent data storage using Docker volumes
     - Health checks to ensure database availability
     - Resource limits (512MB memory, 0.5 CPU)
     - Automatic restart policy
     - Secure configuration through environment variables

2. **Redis Stack (`redis`)**
   - Uses Redis Stack for caching and monitoring
   - Features:
     - Redis server for caching (port 6379)
     - RedisInsight for monitoring (port 8001)
     - Persistent data storage using Docker volumes
     - Health checks to ensure Redis availability
     - Resource limits (512MB memory, 0.5 CPU)
     - Automatic restart policy

3. **Backend Service (`backend`)**
   - Node.js application running on port 3001 (mapped to container port 3000)
   - Features:
     - Connects to PostgreSQL database
     - Connects to Redis for caching
     - Health check endpoint at `/health`
     - Resource limits (1GB memory, 1 CPU)
     - Security features:
       - Runs as non-root user
       - Read-only filesystem
       - Temporary filesystem for required writable directories
     - Automatic restart policy
     - CORS configuration for frontend access

4. **Frontend Service (`frontend`)**
   - Nginx-based web application running on port 5174
   - Features:
     - Serves static web content
     - Health check for web server availability
     - Resource limits (256MB memory, 0.5 CPU)
     - Security features:
       - Read-only filesystem
       - Temporary filesystem for Nginx cache and logs
     - Automatic restart policy

### Network Configuration

- All services are connected through a custom bridge network `f1-network`
- Network isolation is configurable through the `internal` flag
- Services can communicate with each other using their service names as hostnames

### Data Persistence

- PostgreSQL data is persisted using a named volume `f1_postgres_data`
- Redis data is persisted using a named volume `f1_redis_data`
- Volumes are mounted at their respective data directories in the containers

### Security Features

- Environment variables for sensitive configuration
- Resource limits for all services
- Health checks for service availability
- Read-only filesystems where possible
- Non-root user execution
- Temporary filesystem for required writable directories
- Configurable network isolation

### Environment Variables

The services can be configured using environment variables. Create a `.env` file in the project root directory with the following variables:

```env
# Database Configuration
DB_USERNAME=postgres    # Database username
DB_PASSWORD=postgres    # Database password
DB_DATABASE=f1_db      # Database name

# Redis Configuration
REDIS_HOST=redis       # Redis host (default: redis)
REDIS_PORT=6379       # Redis port (default: 6379)

# Application Configuration
CORS_ORIGINS=http://localhost:5173,http://localhost:5174  # Allowed CORS origins
NODE_ENV=production    # Node environment (production/development)
```

Make sure to set appropriate values for these variables based on your environment. The values shown above are examples and should be changed in production.

### Service Dependencies

- Backend service depends on PostgreSQL and Redis being healthy
- Frontend service depends on Backend being healthy
- Services start in the correct order due to dependency configuration

### Logging

All services are configured with JSON file logging with rotation:
- Maximum log file size: 10MB
- Maximum number of log files: 3
- Logs can be viewed using `docker compose logs`

## Docker Compose Usage

This project uses Docker Compose to manage the infrastructure services. Here's how to use it:

### Prerequisites
- Docker installed on your system
- Docker Compose installed on your system

### Basic Commands

1. **Start all services**
   ```bash
   docker compose up
   ```
   To run in detached mode (background):
   ```bash
   docker compose up -d
   ```

2. **Stop all services**
   ```bash
   docker compose down
   ```

3. **View running services**
   ```bash
   docker compose ps
   ```

4. **View logs**
   ```bash
   # View logs for all services
   docker compose logs
   
   # View logs for a specific service
   docker compose logs <service-name>
   
   # Follow logs in real-time
   docker compose logs -f
   ```

5. **Rebuild services**
   ```bash
   docker compose build
   ```

### Service Management

- To start a specific service:
  ```bash
  docker compose up <service-name>
  ```

- To stop a specific service:
  ```bash
  docker compose stop <service-name>
  ```

- To restart a service:
  ```bash
  docker compose restart <service-name>
  ```

### Troubleshooting

If you encounter any issues:

1. Check the service logs using `docker compose logs`
2. Ensure all required ports are available
3. Verify that the `.env` file contains all necessary configuration
4. Try rebuilding the services with `docker compose build --no-cache`
