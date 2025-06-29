# Film Catalog API

A Spring Boot REST API for managing a catalog of films and actors with many-to-many relationships. Built with Java 17, Spring Boot 3.5.3, PostgreSQL, and Docker.
### Prerequisites

- Docker and Docker Compose installed
- Git (to clone the repository)

### Running the Application

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd film-catalog
   ```

2. **Start the application with Docker Compose**
   ```bash
   docker-compose up -d
   ```

   This will start:
    - **PostgreSQL database** on port `5432`
    - **Film Catalog API** on port `8080`
    - **pgAdmin** (optional) on port `5050`

3. **Verify the application is running**
   ```bash
   curl http://localhost:8080/actuator/health
   ```

4. **Access the API documentation**
    - Swagger UI: http://localhost:8080/swagger-ui.html
    - OpenAPI JSON: http://localhost:8080/v3/api-docs

## Docker Services

### Core Services
- **app**: Film Catalog API application
- **postgres**: PostgreSQL 15 database
- **pgadmin**: Database management interface (optional)

### Optional Services (Profiles)
```bash
# Start with pgAdmin for database management
docker-compose --profile admin up -d

# Start with Redis caching
docker-compose --profile cache up -d

# Start with monitoring (Prometheus + Grafana)
docker-compose --profile monitoring up -d

# Start everything
docker-compose --profile admin --profile cache --profile monitoring up -d
```

## API Endpoints

### Films (`/api/filmovi`)
- `GET /api/filmovi` - List all films
- `GET /api/filmovi/{id}` - Get film by ID
- `POST /api/filmovi` - Create new film
- `PUT /api/filmovi/{id}` - Update film
- `DELETE /api/filmovi/{id}` - Delete film
- `GET /api/filmovi/filter` - Filter films
- `GET /api/filmovi/paginated` - Paginated films
- `GET /api/filmovi/stats` - Film statistics

### Actors (`/api/glumci`)
- `GET /api/glumci` - List all actors
- `GET /api/glumci/{id}` - Get actor by ID
- `POST /api/glumci` - Create new actor
- `PUT /api/glumci/{id}` - Update actor
- `DELETE /api/glumci/{id}` - Delete actor
- `GET /api/glumci/filter` - Filter actors
- `GET /api/glumci/paginated` - Paginated actors
- `POST /api/glumci/{glumacId}/filmovi/{filmId}` - Assign actor to film
- `DELETE /api/glumci/{glumacId}/filmovi/{filmId}` - Remove actor from film
- `GET /api/glumci/most-active` - Most active actors
- `GET /api/glumci/without-films` - Actors without films
- `GET /api/glumci/stats` - Actor statistics

## 🛠️ Development

### Local Development Setup

1. **Start only the database**
   ```bash
   docker-compose up postgres -d
   ```

2. **Run the application locally**
   ```bash
   ./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
   ```

### Build and Test

```bash
# Build the application
./mvnw clean package

# Run tests
./mvnw test

# Run integration tests
./mvnw verify -P integration-tests

# Build Docker image
docker build -t film-catalog:latest .
```

## 🔧 Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `SPRING_PROFILES_ACTIVE` | Active Spring profile | `docker` |
| `SPRING_DATASOURCE_URL` | Database URL | `jdbc:postgresql://postgres:5432/film_catalog` |
| `SPRING_DATASOURCE_USERNAME` | Database username | `postgres` |
| `SPRING_DATASOURCE_PASSWORD` | Database password | `password` |
| `SERVER_PORT` | Application port | `8080` |
| `JAVA_OPTS` | JVM options | `-Xmx512m -Xms256m` |

### Database Access

**Using pgAdmin** (when admin profile is active):
- URL: http://localhost:5050
- Email: `admin@filmcatalog.com`
- Password: `admin123`

**Direct database connection**:
- Host: `localhost`
- Port: `5432`
- Database: `film_catalog`
- Username: `postgres`
- Password: `password`

## Monitoring

When monitoring profile is enabled:
- **Prometheus**: http://localhost:9090
- **Grafana**: http://localhost:3000 (admin/admin123)

Health check endpoint: http://localhost:8080/actuator/health

## Sample Data

The application includes sample data with popular films and actors:

**Films**: Avengers: Endgame, Iron Man, Inception, Forrest Gump, etc.
**Actors**: Robert Downey Jr., Leonardo DiCaprio, Tom Hanks, Meryl Streep, etc.

## Project Structure

```
src/
├── main/
│   ├── java/com/filmcatalog/
│   │   ├── controllers/          # REST controllers
│   │   ├── services/             # Business logic
│   │   ├── repositories/         # Data access layer
│   │   ├── entities/             # JPA entities
│   │   ├── dtos/                 # Data transfer objects
│   │   ├── specifications/       # JPA specifications for filtering
│   │   ├── exception/            # Exception handling
│   │   └── config/               # Configuration classes
│   └── resources/
│       ├── db/migration/         # Flyway database migrations
│       └── application*.properties # Configuration files
└── test/                         # Test classes
```

## Troubleshooting

### Common Issues

1. **Port already in use**
   ```bash
   # Check what's using port 8080
   sudo lsof -i :8080
   # Kill the process or change the port in docker-compose.yml
   ```

2. **Database connection issues**
   ```bash
   # Check database logs
   docker-compose logs postgres
   
   # Restart database
   docker-compose restart postgres
   ```

3. **Application won't start**
   ```bash
   # Check application logs
   docker-compose logs app
   
   # Rebuild and restart
   docker-compose down
   docker-compose build --no-cache
   docker-compose up -d
   ```

### Useful Commands

```bash
# View logs
docker-compose logs -f app
docker-compose logs -f postgres

# Access application container
docker-compose exec app sh

# Access database
docker-compose exec postgres psql -U postgres -d film_catalog

# Stop all services
docker-compose down

# Stop and remove volumes (careful - this deletes data!)
docker-compose down -v

# Update and restart a service
docker-compose up -d --no-deps app
```