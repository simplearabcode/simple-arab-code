# Simple Arab Code - Docker Compose Setup

This Docker Compose configuration allows you to run the entire Simple Arab Code platform (frontend, backend, and all dependencies) with a single command.

## 🚀 Quick Start

### Prerequisites

- [Docker](https://www.docker.com/get-started) (version 20.10 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 2.0 or higher)

### 1. Initial Setup

1. **Clone the repository** (if you haven't already):

   ```bash
   git clone <repository-url>
   cd simple-arab-code
   ```

2. **Create environment files**:

   **Root environment file:**

   ```bash
   cp .env.example .env
   ```

   **Backend environment file:**

   ```bash
   cd backend
   cp .env.example .env
   cd ..
   ```

   **Frontend environment file (if exists):**

   ```bash
   cd frontend
   # Create .env file with necessary variables
   echo "NEXT_PUBLIC_API_URL=http://localhost:4000" > .env
   cd ..
   ```

3. **Edit environment variables** (optional):
   - Update `.env` in the root directory for port configurations and database credentials
   - Update `backend/.env` for backend-specific configurations (JWT secrets, OAuth, etc.)
   - Update `frontend/.env` for frontend-specific configurations

### 2. Start All Services

```bash
# Start all services (frontend, backend, postgres, redis)
docker compose up

# Or run in detached mode (background)
docker compose up -d
```

This will start:

- **Frontend** (Next.js) on http://localhost:3000
- **Backend** (NestJS) on http://localhost:4000
- **PostgreSQL** on localhost:5432
- **Redis** on localhost:6379

### 3. View Logs

```bash
# View all logs
docker compose logs -f

# View specific service logs
docker compose logs -f frontend
docker compose logs -f backend
docker compose logs -f postgres
```

### 4. Stop Services

```bash
# Stop all services (keeps data)
docker compose down

# Stop and remove all data
docker compose down -v
```

## 🛠️ Available Commands

### Development

```bash
# Rebuild services (after code changes that require rebuild)
docker compose up --build

# Restart a specific service
docker compose restart backend

# Stop a specific service
docker compose stop frontend

# Start a specific service
docker compose start frontend
```

### Database Management

```bash
# Run Prisma migrations
docker compose exec backend pnpm prisma:migrate

# Generate Prisma client
docker compose exec backend pnpm prisma:generate

# Open Prisma Studio
docker compose exec backend pnpm prisma:studio
```

### Execute Commands in Containers

```bash
# Backend shell
docker compose exec backend sh

# Frontend shell
docker compose exec frontend sh

# Run backend tests
docker compose exec backend pnpm test

# Run frontend tests
docker compose exec frontend pnpm test
```

## 📦 Services Overview

### Core Services

| Service    | Port | Description           |
| ---------- | ---- | --------------------- |
| Frontend   | 3000 | Next.js application   |
| Backend    | 4000 | NestJS API server     |
| PostgreSQL | 5432 | Primary database      |
| Redis      | 6379 | Cache & session store |

### Optional Management Tools

To start optional management tools, use the `--profile tools` flag:

```bash
docker compose --profile tools up -d
```

| Service         | Port | Description              |
| --------------- | ---- | ------------------------ |
| pgAdmin         | 5050 | PostgreSQL management UI |
| Redis Commander | 8081 | Redis management UI      |

**Access credentials:**

- **pgAdmin**: admin@simplearabcode.com / admin

## 🔧 Configuration

### Environment Variables

#### Root `.env`

Controls ports and basic service configurations:

```env
FRONTEND_PORT=3000
BACKEND_PORT=4000
DB_PORT=5432
REDIS_PORT=6379
```

#### Backend `backend/.env`

Contains backend-specific configurations:

- Database connection
- JWT secrets
- OAuth credentials
- Email service configuration
- etc.

#### Frontend `frontend/.env`

Contains frontend-specific configurations:

```env
NEXT_PUBLIC_API_URL=http://localhost:4000
```

### Port Conflicts

If default ports are already in use, you can change them in the root `.env` file:

```env
FRONTEND_PORT=3001  # Change from 3000
BACKEND_PORT=4001   # Change from 4000
```

## 🐛 Troubleshooting

### Port Already in Use

If you get a port binding error:

1. Check what's using the port: `lsof -i :3000` (replace 3000 with your port)
2. Stop the conflicting service or change the port in `.env`

### Database Connection Issues

If the backend can't connect to the database:

1. Check if PostgreSQL is healthy: `docker compose ps`
2. View PostgreSQL logs: `docker compose logs postgres`
3. Restart services: `docker compose restart postgres backend`

### Frontend Can't Connect to Backend

1. Ensure the backend is running: `docker compose ps backend`
2. Check backend health: `curl http://localhost:4000/health`
3. Verify `NEXT_PUBLIC_API_URL` in frontend/.env

### Hot Reload Not Working

If code changes aren't being detected:

1. Ensure source directories are mounted (check `docker-compose.yml` volumes)
2. Restart the service: `docker compose restart frontend` or `docker compose restart backend`

### Clean Start (Reset Everything)

```bash
# Stop and remove all containers, networks, and volumes
docker compose down -v

# Remove images
docker compose down --rmi all

# Start fresh
docker compose up --build
```

## 📋 Health Checks

All services include health checks. Check service health:

```bash
docker compose ps
```

Healthy services will show `(healthy)` in the status column.

## 🔄 Development Workflow

### Making Backend Changes

1. Edit files in `backend/src/`
2. Changes are automatically detected (hot reload)
3. View logs: `docker compose logs -f backend`

### Making Frontend Changes

1. Edit files in `frontend/src/` or `frontend/app/`
2. Changes are automatically detected (hot reload)
3. View in browser: http://localhost:3000

### Running Database Migrations

```bash
# Create a new migration
docker compose exec backend pnpm prisma migrate dev --name your_migration_name

# Apply migrations
docker compose exec backend pnpm prisma:migrate
```

### Adding New Dependencies

**Backend:**

```bash
docker compose exec backend pnpm add package-name
# Rebuild if needed
docker compose up --build backend
```

**Frontend:**

```bash
docker compose exec frontend pnpm add package-name
# Rebuild if needed
docker compose up --build frontend
```

## 🚀 Production Deployment

This Docker Compose file is configured for **development only**. For production:

1. Create a separate `docker-compose.prod.yml`
2. Use production-ready images (remove volume mounts for source code)
3. Set `NODE_ENV=production`
4. Use proper secrets management
5. Configure proper networking and security
6. Set up proper logging and monitoring

## 📚 Additional Resources

- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [NestJS Documentation](https://nestjs.com/)
- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)

## 📝 Notes

- **Node modules** are stored in Docker volumes to improve performance
- **Source code** is mounted as volumes for hot reload functionality
- **Database data** persists in Docker volumes (survives container restarts)
- Services use a shared network (`app-network`) for inter-service communication

## 🤝 Contributing

When making changes to the Docker setup:

1. Test thoroughly with `docker compose down -v && docker compose up --build`
2. Update this README if adding new services or changing configurations
3. Ensure hot reload still works after changes

## 📄 License

[Your License Here]
