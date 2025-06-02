# F1 Dashboard - Full Stack Implementation Submission

This a document which is an overview of the whole assignment, for more information of respective projects BE and FE please refer to the README file of those projects.

## Live demo

- You can find a live demo here -> https://f1-fe-latest.onrender.com/
- You can find the swagger api here -> https://f1-be-latest.onrender.com/api

> [!NOTE]
> This is deployed to the free tire of render, So it can take up to a minute to spin up after an idle state.
> The BE is deployed to Render as well. So, if it was idle, the first time data fetch can also take upto a minute.

## Behaviour as a whole

- On startup, the application does not fetch any data immediately due to the rate limiter, as it would not be able to retrieve much data in a short time.
- On the first request for the seasons list, it fetches the seasons from the Jolpi API and attempts to get the winner data for those seasons in a loop.
- Due to the rate limiter and to maintain fast response times, we return null for winnerData for seasons that failed due to errors (primarily 429 status codes).
- For each subsequent request for the season list, we attempt to fetch winner data for seasons where it is missing.
- We follow a similar structure for races within a season.
- When retrieving races for a season, we first get the race list and then attempt to fetch as much winner data as possible, returning partial data where necessary.
- For each new request, we attempt to fetch winner data that is not available in our database.
- Once we have all the required data, we no longer make calls to the Jolpi API.
- We only make requests to the Jolpi API for data that is unavailable in our database, not for data we already have.
- Weekly CronJobs attempt to fetch and update our database with additional data. We schedule these calls at different hours to work around the Jolpi API's rate limits.

You can read more about the Jolpi API rate limits [here](https://github.com/jolpica/jolpica-f1/blob/main/docs/rate_limits.md).

## Project Overview
This submission presents a complete full-stack implementation of an F1 Dashboard application, consisting of three main components:
1. Frontend (React-based web application)
2. Backend (NestJS API service)
3. Infrastructure (Docker and deployment configuration)

## Architecture Overview
The application follows a modern microservices architecture with:
- React-based Single Page Application (SPA) frontend
- NestJS RESTful API backend
- PostgreSQL database for persistent storage
- Redis for caching
- Docker containerization
- CI/CD pipeline with GitHub Actions
- Automated deployment to Render

## Implemented Requirements

### 1. Frontend Implementation
- **Modern UI Framework**: Built with React and TypeScript
- **State Management**: Implemented using React Context API and React Query
- **Responsive Design**: Mobile-first approach with modern UI components and with Tailwind
- **Lighthouse Score**: Accessible code with high lighthouse score of more that 90.
- **Unit Test**: Unit test coverage using Vitest and react testing library.
- **API Contract**: Used Orval to generate API client code from OpenAPI (Swagger) specs
- **Features**:
  - Season selection
  - Theme (dark/light) selection
  - Highlighting Season champion won races

### 2. Backend Implementation
- **RESTful API**: Comprehensive endpoints for F1 data
- **Database Integration**: PostgreSQL with TypeORM
- **Database Management**: TypeORM migrations for database schema management
- **Caching Strategy**: 
  - Two-tier caching (In-memory + Redis)
  - Configurable TTL and cache size
- **API Documentation**: OpenAPI/Swagger integration
- **Health Monitoring**: 
  - Database health checks
  - HTTP connectivity monitoring
  - Integration with container health checks
- **CronJob**: Used Cron job to synch Seasons and Current season's races data.

### 3. Infrastructure & DevOps
- **Containerization**: Docker support for all components
- **CI/CD Pipeline**:
  - Automated testing
  - Security scanning (CodeQL, Trivy)
  - Multi-platform Docker image builds (supported but disable to save compute time)
  - Automated deployments to Render
- **Monitoring**: Health check endpoints
- **Security**:
  - CodeQL analysis
  - Vulnerability scanning
  - Secure secret management

## Deployment & Accessibility
- **Frontend**: Deployed on Render
- **Backend**: Deployed on Render
- **API Documentation**: Available at `/api` endpoint
- **Live Demo**: Accessible through provided URLs

## Conclusion
This implementation successfully delivers a modern, scalable, and maintainable F1 Dashboard application. The architecture choices, technical implementations, and development practices follows full-stack development principles and best practices.

## Repository Links
- Frontend: [F1 Dashboard Frontend](https://github.com/sarnenduhexa/f1-fe)
- Backend: [F1 Dashboard Backend](https://github.com/sarnenduhexa/f1-be)
- Infrastructure: [F1 Dashboard Infrastructure](https://github.com/sarnenduhexa/f1-infra) 

## Running the Setup Locally

### Prerequisites
- Node.js (v22 was used for development)
- Docker and Docker Compose
- PostgreSQL (containerised)
- Redis (containerised)
- Git

### Step 1: Clone All Repositories
```bash
# Clone all three repositories
git clone https://github.com/sarnenduhexa/f1-fe.git
git clone https://github.com/sarnenduhexa/f1-be.git
git clone https://github.com/sarnenduhexa/f1-infra.git
```

### Step 2: Using Docker Compose
Using Docker, you can use the infrastructure repository to run everything:

```bash
cd f1-infra

# Start all services
docker-compose up -d

# To stop all services
docker-compose down
```

### Step 3: Verify the Setup
1. Backend API should be running at: http://localhost:3001
   - Swagger documentation available at: http://localhost:3001/api
2. Frontend application should be running at: http://localhost:5174
3. PostgreSQL database should be running on port 5432
4. pgAdmin4 should be running on port 5050
5. Redis should be running on port 6379
6. Redis Insight should be running on 8001

**Note:** I have intentionally used different ports for docker compose so that it does not clash when running the apps individually. That is why the Docker compose BE and FE apps are running on 3001 and 5174 respectively.

### Troubleshooting
1. **Database Connection Issues**:
   - Ensure PostgreSQL is running
   - Verify database credentials in .env file
   - Check if the database exists

2. **Redis Connection Issues**:
   - Ensure Redis server is running
   - Verify Redis connection details in .env file

3. **Port Conflicts**:
   - Check if ports 3000, 5173, 5432, and 6379 are available (for individual instances)
   - Check if ports 3001, 5743 (for docker compose)
   - Modify ports in .env files if needed

4. **Docker Issues**:
   - Ensure Docker daemon is running
   - Check Docker logs: `docker-compose logs`
   - Verify Docker network settings 