# API Quick Reference

Quick reference guide for the Vocabulary Card Service API.

## Base URL

- Development: `http://localhost:5000`
- Docker: `http://localhost:8080`
- Production: `https://api.vocabularycard.com` (to be configured)

## Authentication

All protected endpoints require a JWT token in the Authorization header:

```
Authorization: Bearer <your-jwt-token>
```

## Common Response Codes

- `200 OK` - Request succeeded
- `201 Created` - Resource created successfully
- `204 No Content` - Request succeeded with no response body
- `400 Bad Request` - Invalid request data
- `401 Unauthorized` - Missing or invalid authentication
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error

## Endpoints Overview

### Authentication (`/api/v1/auth`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/register` | Register new user | No |
| POST | `/login` | Login and get token | No |
| POST | `/refresh` | Refresh access token | No |
| POST | `/logout` | Logout user | Yes |
| POST | `/forgot-password` | Request password reset | No |
| POST | `/reset-password` | Reset password with token | No |
| POST | `/verify-email` | Verify email address | No |

### Users (`/api/v1/users`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/me` | Get current user profile | Yes |
| PUT | `/me` | Update user profile | Yes |
| POST | `/me/change-password` | Change password | Yes |
| DELETE | `/me` | Delete account | Yes |

### Vocabulary Cards (`/api/v1/vocabulary-cards`)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/` | Create new card | Yes |
| GET | `/` | List cards (with filters) | Yes |
| GET | `/{id}` | Get card by ID | Yes |
| PUT | `/{id}` | Update card | Yes |
| DELETE | `/{id}` | Delete card | Yes |
| GET | `/statistics` | Get user statistics | Yes |

## Request Examples

### Register User

```bash
curl -X POST http://localhost:8080/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecureP@ssw0rd",
    "confirmPassword": "SecureP@ssw0rd",
    "firstName": "John",
    "lastName": "Doe"
  }'
```

### Login

```bash
curl -X POST http://localhost:8080/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecureP@ssw0rd"
  }'
```

### Create Vocabulary Card

```bash
curl -X POST http://localhost:8080/api/v1/vocabulary-cards \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "sourceWord": "hello",
    "translation": "你好",
    "sourceLanguage": "en",
    "targetLanguage": "zh",
    "pronunciation": "nǐ hǎo",
    "exampleSentence": "Hello, how are you?",
    "difficultyLevel": "beginner",
    "tags": ["greeting", "common"]
  }'
```

### List Cards with Filters

```bash
curl -X GET "http://localhost:8080/api/v1/vocabulary-cards?page=1&pageSize=20&sourceLanguage=en&targetLanguage=zh&difficultyLevel=beginner" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Search Cards

```bash
curl -X GET "http://localhost:8080/api/v1/vocabulary-cards?search=hello" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Update Card

```bash
curl -X PUT http://localhost:8080/api/v1/vocabulary-cards/CARD_ID \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "notes": "Updated notes for this card",
    "difficultyLevel": "intermediate"
  }'
```

### Delete Card

```bash
curl -X DELETE http://localhost:8080/api/v1/vocabulary-cards/CARD_ID \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Statistics

```bash
curl -X GET http://localhost:8080/api/v1/vocabulary-cards/statistics \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Query Parameters

### Pagination

- `page` (integer, default: 1) - Page number
- `pageSize` (integer, default: 20, max: 100) - Items per page

### Filtering (Vocabulary Cards)

- `search` (string) - Search in source word and translation
- `sourceLanguage` (string) - Filter by source language (ISO 639-1)
- `targetLanguage` (string) - Filter by target language (ISO 639-1)
- `difficultyLevel` (string) - Filter by difficulty (beginner, intermediate, advanced)
- `tags` (string) - Comma-separated list of tags
- `createdAfter` (datetime) - Cards created after this date
- `createdBefore` (datetime) - Cards created before this date

### Sorting (Vocabulary Cards)

- `sortBy` (string, default: createdAt) - Field to sort by
  - `createdAt` - Creation date
  - `sourceWord` - Alphabetically by source word
  - `difficultyLevel` - By difficulty level
- `sortOrder` (string, default: desc) - Sort order (asc, desc)

## Supported Languages

ISO 639-1 codes:

- `en` - English
- `zh` - Chinese (Mandarin)
- `ja` - Japanese
- `es` - Spanish
- `fr` - French
- `de` - German
- `ko` - Korean

## Difficulty Levels

- `beginner` - Beginner level
- `intermediate` - Intermediate level
- `advanced` - Advanced level

## Rate Limits

- Login: 5 attempts per 15 minutes per email
- Registration: 5 per hour per IP
- Password Reset: 3 per hour per email
- Card Creation: 100 per hour per user
- Card List/Search: 100 per minute per user
- Card Get: 500 per minute per user
- Card Update: 50 per minute per user
- Card Delete: 20 per minute per user

## Error Response Format

```json
{
  "error": "ErrorType",
  "message": "Human-readable error message",
  "validationErrors": [
    {
      "field": "fieldName",
      "message": "Field-specific error message"
    }
  ]
}
```

## Response Formats

### Success Response (List)

```json
{
  "items": [...],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

### Success Response (Single Resource)

```json
{
  "id": "uuid",
  "field1": "value1",
  "field2": "value2",
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-15T10:30:00Z"
}
```

## Testing with Swagger

Once the API is running, access interactive documentation at:

- Development: `http://localhost:5000/swagger`
- Docker: `http://localhost:8080/swagger`

## Additional Resources

- Full API Specifications: See `specs/` directory
- Development Guide: See `DEVELOPMENT.md`
- Contributing Guide: See `CONTRIBUTING.md`
