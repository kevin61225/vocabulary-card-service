# Vocabulary Card Service API

[![CI/CD Pipeline](https://github.com/kevin61225/vocabulary-card-service/workflows/CI/CD%20Pipeline/badge.svg)](https://github.com/kevin61225/vocabulary-card-service/actions)

An API service built with .NET 10 that provides users with tools to learn vocabulary in different languages through vocabulary cards.

## Overview

This project follows a **Spec-Driven Design** approach, where API specifications are defined first in Markdown format, and implementation follows the specifications. The service enables users to create, manage, and organize vocabulary cards for language learning.

## Features (Phase 1)

### 1. Vocabulary Card Creation
- Create vocabulary cards with source word and translation
- Support multiple languages (English, Chinese, Japanese, Spanish, French, German, Korean)
- Add pronunciation, example sentences, and notes
- Categorize cards with tags and difficulty levels

### 2. Vocabulary Card Management
- View, update, and delete vocabulary cards
- List cards with pagination and filtering
- Search cards by word or translation
- Filter by language pair, difficulty, and tags
- Sort by various criteria
- Get statistics about vocabulary collection

### 3. User Management
- User registration and authentication
- JWT-based secure authentication
- User profile management
- Password management and reset
- Email verification

## Technology Stack

- **Framework**: .NET 10
- **Language**: C# 13
- **Architecture**: RESTful API
- **Authentication**: JWT (JSON Web Tokens)
- **Database**: SQL Server (configurable)
- **Containerization**: Docker
- **CI/CD**: GitHub Actions & GitLab CI

## Project Structure

```
vocabulary-card-service/
├── src/
│   └── VocabularyCardService.API/      # Main API project
├── specs/                               # API specifications (Spec-Driven Design)
│   ├── README.md
│   ├── vocabulary-card-creation.md
│   ├── vocabulary-card-management.md
│   └── user-management.md
├── .github/
│   └── workflows/
│       └── ci-cd.yml                    # GitHub Actions workflow
├── .gitlab-ci.yml                       # GitLab CI configuration
├── Dockerfile                           # Docker configuration
├── docker-compose.yml                   # Docker Compose for local development
└── VocabularyCardService.sln            # Solution file

```

## Getting Started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- [Docker](https://www.docker.com/get-started) (optional, for containerized deployment)
- [SQL Server](https://www.microsoft.com/sql-server) or SQL Server in Docker

### Running Locally

1. **Clone the repository**
   ```bash
   git clone https://github.com/kevin61225/vocabulary-card-service.git
   cd vocabulary-card-service
   ```

2. **Restore dependencies**
   ```bash
   dotnet restore
   ```

3. **Build the project**
   ```bash
   dotnet build
   ```

4. **Run the API**
   ```bash
   cd src/VocabularyCardService.API
   dotnet run
   ```

5. **Access the API**
   - API: http://localhost:5000
   - Swagger UI: http://localhost:5000/swagger

### Running with Docker

1. **Build and run with Docker Compose**
   ```bash
   docker-compose up -d
   ```

2. **Access the API**
   - API: http://localhost:8080
   - Swagger UI: http://localhost:8080/swagger

3. **Stop the containers**
   ```bash
   docker-compose down
   ```

### Building Docker Image

```bash
docker build -t vocabulary-card-service:latest .
```

### Running Docker Container

```bash
docker run -d -p 8080:8080 --name vocabulary-api vocabulary-card-service:latest
```

## API Documentation

Detailed API specifications can be found in the `specs/` directory:

- [API Overview](./specs/README.md)
- [Vocabulary Card Creation](./specs/vocabulary-card-creation.md)
- [Vocabulary Card Management](./specs/vocabulary-card-management.md)
- [User Management](./specs/user-management.md)

Once the API is running, interactive API documentation is available via Swagger UI at `/swagger`.

## Development

### Code Style

This project follows standard .NET coding conventions. Use `dotnet format` to ensure code formatting:

```bash
dotnet format
```

### Running Tests

```bash
dotnet test
```

### Database Migrations

```bash
# Add a new migration
dotnet ef migrations add MigrationName -p src/VocabularyCardService.API

# Update database
dotnet ef database update -p src/VocabularyCardService.API
```

## CI/CD

This project includes CI/CD configurations for both GitHub Actions and GitLab CI:

### GitHub Actions
- Automated build and test on push/PR
- Code quality analysis
- Docker image build and push to GitHub Container Registry
- Security scanning with Trivy

### GitLab CI
- Multi-stage pipeline (build, test, quality, docker, security)
- Code coverage reporting
- Docker image build and push to GitLab Container Registry
- Security scanning with Trivy

## Deployment

### Environment Variables

Configure the following environment variables for production:

```bash
ASPNETCORE_ENVIRONMENT=Production
ASPNETCORE_URLS=http://+:8080
ConnectionStrings__DefaultConnection=<your-connection-string>
JwtSettings__Secret=<your-jwt-secret>
JwtSettings__Issuer=VocabularyCardService
JwtSettings__Audience=VocabularyCardServiceUsers
JwtSettings__ExpirationMinutes=60
```

## Security

- All passwords are hashed using bcrypt
- JWT tokens for authentication
- HTTPS enforced in production
- Input validation and sanitization
- SQL injection prevention through parameterized queries
- Rate limiting on sensitive endpoints
- Security scanning in CI/CD pipeline

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Roadmap

### Phase 1 (Current)
- ✅ Basic vocabulary card CRUD operations
- ✅ User authentication and management
- ✅ Docker support
- ✅ CI/CD pipelines

### Phase 2 (Planned)
- Spaced repetition learning algorithm
- Study sessions and progress tracking
- Multiple learning modes (flashcards, quizzes, matching)
- Audio pronunciation support

### Phase 3 (Future)
- Mobile app integration
- Social features (share cards, follow users)
- AI-powered word suggestions
- Multi-language support for UI

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Contact

Project Link: [https://github.com/kevin61225/vocabulary-card-service](https://github.com/kevin61225/vocabulary-card-service)

## Acknowledgments

- Built with [.NET 10](https://dotnet.microsoft.com/)
- Containerized with [Docker](https://www.docker.com/)
- Following Spec-Driven Design principles
