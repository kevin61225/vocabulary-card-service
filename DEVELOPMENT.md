# Development Guide

## Overview

This document provides guidance for developers working on the Vocabulary Card Service API project.

## Project Architecture

The project follows a **Spec-Driven Design** approach:

1. **Specifications First**: All features are defined in markdown specifications in the `specs/` directory before implementation
2. **API Implementation**: Code is written to match the specifications
3. **Tests**: Tests are written based on the specifications
4. **Documentation**: API documentation is auto-generated from code and specifications

### Directory Structure

```
vocabulary-card-service/
├── specs/                                   # API Specifications (Spec-Driven Design)
│   ├── README.md                           # Specs overview
│   ├── vocabulary-card-creation.md         # Card creation spec
│   ├── vocabulary-card-management.md       # Card management spec
│   └── user-management.md                  # User management spec
├── src/
│   └── VocabularyCardService.API/          # Main API project
│       ├── Controllers/                    # API controllers (to be added)
│       ├── Models/                         # Domain models (to be added)
│       ├── Services/                       # Business logic (to be added)
│       ├── Data/                           # Database context and repositories (to be added)
│       ├── DTOs/                           # Data transfer objects (to be added)
│       ├── Middleware/                     # Custom middleware (to be added)
│       ├── Program.cs                      # Application entry point
│       └── appsettings.json                # Configuration
├── tests/                                  # Test projects (to be added)
│   ├── VocabularyCardService.API.Tests/   # Unit tests
│   └── VocabularyCardService.IntegrationTests/  # Integration tests
├── .github/workflows/                      # GitHub Actions CI/CD
├── .gitlab-ci.yml                          # GitLab CI configuration
├── Dockerfile                              # Docker configuration
├── docker-compose.yml                      # Docker Compose for local dev
└── VocabularyCardService.sln              # Solution file
```

## Getting Started

### Prerequisites

- .NET 10 SDK
- SQL Server (or Docker for containerized database)
- IDE: Visual Studio 2022, Visual Studio Code, or JetBrains Rider
- Git

### Initial Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/kevin61225/vocabulary-card-service.git
   cd vocabulary-card-service
   ```

2. **Restore NuGet packages**
   ```bash
   dotnet restore
   ```

3. **Set up the database**
   
   Option A: Using Docker Compose
   ```bash
   docker-compose up -d db
   ```
   
   Option B: Using local SQL Server
   - Install SQL Server
   - Update connection string in `appsettings.Development.json`

4. **Run the application**
   ```bash
   cd src/VocabularyCardService.API
   dotnet run
   ```

## Development Workflow

### 1. Spec-Driven Development Process

Follow this workflow when adding new features:

1. **Write the Specification**
   - Create or update markdown file in `specs/` directory
   - Define user stories, API endpoints, business rules
   - Include request/response examples
   - Define validation rules and error cases

2. **Review the Specification**
   - Get team review on the spec
   - Ensure all edge cases are covered
   - Validate with stakeholders if needed

3. **Implement the Feature**
   - Create models, DTOs, services, controllers
   - Follow the specification exactly
   - Use dependency injection
   - Write clean, testable code

4. **Write Tests**
   - Unit tests for business logic
   - Integration tests for API endpoints
   - Cover all scenarios from the spec

5. **Update Documentation**
   - Ensure XML comments are added
   - Update README if needed
   - Verify Swagger/OpenAPI docs are correct

### 2. Code Standards

#### Naming Conventions
- **Classes/Interfaces**: PascalCase (e.g., `VocabularyCard`, `IVocabularyCardService`)
- **Methods**: PascalCase (e.g., `CreateCard`, `GetCardById`)
- **Parameters/Variables**: camelCase (e.g., `sourceWord`, `userId`)
- **Private fields**: _camelCase (e.g., `_repository`, `_logger`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `MAX_CARDS_PER_USER`)

#### Code Organization
```csharp
// 1. Using statements
using System;
using Microsoft.AspNetCore.Mvc;

// 2. Namespace
namespace VocabularyCardService.API.Controllers;

// 3. Class with XML documentation
/// <summary>
/// Handles vocabulary card operations
/// </summary>
public class VocabularyCardsController : ControllerBase
{
    // 4. Private fields
    private readonly IVocabularyCardService _service;
    private readonly ILogger<VocabularyCardsController> _logger;

    // 5. Constructor
    public VocabularyCardsController(
        IVocabularyCardService service,
        ILogger<VocabularyCardsController> logger)
    {
        _service = service;
        _logger = logger;
    }

    // 6. Public methods
    // 7. Private methods
}
```

#### Dependency Injection

Register services in `Program.cs`:
```csharp
// Repository pattern
builder.Services.AddScoped<IVocabularyCardRepository, VocabularyCardRepository>();

// Services
builder.Services.AddScoped<IVocabularyCardService, VocabularyCardService>();

// DbContext
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

### 3. Database Migrations

```bash
# Add a new migration
dotnet ef migrations add MigrationName -p src/VocabularyCardService.API

# Update database to latest migration
dotnet ef database update -p src/VocabularyCardService.API

# Remove last migration (if not applied)
dotnet ef migrations remove -p src/VocabularyCardService.API

# Generate SQL script for a migration
dotnet ef migrations script -p src/VocabularyCardService.API
```

### 4. Testing

#### Unit Tests
```bash
# Run all tests
dotnet test

# Run with code coverage
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover

# Run specific test class
dotnet test --filter FullyQualifiedName~VocabularyCardServiceTests
```

#### Integration Tests
```bash
# Run integration tests only
dotnet test --filter Category=Integration
```

### 5. Code Formatting

```bash
# Check code formatting
dotnet format --verify-no-changes

# Auto-format code
dotnet format
```

## API Development

### Creating a New Endpoint

1. **Define in Specification** (`specs/`)
   ```markdown
   ### POST /api/v1/vocabulary-cards
   Create a new vocabulary card
   ```

2. **Create DTO** (`DTOs/`)
   ```csharp
   public record CreateVocabularyCardDto(
       string SourceWord,
       string Translation,
       string SourceLanguage,
       string TargetLanguage);
   ```

3. **Create Service** (`Services/`)
   ```csharp
   public interface IVocabularyCardService
   {
       Task<VocabularyCard> CreateCardAsync(CreateVocabularyCardDto dto, string userId);
   }
   ```

4. **Create Controller** (`Controllers/`)
   ```csharp
   [ApiController]
   [Route("api/v1/vocabulary-cards")]
   [Authorize]
   public class VocabularyCardsController : ControllerBase
   {
       [HttpPost]
       public async Task<ActionResult<VocabularyCardDto>> CreateCard(
           [FromBody] CreateVocabularyCardDto dto)
       {
           // Implementation
       }
   }
   ```

5. **Add Tests**
   ```csharp
   [Fact]
   public async Task CreateCard_WithValidData_ReturnsCreatedCard()
   {
       // Arrange, Act, Assert
   }
   ```

## Security Best Practices

1. **Authentication**: Use JWT tokens for all protected endpoints
2. **Authorization**: Verify user owns resources before modifying
3. **Input Validation**: Always validate and sanitize user input
4. **SQL Injection**: Use parameterized queries (Entity Framework does this)
5. **XSS Protection**: Encode output, use DTOs
6. **Secrets Management**: Never commit secrets, use environment variables
7. **HTTPS**: Always use HTTPS in production
8. **Rate Limiting**: Implement rate limiting on all endpoints

## Configuration

### Development Settings (`appsettings.Development.json`)
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=VocabularyCardService;Trusted_Connection=True;"
  },
  "JwtSettings": {
    "Secret": "development-secret-key-change-in-production-min-32-chars",
    "Issuer": "VocabularyCardService",
    "Audience": "VocabularyCardServiceUsers",
    "ExpirationMinutes": 60
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

### Production Settings (Environment Variables)
```bash
ConnectionStrings__DefaultConnection="..."
JwtSettings__Secret="..."
ASPNETCORE_ENVIRONMENT=Production
```

## Debugging

### Visual Studio Code
1. Press F5 or use Run > Start Debugging
2. Set breakpoints by clicking left of line numbers
3. Use Debug Console for evaluation

### Visual Studio
1. Press F5 to start debugging
2. Set breakpoints
3. Use Immediate Window for evaluation

### Logging
```csharp
// Inject ILogger<T>
private readonly ILogger<MyClass> _logger;

// Log at different levels
_logger.LogInformation("Card created: {CardId}", cardId);
_logger.LogWarning("User attempted unauthorized access: {UserId}", userId);
_logger.LogError(ex, "Failed to create card");
```

## Docker Development

### Build and Run with Docker
```bash
# Build image
docker build -t vocabulary-card-service:dev .

# Run container
docker run -d -p 8080:8080 --name vocab-api vocabulary-card-service:dev

# View logs
docker logs vocab-api -f

# Stop and remove
docker stop vocab-api
docker rm vocab-api
```

### Using Docker Compose
```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Rebuild after changes
docker-compose up -d --build

# Stop all services
docker-compose down

# Reset everything (including volumes)
docker-compose down -v
```

## CI/CD Pipeline

### GitHub Actions
- Triggers on push to `main` and `develop` branches
- Runs build, test, code quality checks
- Builds and pushes Docker images
- Runs security scans

### GitLab CI
- Multi-stage pipeline
- Code coverage reporting
- Container registry integration
- Manual deployment gates

## Troubleshooting

### Common Issues

**Problem**: `dotnet restore` fails
- **Solution**: Check internet connection, clear NuGet cache: `dotnet nuget locals all --clear`

**Problem**: Database connection fails
- **Solution**: Verify SQL Server is running, check connection string

**Problem**: Migration fails
- **Solution**: Check for conflicts, drop database and reapply all migrations

**Problem**: Docker build fails
- **Solution**: Check Dockerfile syntax, ensure all files are in build context

## Resources

- [.NET 10 Documentation](https://docs.microsoft.com/en-us/dotnet/)
- [ASP.NET Core Documentation](https://docs.microsoft.com/en-us/aspnet/core/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [REST API Guidelines](https://github.com/microsoft/api-guidelines)
- [Swagger/OpenAPI](https://swagger.io/specification/)

## Contributing

1. Create a feature branch from `develop`
2. Follow the spec-driven development process
3. Write tests for your changes
4. Ensure all tests pass
5. Run code formatter
6. Create a pull request
7. Address code review feedback

## Support

For questions or issues, please:
1. Check existing documentation
2. Search existing issues
3. Create a new issue with detailed information
