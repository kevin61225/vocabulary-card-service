# Vocabulary Card Creation Specification

## Feature Overview

This specification defines the functionality for creating vocabulary cards in the system. Users can create vocabulary cards with translations in different languages to aid their language learning.

## User Stories

### US-001: Create a Basic Vocabulary Card
**As a** language learner  
**I want to** create a vocabulary card with a word and its translation  
**So that** I can start building my personal vocabulary collection

**Acceptance Criteria:**
- User can provide a word in the source language
- User can provide a translation in the target language
- User can specify source and target language codes (e.g., "en", "zh", "ja", "es")
- System validates that required fields are provided
- System generates a unique card ID upon creation
- System stores the creation timestamp

### US-002: Create a Detailed Vocabulary Card
**As a** language learner  
**I want to** add additional information to my vocabulary card  
**So that** I can have better context and examples for learning

**Acceptance Criteria:**
- User can optionally add pronunciation/phonetic notation
- User can optionally add example sentences
- User can optionally add notes or context
- User can optionally add difficulty level (beginner, intermediate, advanced)
- User can optionally add tags/categories

## API Endpoints

### POST /api/v1/vocabulary-cards

Create a new vocabulary card.

**Request Body:**
```json
{
  "sourceWord": "string (required, max 100 chars)",
  "translation": "string (required, max 100 chars)",
  "sourceLanguage": "string (required, ISO 639-1 code)",
  "targetLanguage": "string (required, ISO 639-1 code)",
  "pronunciation": "string (optional, max 100 chars)",
  "exampleSentence": "string (optional, max 500 chars)",
  "notes": "string (optional, max 1000 chars)",
  "difficultyLevel": "string (optional, enum: beginner|intermediate|advanced)",
  "tags": ["string (optional, array of strings)"]
}
```

**Response 201 (Created):**
```json
{
  "id": "uuid",
  "sourceWord": "string",
  "translation": "string",
  "sourceLanguage": "string",
  "targetLanguage": "string",
  "pronunciation": "string",
  "exampleSentence": "string",
  "notes": "string",
  "difficultyLevel": "string",
  "tags": ["string"],
  "userId": "uuid",
  "createdAt": "datetime (ISO 8601)",
  "updatedAt": "datetime (ISO 8601)"
}
```

**Response 400 (Bad Request):**
```json
{
  "error": "string",
  "message": "string",
  "validationErrors": [
    {
      "field": "string",
      "message": "string"
    }
  ]
}
```

**Response 401 (Unauthorized):**
```json
{
  "error": "Unauthorized",
  "message": "Authentication required"
}
```

## Business Rules

### BR-001: Required Fields
- `sourceWord`, `translation`, `sourceLanguage`, and `targetLanguage` are mandatory fields
- System must return 400 Bad Request if any required field is missing

### BR-002: Language Validation
- Language codes must be valid ISO 639-1 codes
- Source and target languages must be different
- System maintains a list of supported languages

### BR-003: Character Limits
- Source word and translation: maximum 100 characters
- Pronunciation: maximum 100 characters
- Example sentence: maximum 500 characters
- Notes: maximum 1000 characters

### BR-004: User Association
- Each vocabulary card must be associated with the authenticated user
- User ID is extracted from the JWT token

### BR-005: Duplicate Detection
- System should allow duplicate words (same word can have different translations or contexts)
- Each card has a unique ID regardless of content

### BR-006: Default Values
- If difficulty level is not specified, default to "beginner"
- Creation and update timestamps are automatically set by the system

## Validation Rules

1. **sourceWord**: Required, non-empty, max 100 chars, trim whitespace
2. **translation**: Required, non-empty, max 100 chars, trim whitespace
3. **sourceLanguage**: Required, must be valid ISO 639-1 code
4. **targetLanguage**: Required, must be valid ISO 639-1 code, must differ from sourceLanguage
5. **pronunciation**: Optional, max 100 chars
6. **exampleSentence**: Optional, max 500 chars
7. **notes**: Optional, max 1000 chars
8. **difficultyLevel**: Optional, must be one of: beginner, intermediate, advanced
9. **tags**: Optional, array of strings, max 10 tags, each tag max 50 chars

## Database Schema

### Table: VocabularyCards

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| SourceWord | NVARCHAR(100) | NOT NULL |
| Translation | NVARCHAR(100) | NOT NULL |
| SourceLanguage | VARCHAR(2) | NOT NULL |
| TargetLanguage | VARCHAR(2) | NOT NULL |
| Pronunciation | NVARCHAR(100) | NULL |
| ExampleSentence | NVARCHAR(500) | NULL |
| Notes | NVARCHAR(1000) | NULL |
| DifficultyLevel | VARCHAR(20) | DEFAULT 'beginner' |
| UserId | UUID | NOT NULL, Foreign Key |
| CreatedAt | DATETIME | NOT NULL |
| UpdatedAt | DATETIME | NOT NULL |

### Table: VocabularyCardTags

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| VocabularyCardId | UUID | Foreign Key |
| Tag | NVARCHAR(50) | NOT NULL |

## Supported Languages (Initial Phase)

- en: English
- zh: Chinese (Mandarin)
- ja: Japanese
- es: Spanish
- fr: French
- de: German
- ko: Korean

## Security Considerations

- All endpoints require JWT authentication
- Users can only create cards for themselves
- Input sanitization to prevent XSS and SQL injection
- Rate limiting: max 100 card creations per hour per user

## Testing Scenarios

### Positive Test Cases
1. Create card with all required fields only
2. Create card with all fields populated
3. Create card with various language combinations
4. Create multiple cards with same word but different translations

### Negative Test Cases
1. Create card without authentication token
2. Create card missing required fields
3. Create card with invalid language codes
4. Create card with same source and target language
5. Create card exceeding character limits
6. Create card with invalid difficulty level
7. Create card with more than 10 tags

## Performance Requirements

- Card creation should complete in < 200ms (95th percentile)
- System should support 1000 concurrent card creations
