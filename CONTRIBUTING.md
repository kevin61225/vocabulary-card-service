# Contributing to Vocabulary Card Service

Thank you for your interest in contributing to the Vocabulary Card Service! This document provides guidelines and instructions for contributing.

## Code of Conduct

- Be respectful and inclusive
- Welcome newcomers and help them get started
- Accept constructive criticism gracefully
- Focus on what is best for the community

## How to Contribute

### Reporting Bugs

Before creating a bug report, please check existing issues to avoid duplicates.

**Good Bug Report includes:**
- Clear, descriptive title
- Steps to reproduce the issue
- Expected behavior
- Actual behavior
- Environment details (OS, .NET version, etc.)
- Screenshots if applicable
- Error messages and stack traces

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- Use a clear, descriptive title
- Provide detailed description of the proposed feature
- Explain why this enhancement would be useful
- Include examples of how it would work
- Reference any relevant specifications or documentation

### Contributing Code

#### Spec-Driven Development Process

This project follows **Spec-Driven Design**. Before writing code:

1. **Discuss the Feature**: Open an issue to discuss your proposed changes
2. **Write the Specification**: Create or update specification in `specs/` directory
3. **Get Specification Approved**: Wait for maintainer approval of the spec
4. **Implement**: Write code that matches the approved specification
5. **Test**: Write comprehensive tests based on the specification
6. **Submit PR**: Create a pull request with your changes

#### Pull Request Process

1. **Fork the Repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/vocabulary-card-service.git
   ```

2. **Create a Branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/your-bug-fix
   ```

3. **Make Your Changes**
   - Follow the coding standards (see below)
   - Write or update tests
   - Update documentation as needed
   - Follow the spec-driven process

4. **Test Your Changes**
   ```bash
   # Run all tests
   dotnet test
   
   # Run code formatter
   dotnet format
   
   # Build the project
   dotnet build
   ```

5. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "feat: add vocabulary card search functionality"
   ```

   Follow [Conventional Commits](https://www.conventionalcommits.org/):
   - `feat:` - New feature
   - `fix:` - Bug fix
   - `docs:` - Documentation changes
   - `style:` - Code style changes (formatting, etc.)
   - `refactor:` - Code refactoring
   - `test:` - Adding or updating tests
   - `chore:` - Maintenance tasks

6. **Push to Your Fork**
   ```bash
   git push origin feature/your-feature-name
   ```

7. **Create Pull Request**
   - Go to the original repository
   - Click "New Pull Request"
   - Select your fork and branch
   - Fill out the PR template
   - Link related issues

#### Pull Request Checklist

- [ ] Code follows project coding standards
- [ ] All tests pass (`dotnet test`)
- [ ] Code is formatted (`dotnet format`)
- [ ] New tests added for new functionality
- [ ] Documentation updated (if applicable)
- [ ] Specification updated (if applicable)
- [ ] Commit messages follow conventional commits
- [ ] PR description clearly describes changes
- [ ] Related issues are linked

## Coding Standards

### C# Style Guide

- Follow [Microsoft C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- Use modern C# features (pattern matching, records, etc.)
- Prefer `var` when type is obvious
- Use nullable reference types
- Add XML documentation comments for public APIs

### Naming Conventions

```csharp
// Classes and Interfaces: PascalCase
public class VocabularyCard { }
public interface IVocabularyCardService { }

// Methods and Properties: PascalCase
public string SourceWord { get; set; }
public async Task<VocabularyCard> GetCardAsync(string id) { }

// Parameters and local variables: camelCase
public void CreateCard(string sourceWord, string translation) { }

// Private fields: _camelCase
private readonly ILogger _logger;

// Constants: UPPER_SNAKE_CASE
private const int MAX_CARDS_PER_USER = 10000;
```

### Code Organization

```csharp
// 1. Using statements (sorted)
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

// 2. Namespace (file-scoped in .NET 10)
namespace VocabularyCardService.API.Controllers;

// 3. XML documentation
/// <summary>
/// Handles vocabulary card operations
/// </summary>
public class VocabularyCardsController : ControllerBase
{
    // 4. Constants
    private const int DefaultPageSize = 20;
    
    // 5. Private readonly fields
    private readonly IVocabularyCardService _service;
    private readonly ILogger<VocabularyCardsController> _logger;
    
    // 6. Constructor
    public VocabularyCardsController(
        IVocabularyCardService service,
        ILogger<VocabularyCardsController> logger)
    {
        _service = service ?? throw new ArgumentNullException(nameof(service));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }
    
    // 7. Public methods
    // 8. Private methods
}
```

### Testing Guidelines

- Write unit tests for business logic
- Write integration tests for API endpoints
- Aim for high code coverage (>80%)
- Use meaningful test names: `MethodName_Scenario_ExpectedBehavior`
- Follow AAA pattern: Arrange, Act, Assert

```csharp
[Fact]
public async Task CreateCard_WithValidData_ReturnsCreatedCard()
{
    // Arrange
    var dto = new CreateVocabularyCardDto("hello", "你好", "en", "zh");
    
    // Act
    var result = await _service.CreateCardAsync(dto, "user-123");
    
    // Assert
    Assert.NotNull(result);
    Assert.Equal("hello", result.SourceWord);
}
```

## Specification Writing

When writing or updating specifications in `specs/`:

1. **Use Clear Structure**
   - Feature Overview
   - User Stories
   - API Endpoints
   - Business Rules
   - Database Schema
   - Security Considerations
   - Testing Scenarios

2. **Be Specific**
   - Define exact endpoint paths, HTTP methods
   - Include complete request/response examples
   - Specify all validation rules
   - Define error cases

3. **Follow Template**
   See existing specs for examples

4. **Get Review**
   Specifications should be reviewed before implementation

## Documentation

- Update README.md if you add user-facing features
- Update DEVELOPMENT.md if you change development workflows
- Add XML comments to public APIs
- Update API specifications in `specs/`
- Keep comments up-to-date with code changes

## Review Process

1. **Automated Checks**: CI/CD pipeline runs automatically
   - Build verification
   - Test execution
   - Code quality checks
   - Security scanning

2. **Code Review**: Maintainers will review your PR
   - Code quality
   - Test coverage
   - Documentation
   - Specification compliance

3. **Feedback**: Address review comments
   - Make requested changes
   - Push updates to your branch
   - Respond to questions

4. **Merge**: Once approved, maintainer will merge

## Development Setup

See [DEVELOPMENT.md](DEVELOPMENT.md) for detailed setup instructions.

Quick start:
```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/vocabulary-card-service.git

# Navigate to directory
cd vocabulary-card-service

# Restore dependencies
dotnet restore

# Run tests
dotnet test

# Run the application
cd src/VocabularyCardService.API
dotnet run
```

## Getting Help

- 📖 Read the [README.md](README.md)
- 📚 Check [DEVELOPMENT.md](DEVELOPMENT.md)
- 🔍 Search existing issues
- 💬 Ask questions in GitHub Discussions
- 📧 Contact maintainers if needed

## Recognition

Contributors will be recognized in:
- GitHub contributors list
- Release notes
- Special thanks in major releases

## License

By contributing, you agree that your contributions will be licensed under the Apache License 2.0.

---

Thank you for contributing to Vocabulary Card Service! 🎉
