# RealWorld Conduit Application - Mock Project

This is a mock project implementing the [RealWorld](https://github.com/gothinkster/realworld) specification - a full-stack social blogging application (Medium.com clone) called "Conduit". It demonstrates modern software architecture patterns and DevOps practices for training purposes.

## 🏗️ Architecture Overview

### Approach 1: Direct Connection (Development)

In development, the frontend connects directly to the backend API:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │    Backend      │    │    Database     │
│   (React+Redux) │────│  (Spring Boot)  │────│    (H2/MySQL)   │
│   Port: 4100    │    │   Port: 8080    │    │   Port: 3306    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
     │                        │                        │
     └────────────────────────┴────────────────────────┘
                  Direct API calls from browser
```

**Configuration**: Frontend `agent.js` configured to call `http://localhost:8080/api`

### Approach 2: Reverse Proxy (Production)

In production, an Nginx reverse proxy routes traffic and handles SSL termination:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Browser                            │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ HTTPS/HTTP (Port 80/443)
                             │
                ┌────────────▼────────────┐
                │   Reverse Proxy         │
                │   (Nginx)               │
                │   Port: 80/443          │
                └─────┬───────────┬───────┘
                      │           │
         ┌────────────┘           └────────────┐
         │                                      │
┌────────▼────────┐                  ┌─────────▼────────┐
│   Frontend      │                  │    Backend       │
│   (React+Redux) │                  │  (Spring Boot)   │
│   Port: 4100    │                  │   Port: 8080     │
│   (internal)    │                  │   (internal)     │
└─────────────────┘                  └─────────┬────────┘
                                               │
                                  ┌────────────▼────────────┐
                                  │    Database             │
                                  │    (H2/MySQL)           │
                                  │    Port: 3306           │
                                  └─────────────────────────┘
```

**Benefits of Reverse Proxy**:
- **Single Entry Point**: All traffic goes through port 80/443
- **SSL/TLS Termination**: HTTPS handled at proxy level
- **Load Balancing**: Can distribute load across multiple backend instances
- **Security**: Hide internal service ports and IPs
- **Caching**: Static asset caching at proxy level
- **Rate Limiting**: Protect backend from excessive requests
- **Routing**: Clean URL routing (`/api/*` → backend, `/*` → frontend)
- **Health Checks**: Proxy can route around unhealthy services

**Configuration**:
- Nginx routes `/api/*` to backend
- Nginx routes `/*` to frontend (with SPA fallback)
- All services communicate internally via Docker network

## 📁 Project Structure

```
mock-project/
├── README.md                    # This file
├── backend-springboot/          # Spring Boot + MyBatis backend API
│   ├── build.gradle             # Gradle build configuration
│   ├── gradlew / gradlew.bat    # Gradle wrapper scripts
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── io/spring/
│   │   │   │       ├── api/              # Web layer (REST controllers)
│   │   │   │       ├── application/      # Query services (CQRS read model)
│   │   │   │       ├── core/             # Domain model (entities, repositories)
│   │   │   │       └── infrastructure/   # MyBatis implementation details
│   │   │   └── resources/
│   │   │       ├── application.properties
│   │   │       ├── mapper/               # MyBatis XML mappers
│   │   │       └── db/migration/         # Flyway migrations
│   │   └── test/                         # Test suite
│   └── README.md
├── frontend-react/              # React + Redux frontend application
│   ├── package.json
│   ├── src/
│   │   ├── components/          # React components
│   │   ├── reducers/            # Redux reducers
│   │   ├── agent.js             # API client (superagent)
│   │   └── store.js             # Redux store configuration
│   └── README.md
└── docker-compose.yml           # Docker Compose configuration
```

## 🎯 Learning Objectives

This mock project is designed to teach:

1. **Module 4 (Linux)**: Manual deployment and system administration
2. **Module 5 (Terraform)**: Infrastructure as Code implementation
3. **Module 6 (Docker)**: Containerization of all application components
4. **Module 7 (CI/CD)**: Automated testing and deployment pipelines
5. **Module 8 (Observability)**: Monitoring and logging implementation

## 🚀 Application Features

### Frontend (React + Redux)
- **Article Management**: Create, read, update, delete articles
- **User Authentication**: JWT-based login and registration
- **User Profiles**: View and follow user profiles
- **Article Feed**: Global feed, personal feed, and tag-based filtering
- **Comments**: Add and delete comments on articles
- **Favorites**: Favorite and unfavorite articles
- **Pagination**: Navigate through article lists
- **Tags**: Browse articles by tags
- **RealWorld Spec**: Fully compliant with RealWorld API specification

### Backend (Spring Boot + MyBatis)
- **REST API**: RESTful endpoints following RealWorld spec
- **Authentication**: JWT token-based authentication and authorization
- **Domain Driven Design**: Clean separation of business logic
- **CQRS Pattern**: Separate read and write models
- **Data Mapper Pattern**: MyBatis for persistence layer
- **Database**: H2 (default) or MySQL support
- **Testing**: Comprehensive test suite (API and repository tests)

### Database
- **Development**: H2 in-memory database (default, no setup required)
- **Production**: MySQL 8.0 (optional, via environment variables)
- **Migration**: Flyway for database versioning

## 🛠️ Technology Stack

### Frontend
- **Framework**: React 16.3.0
- **State Management**: Redux 3.6.0
- **Routing**: React Router 4.1.2
- **HTTP Client**: Superagent 3.8.2
- **Build Tool**: Create React App (react-scripts 1.1.1)
- **Styling**: CSS Modules
- **Port**: 4100 (configured to avoid conflicts)

### Backend
- **Framework**: Spring Boot 2.7.18
- **Language**: Java 17
- **Build Tool**: Gradle 7.6 (via Gradle Wrapper)
- **Database Access**: MyBatis Spring Boot Starter 2.3.1
- **Security**: Spring Security with JWT (JJWT 0.11.2)
- **Database**: H2 (default for development/tests) or MySQL 8.0
- **Migration Tool**: Flyway with MySQL support
- **API Base Path**: `/api` (configured via `server.servlet.context-path`)
- **Testing**: JUnit 4, REST Assured 4.5.1, Spring Boot Test, MyBatis Test
- **Test Database**: H2 in-memory (automatically configured for tests)
- **Architecture Patterns**:
  - Domain Driven Design (DDD)
  - Command Query Responsibility Segregation (CQRS)
  - Data Mapper Pattern

### Database
- **Development**: H2 in-memory database (default)
- **Production**: MySQL 8.0 (optional)
- **Migration Tool**: Flyway

### DevOps Tools
- **Containerization**: Docker
- **Orchestration**: Kubernetes (optional)
- **Infrastructure**: Terraform (optional)
- **CI/CD**: Jenkins, GitHub Actions (optional)
- **Monitoring**: Prometheus, Grafana (optional)
- **Logging**: ELK Stack (optional)

## 🏃‍♂️ Quick Start

### Prerequisites
- **Java**: JDK 17 or higher (required for backend)
- **Node.js**: 8.x or higher (for React 16.3.0)
- **Gradle**: Included via Gradle Wrapper (Gradle 7.6, no installation needed)
- **Docker & Docker Compose** (optional, for containerized setup)
- **MySQL 8.0** (optional, only if not using H2)

### Local Development Setup

#### Option 1: Quick Start with H2 (No Database Setup)

1. **Start Backend** (uses H2 in-memory database):
   ```bash
   cd backend-springboot
   
   # Linux/Mac:
   ./gradlew bootRun
   
   # Windows:
   gradlew.bat bootRun
   ```
   
   The backend will start on `http://localhost:8080`
   
   **Test it**: Open `http://localhost:8080/tags` in your browser or:
   ```bash
   curl http://localhost:8080/tags
   ```

2. **Start Frontend**:
   ```bash
   cd frontend-react
   npm install
   npm start
   ```
   
   The frontend will start on `http://localhost:4100`

3. **Configure Frontend API** (if using local backend):
   Edit `frontend-react/src/agent.js` and change:
   ```javascript
   const API_ROOT = 'http://localhost:8080/api';  // Change from production URL
   ```

#### Option 2: Using MySQL Database

1. **Set Environment Variables**:
   ```bash
   # Linux/Mac:
   export DB_USERNAME=ecommerce
   export DB_PASSWORD=password
   export DB_URL=localhost
   export DB_PORT=3306
   export DB_NAME=realworld
   
   # Windows PowerShell:
   $env:DB_USERNAME="ecommerce"
   $env:DB_PASSWORD="password"
   $env:DB_URL="localhost"
   $env:DB_PORT="3306"
   $env:DB_NAME="realworld"
   ```

2. **Start Backend**:
   ```bash
   cd backend-springboot
   ./gradlew bootRun  # or gradlew.bat on Windows
   ```

3. **Start Frontend** (same as Option 1)

### Docker Development Setup

```bash
# Start all services (MySQL, Backend, Frontend)
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down
```

## 🔧 Configuration

### Backend Configuration

**File**: `backend-springboot/src/main/resources/application.properties`

#### Default Configuration (H2)
The application uses H2 in-memory database by default. No configuration needed.

#### MySQL Configuration
Set the following environment variables:
```bash
DB_USERNAME=your_username
DB_PASSWORD=your_password
DB_URL=localhost              # MySQL host (without jdbc:mysql://)
DB_PORT=3306                  # MySQL port
DB_NAME=your_database_name    # Database name
```

The application.properties will automatically use these:
```properties
spring.datasource.url=jdbc:mysql://${DB_URL}:${DB_PORT}/${DB_NAME}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}
```

#### JWT Configuration
JWT secret is configured in `application.properties`:
```properties
jwt.secret=your-secret-key-here
jwt.sessionTime=86400  # 24 hours in seconds
```

#### MyBatis Configuration
```properties
mybatis.mapper-locations=mapper/*.xml
mybatis.configuration.map-underscore-to-camel-case=true
mybatis.configuration.cache-enabled=true
```

### Frontend Configuration

**File**: `frontend-react/src/agent.js`

Change the API root URL:
```javascript
// For local backend:
const API_ROOT = 'http://localhost:8080/api';

// For production backend:
const API_ROOT = 'https://conduit.productionready.io/api';
```

**File**: `frontend-react/package.json`

Port configuration (already set to 4100):
```json
"scripts": {
  "start": "cross-env PORT=4100 react-scripts start"
}
```

Or create `.env` file:
```bash
PORT=4100
```

## 🧪 Testing

### Backend Testing

```bash
cd backend-springboot

# Run all tests
./gradlew test              # Linux/Mac
gradlew.bat test            # Windows

# Run specific test suites
./gradlew test --tests "*ApiTest"                    # API tests only
./gradlew test --tests "*RepositoryTest"             # Repository tests only
./gradlew test --tests "*QueryServiceTest"           # Query service tests only

# Run with coverage (if configured)
./gradlew test jacocoTestReport
```

### Frontend Testing

```bash
cd frontend-react

# Run tests
npm test

# Run tests in watch mode
npm test -- --watch

# Run tests with coverage
npm test -- --coverage
```

## 📊 API Endpoints

The backend implements the [RealWorld API specification](https://github.com/gothinkster/realworld/tree/main/api).

### Authentication
- `POST /api/users/login` - User login
- `POST /api/users` - User registration
- `GET /api/user` - Get current user
- `PUT /api/user` - Update user

### Articles
- `GET /api/articles` - List articles (with pagination, filtering, tagging)
- `GET /api/articles/feed` - Get authenticated user's feed
- `GET /api/articles/{slug}` - Get article by slug
- `POST /api/articles` - Create article
- `PUT /api/articles/{slug}` - Update article
- `DELETE /api/articles/{slug}` - Delete article

### Comments
- `GET /api/articles/{slug}/comments` - Get comments for article
- `POST /api/articles/{slug}/comments` - Add comment to article
- `DELETE /api/articles/{slug}/comments/{id}` - Delete comment

### Favorites
- `POST /api/articles/{slug}/favorite` - Favorite an article
- `DELETE /api/articles/{slug}/favorite` - Unfavorite an article

### Profiles
- `GET /api/profiles/{username}` - Get profile
- `POST /api/profiles/{username}/follow` - Follow user
- `DELETE /api/profiles/{username}/follow` - Unfollow user

### Tags
- `GET /api/tags` - Get all tags

**Base URL**: `http://localhost:8080` (no `/api` prefix needed, but frontend uses `/api`)

## 📊 Monitoring and Observability

### Health Checks
- **Backend Tags Endpoint**: http://localhost:8080/tags (public endpoint)
- **Backend Health**: Check application logs for startup confirmation

### Logging
- **Backend Logs**: Console output, configured via `logback.xml`
- **MyBatis Logging**: Debug level for read services (configurable)
- **Frontend Logs**: Browser console

### Database Console (H2)
Uncomment in `application.properties` to enable:
```properties
spring.h2.console.enabled=true
```
Then access at: http://localhost:8080/h2-console

## 🏗️ Architecture Details

### Backend Architecture

The backend follows **Domain Driven Design** and **CQRS** patterns:

#### Layer Structure:
1. **`api`** (Web Layer)
   - REST controllers (`ArticleApi`, `UsersApi`, etc.)
   - Exception handlers
   - Security configuration (JWT, CORS)

2. **`core`** (Domain Layer)
   - Domain entities (`Article`, `User`, `Comment`, etc.)
   - Repository interfaces
   - Domain services (`AuthorizationService`, `JwtService`)

3. **`application`** (Application Layer - Read Model)
   - Query services (CQRS read side)
   - Data Transfer Objects (DTOs)
   - `ArticleQueryService`, `UserQueryService`, etc.

4. **`infrastructure`** (Infrastructure Layer)
   - MyBatis mappers and implementations
   - Repository implementations (`MyBatisArticleRepository`, etc.)
   - Service implementations (`DefaultJwtService`, etc.)

#### CQRS Implementation:
- **Write Side**: Uses domain repositories for mutations
- **Read Side**: Uses MyBatis read services for queries (optimized for read performance)

#### Database Schema:
- Managed by Flyway migrations (`db/migration/V1__create_tables.sql`)
- Tables: `articles`, `users`, `comments`, `article_favorites`, `follow_relations`, `tags`

### Frontend Architecture

- **Component Structure**: React functional/components
- **State Management**: Redux with separate reducers per domain
- **API Communication**: Centralized `agent.js` using Superagent
- **Routing**: React Router for client-side navigation
- **Redux Store**: Configured with middleware (logger, Redux DevTools)

## 🐛 Troubleshooting

### Backend Issues

#### Backend Won't Start
```bash
# Check Java version (should be 8+)
java -version

# Clean and rebuild
cd backend-springboot
./gradlew clean build

# Check if port 8080 is available
netstat -ano | findstr :8080  # Windows
lsof -i :8080                  # Linux/Mac
```

#### Database Connection Issues
```bash
# If using H2 (default), it should work automatically
# If using MySQL, verify environment variables:
echo $DB_USERNAME
echo $DB_URL

# Test MySQL connection
mysql -h $DB_URL -P $DB_PORT -u $DB_USERNAME -p

# Verify database exists
mysql> SHOW DATABASES;
mysql> USE $DB_NAME;
```

#### Gradle Build Errors
```bash
# Clean Gradle cache
./gradlew clean

# Rebuild
./gradlew build

# If wrapper has permission issues (Linux/Mac)
chmod +x gradlew

# Fix CRLF line endings in gradlew (if needed on Windows)
# Use Git to normalize: git add --renormalize gradlew
# Or configure Git: git config core.autocrlf false
```

### Frontend Issues

#### Frontend Won't Start
```bash
# Clear node modules and reinstall
cd frontend-react
rm -rf node_modules package-lock.json
npm install

# Check Node.js version (should be 8+)
node --version

# Check if port 4100 is available
netstat -ano | findstr :4100  # Windows
lsof -i :4100                  # Linux/Mac
```

#### API Connection Issues
```bash
# Verify backend is running
curl http://localhost:8080/tags

# Check agent.js API_ROOT configuration
# Should be: http://localhost:8080/api for local backend
```

#### Module Not Found Errors
```bash
# Reinstall dependencies
cd frontend-react
rm -rf node_modules package-lock.json
npm install
```

## 📚 Documentation

### RealWorld Resources
- **RealWorld Main Repo**: https://github.com/gothinkster/realworld
- **API Specification**: https://github.com/gothinkster/realworld/tree/main/api
- **Backend Source**: https://github.com/vitamin-b12/devops-training-project-backend
- **Frontend Source**: https://github.com/vitamin-b12/devops-training-project-frontend
- **Live Demo**: https://react-redux.realworld.io/

### Technical Documentation
- **Spring Boot**: https://spring.io/projects/spring-boot
- **MyBatis**: https://mybatis.org/mybatis-3/
- **React**: https://reactjs.org/docs/getting-started.html
- **Redux**: https://redux.js.org/introduction/getting-started

## 🚀 Deployment

### Local Deployment

```bash
# Backend
cd backend-springboot
./gradlew bootRun

# Frontend (separate terminal)
cd frontend-react
npm start
```

### Docker Deployment

```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f backend
docker-compose logs -f frontend

# Stop services
docker-compose down
```

### Production Deployment

1. **Build Backend JAR**:
   ```bash
   cd backend-springboot
   ./gradlew clean build
   java -jar build/libs/*.jar
   ```

2. **Build Frontend**:
   ```bash
   cd frontend-react
   npm run build
   # Serve build/ directory with nginx or any static server
   ```

## 🔐 Security

### Backend Security
- **JWT Authentication**: Token-based stateless authentication
- **Spring Security**: Integrated with custom JWT filter
- **CORS Configuration**: Configured for frontend access
- **Input Validation**: Request validation
- **SQL Injection Prevention**: MyBatis parameterized queries

### Frontend Security
- **JWT Storage**: Tokens stored in localStorage
- **Protected Routes**: Client-side route protection
- **API Token Injection**: Automatic token inclusion in requests

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Ensure all tests pass
5. Submit a pull request

## 📄 License

This project follows the RealWorld specification and uses MIT License - see individual repository licenses for details.

## 📞 Support

For questions and support:
- **Training Materials**: Review module content
- **Technical Issues**: Check troubleshooting section above
- **Instructor Help**: Contact your training instructor
